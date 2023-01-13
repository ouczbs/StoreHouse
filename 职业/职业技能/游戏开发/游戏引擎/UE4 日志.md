#### 声明新的日志类型

- .h 头文件
- DECLARE_LOG_CATEGORY_EXTERN(ZLog, Log, All); 
- .cpp文件
- DEFINE_LOG_CATEGORY(ZLog); 

#### 控制台命令

- Log ZLog off // 停止 LogName（日志名称）从输出处显示

  Log ZLog  Log //  再次打开 LogName（日志名称）的输出 