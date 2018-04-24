# 相关配置

[https://xdebug.org/docs/all\_settings](https://xdebug.org/docs/all_settings)

```
[xdebug]
zend_extension="/usr/local/opt/php71-xdebug/xdebug.so"
;[扩展配置]==========
xdebug.idekey=PHPSTORM
xdebug.file_link_format="phpstorm://open?file=%f&line=%l"
;默认开启,当对脚本进行追踪或者效能分析时建议关闭,影响性能
;xdebug.extended_info=0
;关闭作用域分析,默认开启
;xdebug.coverage_enable=0
;将所有的NOTICE和WARNING都转换为错误
;xdebug.halt_level=E_WARNING|E_NOTICE|E_USER_WARNING|E_USER_NOTICE
;下面的保持默认
;xdebug.scream=0
;xdebug.force_display_errors=1
;xdebug.force_error_reporting=1
;xdebug.max_nesting_level=256
;xdebug.max_stack_frames=-1
;xdebug.manual_url版本<2.2.1可用

;[错误显示]==========
;默认为1,显示错误轨迹
xdebug.default_enable=1
xdebug.dump.SERVER=REMOTE_ADDR,REQUEST_METHOD
xdebug.dump.REQUEST=*
xdebug.dump.GET=*
xdebug.dump.POST=*
xdebug.dump.COOKIE=*
xdebug.dump.SESSION=*
xdebug.dump.FILE=*
;默认为1,设置0值关闭前面设置的全局变量的显示
;xdebug.dump_globals=0
;默认为1,设置0值关闭前面设置的全局变量的一次显示
;xdebug.dump_once=0
xdebug.dump_undefined=1

;[打印显示]==========
xdebug.cli_color=2
xdebug.overload_var_dump=2
xdebug.var_display_max_children=-1
xdebug.var_display_max_data=-1
xdebug.var_display_max_depth=-1

;[运行轨迹]==========
xdebug.auto_trace=1
;开启手动跟踪XDEBUG_TRACE
;xdebug.trace_enable_trigger=1
;xdebug.trace_enable_trigger_value="trace_log"
;追加文件
xdebug.trace_options=1
xdebug.trace_format=2
xdebug.trace_output_dir="/Users/headplan/Desktop/Log/Xdebug"
xdebug.trace_output_name="Trace.%R"
;[收集]=====
xdebug.collect_assignments=1
;默认值为1
;xdebug.collect_includes=1
xdebug.collect_params=4
xdebug.collect_return=1
;返回申明的变量集合时启用
;xdebug.collect_vars=1
;[展示]=====
xdebug.show_error_trace=1
xdebug.show_exception_trace=1
;展示所有变量信息,信息较多通常关闭
;xdebug.show_local_vars=0
;显示增量内存
xdebug.show_mem_delta=1

;[远程调试]==========
xdebug.remote_autostart=1
xdebug.remote_enable=1
xdebug.remote_host=localhost
xdebug.remote_port=9009
;开启根据$_SERVER['REMOTE_ADDR']变量获取host地址
;xdebug.remote_connect_back=1
;自定义$_SERVER变量键
;xdebug.remote_addr_header="REMOTE_ADDR2"
;开启PHP3支持协议,默认dbgp
;xdebug.remote_handler="gdb"
;xdebug.remote_cookie_expire_time=3600
xdebug.remote_log="/Users/headplan/Desktop/Log/Xdebug/remote.log"

;[性能分析]==========
xdebug.profiler_enable=1
;开启手动配置XDEBUG_PROFILE
;xdebug.profiler_enable_trigger=1
;xdebug.profiler_enable_trigger_value="profiler_log"
;追加文件
;xdebug.profiler_append=1
xdebug.profiler_output_dir="/Users/headplan/Desktop/Log/Xdebug"
xdebug.profiler_output_name="Profiler.%p"
;开启多个页面或多次重新加载页面平均值数据
;xdebug.profiler_aggregate=1
```



