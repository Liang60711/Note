## 1. 添加依賴

swagger2 使用 `SpringFox`

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>3.0.0</version>    <!--可改-->
</dependency>
```
swagger3 使用 `OpenAPI 3.0`
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.8</version>    <!--可改-->
</dependency>
```

<br/>

<br/>

## 2. 設定檔
```yml
springdoc:
   writer-with-default-pretty-printer: true
   swagger-ui:
      path: /api/incommon/v2/docs/index.html # 輸入此url導到swagger-ui頁面
      disable-swagger-default-url: true # 關閉內建的範例
   api-docs:
      path: /api/incommon/v2/docs/category
```

<br/>

<br/>

## 3. 新增註解

建立一個 `SwaggerConfig`

```java
@Configuration
@OpenAPIDefinition(
    info = @Info(title = "ProjectName", version = "1.0.0"),
    servers = { @Server(url = "/") }
)
public class SwaggerConfig {

    private static final String PACKAGE_CONTROLLER_AUTH = "com.insynerger.common.controller.auth";

    @Value("${common.version:1.0}")
    private String version;

    @Bean
    @Primary
    public SpringDocConfigProperties springDocConfigProperties(RequestMappingHandlerMapping requestMappingHandlerMapping) {
        SpringDocConfigProperties prop = new SpringDocConfigProperties();
        prop.setPackagesToScan(Arrays.asList(PACKAGE_CONTROLLER_AUTH));
        prop.setPathsToMatch(Arrays.asList(collectExposedApis(requestMappingHandlerMapping)));
        return prop;
    }

    @Bean
    public OpenAPI apiInfo() {
        return new OpenAPI() //
                .info(new Info() //
                        .title("InSynerger Common APIs") //
                        .version(version));
    }

    @Bean
    public GroupedOpenApi commonApi(RequestMappingHandlerMapping requestMappingHandlerMapping) {
        return GroupedOpenApi.builder() //
                .group("common") //
                .packagesToScan(PACKAGE_CONTROLLER_AUTH) //
                .pathsToMatch(collectExposedApis(requestMappingHandlerMapping)) //
                .addOpenApiCustomiser(this::sortSchemasByAlphabet) //
                .build();
    }

    private String[] collectExposedApis(RequestMappingHandlerMapping requestMappingHandlerMapping) {
        // 掃瞄所有的 API，收集以 @Operation 標注的 API method
        return requestMappingHandlerMapping.getHandlerMethods().entrySet().stream() //
                .filter(entry -> AnnotationUtils.getAnnotation(entry.getValue().getMethod(), Operation.class) != null) //
                .map(entry -> entry.getKey().getPatternsCondition().getPatterns().iterator().next()) //
                .toArray(String[]::new);
    }

    @SuppressWarnings("rawtypes")
    private void sortSchemasByAlphabet(OpenAPI openApi) {
        Map<String, Schema> schemas = openApi.getComponents().getSchemas();
        if (schemas != null) {
            openApi.getComponents().setSchemas( //
                    schemas.entrySet().stream() //
                            .sorted(Comparator.comparing( //
                                    entry -> entry.getValue() == null || StringUtils.isBlank(entry.getValue().getTitle()) //
                                            ? entry.getKey()
                                            : entry.getValue().getTitle()))
                            .collect(LinkedHashMap::new, //
                                    (map, entry) -> map.put(entry.getKey(), entry.getValue()), //
                                    Map::putAll));
        }
    }

}
```
controller 使用註解 `@Tag` 和 `@Operation`
```java
@Tag(
    name = "此類的title", 
    description = "此類的詳細說明..."
)
@RestController
@RequestMapping("/index")
@RequiredArgsConstructor(onConstructor = @__(@Autowired))
public class XxxController{

    @Operation(
        summary = "查詢指定群組", 
        description = "詳細說明...")
    @GetMapping("/group/search/")
    public @ResponseBody List<xxx> searchLight() {
        return xxxService.handle();
    }
}
```

<br/>

<br/>


## 參考資料
> https://www.ruyut.com/2022/05/spring-boot-openapi-3-swagger-ui.html