# Mybatis数据库物理分页插件
支持Mysql、MSSQL、Oracle、MSSQL2005、Postgre SQL、DB2数据库。

## 使用方法
在Mybatis的配置文件中，增加这个插件
	
	 <plugins>
        <plugin interceptor="org.noo.dialect.interceptor.PaginationInterceptor">
              <property name="dialectClass" value="org.noo.pagination.dialect.db.MySQLDialect"/>
              <property name="sqlPattern" value=".*findAll.*"/>
              <property name="noPagingPattern" value=".*NoPage.*" />
        </plugin>
    </plugins>

* 第一个属性 `dialectClass`，方言实现类，需要实现`org.noo.pagination.dialect.Dialect` 接口
* 第二个属性`sqlPattern`表示插件需要拦截的SQL ID，为正则表达式。
	* 例如`.*findAll.*` 表示拦截 包含 findAll 的查询sql
* 第三个属性`noPagingPattern`表示插件需要拦截的非分页SQL ID，为正则表达式。
  * 例如`.*NoPage.*` 表示拦截 包含 NoPage 的查询sql


### Sql Mapper配置
示例
	
	<select id="findAllDict" parameterType="org.noo.dialect.model.Pagetag"
            resultType="org.noo.module.Dict">
        select
          ID,CREATORTIME,DATASOURCE,DATATYPE,DICTNAME,DICTNUMBER,ENABLE,RENEWTIME,SORT
         from CD_DICT ORDER BY SORT
    </select>
 需要注意的是：`parameterType`是插件内部的一个分页实体，也可以为其他的分页实体，需要实现`org.noo.dialect.page.RecordPage` 接口。
 另外，如果 `parameterType` 非 `RecordPage`的实现，而是其他的实体，那么具体用法为：
 		
 		@Paging(field="page1")
 		public class BusinessObject{
 			private RecordPage page1;
 			
 			//... get set ...
 		}
 上面的`BusinessObject`中有个`page1`的属性，它为RecordPage的实现，通过注解`Paging`来指定它为分页参数信息(field指定)。
### 大数据查询
大数据查询时，自身所带的统计总数SQL可能会有较大的性能问题，所以在大数据查询时可以将你的查询修改为noPagingPattern匹配的规则。
然后需要在调用查询方法之前指定自己查询出来的总数

		Pagination pager = PageContext.getPageContext();
		pager.setTotalRows(count);//count为自写代码查询出来的总数
		List<Map<String,Object>> list =adRecordDao.showNoPage(adRecord);

### 增加一个拦截器 QueryPaginationInterceptor
这个拦截器只对查询进行拦截

### 增加分页查询上下文
增加上下文机制，较少基础配置和代码侵入

# Spring例子
TODO