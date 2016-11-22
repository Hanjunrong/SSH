*struts2第一天*

# 目录

- struts2执行过程


----
> 访问->过滤器->得到请求地址->找到struts.xml并解析->拿着xxxAction去找action标签->比对name属性
->值一样,得到对应的class属性值->反射得到字节码文件
->让execute方法执行->拿着方法的返回值,到action里面找result标签
->如果返回值匹配->跳转到相应的页面
*****
> `extends = "struts-default"`
## action获取表单数据
- actionContext
- ServletActionContext