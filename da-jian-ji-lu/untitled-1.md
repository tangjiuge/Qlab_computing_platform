# Evernote Export

[使用方式1：jupyter中导入pyspark使用step1: 打开jupyter, 映射已经设置好，端口号10.129.2.155:8899hadoop@slave1:~$ jupyter notebookstep2: 新建ipynb文件，输入import osimport sysspark\_name = os.environ.get\('SPARK\_HOME',None\)if not spark\_name:    raise ValueErrorError\('spark环境没有配置好'\)sys.path.insert\(0,os.path.join\(spark\_name,'python'\)\)sys.path.insert\(0,os.path.join\(spark\_name,'python/lib/py4j-0.10.4-src.zip'\)\)exec\(open\(os.path.join\(spark\_name,'python/pyspark/shell.py'\)\).read\(\)\)如图：  
   
 就可以使用了]()

[参考链接](https://blog.csdn.net/dxyna/article/details/79772343)  
 主要是将pyspark中的一些包导入python中

### 缺点：不能分配核数与内存，都是默认的参数

## 使用方式2：

### 直接在命令行中启动pyspark，设置启动选项为jupyter

前提anaconda或者jupyter已经安装  
 在命令行中输入：

```text
PYSPARK_DRIVER_PYTHON=~/anaconda3/bin/jupyter-notebook pyspark
```

```text
master$ PYSPARK_DRIVER_PYTHON=~/anaconda3/bin/jupyter-notebook PYSPARK_DRIVER_PYTHON_OPTS=" --ip=0.0.0.0 --port=7777" pyspark --packages com.databricks:spark-csv_2.11:1.1.0 --master spark://spark_master_hostname:7077 --executor-memory 6400M --driver-memory 6400M

```

## 使用方式3：直接在环境变量中添加（不推荐）

**step1:**

```text
vim ~/.bashrc
```

**step2: 添加如下信息**  
 export PYSPARK\_DRIVER\_PYTHON=jupyter  
 export PYSPARK\_PYTHON=/home/hadoop/anaconda3/bin/python3  
 export PYSPARK\_DRIVER\_PYTHON\_OPTS="notebook"  
 **step3:**  
 source ~/.bashrc  
 **step4: 启动**  
 运行pyspark直接启动

```text
hadoop@slave1:~$ pyspark
```

或者指定一些：

```text
pyspark --master spark://slave:7077 --executor-memory xxxM --total-executor-cores xx
```

### 缺点：

因为export了PYSPARK\_DRIVER\_PYTHON与PYSPARK\_DRIVER\_PYTHON\_OPTS两个环境变量后，非shell的pyspark 生怕认可应用也将使用jupyter-notebook，这必然引起混乱，**所以推荐的还是在pyspark的启动命令中当时指定**  
 实测，添加到环境变量中后，spark只支持ipynb的文件，因此不用。

[参考链接](https://blog.csdn.net/NJZhuJinhua/article/details/79441217)

spark-submit --master spark://slave1:7077 --total-executor-cores 144 --executor-memory 70g --driver-memory 30g
