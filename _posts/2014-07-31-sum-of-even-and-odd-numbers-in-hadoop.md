---
layout: post
title: Sum of even and odd numbers in Hadoop
date: 2014-07-31 20:29:40.000000000 +00:00
categories:
- Hadoop
- Java
tags:
- Hadoop
- Java
author: luka
---
I have been reading about Hadoop in the last few weeks. I went through few books and articles, and wanted to try a more hands-on approach. So, while googling for examples I noticed that there is only one real simple example for Hadoop - Word count. This one is pretty easy, but it would not be really interesting to write another article about it. So, my example will be to count the sum of even and odd numbers in a file.

Sample file (for testing) would consist of numbers - each one on its own line. Example would be this one:
```
1
2
3
4
1
2
```

Our application will, of course, consist of 3 parts: **Mapper**, **Reducer** and a main function to start the Job (in this example called the **Driver**). My files are called: `EvenOddSumMapper.java`, `EvenOddSumReducer.java` and `EvenOddSumDriver.java`.

Here is the code for Mapper
```java
// EvenOddSumMapper.java
public class EvenOddSumMapper extends Mapper<LongWritable, Text, Text, IntWritable> {

    private Text even = new Text("even");
    private Text odd = new Text("odd");

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        int number = Integer.parseInt(value.toString());
        IntWritable n = new IntWritable(number);
        if (number % 2 == 0) {
            context.write(even, n);
        } else {
            context.write(odd, n);
        }
    }
}
```

Simple run through the code: Hadoop reads the file line by line. It parses the line and gets the string out. Checks if the string is even or odd, and depending on the condition it writes the number as a value with the according key being even ("even") or odd ("odd").

Reduces is as follows:
```java
// EvenOddSumReducer.java
public class EvenOddSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {

    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
            sum += val.get();
        }
        context.write(key, new IntWritable(sum));
    }
}
```
Almost exactly (if not) the same code as for the Word Count example. We collect the same keys and update sum of even and odd numbers.

Driver - class responsible for starting the hadoop job:
```java
// EvenOddSumDriver.java
public class EvenOddSumDriver extends Configured implements Tool {

    @Override
    public int run(String[] args) throws Exception {
        Configuration conf = getConf();

        Job job = new Job(conf);
        job.setJarByClass(EvenOddSumDriver.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        job.setMapperClass(EvenOddSumMapper.class);
        job.setCombinerClass(EvenOddSumReducer.class);
        job.setReducerClass(EvenOddSumReducer.class);

        job.setInputFormatClass(TextInputFormat.class);
        job.setOutputFormatClass(TextOutputFormat.class);

        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        return (job.waitForCompletion(true) ? 0 : 1);
    }

    public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new EvenOddSumDriver(), args);
        System.exit(res);
    }
}
```

Textbook example - set classes that represent the mapper and reducer. Start the job and voila. Result should be visible in your output folder. Sample result:
```
even 8
odd 5
```

Now try it on the larger set. :)