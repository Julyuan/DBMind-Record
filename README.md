# DBMind诊断部分项目结构说明

下面是auto_detector和auto_diag的项目结构说明，auto_detector是数据库和操作系统的指标异常监控模块，auto_diag是根因分析的模块

## auto_detector

auto_detector是数据库和操作系统的指标异常监控模块

### 启动服务

1. 首先进入auto_detector目录

```bash
cd auto_detecor.py
```

2. 然后使用python启动脚本src/statistics/detector.py

```bash
pyhon src/statistics/detector.py >> out.txt 2 > &1
```

这里假设所有的输出都重定向到out.txt文件里

3. 监测到异常以后的结果汇报

auto_detector的结果位于/auto_detector/result_pickle目录下面，其中的文件是以日期命名的
auto_diag的结果位于/auto_diag/res_json_new目录下面，其中文件的日期和auto_detector下面的结果是一致的

4. 退出detect程序

因为detector.py程序是一个死循环，要想退出程序使用用

### 执行逻辑

detector.py是一个死循环，最外面是一个while True的结构，在循环里面每过一个detect_interval就调用grab_metrics以及extract_anomaly_metrics，抓取监控数据并且分析异常指标。如果系统认为此时出现了异常，则调用/auto_diag/exec_diag.py诊断异常

抓取模块的代码位于src/data_collector目录下的metrics_collector.py以及metrics_processor.py，前者是向prometheus数据库发送请求并接收数据，后者则是使用pandas处理返回以后的数据。data_collector的配置文件位于conf_files目录下面。

## auto_diag

exec_diag.py是当前的根因诊断模块，在这里程序会解析异常指标，并和历史异常数据进行特征匹配(thumprint_new.pickle)，然后汇总汇报结果，auto_diag的结果位于/auto_diag/res_json_new目录下面。
