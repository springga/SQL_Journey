# SQL新启程

新入职，在不看文档的情况下， 如何快速了解现有数据库的结构，立即开始我们的探索之旅

1. 列出所有表及其数据行数
  
        create table #temp(Recordcount int ,tableName varchar(100))
        
        declare @tableschema varchar(30)
        declare @tablename varchar(100)
        declare @sql varchar(300)
        declare tablecursor cursor for
        	select table_schema, table_name 
        from [information_schema].[tables]
        where table_type='BASE TABLE'
        
        open tablecursor
        fetch next from tablecursor into @tableschema, @tablename
        while @@fetch_status=0
        
        begin
        set @sql='insert into #temp(recordcount,tablename) select count(*),'+''''+@tablename+''''+' from '+@tableschema+'.'+@tablename
        exec(@sql)
        fetch next from tablecursor into @tableschema, @tablename
        end
        
        close tablecursor
        deallocate tablecursor
        select * from #temp order by Recordcount desc
        drop table #temp

2. 列出所有表的结构

        select  c.table_schema ,
                c.table_name ,
                c.column_name ,
                case when ( ( charindex('char', c.data_type) > 0
                              or charindex('binary', c.data_type) > 0
                            )
                            and c.character_maximum_length <> -1
                          )
                     then c.data_type + '('
                          + cast(c.character_maximum_length as varchar(4)) + ')'
                     when ( ( charindex('char', c.data_type) > 0
                              or charindex('binary', c.data_type) > 0
                            )
                            and c.character_maximum_length = -1
                          ) then c.data_type + '(max)'
                     when ( charindex('numeric', c.data_type) > 0 )
                     then c.data_type + '(' + cast(c.numeric_precision as varchar(4))
                          + ',' + cast(c.numeric_scale as varchar(4)) + ')'
                     else c.data_type
                end as data_type ,
                c.column_default ,
                c.is_nullable
        from    [information_schema].[columns] c

3. 表、视图、存储过程关系图
