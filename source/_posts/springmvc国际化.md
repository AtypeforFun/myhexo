---
title: 基于统一异常处理的springmvc国际化
date: 2019-08-17 11:28:59
categories:
- 技术
tags:
- spring
---
 
 公司项目使用了比较老的springmvc，要对一些后台response的提示信息做国际化，这里做一下记录。

<!--more-->
 
 <!-- 绑定国际化资源 -->
    <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basenames">
          <list>
             <value>errorCode</value>
          </list>
        </property>
    </bean>
    
basenames处放buddle资源    
    
    
- 统一的异常处理
```

@Slf4j
public  class TimeBaseController extends BaseController{

    private static final String DEFAULT_MSG_EN = "please try latter";
    private static final String DEFAULT_MSG_CH = "请稍后重试";
    // 用于解析buddle中的语言分类
    private static MessageSource messageSource;

    static {
        messageSource = SpringContextUtils.getBean("messageSource", MessageSource.class);
    }

    @ExceptionHandler
    @ResponseBody
    protected ResponseDto timeExceptionHandler(Exception ex, Locale local) {
        log.error("出错了--error--", ex);
        //请求头 当前语言
        // Accept-Language: zh-CN
        // Accept-Language: en-US
       // LocaleResolver localeResolver = RequestContextUtils.getLocaleResolver(request);
        //Locale local= localeResolver.resolveLocale(request);
        String language = local.getLanguage();
        String defaultMsg = DEFAULT_MSG_EN;
        if (Locale.CHINA.getLanguage().equals(language)){
            defaultMsg = DEFAULT_MSG_CH;
        }
        if (ex instanceof TtimeInternalException) {
            int errorCode = ((TimeInternalException) ex).getErrorCode();
            Object[] args = ((TimeInternalException) ex).getArgs();
            String message = messageSource.getMessage(String.valueOf(errorCode), args,defaultMsg,local);
            return ajaxDoneError(message);
        } else {
            return ajaxDoneError(defaultMsg);
        }
    }

}

```

- dubbo 接口通过异常的code进行异常信息的标记
```

public class TimeInternalException extends RuntimeException {

    /**
     * 用于区分不同的异常类型，便于web层做提示信息的国际化
     */
    private int errorCode;
    /**
     * 便于web层做提示信息的国际化提供参数
     */
    private Object[] args;

    public TimeInternalException() {
        super();
    }

    public TimeInternalException(String message) {
        super(message);
    }

    public TimeInternalException(int errorCode,String message) {
        super(message);
        this.errorCode = errorCode;
    }
    public TimeInternalException( int errorCode,String message, Object[] args) {
        super(message);
        this.errorCode = errorCode;
        this.args = args;
    }

    .....
}

```