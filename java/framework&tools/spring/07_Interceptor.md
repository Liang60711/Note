## 攔截器
1. 攔截 request 到 controller 之間的程式，多半用於授權及驗證，等於 laravel 的 middleware 功能。

2. 自定義的攔截器會注入到有`@Configuration註解的設定類`中。


3. HandlerInterceptor 介面下有三個抽象方法

    * preHandle()：請求送到Controller前執行，回傳一個布林值，如果是true通過攔截器，反之則否。
    * postHandle()：Controller處理完後執行。
    * afterCompletion()：整個請求及回應結束後執行。

    ```java
    // 自定義 Interceptor
    @Component
    public class MyInterceptor implements HandlerInterceptor {

        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            System.out.println("I am Interceptor");
            return true;//true允許通過
        }
    }
    ```

4. 建立一個專門設定攔截器的類，並實作 `WebMvcConfigurer` 介面，先將自定義攔截器注入後，並實作`addInterceptors`方法。

    ```java
    @Configuration
    public class InterceptorConfig implements WebMvcCoonfigurer {

        @Autowired
        private MyInterceptor myInterceptor;//注入攔截器

        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            // 登記到類別為InterceptorRegistration的ArrayList中
            registry.addInterceptor(myInterceptor).addPathPatterns("/**");
        }
    }
    ```