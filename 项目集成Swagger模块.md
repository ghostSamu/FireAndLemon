# Mall-learning项目中集成Swagger模块

## 步骤
 1. 在pom.xml中新增Swagger-UI相关依赖
 2. 添加Swagger-UI的Java配置文件
 3. 手动添加Swagger库静态资源文件的映射
 4. 为Controller类添加Swagger注解


### 前提条件
需要使用MyBatis-Generato组件r自动生成Mapper，Mapper中使用 @ApiModelProperty注解


###  添加项目依赖
```<!--Swagger-UI API文档生产工具-->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger2</artifactId>
  <version>2.7.0</version>
</dependency>
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger-ui</artifactId>
  <version>2.7.0</version>
</dependency>
```
### 添加Swagger-UI的配置
指明为哪些类添加注解
``` java  
	@Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
                .paths(PathSelectors.any())
                .build();
    }
```
配置index.html中显示的项目信息
```java   
	private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("SwaggerUI演示")
                .description("mall-learning")
                .contact(new Contact("FireAndLemon","",""))
                .version("1.0")
                .build();
    }
```
### 手动添加Swagger静态文件的映射
不指定的话，访问http://localhost:8080/swagger-ui/index.html 的时候会返回404错误
``` java  
	@Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        String baseUrl = StringUtils.trimTrailingCharacter(this.baseUrl, '/');
        registry.
                addResourceHandler(baseUrl + "/swagger-ui/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/springfox-swagger-ui/")
                .resourceChain(false);
    }
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController(baseUrl + "/swagger-ui/")
                .setViewName("forward:" + baseUrl + "/swagger-ui/index.html");
    }
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry
                .addMapping("/api/pet")
                .allowedOrigins("http://editor.swagger.io");
        registry
                .addMapping("/v2/api-docs.*")
                .allowedOrigins("http://editor.swagger.io");
    }
```

### Controller类添加注解

```java
@Api(tags = "PmsBrandController", description = "商品品牌管理")
@Controller
@RequestMapping("/brand")
public class PmsBrandController {
    @Autowired
    private PmsBrandService brandService;
    private static final Logger LOGGER = LoggerFactory.getLogger(PmsBrandController.class);
    @ApiOperation("获取所有品牌列表")
    @RequestMapping(value = "listAll", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<List<PmsBrand>> getBrandList() {
        return CommonResult.success(brandService.listAllBrand());
    }
    }
```

### 参考
 - http://www.macrozheng.com/#/architect/mall_arch_02?id=%e8%ae%bf%e9%97%aeswagger-ui%e6%8e%a5%e5%8f%a3%e6%96%87%e6%a1%a3%e5%9c%b0%e5%9d%80
 - https://blog.csdn.net/ljm_csdn/article/details/87615670
 - https://github.com/springfox/springfox-demos/blob/master/boot-swagger/src/main/java/springfoxdemo/boot/swagger/SwaggerUiWebMvcConfigurer.java


### 总结
 - 关于MyBatis的API变动，如何才能找到正确的使用方法，尽量参照Github上的官方例子
 - 对Java常用类不熟悉，对泛型的使用需要加强学习


