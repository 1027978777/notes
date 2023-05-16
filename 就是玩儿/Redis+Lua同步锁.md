# Redis+Lua同步锁

1.  Jedis配置

```java
@Configuration
@Getter
@Setter
@Slf4j
@ConfigurationProperties(prefix = "jedis")
public class JedisConfig {

    @Value("${spring.redis.host}")
    private String host;
    @Value("${spring.redis.port}")
    private int port;
    @Value("${spring.redis.password}")
    private String password;
    private int timeout;
    private int maxTotal;
    private int maxIdle;
    private int minIdle;

    @Bean
    public JedisPool jedisPool() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxIdle(maxIdle);
        jedisPoolConfig.setMinIdle(minIdle);
        jedisPoolConfig.setMaxTotal(maxTotal);
        JedisPool jedisPool = new JedisPool(jedisPoolConfig, host, port, timeout, password);
        log.info("JedisPool连接成功:" + host + "\t" + port);
        return jedisPool;
    }
}
```

1.  Jedis工具类→获取jedis

```java
@Component
public class JedisUtil {
    @Resource
    private JedisPool jedisPool;

    /**
     * 获取Jedis资源
     */
    public Jedis getJedis() {
        return jedisPool.getResource();
    }

    /**
     * 释放Jedis连接
     */
    public void close(Jedis jedis) {
        if (jedis != null) {
            jedis.close();
        }
    }
}
```

1.  redis 锁工具类

```java
public class RedisLockUtil {
    private static final Long RELEASE_SUCCESS = 1L;
    private static final String PREFIX = "API_LOCK_";

    /**
     * 释放分布式锁
     *
     * @param jedis
     * @param lockKey
     * @param valve
     * @return boolean
     * @author ll
     * @date 2023/02/09 14:31
     */
    public static boolean unLock(Jedis jedis, String lockKey, String valve) {

        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        Object result = jedis.eval(script, Collections.singletonList(PREFIX + lockKey), Collections.singletonList(PREFIX + valve));

        if (RELEASE_SUCCESS.equals(result)) {
            return true;
        }
        return false;

    }

    /**
     * 加分布式锁
     *
     * @param jedis
     * @param lockKey
     * @param valve
     * @param timeout
     * @return boolean
     * @author ll
     * @date 2023/02/09 14:31
     */
    public static boolean lock(Jedis jedis, String lockKey, String valve, int timeout) {

        String script = "if redis.call('setnx',KEYS[1],ARGV[1]) == 1 then" +
                " redis.call('expire',KEYS[1],ARGV[2]) return 1 else return 0 end";
        Object result = jedis.eval(script, Collections.singletonList(PREFIX + lockKey), Lists.newArrayList(PREFIX + valve, String.valueOf(timeout)));
        //判断是否成功
        if (RELEASE_SUCCESS.equals(result)) {
            return true;
        }
        return false;

    }
}
```

1.  加锁示例(jedis+lua)

```java
@Slf4j
@Component
public class InterfaceEventListener {

    @Resource
    private JedisUtil jedisUtil;
    @Value("${jedis.lock.cycle-number}")
    private int cycleNumber;
    @Value("${jedis.lock.expire-time}")
    private int expireTime;
    @Value("${jedis.lock.sleep-time}")
    private int sleepTime;
    @Value("${spring.redis.database}")
    private int database;


    public void onApplicationEvent(InterfaceEvent event) {
        Jedis jedis = jedisUtil.getJedis();
        jedis.select(database);
        boolean unLock = false;
        boolean lock;
        int currentNumber = 0;
        try {
            do {
                lock = RedisLockUtil.lock(jedis, "lockKey", "valve", expireTime);
                if (lock) {
                    try {
                       //todo 加锁的代码
                        
                    } catch (Exception e) {
                        log.error(e.getMessage());
                        e.printStackTrace();
                    } finally {
                        unLock = RedisLockUtil.unLock(jedis, detectCode, detectCode);
                    }
                }
                currentNumber++;
                Thread.sleep(sleepTime);
            }
            while (!unLock && currentNumber < cycleNumber);
        } catch (Exception e) {
            e.printStackTrace();
            log.error(e.getMessage());
        } finally {
            jedisUtil.close(jedis);
        }
    }
   
}
```
