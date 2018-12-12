# 路由协议详解

#### 默认路由协议

默认的路由协议**Yaf\_Route\_Static** , 就是分析请求中的**request\_uri** , 在去除掉**base\_uri**以后 , 获取到真正的负载路由信息的**request\_uri**片段 , 具体的策略是 , 根据"/"对**request\_uri**分段 , 依次得到Module,Controller,Action在得到Module以后 , 还需要根据Yaf\_Application::$modules来判断Module是否是合法的Module , 如果不是 , 则认为Module并没有体现在**request\_uri**中 , 而把原Module当做Controller , 原Controller当做Action : 



