## config file
1. 在 `WebMvcConfig` 做配置

    ```JAVA
    @Bean
    public TilesConfigurer tilesConfigurer() {
        TilesConfigurer configurer = new TilesConfigurer();
        configurer.setDefinitions("/WEB-INF/tiles/tiles-definition.xml", "/WEB-INF/tiles/il/tiles-definition.xml");
        configurer.setCheckRefresh(true);// 是否在runetime時刷新
        return configurer;
    }
    ```

2. 在上述路徑建立檔案 `tiles-definition.xml`，進行mapping配對

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE tiles-definitions PUBLIC "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN" "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">
    <tiles-definitions>
        
        <definition name="mainLayout" templateExpression="${mainLayout}">
            <put-attribute name="header" expression="${header_old}" />
            <put-attribute name="menu" value="/WEB-INF/jsp/common/layout/menu.jsp" />
            <put-attribute name="content" value="/WEB-INF/jsp/common/default-content.jsp" />
            <put-attribute name="footer" expression="${footer_old}" />
        </definition>
        
    </tiles-definitions>
    ```