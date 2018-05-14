## cassandra

[Cassandra NoSQL数据模型设计指南](http://blog.csdn.net/dev_csdn/article/details/78594658)

## redis

[Spring Boot+Spring Data Redis访问redis cluster](https://my.oschina.net/u/3786091/blog/1632316)

[docker-redis-cluster](https://github.com/Grokzen/docker-redis-cluster)

### demo

*application.properties*

```
spring.redis.cluster.nodes=10.180.126.181:7000,10.180.126.181:7001,10.180.126.181:7002,10.180.126.181:7003,10.180.126.181:7004,10.180.126.181:7005
```
*ClusterConfigurationProperties*

```
@Component
@ConfigurationProperties(prefix = "spring.redis.cluster")
public class ClusterConfigurationProperties
{
    // spring.redis.cluster.nodes=127.0.0.1:7000,127.0.0.1:7001,127.0.0.1:7002,127.0.0.1:7003,127.0.0.1:7004,127.0.0.1:7005
    List<String> nodes;

    public List<String> getNodes() {
        return nodes;
    }

    public void setNodes(List<String> nodes) {
        this.nodes = nodes;
    }

}
```

*AppConfig*
```
@Configuration
public class AppConfig
{
    @Autowired
    ClusterConfigurationProperties clusterProperties;

    @Bean
    public RedisClusterConfiguration getClusterConfig() {
        RedisClusterConfiguration rcc = new RedisClusterConfiguration(clusterProperties.getNodes());
        return rcc;
    }

    @Bean
    public JedisConnectionFactory getConnectionFactory(RedisClusterConfiguration cluster) {
        return new JedisConnectionFactory(cluster);
    }


    @Bean
    public RedisTemplate getRedisTemplate(JedisConnectionFactory factory) {
        RedisTemplate redisTemplate = new RedisTemplate();
        redisTemplate.setConnectionFactory(factory);
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        redisTemplate.setKeySerializer(redisSerializer);
        return redisTemplate;
    }

    @Bean
    public StringRedisTemplate getStringRedisTemplate(RedisConnectionFactory factory) {
        StringRedisTemplate stringTemplate = new StringRedisTemplate();
        stringTemplate.setConnectionFactory(factory);
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        stringTemplate.setKeySerializer(redisSerializer);
        stringTemplate.setHashKeySerializer(redisSerializer);
        stringTemplate.setValueSerializer(redisSerializer);
        return stringTemplate;
    }
}
```

