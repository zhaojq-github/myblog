# springmvc json序列化和反序列化自定义配置 fastjson jackson配置

```java
package com.kfit.common.config.mvc;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.json.MappingJackson2HttpMessageConverter;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.text.SimpleDateFormat;
import java.util.List;

@Configuration
public class SpringMVCConfig implements WebMvcConfigurer {

    //<editor-fold desc="======>>spirngmvc json序列化指定">
    //<editor-fold desc="（1）启动implements WebMvcConfigurer">
//    @Override
//    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
//        /*
//         * 1、需要先定义一个 convert 转换消息的对象;
//         * 2、添加fastJson 的配置信息，比如：是否要格式化返回的json数据;
//         * 3、在convert中添加配置信息.
//         * 4、将convert添加到converters当中.
//         *
//         */
//
//        // 1、需要先定义一个 convert 转换消息的对象;
//        FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
//
//        //2、添加fastJson 的配置信息，比如：是否要格式化返回的json数据;
//        FastJsonConfig fastJsonConfig = new FastJsonConfig();
//        fastJsonConfig.setSerializerFeatures(SerializerFeature.DisableCircularReferenceDetect,
//                SerializerFeature.WriteMapNullValue);
//
//        //3、在convert中添加配置信息.
//        fastConverter.setFastJsonConfig(fastJsonConfig);
//
//        //4、将convert添加到converters当中.  注意添加在列表的前面才能生效
//        converters.add(0,fastConverter);
//    }

    /**
     * <B>Description:</B> jackson自定义序列化配置 <br>
     * <B>Create on:</B> 2019-01-06 22:02 <br>
     *
     * @author xiangyu.ye
     */
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {

        for (HttpMessageConverter<?> converter : converters) {
            if (converter instanceof MappingJackson2HttpMessageConverter){
                MappingJackson2HttpMessageConverter messageConverter = (MappingJackson2HttpMessageConverter) converter;
                ObjectMapper objectMapper = messageConverter.getObjectMapper();
                //jackson自定义配置
                objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
                break;
            }
        }
    }
    //</editor-fold>

    //<editor-fold desc="（2）覆盖方法configureMessageConverters">
    /**
     * 在这里我们使用 @Bean注入 fastJsonHttpMessageConvert
     */
//    @Bean
//    public HttpMessageConverters fastJsonHttpMessageConverters() {
//        // 1、需要先定义一个 convert 转换消息的对象;
//        FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
//
//        //2、添加fastJson 的配置信息，比如：是否要格式化返回的json数据;
//        FastJsonConfig fastJsonConfig = new FastJsonConfig();
//        fastJsonConfig.setSerializerFeatures(SerializerFeature.DisableCircularReferenceDetect,
//                SerializerFeature.WriteMapNullValue);
//
//        //3、在convert中添加配置信息.
//        fastConverter.setFastJsonConfig(fastJsonConfig);
//
//        HttpMessageConverter<?> converter = fastConverter;
//        return new HttpMessageConverters(converter);
//    }
    //</editor-fold>

    //</editor-fold>
 

}

```

