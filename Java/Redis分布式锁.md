### RedisUtil

```
	/**
	 * Redis锁前缀
	 */
	private static final String LOCK_PREFIX = "_Redis_Lock_";

	/**
	 * 锁失效时间
	 */
	public static final int LOCK_EXPIRE = 500; // ms

  	/**
	 * 加锁，防止击穿
	 * @param key 加锁的Key
	 * @return 是否抢到了锁
	 */
	public boolean lock(String key){
		String lock = LOCK_PREFIX + key;
		return (Boolean) redisTemplate.execute((RedisCallback) connection -> {

			long expireAt = System.currentTimeMillis() + LOCK_EXPIRE + 1;
			Boolean acquire = connection.setNX(lock.getBytes(), String.valueOf(expireAt).getBytes());
			if (acquire) {
				return true;
			} else {
				byte[] value = connection.get(lock.getBytes());
				if (ObjectUtil.isNotNull(value) && value.length > 0) {
					long expireTime = Long.parseLong(new String(value));
					// 如果锁已经过期
					if (expireTime < System.currentTimeMillis()) {
						// 重新加锁，防止死锁
						byte[] oldValue = connection.getSet(lock.getBytes(), String.valueOf(System.currentTimeMillis() + LOCK_EXPIRE + 1).getBytes());
						// 当key不存时会返回空，表示key不存在或者已在管道中使用
						return oldValue == null ? false : Long.parseLong(new String(oldValue)) < System.currentTimeMillis();
					}
				}
			}
			return false;
		});
	}

	/**
	 * 删除锁
	 *
	 * @param key
	 */
	public void unLock(String key) {
		redisTemplate.delete(key);
	}
```


### 使用处


```
	@Override
    	public <T> T getObject(ColaCacheName cacheName, String key) {
		if (StrUtil.hasEmpty(cacheName.cacheName(),key)) {
		    return null;
		}
		Cache.ValueWrapper valueWrapper = getCache(cacheName).get(key);
		return valueWrapper == null ? null : (T) valueWrapper.get();
    	}

	public <T> T getObjectFromLoader(ColaCacheName cacheName, String key, Callable<T> valueLoader) {
		if (ObjectUtil.isNull(valueLoader)) {
		    return null;
		}
		if (StrUtil.hasEmpty(cacheName.cacheName(),key)) {
		    return null;
		}

		Object value = getObject(cacheName,key);

		if (ObjectUtil.isNull(value)) {
		    RedisUtil redisUtil = SpringUtil.getBean(RedisUtil.class);
		    boolean lock = redisUtil.lock(cacheName.cacheName() + key);
		    if (lock) {
			try {
			    return getCache(cacheName).get(key,valueLoader);
			} finally {
			    redisUtil.unLock(cacheName.cacheName() + key);
			}
		    } else {
			try {
			    Thread.sleep(500);
			    // 自旋
			    return getObjectFromLoader(cacheName, key, valueLoader);
			} catch (Exception ignore) {
			} 
		    }
		}
		return (T)value;
    	}
```
