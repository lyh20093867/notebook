# flink_study

flink --help

yarn提交

> flink run -m yarn-cluster -p 20 -yd -ynm OldBuryReceiver2 -yjm 2048 -ytm 4096 -c com.moji.bigdata.task.OldBuryReceiver2  -yt lib/flink-connector-kafka_2.11-1.12.2.jar -yt lib/kafka-clients-2.4.1.jar -yt lib/mysql-connector-java-8.0.11.jar ./datahub-flink-1.0.jar

flink的action命令有
 run:编译和运行一个program
 -m：指定执行模式，yarn-cluster\
 -p 指定并行度
 -y
 -d指定后台运行
 -c 指定class主类名
 -C指定classpath
 -D key=value，指定配置属性
  yarn支持的配置
  -d 指定后台运行
  -m yarn-cluster指定到yarn-cluster执行模式
  -yD key=value 指定配置属性
  -yh 查看yarn模式下的帮助文档
  -yj 指定flink的jar文件的路径
  -yjm 指定JobManager的内存大小，默认为MB单位
  -ynm 指定flink任务的任务名
  -yq 查看yarn上的资源
  -yqu 指定yarn上的队列，不指定默认为default
  -ys 指定每个TaskManager的slot数
  -yt 指定shipfile的路径，一般为额外的jar包路径
  -ytm 指定每个TaskManager的内存大小，默认单位为MB
  -yz 指定在zookeeper中的命名空间，也就是路径，一般用于高可用模式
 run-application:在Application模式下运行一个application
 info:显示program的优化后的执行计划
 list:列出正在运行和调度中的program
   -a 全部的程序
   -r 正在运行的程序
   -s 正处于调度中的程序
 stop:结束一个正在运行的程序，并将数据保存到保存点
    -p 指定savepoint的路径
 cancel:取消一个正在运行的程序       
 savepoint:触发生成检查点

flink run -m yarn-cluster -p 20 -ynm BuryTestLyh -yjm 2048 -ytm 4096 -c com.lyh.flink.ods.BuryTest /home/hadoop/weather_test/flink/test/flink_study-1.0-SNAPSHOT-jar-with-dependencies.jar

flink run -t yarn-per-job -yqu task_moji -p 20 -ynm BuryTestLyh -yjm 2048 -ytm 4096 -c com.lyh.flink.ods.BuryTest /home/hadoop/weather_test/flink/test/flink_study-1.0-SNAPSHOT-jar-with-dependencies.jar

flink run -t yarn-per-job -yqu task_moji -p 20 -ynm BuryTestLyh -yjm 2048 -ytm 4096 -c com.lyh.flink.ods.BuryTest /home/hadoop/weather_test/flink/test/flink_study-1.0-SNAPSHOT-jar-with-dependencies.jar


flink run -m yarn-cluster -yqu task_moji -p 20 -ynm BuryTestLyh -yjm 2048 -ytm 4096 -c com.lyh.flink.ods.BuryTest /home/hadoop/weather_test/flink/test/flink_study.jar

以application模式在yarn上运行TopSpeedWindowing.jar
/data1/moji/soft/flink/bin/flink run-application -t yarn-application /data1/moji/soft/flink/examples/streaming/TopSpeedWindowing.jar
/data1/moji/soft/flink/bin/flink list -t yarn-application -Dyarn.application.id=application_1586917794041_1336951
/data1/moji/soft/flink/bin/flink cancel -t yarn-application -Dyarn.application.id=application_1586917794041_1336951 18604b0a3189449a9f62c70690aaf2a6
以yarn-per-job模式在yarn上运行TopSpeedWindowing.jar
/data1/moji/soft/flink/bin/flink run -yqu task_moji -ynm BuryTestLyh -t yarn-per-job --detached /data1/moji/soft/flink/examples/streaming/TopSpeedWindowing.jar
/data1/moji/soft/flink/bin/flink list -t yarn-per-job -Dyarn.application.id=application_1586917794041_1337546
/data1/moji/soft/flink/bin/flink cancel -t yarn-per-job -Dyarn.application.id=application_1586917794041_1337546 c1634ca0183cdf0b8e812929e8709e82

以session模式运行，session模式中的操作模式有attached和detached两种方式