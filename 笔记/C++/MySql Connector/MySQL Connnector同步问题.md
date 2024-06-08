# 问题
使用Mysql Connector调用存储过程再执行sql语句后报错，
```
Commands out of sync; you can't run this command now
```

后续的sql语句只要执行都报这个错

# 原因
疑似是调用的statment中还有ResultSet没有获取到或者是有资源没有释放

# 解决方法
使用智能指针，执行一段释放一段资源，看这段实例代码
```C++
	int ret = 0;
	
	try
	{
		{
			std::string sql_for_check = "call " PROCEDURE_CHECK_SOMEONE_IS_RENT "( ? , @result)";
			std::unique_ptr<sql::PreparedStatement> prep_query(p_conn_->prepareStatement(sql_for_check));
			prep_query->setString(1, user_id);
			prep_query->execute();
		}

		std::string sql_for_result = "select @result as reuslt";
		std::unique_ptr<sql::PreparedStatement> prep_select(p_conn_->prepareStatement(sql_for_result));
		std::unique_ptr<sql::ResultSet> res(prep_select->executeQuery());
		size_t rows = res->rowsCount();
		while (res->next()) {
			ret = res->getInt(1);
		}
	}
	catch (const sql::SQLException& e)
	{
		BDEBUG(e.what());
		ret = -1;
	}

	return ret;
```

利用智能指针的RAII，shou'x