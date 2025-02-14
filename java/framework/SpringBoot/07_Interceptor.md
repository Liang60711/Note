## 攔截器
1. 攔截 request 到 controller 之間的程式，多半用於授權及驗證，類似 laravel 的 middleware 功能。

2. 自定義的攔截器會注入到`有@Configuration註解的設定類`中。

3. 使用 Content-Type 為 `multipart/form-data` 的資料格式，如果要做 formData 中的參數檢核(是否有xss)，只能在 interceptor 做，不能提前到 filter 做。 

    * 原因 : 一個請求到達 filter 時還未經過 spring 的請求解析，到達 interceptor 時請求已經經過 spring 的解析，而 spring 對`multipart/form-data`方式請求已做處理。

    * 此`multipart/form-data`請求，到達 interceptor 時，已經封裝成 DefaultMultipartHttpServletRequest，這個類的getParameter方法可以獲取到 multipart/form-data 和非 multipart/form-data 方法上傳的參數。


4. HandlerInterceptor 介面下有三個抽象方法

    * preHandle()：請求送到Controller前執行，回傳一個布林值，如果是true通過攔截器，反之則否。
    * postHandle()：Controller處理完後執行。
    * afterCompletion()：整個請求、回應、view渲染結束後執行，用來釋放資源，若該攔截器 preHandle 返回 false，則不會執行。

    ```java
    // 自定義 Interceptor
    @Component
    public class MyInterceptor implements HandlerInterceptor {

        @Override
        public boolean preHandle(HttpServletRequest request, 
                                 HttpServletResponse response, 
                                 Object handler) throws Exception {
            System.out.println("I am Interceptor");

            // 判斷邏輯
            if (login) {
                return true;//true允許通過
            } else {

                // 以下二選一
                // 1.轉導到首頁(sendRedirect是重發一個request，所以無法使用setAttribute帶參數)
                response.sendRedirect("/");

                // 2.轉導到首頁(帶參數)
                request.setAttribute("msg", "請先登入");
                request.getRequestDispatcher("/").forward(request, response);

                return false;
            }
        }
    }
    ```

    forward 和 sendRedirect 差別: 
    > https://ithelp.ithome.com.tw/articles/10185109


5. 建立一個專門設定攔截器的類，並實作 `WebMvcConfigurer` 介面，先將自定義攔截器注入後，並實作`addInterceptors`方法。

    ```java
    @Configuration
    public class InterceptorConfig implements WebMvcCoonfigurer {

        @Autowired
        private MyInterceptor myInterceptor;//注入攔截器

        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            // 登記到類別為InterceptorRegistration的ArrayList中
            registry.addInterceptor(myInterceptor)
            .addPathPatterns("/**") // 所有路徑都會攔截(連靜態資源都會攔截)
            .excludePathPatterns(
                "/", 
                "/login", 
                "/css/**", 
                "/js/**",
                "/fonts/**",
                "/images/**"
            ); // 放行的路徑
        }
    }
    ```

<br/>

<br/>

## 攔截器規則
1. 根據當前請求，找到 `HandlerExecutionChain` (所有攔截器鍊)。
2. 依照順序執行所有攔截器的 `preHandle` 方法。

    * 如果攔截器返回 true，則執行下一個攔截器的 preHandle。
    * 如果攔截器返回 false，則`倒序`執行之前返回 true 的攔截器的 `afterCompletion` 方法，注意 : 返回false的攔截器不會觸發 `afterCompletion`。

3. 如果任何一個攔截器返回 false，則不執行該路徑的方法(controller)。

4. 如果所有攔截器返回 true，執行該路徑的方法，並且`倒序`執行`postHandle`方法。

5. 以上步驟，若產生 exception，會直接觸發已經執行過的攔截器的 `afterCompletion`。

6. 舉例，攔截器 1，2，3，執行到 1 的 preHandle 產生 exception，則只會觸發 1 的 afterCompletion。

<br/>

<br/>

## filter 和 interceptor 差別

1. `實現的原理不同`:  

    * filter 是基於函數 `callback` 調用；interceptor 是基於 spring mvc 機制實現。

    * filter 會實現一個 dofilter()

        ```java
        public class HttpMethodFilter extends GenericFilterBean {

            @Override
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                // ...

                chain.doFilter(request, response); // 最後結束本 filter 時，會call filterChain 的下一個 dofilter()，達到callback的效果。
            }
        }
        ```

    * 如果直接在 doFilter 中使用 `return`，代表本過濾器的邏輯終止，依照是否繼續呼叫 chain.doFilter() 判斷是否繼續執行其他 filter chain。

    
    * interceptor 會針對攔截條件的前後加上 preHandle(), postHandle(), afterCompletion()

2. `依賴不同`

    * filter 是存在於 `javax.servlet.Filter`，此 interface 是 Servlet 規範中定義的，所以 filter 是依賴於 Servlet 容器，導致只能在 web 中使用。
    * interceptor 是由 Spring 容器管理，不依賴 Servlet，可以單獨使用，不僅用在 web 中，也可以用在其他 Application 中。

3. `觸發順序`

    * filter 觸發在 request 進入容器後，在 servlet 前執行。
    * interceptor 觸發在 request 進入 servlet 後，在 controller 前執行。

    <img src="https://img-blog.csdnimg.cn/20200602173814901.png?#pic_center" width="50%">


4. `觸發時機`

    * filter 幾乎可以對所有 request 起作用。
    * interceptor 只能對 controller 或 static 下的資源起作用。

5. `執行鍊順序`

    * filter 使用 @Order 來控制 filterChain 的執行順序，越小的越優先。

        ```java
        // filter
        @Order(1)
        @Component
        public class httpMethodFilter extend Filter {

        }
        ```

    * interceptor 預設的執行順序是 registry 的順序，也可以通過 `order()` 設置，越小的越優先。

        ```java
        // interceptor
        @Configuration
        public class InterceptorConfig implements WebMvcCoonfigurer {

            @Override
            public void addInterceptors(InterceptorRegistry registry) {
                registry.addInterceptor(interceptor1).addPathPatterns("/**").order(2);
                registry.addInterceptor(interceptor2).addPathPatterns("/**").order(1);
                registry.addInterceptor(interceptor3).addPathPatterns("/**").order(3);
            }
        }
        ```

<br/>

<br/>


## 參考
> https://segmentfault.com/a/1190000022833940