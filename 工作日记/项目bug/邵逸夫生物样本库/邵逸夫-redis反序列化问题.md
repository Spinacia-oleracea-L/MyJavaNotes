### 报错详情

```bash
24-07-24 08:23:30,452 [XNIO-1 task-89] ERROR [c.g.b.c.s.component.GlobalExceptionHandlerResolver] GlobalExceptionHandlerResolver.java:63 - 全局异常信息 ex=Cannot deserialize; nested exception is org.springframework.core.serializer.support.SerializationFailedException: Failed to deserialize payload. Is the byte array a result of corresponding serialization for DefaultDeserializer?; nested exception is java.io.StreamCorruptedException: invalid stream header: 22353022
org.springframework.data.redis.serializer.SerializationException: Cannot deserialize; nested exception is org.springframework.core.serializer.support.SerializationFailedException: Failed to deserialize payload. Is the byte array a result of corresponding serialization for DefaultDeserializer?; nested exception is java.io.StreamCorruptedException: invalid stream header: 22353022
	at org.springframework.data.redis.serializer.JdkSerializationRedisSerializer.deserialize(JdkSerializationRedisSerializer.java:84)
	at org.springframework.data.redis.core.AbstractOperations.deserializeHashValue(AbstractOperations.java:355)
	at org.springframework.data.redis.core.AbstractOperations.deserializeHashMap(AbstractOperations.java:299)
	at org.springframework.data.redis.core.DefaultHashOperations.entries(DefaultHashOperations.java:247)
	at com.gy.biobank.cache.RedisService.getMapValue(RedisService.java:55)
	at com.gy.biobank.storage.service.impl.SamplePositionRecordServiceImpl.exportSampleExcel(SamplePositionRecordServiceImpl.java:6444)
	at com.gy.biobank.storage.service.impl.SamplePositionRecordServiceImpl$$FastClassBySpringCGLIB$$bf04b068.invoke(<generated>)
	at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218)
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:685)
	at com.gy.biobank.storage.service.impl.SamplePositionRecordServiceImpl$$EnhancerBySpringCGLIB$$296f9649.exportSampleExcel(<generated>)
	at com.gy.biobank.storage.controller.SamplePositionRecordController.exportSampleExcel(SamplePositionRecordController.java:1179)
```



报错发生在从Redis获取哈希数据并反序列化的过程中。这通常是因为Redis中存储的数据格式不正确或与反序列化机制不匹配。

### 具体问题分析
`redisService.getMapValue(CacheConstants.NATION_LABEL_KEY)`从Redis中获取哈希数据并将其反序列化为`Map<String, Object>`。但如果存储的数据格式不正确或被污染，就会导致反序列化失败。

### 解决方案

1. **检查Redis中的数据格式**:
   确保Redis中存储的数据是正确的`Map<String, Object>`格式。可以使用Redis命令行工具或客户端工具查看数据。

2. **一致的序列化和反序列化机制**:
   确保在写入和读取Redis时使用相同的序列化机制。

3. **处理异常情况**:
   在代码中增加异常处理，以便在数据格式不正确时进行相应处理。

### 改进代码
下面是改进后的代码，包括异常处理和日志记录：

```java
import org.springframework.data.redis.core.HashOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;
import java.util.HashMap;
import java.util.Map;

@Service
public class RedisService {

    private final RedisTemplate<String, Object> redisTemplate;

    public RedisService(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    /**
     * 获取数据Map
     *
     * @param mapName
     * @return
     */
    public Map<String, Object> getMapValue(String mapName) {
        HashOperations<String, String, Object> hps = this.redisTemplate.opsForHash();
        try {
            return hps.entries(mapName);
        } catch (Exception e) {
            // 记录错误日志
            System.err.println("Error deserializing data from Redis: " + e.getMessage());
            e.printStackTrace();
            // 返回空Map或者其他处理
            return new HashMap<>();
        }
    }
}
```

### 使用Jackson作为序列化工具
可以考虑使用`GenericJackson2JsonRedisSerializer`替代默认的`JdkSerializationRedisSerializer`，这样可以减少由于序列化问题导致的错误。

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        return template;
    }
}
```

### 检查Redis中的数据
确保Redis中存储的数据格式正确，可以通过Redis CLI或其他工具检查存储的数据：

```sh
# 连接到Redis
redis-cli

# 获取并查看数据
HGETALL CacheConstants.NATION_LABEL_KEY
```

通过这些步骤，可以更好地确保数据格式的正确性和一致性，避免反序列化时的错误。