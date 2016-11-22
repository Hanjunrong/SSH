*hibernate第四天*
# 目录
- hibernate五种查询方式
    - 对象导航查询
    - OID查询
    - HQL查询
        - 查询所有
        - 条件查询
        - 排序查询
        - 分页查询
        - 投影查询
        - 聚集函数使用
    - OBC查询
    - HQL多表查询
    - hibernate检索策略
        - 立即检索
        - 延迟检索
            - 类级别延迟
            - 关联级别 

-----
> 对象图导航检索
>> 根据已经加载的对象,导航到他的关联对象,利用类与类之间的关系来检索对象

> 条件查询
>> `String hql = from Customer c where c.custName=? and c.suctPhone=?`
`Query query = session.createQuery(hql);`
`query.setParamater(0,"");//从0开始` 

> 分页查询
>> (当前页-1)*每页查询记录数

> 投影查询
>> 查询部分数据(不支持 * 写法)
------
> QBC查询
------
> 内连接: from Customer c inner join c.setLinkMan(*写属性*)
> 迫切内连接 ..join fetch ..
> 没有迫切右外连接
> 内连接和迫切内连接区别
>> 内连接返回集合每部分是数组,迫切内连接返回集合中是对象
------
> hibernate检索策略
> 立即检索
> 延迟检索
