# �ֲ�ʽ�����ܡ���MapReduce

<nav>
<a href="#һMapReduce����">һ��MapReduce����</a><br/>
<a href="#��MapReduce���ģ�ͼ���">����MapReduce���ģ�ͼ���</a><br/>
<a href="#��combiner--partitioner">����combiner & partitioner</a><br/>
<a href="#��MapReduce��Ƶͳ�ư���">�ġ�MapReduce��Ƶͳ�ư���</a><br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#41-��Ŀ���">4.1 ��Ŀ���</a><br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#42-��Ŀ����">4.2 ��Ŀ����</a><br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#43-WordCountMapper">4.3 WordCountMapper</a><br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#44-WordCountReducer">4.4 WordCountReducer</a><br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#44-WordCountApp">4.4 WordCountApp</a><br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#45-�ύ������������">4.5 �ύ������������</a><br/>
<a href="#���Ƶͳ�ư�������֮Combiner">�塢��Ƶͳ�ư�������֮Combiner</a><br/>
<a href="#����Ƶͳ�ư�������֮Partitioner">������Ƶͳ�ư�������֮Partitioner</a><br/>
</nav>




## һ��MapReduce����

Hadoop MapReduce ��һ���ֲ�ʽ�����ܣ����ڱ�д������Ӧ�ó��򡣱�д�õĳ�������ύ�� Hadoop ��Ⱥ�����ڲ��д�����ģ�����ݼ���

MapReduce ��ҵͨ������������ݼ����Ϊ�����Ŀ飬��Щ���� `map` �Բ��еķ�ʽ������ܶ� `map` �������������Ȼ�����뵽 `reduce` �С�MapReduce ���ר������ `<key��value>` ��ֵ�Դ���������ҵ��������Ϊһ�� `<key��value>` �ԣ�������һ�� `<key��value>` ����Ϊ��������������� `key` �� `value` ������ʵ��[Writable](http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/io/Writable.html) �ӿڡ�

```
(input) <k1, v1> -> map -> <k2, v2> -> combine -> <k2, v2> -> reduce -> <k3, v3> (output)
```



## ����MapReduce���ģ�ͼ���

�����Դ�Ƶͳ��Ϊ������˵����MapReduce ������������£�

<div align="center"> <img width="600px" src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/mapreduceProcess.png"/> </div>

1. **input** : ��ȡ�ı��ļ���

2. **splitting** : ���ļ������н��в�֣���ʱ�õ��� `K1` ������`V1` ��ʾ��Ӧ�е��ı����ݣ�

3. **mapping** : ���н�ÿһ�а��տո���в�֣���ֵõ��� `List(K2,V2)`������ `K2` ����ÿһ�����ʣ�����������Ƶͳ�ƣ����� `V2` ��ֵΪ 1��������� 1 �Σ�
4. **shuffling**������ `Mapping` �����������ڲ�ͬ�Ļ����ϲ��д���ģ�������Ҫͨ�� `shuffling` ����ͬ `key` ֵ�����ݷַ���ͬһ���ڵ���ȥ�ϲ�����������ͳ�Ƴ����յĽ������ʱ�õ� `K2` Ϊÿһ�����ʣ�`List(V2)` Ϊ�ɵ������ϣ�`V2` ���� Mapping �е� V2��
5. **Reducing** : ����İ�����ͳ�Ƶ��ʳ��ֵ��ܴ��������� `Reducing` �� `List(V2)` ���й�Լ��Ͳ��������������

MapReduce ���ģ���� `splitting` �� `shuffing` ���������ɿ��ʵ�ֵģ���Ҫ�����Լ����ʵ�ֵ�ֻ�� `mapping` �� `reducing`����Ҳ���� MapReduce ����ƺ�����Դ��



## ����combiner & partitioner

<div align="center"> <img width="600px" src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/Detailed-Hadoop-MapReduce-Data-Flow-14.png"/> </div>

### 3.1 InputFormat & RecordReaders 

`InputFormat` ������ļ����Ϊ��� `InputSplit`������ `RecordReaders` �� `InputSplit` ת��Ϊ��׼��<key��value>��ֵ�ԣ���Ϊ map ���������һ������������ֻ���Ƚ����߼���ֲ�תΪ��׼�ļ�ֵ�Ը�ʽ�󣬲���Ϊ��� `map` �ṩ���룬�Ա���в��д���



### 3.2 Combiner

`combiner` �� `map` �����Ŀ�ѡ��������ʵ������һ�����ػ��� `reduce` ����������Ҫ���� `map` ������м��ļ�����һ���򵥵ĺϲ��ظ� `key` ֵ�Ĳ����������Դ�Ƶͳ��Ϊ����

`map` ������һ�� hadoop �ĵ���ʱ�ͻ��¼Ϊ 1��������ƪ������ hadoop ���ܻ���� n ��Σ���ô `map` ����ļ�����ͻ�ܶ࣬����� `reduce` ����ǰ����ͬ�� key ��һ���ϲ���������ô��Ҫ������������ͻ���٣�����Ч�ʾͿ��Եõ�������

���������г������ʺ�ʹ�� `combiner`��ʹ������ԭ���� `combiner` ���������Ӱ�쵽 `reduce` ������������룬���磺�����������ֵ����Сֵʱ������ʹ�� `combiner`��������ƽ��ֵ��������ʹ�� `combiner`��

��ʹ�� combiner �������

<div align="center"> <img  width="600px"  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/mapreduce-without-combiners.png"/> </div>

ʹ�� combiner �������

<div align="center"> <img width="600px"  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/mapreduce-with-combiners.png"/> </div>



���Կ���ʹ�� combiner ��ʱ����Ҫ���䵽 reducer �е������� 12keys�����͵� 10keys�����͵ķ���ȡ������ keys ���ظ��ʣ����Ĵ�Ƶͳ�ư�������ʾ�� combiner �������ٱ��Ĵ�������

### 3.3 Partitioner

`partitioner` �������ɷ��������� `map` ��������� key ֵ�Ĳ�ͬ�ֱ�ָ���Ӧ�� `reducer`��֧���Զ���ʵ�֣����İ����������ʾ��



## �ġ�MapReduce��Ƶͳ�ư���

### 4.1 ��Ŀ���

�������һ������Ĵ�Ƶͳ�Ƶİ�����ͳ����������������ÿ�����ʳ��ֵĴ�����

```properties
Spark	HBase
Hive	Flink	Storm	Hadoop	HBase	Spark
Flink
HBase	Storm
HBase	Hadoop	Hive	Flink
HBase	Flink	Hive	Storm
Hive	Flink	Hadoop
HBase	Hive
Hadoop	Spark	HBase	Storm
HBase	Hadoop	Hive	Flink
HBase	Flink	Hive	Storm
Hive	Flink	Hadoop
HBase	Hive
```

Ϊ�����ҿ�����������ĿԴ���з�����һ�������� `WordCountDataUtils`������ģ�������Ƶͳ�Ƶ����������ɵ��ļ�֧����������ػ���ֱ��д�� HDFS �ϡ�

> ��Ŀ����Դ�����ص�ַ��[hadoop-word-count](https://github.com/heibaiying/BigData-Notes/tree/master/code/Hadoop/hadoop-word-count)



### 4.2 ��Ŀ����

��Ҫ���� MapReduce ��̣���Ҫ���� `hadoop-client` ������

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
```

### 4.3 WordCountMapper

��ÿ�����ݰ���ָ���ָ������в�֡�������Ҫע���� MapReduce �б���ʹ�� Hadoop ��������ͣ���Ϊ Hadoop Ԥ��������Ͷ��ǿ����л����ɱȽϵģ��������;�ʵ���� `WritableComparable` �ӿڡ�

```java
public class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, 
                                                                      InterruptedException {
        String[] words = value.toString().split("\t");
        for (String word : words) {
            context.write(new Text(word), new IntWritable(1));
        }
    }

}
```

`WordCountMapper` ��Ӧ��ͼ�� Mapping ������

<div align="center"> <img  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-code-mapping.png"/> </div>



`WordCountMapper` �̳��� `Mappe` �࣬����һ�������࣬�������£�

```java
WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable>

public class Mapper<KEYIN, VALUEIN, KEYOUT, VALUEOUT> {
   ......
}
```

+ **KEYIN** : `mapping` ���� key �����ͣ���ÿ�е�ƫ���� (ÿ�е�һ���ַ��������ı��е�λ��)��`Long` ���ͣ���Ӧ Hadoop �е� `LongWritable` ���ͣ�
+ **VALUEIN** : `mapping` ���� value �����ͣ���ÿ�����ݣ�`String` ���ͣ���Ӧ Hadoop �� `Text` ���ͣ�
+ **KEYOUT** ��`mapping` ����� key �����ͣ���ÿ�����ʣ�`String` ���ͣ���Ӧ Hadoop �� `Text` ���ͣ�
+ **VALUEOUT**��`mapping` ��� value �����ͣ���ÿ�����ʳ��ֵĴ����������� `int` ���ͣ���Ӧ `IntWritable` ���͡�



### 4.4 WordCountReducer

�� Reduce �н��е��ʳ��ִ�����ͳ�ƣ�

```java
public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {

    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, 
                                                                                  InterruptedException {
        int count = 0;
        for (IntWritable value : values) {
            count += value.get();
        }
        context.write(key, new IntWritable(count));
    }
}
```

����ͼ��`shuffling` ������� reduce �����롣����� key ��ÿ�����ʣ�values ��һ���ɵ������������ͣ����� `(1,1,1,...)`��

<div align="center"> <img  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-code-reducer.png"/> </div>

### 4.4 WordCountApp

��װ MapReduce ��ҵ�����ύ�����������У��������£�

```java

/**
 * ��װ��ҵ ���ύ����Ⱥ����
 */
public class WordCountApp {


    // ����Ϊ��ֱ����ʾ���� ʹ����Ӳ���룬ʵ�ʿ����п���ͨ���ⲿ����
    private static final String HDFS_URL = "hdfs://192.168.0.107:8020";
    private static final String HADOOP_USER_NAME = "root";

    public static void main(String[] args) throws Exception {

        //  �ļ�����·�������·�����ⲿ����ָ��
        if (args.length < 2) {
            System.out.println("Input and output paths are necessary!");
            return;
        }

        // ��Ҫָ�� hadoop �û����������� HDFS �ϴ���Ŀ¼ʱ���ܻ��׳�Ȩ�޲�����쳣
        System.setProperty("HADOOP_USER_NAME", HADOOP_USER_NAME);

        Configuration configuration = new Configuration();
        // ָ�� HDFS �ĵ�ַ
        configuration.set("fs.defaultFS", HDFS_URL);

        // ����һ�� Job
        Job job = Job.getInstance(configuration);

        // �������е�����
        job.setJarByClass(WordCountApp.class);

        // ���� Mapper �� Reducer
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);

        // ���� Mapper ��� key �� value ������
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);

        // ���� Reducer ��� key �� value ������
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        // ������Ŀ¼�Ѿ����ڣ��������ɾ���������ظ����г���ʱ���׳��쳣
        FileSystem fileSystem = FileSystem.get(new URI(HDFS_URL), configuration, HADOOP_USER_NAME);
        Path outputPath = new Path(args[1]);
        if (fileSystem.exists(outputPath)) {
            fileSystem.delete(outputPath, true);
        }

        // ������ҵ�����ļ�������ļ���·��
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, outputPath);

        // ����ҵ�ύ��Ⱥ�����ȴ�����ɣ���������Ϊ true �����ӡ��ʾ��Ӧ�Ľ���
        boolean result = job.waitForCompletion(true);

        // �ر�֮ǰ������ fileSystem
        fileSystem.close();

        // ������ҵ���,��ֹ��ǰ���е� Java �����,�˳�����
        System.exit(result ? 0 : -1);

    }
}
```

��Ҫע����ǣ���������� `Mapper` ������������ͣ������Ĭ������ `Reducer` ���������������ͬ��

### 4.5 �ύ������������

��ʵ�ʿ����У������ڱ������� hadoop ����������ֱ���� IDE ���������в��ԡ�������Ҫ����һ�´���ύ�����������С����ڱ���Ŀû��ʹ�ó� Hadoop ��ĵ�����������ֱ�Ӵ�����ɣ�

```shell
# mvn clean package
```

ʹ�����������ύ��ҵ��

```shell
hadoop jar /usr/appjar/hadoop-word-count-1.0.jar \
com.heibaiying.WordCountApp \
/wordcount/input.txt /wordcount/output/WordCountApp
```

��ҵ��ɺ�鿴 HDFS ������Ŀ¼��

```shell
# �鿴Ŀ¼
hadoop fs -ls /wordcount/output/WordCountApp

# �鿴ͳ�ƽ��
hadoop fs -cat /wordcount/output/WordCountApp/part-r-00000
```

<div align="center"> <img  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-wordcountapp.png"/> </div>



## �塢��Ƶͳ�ư�������֮Combiner

### 5.1 ����ʵ��

��Ҫʹ�� `combiner` ����ֻҪ����װ��ҵʱ���������һ�д��뼴�ɣ�

```java
// ���� Combiner
job.setCombinerClass(WordCountReducer.class);
```

### 5.2 ִ�н��

���� `combiner` ��ͳ�ƽ���ǲ����б仯�ģ����ǿ��ԴӴ�ӡ����־���� `combiner` ��Ч����

û�м��� `combiner` �Ĵ�ӡ��־��

<div align="center"> <img  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-no-combiner.png"/> </div>

���� `combiner` ��Ĵ�ӡ��־���£�

<div align="center"> <img  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-combiner.png"/> </div>

��������ֻ��һ�������ļ�����С�� 128M������ֻ��һ�� Map ���д������Կ������� combiner ��records �� `3519` ����Ϊ `6`(�����е��������ֻ�� 6 ��)������������� combiner ���ܼ���ؽ�����Ҫ�������������



## ������Ƶͳ�ư�������֮Partitioner

### 6.1  Ĭ�ϵ�Partitioner

��������и����󣺽���ͬ���ʵ�ͳ�ƽ���������ͬ�ļ�����������ʵ���ϱȽϳ���������ͳ�Ʋ�Ʒ������ʱ����Ҫ��������ղ�Ʒ������в�֡�Ҫʵ��������ܣ�����Ҫ�õ��Զ��� `Partitioner`��

�����Ƚ����� MapReduce Ĭ�ϵķ�������ڹ��� job ʱ�������ָ����Ĭ�ϵ�ʹ�õ��� `HashPartitioner`���� key ֵ���й�ϣɢ�в��� `numReduceTasks` ȡ�ࡣ��ʵ�����£�

```java
public class HashPartitioner<K, V> extends Partitioner<K, V> {

  public int getPartition(K key, V value,
                          int numReduceTasks) {
    return (key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
  }

}
```

### 6.2 �Զ���Partitioner

�������Ǽ̳� `Partitioner` �Զ������������ﰴ�յ��ʽ��з��ࣺ

```java
public class CustomPartitioner extends Partitioner<Text, IntWritable> {

    public int getPartition(Text text, IntWritable intWritable, int numPartitions) {
        return WordCountDataUtils.WORD_LIST.indexOf(text.toString());
    }
}
```

�ڹ��� `job` ʱ��ָ��ʹ�������Լ��ķ�����򣬲����� `reduce` �ĸ�����

```java
// �����Զ����������
job.setPartitionerClass(CustomPartitioner.class);
// ���� reduce ����
job.setNumReduceTasks(WordCountDataUtils.WORD_LIST.size());
```



### 6.3  ִ�н��

ִ�н�����£��ֱ����� 6 ���ļ���ÿ���ļ���Ϊ��Ӧ���ʵ�ͳ�ƽ����

<div align="center"> <img  src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-wordcountcombinerpartition.png"/> </div>


