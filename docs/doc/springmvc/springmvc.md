## springmvc 执行流程

![](../../.images/springmvc/process.png)

## 过滤器

## 拦截器

## 数据绑定

## 消息转换器

## 异常处理
* 注解ExceptionHandler
    > 注解ExceptionHandler作用对象为方法，最简单的使用方法就是放在controller文件中，详细的注解定义不再介绍。
    > 如果项目中有多个controller文件，通常可以在baseController中实现ExceptionHandler的异常处理，而各个contoller继承basecontroller从而达到统一异常处理的目的。因为比较常见，简单代码如下：

    <details><summary>单个controller处理</summary>

    ```java
    @ExceptionHandler(Exception.class)
    @ResponseBody
    public String exceptionHandler(Exception ex){
        return this.getClass().getSimpleName() + ": " + ex.getMessage();
    } 
    ```
    </details>

* 注解ControllerAdvice
    > 这里虽说是ControllerAdvice注解，其实是其与ExceptionHandler的组合使用。
    > 在上文中可以看到，单独使用@ExceptionHandler时，其必须在一个Controller中，然而当其与ControllerAdvice组合使用时就完全没有了这个限制。换句话说，二者的组合达到的全局的异常捕获处理。
    > 
    > `***` 注解ControllerAdvice 等级低于第一个。而且第一个只处理当前Controller。顺序为 @ExceptionHandler,@ControllerAdvice,HandlerExceptionResolver 
    
    <details><summary>全局异常处理</summary>

    ```java
    @ControllerAdvice
    public class ExceptionHandlerAdvice {
        //也可以出现   自定义Exception，和其他Exception
        @ExceptionHandler(Exception.class)
        @ResponseBody
        public String exceptionHandler(Exception ex){
            return this.getClass().getSimpleName() + ": " + ex.getMessage();
        }
    }   
    ```
    </details>

* 实现HandlerExceptionResolver接口
    > HandlerExceptionResolver本身SpringMVC内部的接口，其内部只有resolveException一个方法，通过实现该接口我们可以达到全局异常处理的目的。
    > 
    > `***` 400异常不会处理，500 可以 出现的原因；被默认的处理器处理了，如图： https://segmentfault.com/img/bVbnJqq?w=866&h=497
    > 解决办法，实现ordered 接口排序第一位。就可以实现所以异常处理。

    <details><summary>全局异常处理</summary>

    ```java
    @Component
    public class MyHandlerExceptionResolver implements HandlerExceptionResolver, Ordered {

        @Override
        public ModelAndView resolveException(HttpServletRequest request,
                                             HttpServletResponse response,
                                             Object handler,
                                             Exception ex) {
            PrintWriter writer = null;
            try {
                writer = response.getWriter();
            } catch (IOException e) {
                e.printStackTrace();
            }
            writer.write(this.getClass().getSimpleName() + ": " + ex.getMessage());
            writer.flush();
            writer.close();
            return new ModelAndView();
        }

        @Override
        public int getOrder() {
            return -1;
        }
    }
    ```
    </details>
  

## Reference
* [深入理解Spring异常处理](https://segmentfault.com/a/1190000018010162)