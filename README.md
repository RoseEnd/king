### 关于全文索引sphinx的使用
```php
	$keyword = I('post.keywords');//表单提交过来的搜索关键字
	
	//1创建sphinx对象
	$sphinx = new SphinxClient();
	//2.连接sphinx服务
	$sphinx->setServer('localhost','9312');
	//3.设置匹配模式
	$sphinx->setMatchMode(SPH_MATCH_ANY);//常用的有any  all(关键字必须全部匹配成功)
	//4.进行sphinx查询
	$result = $sphinx->query("$keyword",'*');//  *指搜索所有的索引,返回的是一个四维数组

	//用于返回的数组
	$arr = [];
	if($result['total'] == 0){ //没有查询到返回空数组
		return $arr;
	}
	$idarr = array_keys($result['matches']);  	//得到关键字索引出的id数组
	$ids = join(',',$idarr); 						//将数组中的id拼接成字符串格式为6,7,8

	//连接数据库执行
	$dsn = 'mysql:host='.HOST.'dbname='.DBNAME.'charset='.CHARSET;
	try{
		$pdo = new PDO($dsn,'USER','PASS');
		$pdo->setAttribute(PDO::ATTR_ERROR,PDO::ERROR_EXCEPTION);
		//准备sql语句
		$sql= "select * from ".PIX.$tabName."where id in({$ids})";
		//发送sql语句执行
		$stmt = $pdo->query($sql);
		$arr = $stmt->fetchAll(PDO::FETCH_ASSOC);
		}catch(PDOException $e){
			echo $e->getMessage();
	}
```

