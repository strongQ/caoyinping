---
layout:  post
title: Dapper-一个轻量级的orm
description:  Dapper只有一个代码文件，完全开源，你可以放在项目里的任何位置，来实现数据到对象的ORM操作，体积小速度快
keywords:  ORM
categories: [数据库]
tags: [sql]
---

Dapper 用法示例

-------------------

## 1.插入记录

            const string sql = @"
            INSERT INTO [dbo].[Contacts] (
            	    [UserName]
            	    ,[Tel]
            	    ,[Tel1]
            	    ,[Address]
            )
            VALUES (
            	    @UserName
            	    ,@Tel
            	    ,@Tel1
            	    ,@Address
            );select @@IDENTITY";
             using (SqlConnection  connection=new SqlConnection(connstr))
            {
               /* model可为list集合 */
                return connection.Execute(sql, model);
            }  

## 2.删除记录
            const string sql = "DELETE FROM [dbo].[Contacts] WHERE [Id] = @Id";
            using (SqlConnection  connection=new SqlConnection(connstr))
            {
            	/* 参数id */
                 return connection.Execute(sql, new{ID=id});
                 /* 参数model */
                  return connection.Execute(sql, model);
            }  

## 3.更新记录
            const string sql = @"
            UPDATE [dbo].[Contacts]
            SET 
            	    [UserName] = @UserName
            	    ,[Tel] = @Tel
            	    ,[Tel1] = @Tel1
            	    ,[Address] = @Address
                        WHERE [Id] = @Id";
            #endregion
            using (SqlConnection  connection=new SqlConnection(connstr))
            {
               /* model可为list集合 */
                return connection.Execute(sql,model);
            }  

## 4.查询记录
        /* 获取集合 */
        public IEnumerable<Model.Contacts> GetAllList()
        {
            const string sql = "SELECT [Id], [UserName], [Tel], [Tel1], [Address] FROM [dbo].[Contacts] ";
            using (SqlConnection  connection=new SqlConnection(connstr))
            {
                return connection.Query<Model.Contacts>(sql);
            }
        }

        /* 查询单个模型实体 */
        public Model.Contacts QuerySingle(int id)
        {
            const string sql = "SELECT TOP 1 [Id], [UserName], [Tel], [Tel1], [Address] FROM [dbo].[Contacts] WHERE [Id] = @Id";
            using (SqlConnection  connection=new SqlConnection(connstr))
            {
                return connection.Query<Model.Contacts>(sql,new{Id=id}).SingleOrDefault();
            }
        }

        /* 查询记录条数 */
        public int Count()
        {
            const string sql = "SELECT count(*) as id FROM [dbo].[Contacts]";
            using (SqlConnection connection = new SqlConnection(connstr))
            {
                int list = Convert.ToInt32(connection.ExecuteScalar(sql));
                return list;
            }

        }


## 5.分页
        public IEnumerable<Model.Contacts> Page(int pageIndex, int pageSize)
        {
            const string sql = @"select * from(select *,(ROW_NUMBER() over(order by id asc))as newId from Contacts) as t 
                                 where t.newId between (@pageIndex-1)*@pageSize+1 and  @pageSize*@pageIndex";
            using (SqlConnection connection = new SqlConnection(connstr))
            {
                var reader = connection.Query<Model.Contacts>(sql, new { pageIndex = pageIndex, pageSize = pageSize });
                return reader;
            }

        }


