##### 缓存预热

```java
 @PostConstruct
    public void init(){
        List<HashMap> list = telDao.getList();
        for (HashMap map:list){
            //操作string
            redisTemplate.opsForValue().set(map.get("id").toString(),map.get("tel").toString());
            System.out.println(map.get("id").toString()+"----"+redisTemplate.opsForValue().get(map.get("id").toString()));
        }
    }
```





##### 进程缓存