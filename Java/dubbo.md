### 手动获取DubboService

```
public static  <T> T getService(Class<T> interfaceClass,String group,String version) {

        Environment environment = SpringUtil.getApplicationContext().getBean(Environment.class);
        RegistryConfig registry = new RegistryConfig();
        registry.setAddress(environment.getProperty("dubbo.registry.address"));

        ReferenceConfig<T> reference = new ReferenceConfig<>();
        reference.setRegistry(registry);
        reference.setInterface(interfaceClass);
        reference.setRetries(0);

        if (StrUtil.isNotEmpty(group)) {
            reference.setGroup(group);
        }

        if (StrUtil.isNotEmpty(version)) {
            reference.setVersion(version);
        }

        SimpleReferenceCache cache = SimpleReferenceCache.getCache();

        return cache.get(reference);
    }
```
