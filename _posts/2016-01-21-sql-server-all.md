---
layout: post
title: SqlServer操作
category: 数据库
tag: sql
---
# SqlServer操作
1.得到数据库中所有用户表
 <pre></prSelect>Select [name] from sysObjects Where xtype=’U’and [name]<>’dtproperties’ Order By [name]
 </pre>
2.得到数据库中所有用户视图
<pre>
Select [name] From sysObjects Where xtype=’V’ And [name]<>’syssegments’ And [name]<>’sysconstraints’ Order By [name]
</pre>
3.获得指定表中所有列
<pre>Select
c.name As ColumnName,
t.name As TypeName
From syscolumns c, systypes t, sysobjects o
Where c.xtype = t.xusertype
And c.id = o.id
And o.name = ‘Book’
Order By c.colorder</pre>
4.获得表中所有列的详细信息
<pre>Select ColOrder = col.colorder, --排序号
ColumnName = col.name, --列名
TypeName = type.name,--数据类型名称
Length = (Case When type.name='nvarchar' Or type.name='nchar' Then col.length/2 Else col.length End), --长度
[PRECISION] = COLUMNPROPERTY(col.id, col.name, 'PRECISION'), --精度
Scale = ISNULL(COLUMNPROPERTY(col.id, col.name, 'Scale'), 0), --小数
IsIdentity = Case When COLUMNPROPERTY(col.id, col.name, 'IsIdentity')=1 Then '√' Else '' End, --是否为自动编号列
IsPK = Case When Exists(Select 1 From sysobjects Where xtype = 'PK' And name In (
Select name From sysindexes Where indid In (Select indid From sysindexkeys Where id = col.id And colid = col.colid))) Then '√' Else '' End, --是否为主键
AllowNull = Case When col.isnullable=1 Then '√' Else '' End, --是否允许为空
DefalutValue = isnull(com.text, '') --默认值
From syscolumns col
Left Join systypes type On col.xtype = type.xusertype
Inner Join sysobjects obj On col.id = obj.id And (obj.xtype = 'U' Or obj.xtype = 'V') And obj.name &lt;&gt; 'dtproperties'
Left Join syscomments com On col.cdefault = com.id
Where obj.name = 'Territories'
</pre>
5.获取MS SQL库数据字典的经典SQL语句
<pre>SELECT sysobjects.name AS [table], sysproperties.[value] AS 表说明,
syscolumns.name AS field, properties.[value] AS 字段说明, systypes.name AS type,
syscolumns.length, ISNULL(COLUMNPROPERTY(syscolumns.id, syscolumns.name,
'Scale'), 0) AS 小数位数, syscolumns.isnullable AS isnull,
CASE WHEN syscomments.text IS NULL
THEN '' ELSE syscomments.text END AS [Default],
CASE WHEN COLUMNPROPERTY(syscolumns.id, syscolumns.name, 'IsIdentity')
= 1 THEN '√' ELSE '' END AS 标识, CASE WHEN EXISTS
(SELECT 1 FROM sysobjects
WHERE xtype = 'PK' AND name IN
(SELECT name FROM sysindexes
WHERE indid IN
(SELECT indid FROM sysindexkeys
WHERE id = syscolumns.id AND colid = syscolumns.colid)))
THEN '√' ELSE '' END AS 主键
FROM syscolumns INNER JOIN
sysobjects ON sysobjects.id = syscolumns.id INNER JOIN
systypes ON syscolumns.xtype = systypes.xtype LEFT OUTER JOIN
sysproperties properties ON syscolumns.id = properties.id AND
syscolumns.colid = properties.smallid LEFT OUTER JOIN
sysproperties ON sysobjects.id = sysproperties.id AND
sysproperties.smallid = 0 LEFT OUTER JOIN
syscomments ON syscolumns.cdefault = syscomments.id
WHERE (sysobjects.xtype = 'U')
</pre>
6.获取数据库中表的字段的名称及类型
<pre>select syscolumns.name,systypes.name from syscolumns,systypes where id=object_id( 'POSmanage..PayWays ') and systypes.xusertype=syscolumns.xusertype</pre>
7.用SQL查询分析器查询表的字段类型长度和表说明
<pre>SELECT sysobjects.name AS 表名, syscolumns.name AS 列名,
systypes.name AS 数据类型, syscolumns.length AS 数据长度, CONVERT(char,
sysproperties.[value]) AS 注释
FROM sysproperties RIGHT OUTER JOIN
sysobjects INNER JOIN
syscolumns ON sysobjects.id = syscolumns.id INNER JOIN
systypes ON syscolumns.xtype = systypes.xtype ON
sysproperties.id = syscolumns.id AND
sysproperties.smallid = syscolumns.colid
WHERE (sysobjects.xtype = 'u' OR
sysobjects.xtype = 'v') AND (systypes.name &lt;&gt; 'sysname')
--and CONVERT(char,sysproperties.[value]) &lt;&gt; 'null' --导出注释不为'null'的记录
--AND (sysobjects.name = 'bbs_bank_log') --逐个关联表名，可以用or连接条件
ORDER BY 表名</pre>
8.SQL单引号的转义
<pre>create proc TestPro
(@conditon varchar(50))
as
declare @sql varchar(1000)
set @sql='select * from test'
if @conditon!=''
set @sql=@sql+' where name='''+@conditon+''''--''转义成'
exec(@sql)
go</pre>
9.分页存储过程
<pre>create PROCEDURE GetPageData
(
@TableName varchar(30),--表名称
@IDName varchar(20),--表主键名称
@PageIndex int,--当前页数
@PageSize int--每页大小
)
AS
IF @PageIndex > 0
BEGIN
set nocount on
   DECLARE @PageLowerBound int,@StartID int,@sql nvarchar(225)
   SET @PageLowerBound = @PageSize * (@PageIndex-1)
   IF @PageLowerBound<1
    SET @PageLowerBound=1
   SET ROWCOUNT @PageLowerBound
   SET @sql=N'SELECT @StartID = ['+@IDName+'] FROM '+@TableName+' ORDER BY '+@IDName
     exec sp_executesql @sql,N'@StartID int output',@StartID output
   SET ROWCOUNT 0
   SET @sql='select top '+str(@PageSize) +' * from '+@TableName+' where ['+@IDName+']>='+ str(@StartID) +' ORDER BY ['+@IDName+'] '
   EXEC(@sql)
set nocount off
END
</pre>
