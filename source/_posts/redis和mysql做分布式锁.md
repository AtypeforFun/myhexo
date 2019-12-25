---
title: redis和mysql做分布式锁
date: 2019-06-06 21:43:59
categories:
- 技术
- 工具
tags:
- 工具
---

### 一、Mysql做分布式锁

使用乐观锁做分布式锁；
一条sql记录就是一个锁
<!--more-->
```
 //获取当前业务的锁（记录）
  Lock myLock = LockService.getById(LockType.lOCK_MY.getValue());
            Boolean validateVersion = validateVersion(myLock);
            if(!validateVersion){
                log.info("");
                return;
            }
            
            /**
            * 业务逻辑
            */
            
            //业务执行成功则释放锁
            myLock.setLocked(LockedStatus.NO);
            myLock.setRevision(null);
            myLock.updateSelective(myLock);

    private Boolean validateVersion(Lock myLock ) {
        Date now =new Date();
        //解决因为业务而产生的死锁
        //锁定超过12小时 认为没有锁定
        if(myLock.getLocked() == LockedStatus.YES && (now.getTime()-myLock.getUpdateTime().getTime())<(1000*60*60*12)){
            log.error("");
            return false;
        }
        int updateCount = LockService.updateLockVersionBy(myLock.getRevision(), myLock.getId());
        if(updateCount!=1){
            log.info("");
            return false;
        }
        return true;
    }
    
    
        //  更据当前获取的没锁的版本和指定锁记录的id区更新记录的版本
        //  如果更新失败则说明有别的线程进行了更新即锁被别的线程获取
        //  如果更新成功则说明没有别的线程操作这条记录，则获取了锁
        //  锁是否被获取通过一个字段进行标记
        //  版本字段用于控制锁的竞争，和锁字段共同维护这个分布式锁
        //  单纯使用版本，每次获取记录都无法分辨上次更新版本的记录是否归还了锁，锁是否还在占用
        //  单纯使用锁字段，无法控制有多个线程读到记录是锁没被占用的情况从而都去更新锁为占用
        //  依赖的基础是数据库的更新操作是排他的。
        <update id="updateLockVersionBy"  >
        update lock
        <set >
                revision = revision+1,
                locked =2
        </set>
        where id = #{id} and revision=#{version}
    </update>



```

1. 先更具id区查询锁记录，判断锁的状态，也就是记录中的锁字段
2. 如果锁没被占用，则更具当前的version和id去竞争锁（更新当前锁字段为已占用）
3. 由于可能存在多个线程读取到锁是为占用的状态，同时去竞争锁，而数据库的update是排他的，我们通过数据库的乐观锁（version），保证竞争锁的时候只用一个线程能够更新成功
4. 获取锁之后进行业务操作，完成后释放锁
5. 通过锁标记字段，version字段，利用数据的乐观锁实现分布式锁


### 二、redis实现分布式锁

redis是单线程的，利用这一特性实现数据的分布式锁

```
// lock
	        //获取锁
	        //给指定的key的值增加1如果得到的不是1则说明不是第一次增加
	        //则锁被别的线程拿到
			try {
				seq = jedis.incr(key);
			} catch (Exception ex) {
				logger.error(ex.getMessage(), ex);
			}
			
			if (seq != 1) {
			/**
			*业务逻辑
			*/
			} else {
				try {
				   //设置锁的过期时间避免死锁
					jedis.expire(key, 60);	// key有效期，单位秒
				} catch (Exception ex) {
					logger.error(ex.getMessage(), ex);
				}
			}
		
		
		try {
		   /**
			*业务逻辑
			*/
		} finally {
			// unlock
			if (lockFlag && key != null) {
				try {
			    	//释放锁，删除key
					jedis.del(key);
				} catch (Exception ex) {
					logger.error(ex.getMessage(), ex);
				}
			}
		}

```
1.  给指定的key值自动+1，得到的值如果不是1则说明不是第一次增加则说明被别的线程获取到了锁
2.  业务逻辑结束后删除key，即，释放锁
3.  给key一个过期时间，避免其他原因死锁

###  NOTE

- 确保线程能够分辨出锁的状态，即 有没有被别的线程获取
- 在竞争锁时，确保只有一个线程能够获取锁（依赖于mysql，redis的特性）
- 释放锁，业务操作完或者发生异常要释放锁，由于是分布式的锁，并不是代码语义上的锁，要避免其他原因，例如网络抖动，或者redis，mysql挂掉等原因锁释放失败等等情况的死锁--设置一个锁自动释放的机制