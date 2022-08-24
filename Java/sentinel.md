## 手动从Nacos中加载规则

```java
ReadableDataSource<String,List<FlowRule>> flowRule = new NacosDataSource<List<FlowRule>>(serverAddr,groupId,flowDataId,s -> JSON.parseObject(s,new TypeReference<List<FlowRule>>() {}));

FlowRuleManager.register2Property(flowRule.getProperty());
```

## 自动从Nacos中加载规则

```java
@Configuration
public class SentinelConfig {

    @Bean
    public void loadRules() throws NacosException {

        String serverAddr = "localhost:8848";
        final String groupId = "SENTINEL_GROUP";

        Properties properties = new Properties();
        properties.setProperty(PropertyKeyConst.SERVER_ADDR, serverAddr);
        ConfigService configService = new NacosConfigService(properties);

        // 流控规则
        String flowDataId = env.getProperty("dubbo.application.name") + "-flow-rules";
        configService.addListener(flowDataId, groupId, new Listener() {
            @Override
            public void receiveConfigInfo(String configInfo) {
                FlowRuleManager.loadRules(JSON.parseObject(configInfo,new TypeReference<List<FlowRule>>() {}));
            }
            @Override
            public Executor getExecutor() {
                return null;
            }
        });

        // 授权规则
        String authorityDataId = env.getProperty("dubbo.application.name") + "-authority-rules";
        configService.addListener(authorityDataId, groupId, new Listener() {
            @Override
            public void receiveConfigInfo(String configInfo) {
                AuthorityRuleManager.loadRules(JSON.parseObject(configInfo,new TypeReference<List<AuthorityRule>>() {}));
            }
            @Override
            public Executor getExecutor() {
                return null;
            }
        });

        // 熔断规则
        String degradeDataId = env.getProperty("dubbo.application.name") + "-degrade-rules";
        configService.addListener(degradeDataId, groupId, new Listener() {
            @Override
            public void receiveConfigInfo(String configInfo) {
                DegradeRuleManager.loadRules(JSON.parseObject(configInfo,new TypeReference<List<DegradeRule>>() {}));
            }
            @Override
            public Executor getExecutor() {
                return null;
            }
        });

        // 热点规则
        String paramDataId = env.getProperty("dubbo.application.name") + "-param-rules";
        configService.addListener(paramDataId, groupId, new Listener() {
            @Override
            public void receiveConfigInfo(String configInfo) {
                ParamFlowRuleManager.loadRules(JSON.parseObject(configInfo,new TypeReference<List<ParamFlowRule>>() {}));
            }
            @Override
            public Executor getExecutor() {
                return null;
            }
        });

        // 系统规则
        String systemDataId = env.getProperty("dubbo.application.name") + "-system-rules";
        configService.addListener(systemDataId, groupId, new Listener() {
            @Override
            public void receiveConfigInfo(String configInfo) {
                SystemRuleManager.loadRules(JSON.parseObject(configInfo,new TypeReference<List<SystemRule>>() {}));
            }
            @Override
            public Executor getExecutor() {
                return null;
            }
        });
    }
}
```

