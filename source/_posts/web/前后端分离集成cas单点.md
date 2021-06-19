---
title: 前后端分离集成cas单点
date: 2020-05-07 09:15:18
tags: ['web', 'cas', '单点登录']
categories: web
---
<!-- toc -->
### 不能使用传统的方式集成cas
1.原来客户端集成cas，主要是判断用户是否登录，如果没登录则重定向到单点登录页面。
2.如果前后端分离，前端在没登录的情况下，发起ajax请求，请求会被重定向到单点登录页面。
3.ajax不能捕获302重定向，当服务器将302响应发给浏览器时，浏览器并不是直接进行ajax回调处理，而是先执行302重定向——从Response Headers中读取Location信息，然后向Location中的Url发出请求，在收到这个请求的响应后才会进行ajax回调处理。大致如下：
jax -> browser -> server -> 302 -> browser(redirect) -> server -> browser -> ajax callback
所以，如果你想在ajax请求中根据302响应通过location.href进行重定向是不可行的。

### 改造单点登录
#### 自定义filter
自定义一个filter，判断用户是否登录，如果用户没有登录，不需要重定向，而是返回json数据告诉前端该用户没有登录，比如{"code":"1302"}，前端判断code="1302"，则前端跳转到单点登录页面
``` java
public class SSOFilter implements Filter {
    private String serverIndex;
    @Override
    public void init(FilterConfig filterConfig) {
        this.serverIndex = "/index";
    }
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;
        // cas票据验证入口不需要过滤
        if (this.serverIndex != null && this.serverIndex.equals(request.getServletPath())) {
            filterChain.doFilter(request, response);
            return;
        }
        final HttpSession session = request.getSession(false);
        final Assertion assertion = session != null ? (Assertion) session.getAttribute(AuthenticationFilter.CONST_CAS_ASSERTION) : null;
        if (assertion != null) {
            filterChain.doFilter(request, response);
            return;
        }
        response.setCharacterEncoding("UTF-8");
        response.setHeader(HttpHeaders.CONTENT_TYPE, "application/json;charset=UTF-8");
        response.getWriter().write("{'code':'1302'}");
    }
    @Override
    public void destroy() {

    }
}
```

#### 添加cas票据验证入口
单点登录页面登录成功后，会重定向到一个地址，并且会带上票据，比如https://sign.xxx.com/login?service=http://xx.xx.xx.xx:8888/index?ticket=ST-28505-xxMljPRj27Ojb61CpsIy5ASUJl4mvxl79xx，当首次访问客户端时，cas会验证票据，如果票据验证通过，则新增一个session。因为需要一个入口验证票据，所以重定向的地址service=xxxxx，不能被自定义的filter过滤。
``` java
@RequestMapping("/index")
public ModelAndView index() {
//        重定向到前端主页面
    return new ModelAndView(new RedirectView("xxxx"));
}
```
还有另外一种方法，不需要入口去验证票据，就是自定义过滤器加上一个判断，如果参数带ticket，则放行，之后就交给cas去验证。如果使用这这方式，单点登录页面登录成功后应该跳转到前端页面。

#### spring boot配置cas客户端
以下就是配置cas代码，注意cas各个过滤器的过滤顺序。
``` java
// 客户端地址
private String serverName = "xxxx";

// cas服务端地址
private String casServerUrlPreFix = "xxxx";

// cas服务端登录地址
private String casServerLoginUrl = "xxxx";

/**
    * 该过滤器用于实现单点登出功能，单点退出配置，一定要放在其他filter之前
    */
@Bean
public FilterRegistrationBean singleSignOutFilter() {
    FilterRegistrationBean<SingleSignOutFilter> filterRegistration = new FilterRegistrationBean<>();
    SingleSignOutFilter filter = new SingleSignOutFilter();
    filterRegistration.setFilter(filter);
    filterRegistration.addUrlPatterns("/*");
    filterRegistration.addInitParameter("casServerUrlPrefix", casServerUrlPreFix);
    filterRegistration.setOrder(1);
    return filterRegistration;
}

/**
    * 用于实现单点登出功能
    */
@Bean
public ServletListenerRegistrationBean<SingleSignOutHttpSessionListener> singleSignOutHttpSessionListener() {
    ServletListenerRegistrationBean<SingleSignOutHttpSessionListener> listener = new ServletListenerRegistrationBean<>();
    listener.setListener(new SingleSignOutHttpSessionListener());
    listener.setOrder(1);
    return listener;
}

/**
    * 配置自定义过滤器
    * @return
    */
@Bean
public FilterRegistrationBean ssoRedirectFilter() {
    FilterRegistrationBean<SSOFilter> filterRegistrationBean = new FilterRegistrationBean<>();
    SSOFilter filter = new SSOFilter();
    filterRegistrationBean.setFilter(filter);
    filterRegistrationBean.addUrlPatterns("/*");
    filterRegistrationBean.addInitParameter("serverIndex", "/index");
    filterRegistrationBean.setOrder(2);
    return filterRegistrationBean;
}

/**
    * 该过滤器负责用户的认证工作
    */
@Bean
public FilterRegistrationBean authenticationFilter() {
    FilterRegistrationBean<AuthenticationFilter> filterRegistration = new FilterRegistrationBean<>();
    AuthenticationFilter filter = new AuthenticationFilter();
    filterRegistration.setFilter(filter);
    filterRegistration.addUrlPatterns("/index");
    //casServerLoginUrl:cas服务的登陆url
    filterRegistration.addInitParameter("casServerLoginUrl", casServerLoginUrl);
    //本项目登录ip+port
    filterRegistration.addInitParameter("serverName", serverName);
    filterRegistration.setOrder(3);
    return filterRegistration;
}

/**
    * 该过滤器使得可以通过org.jasig.cas.client.util.AssertionHolder来获取用户的登录名。
    * 比如AssertionHolder.getAssertion().getPrincipal().getName()。
    * 这个类把Assertion信息放在ThreadLocal变量中，这样应用程序不在web层也能够获取到当前登录信息
    */
@Bean
public FilterRegistrationBean assertionThreadLocalFilter() {
    FilterRegistrationBean<AssertionThreadLocalFilter> filterRegistration = new FilterRegistrationBean<>();
    filterRegistration.setFilter(new AssertionThreadLocalFilter());
    filterRegistration.addUrlPatterns("/*");
    filterRegistration.setOrder(4);
    return filterRegistration;
}

/**
    * 该过滤器负责对Ticket的校验工作
    */
@Bean
public FilterRegistrationBean cas20ProxyReceivingTicketValidationFilter() {
    FilterRegistrationBean<Cas20ProxyReceivingTicketValidationFilter> filterRegistration = new FilterRegistrationBean<>();
    Cas20ProxyReceivingTicketValidationFilter filter = new Cas20ProxyReceivingTicketValidationFilter();
    filterRegistration.setFilter(filter);
    filterRegistration.addUrlPatterns("/index");
    filterRegistration.addInitParameter("casServerUrlPrefix", casServerUrlPreFix);
    filterRegistration.addInitParameter("serverName", serverName);
    filterRegistration.setOrder(5);
    return filterRegistration;
}

/**
    * 该过滤器对HttpServletRequest请求包装， 可通过HttpServletRequest的getRemoteUser()方法获得登录用户的登录名
    */
@Bean
public FilterRegistrationBean httpServletRequestWrapperFilter() {
    FilterRegistrationBean<HttpServletRequestWrapperFilter> filterRegistration = new FilterRegistrationBean<>();
    filterRegistration.setFilter(new HttpServletRequestWrapperFilter());
    filterRegistration.addUrlPatterns("/*");
    filterRegistration.setOrder(6);
    return filterRegistration;
}
```

### 配置nginx解决跨域问题
前后端分离，存在跨域问题，可以配置nginx解决，而且还一看用nginx实现负载均衡功能。