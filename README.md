# Python-SparkMLib-ALS
 Python-SparkMLib-ALS Project

 
 推荐引擎是最常见的机器学习应用，在spark中，Spark MLlib支持ALS（Alternating Least Squares）推荐算法，是机器学习的协同过滤推荐算法。机器学习的协同过滤推荐算法用过观察所有用户给产品的评价来推断每个用户的喜好，并向用户推荐适合的多个产品，也可以把某一个产品推荐给多个用户。


首先输入数据：
======

	from pyspark.mllib.recommendation import Rating

	rawUserData=sc.textFile("file:/home/*******/pythonwork/PythonProject/data/u.data")

	rawRatings=rawUserData.map(lambda line : line.split("\t")[:3])

	ratingsRDD=rawRatings.map(lambda x : (x[0],x[1],x[2]))

接下来训练模型：

	from pyspark.mllib.recommendation import ALS

介绍下模型：
=========

	ALS.train(ratings,rank,iterations,lambda_) 会返回MatrixFactorizationModel

	ratings：训练的数据格式是Rating 的RDD。

	rank：指的是当矩阵分解是，分解的子矩阵的列和行。

	iterations：ALS算法重复计算次数（默认值5）

	lambda：默认值为0.01）

	model=ALS.train(ratingsRDD,10,10,0.01)

训练好模型之后，

1）针对用户推荐电影：

model.recommenProducts(100,5)（100为要被推荐的用户id，5为推荐的项数）

结果如下：

	[Rating(user=100, product=787, rating=5.735050369543498),
	Rating(user=100, product=960, rating=5.5242902096781865),
	Rating(user=100, product=464, rating=5.407452596291639),
	Rating(user=100, product=1615, rating=5.345648719395856),
	Rating(user=100, product=1195, rating=5.294806719861592)]

2）查看针对用户推荐产品的评分

	model.predict(100,1141)

	1.8628844190011093

3）针对电影推荐给用户

	model.recommendUsers(200,5)（200为要被推荐的产品id，5为推荐的项数）

	[Rating(user=55, product=200, rating=7.529960868321198),
	Rating(user=762, product=200, rating=7.114843443398499),
	Rating(user=475, product=200, rating=7.090671698121618),
	Rating(user=124, product=200, rating=6.810174080284194),
	Rating(user=444, product=200, rating=6.635829271857029)]

结合u.item形成一个完整的推荐引擎

	itemRDD=sc.textFile("file:/home/*******/pythonwork/PythonProject/data/u.item")

	movieTitle=itemRDD.map(lambda line : line.split("|").map(lambda a : (float(a[0]),a[1])).collectAsMap()

	recommendP=model.recommendProducts(100,5)

	for p in recommendP:

	print("用户名“+str(p[0])+"推荐电影”+str(movieTitle[p[1]])+"推荐评分"+str(p[2]))