---
layout: post
title:  "LogBack sl4j 通过MDC实现日志记录区分用户Session[以Spring mvc为例]"
date:   2014-08-19 12:37:55
categories: Java
---

1.首先实现一个interceptor,在请求开始的时候MDC put一个Session标志,interceptor结束的时候remove掉

{% highlight java %}
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.slf4j.MDC;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;
public class SessionInterceptor extends HandlerInterceptorAdapter {
    /**
     * 会话ID
     */
    private final static String SESSION_KEY = "sessionId";

    @Override
    public void afterCompletion(HttpServletRequest arg0,
            HttpServletResponse arg1, Object arg2, Exception arg3)
            throws Exception {

        // 删除
        MDC. remove(SESSION_KEY);
    }

    @Override
    public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1,
            Object arg2, ModelAndView arg3) throws Exception {
    }

    @Override
    public boolean preHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler) throws Exception {

        // 放SessionId
        String token = TokenUtil. java.util.UUID.randomUUID().toString();
        MDC. put(SESSION_KEY, token);

        return true;
    }
}
{% endhighlight %}

2.然后在logback相应的配置中加上 seesionId的配置就可以了

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
        <property name="log.base" value="./log/logback" />

        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
               <encoder>
                      <pattern> %X{sessionId} %date [%thread] %-5level %logger{80} - %msg%n
                      </pattern>
               </encoder>
        </appender>

        <appender name="logfile"
               class="ch.qos.logback.core.rolling.RollingFileAppender">
               <File> ${log.base}.log</File >
               <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                      <FileNamePattern> ${log.base}.%d{yyyy -MM-dd}.log.zip </FileNamePattern>
               </rollingPolicy>
               <encoder>
                      <pattern> %X{sessionId} %date [%thread] %-5level %logger{80} - %msg%n
                      </pattern>
               </encoder>
        </appender>
        <logger name="com.sample" level="TRACE" />
        <root>
               <level value="INFO" />
               <appender-ref ref="stdout" />
               <appender-ref ref="logfile" />
        </root>
</configuration>
{% endhighlight %}

3.3.日志打出的时候就会有可以标识区分不同客户端的请求了,用前面不同的token区分

{% highlight xml %}
e6668b68-ee47-4cde-b673-25ed9bb74f1e 2014-04-09 16:31:14,579 [qtp14850080-27] INFO  o.c.a.f.i.AuthInterceptor - GET:/project
c3b84462-81c6-49f7-923b-d8ba48c53c07 2014-04-09 16:31:31,295 [qtp14850080-27] INFO  o.c.a.f.i.AuthInterceptor - GET:/feedbacks
b0e0f1fe-f30a-42b2-a103-e70a108561b7 2014-04-09 16:31:32,254 [qtp14850080-22] INFO  o.c.a.f.i.AuthInterceptor - GET:/feedbacks
a58ed619-c2e0-4b71-9c45-995a78c1b602 2014-04-09 16:31:42,788 [qtp14850080-29] INFO  o.c.a.f.i.AuthInterceptor - GET:/project
70de174a-5a05-41c6-a8b4-f17fcd33be5c 2014-04-09 16:31:43,537 [qtp14850080-22] INFO  o.c.a.f.i.AuthInterceptor - GET:/project
f3efb2d4-f361-4c6b-bab6-16c8476c8dd0 2014-04-09 16:31:47,157 [qtp14850080-26] INFO  o.c.a.f.i.AuthInterceptor - POST:/project
f3efb2d4-f361-4c6b-bab6-16c8476c8dd0 2014-04-09 16:31:47,161 [qtp14850080-26] INFO  o.c.a.f.i.ProjectController - >> create() > GOT PARAMS 'Project [name=url, abstractContent=a, token=null]','(POST /project)@12571381 org.eclipse.jetty.server.Request@bfd2f5'
{% endhighlight %}

参考:<a href="http://logback.qos.ch/manual/mdc.html#autoMDC">http://logback.qos.ch/manual/mdc.html#autoMDC</a>
