# docker-spark-mongo
Docker image with spark integrated with pyspark-mongo.

PySpark + [Mongo Hadoop](https://github.com/mongodb/mongo-hadoop):

* Ubuntu 16.04
* Apache Spark 1.6.1
* Mongo Hadoop 1.5.1

To see how it work, you can run a mongo instance:

```bash
docker run -d --name mongo mongo:3.2
```

And after that, run this image:

```bash
docker run -i -t --link mongo:mongo josemyd/docker-spark-mongo /bin/bash
```

Finally, run pyspark as follow:

```bash
$ pyspark --jars ${JARS} --driver-class-path ${SPARK_DRIVER_EXTRA_CLASSPATH}
```
And then check if it works:

```python
import pymongo
import pymongo_spark

mongo_url = 'mongodb://mongo:27017/'

client = pymongo.MongoClient(mongo_url)
client.foo.bar.insert_many([
    {"x": 1.0, "y": -1.0}, {"x": 0.0, "y": 4.0}])
client.close()

pymongo_spark.activate()
rdd = (sc.mongoRDD('{0}foo.bar'.format(mongo_url))
    .map(lambda doc: (doc.get('x'), doc.get('y'))))
rdd.collect()

## [(1.0, -1.0), (0.0, 4.0)]
```

Fork from [zero323](https://hub.docker.com/r/zero323/mongo-spark/) with reference to [stackoverflow](http://stackoverflow.com/questions/33391840/getting-spark-python-and-mongodb-to-work-together). Fixed some problems with enviroment variables and java.

Reference:
[mongo-hadoop](https://github.com/mongodb/mongo-hadoop/tree/master/spark/src/main/python#usage)
