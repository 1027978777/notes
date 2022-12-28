> ### CompletableFuture提高接口性能

```java
//线程池
    public static final ExecutorService EXECUTOR =
        new ThreadPoolExecutor(10, 30, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<>(2000));

//根据排口查询因子信息（异步）
CompletableFuture<List<FactorInfo>> listCompletableFuture =
            CompletableFuture.supplyAsync(() -> mapper.loadPortFactor(spec.getPort().getId()), EXECUTOR);
//排口所配因子
List<FactorInfo> factorInfos = listCompletableFuture.join();
//异步 所以用ConcurrentHashMap
ConcurrentHashMap<String, List<Map>> testmap1 = new ConcurrentHashMap<>();
//异步查询所有因子的数据
CompletableFuture[] completableFutures = factorInfos.stream().map(factor -> CompletableFuture.runAsync(() -> {
            DataReportSpec dataReportSpec = new DataReportSpec();
            BeanUtils.copyProperties(spec, dataReportSpec);
            dataReportSpec.setFactor(factor);
            testmap1.put(factor.getCode(), mapper.loadGasBySpec(dataReportSpec));
        }, EXECUTOR)).toArray(CompletableFuture[]::new);
//等待所有执行完毕
CompletableFuture.allOf(completableFutures).join();
```

