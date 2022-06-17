# Python-SparkMLib-ALS
 Python-SparkMLib-ALS Project

 
 Recommendation engine is the most common machine learning application. In spark, spark mllib supports als (alternative least squares) recommendation algorithm, which is a collaborative filtering recommendation algorithm for machine learning. The collaborative filtering recommendation algorithm of machine learning infers the preferences of each user by observing the evaluation of products given by all users, and recommends multiple suitable products to users. It can also recommend a product to multiple users。


Input data first：
======

	from pyspark.mllib.recommendation import Rating

	rawUserData=sc.textFile("file:/home/*******/pythonwork/PythonProject/data/u.data")

	rawRatings=rawUserData.map(lambda line : line.split("\t")[:3])

	ratingsRDD=rawRatings.map(lambda x : (x[0],x[1],x[2]))

Next training model：
======

	from pyspark.mllib.recommendation import ALS

Introduce the model：
=========

	ALS.train(ratings,rank,iterations,lambda_) then return [MatrixFactorizationModel]

	ratings：The training data format is the RDD of rating。

	rank：It means that when the matrix decomposition is, the columns and rows of the decomposed submatrix。

	iterations：Repeated calculation times of ALS algorithm (default value: 5)

	lambda：default value:0.01

	model=ALS.train(ratingsRDD,10,10,0.01)

After training the model:
=========

1）Recommend movies for users：

model.recommenProducts(100,5)（100 is the user ID to be recommended, and 5 is the number of recommended items）

the result as follows：

	[Rating(user=100, product=787, rating=5.735050369543498),
	Rating(user=100, product=960, rating=5.5242902096781865),
	Rating(user=100, product=464, rating=5.407452596291639),
	Rating(user=100, product=1615, rating=5.345648719395856),
	Rating(user=100, product=1195, rating=5.294806719861592)]

2）View the rating of the product recommended by the user

	model.predict(100,1141)

	1.8628844190011093

3）Recommend movies to users

	model.recommendUsers(200,5)（200 is the product ID to be recommended, and 5 is the number of recommended items）

	[Rating(user=55, product=200, rating=7.529960868321198),
	Rating(user=762, product=200, rating=7.114843443398499),
	Rating(user=475, product=200, rating=7.090671698121618),
	Rating(user=124, product=200, rating=6.810174080284194),
	Rating(user=444, product=200, rating=6.635829271857029)]

Combine u.item to form a complete recommendation engine

	itemRDD=sc.textFile("file:/home/*******/pythonwork/PythonProject/data/u.item")

	movieTitle=itemRDD.map(lambda line : line.split("|").map(lambda a : (float(a[0]),a[1])).collectAsMap()

	recommendP=model.recommendProducts(100,5)

	for p in recommendP:

	print("Usename:"+str(p[0])+" Recommend movies:"+str(movieTitle[p[1]])+"   rating:"+str(p[2]))