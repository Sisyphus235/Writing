# Background
python 以开发快速，灵活多变，兼容性强著称，在一些情况中，python 需要调用其他语言的代码，例如某项服务不提供 python SDK，或者效率上 Cpython 的解析运行效率达不到要求，又或者其他语言或框架在某个领域更加擅长。本文记录了一次 python 调用 java 服务的实战经历，核心原因是阿里云的实时语音转文本接口没有提供 python SDK。

# Python Preparation
python 调用 java 常见有 jpype, pyjnius, py4j 等，其中 jpype 和 py4j 有相同的核心开发者，后者的出现是因为开发者认为 jpype 的底层架构不够好，所以本文使用 py4j 作为工具实现。
首先安装 py4j
```shell
$ pip install py4j
```
接着准备 python 关于阿里云实时语音转文本的接口函数
```python
import json
from aliyunsdkcore.client import AcsClient  # 阿里云
from aliyunsdkcore.request import CommonRequest  # 阿里云
from werkzeug.exceptions import BadRequest  # 异常处理
from py4j.java_gateway import JavaGateway  # 从 py4j 引入 java gateway

# 需要设置的全局变量，从阿里云账户获得
APP_KEY = 'xxx'
ACCESS_KEY_SECRET = 'xxx'
ONLINE_DOMAIN = 'xxx'
ONLINE_API = 'xxx'
URI_PATTERN = 'xxx'

def create_token(method):
    """
    创建阿里云连接 token
    """
    # create request
    common_request = CommonRequest(domain=ONLINE_DOMAIN, version=ONLINE_API, uri_pattern=URI_PATTERN)
    common_request.set_method(method)

    # create AcsClient
    my_client = AcsClient(ACCESS_KEY_ID, ACCESS_KEY_SECRET, 'cn-shanghai')

    response = my_client.do_action_with_exception(common_request)

    return response
    
def get_online_transcribe():
    """
    连接 java 服务获得阿里云实时语音转文本的响应
    """
    # create token
    token_response = eval(create_token('POST').decode('utf-8'))
    token = token_response.get('Token', None)
    if not token:
        raise BadRequest('internal error')
    token_id = token.get('Id', None)
    if not token_id:
        raise BadRequest('internal error')

    # create client
    gateway = JavaGateway()
    gateway.entry_point.run(APP_KEY, token_id)  # 和 java 服务配置相关
```

# Java Preparation
首先在 maven 中加入 py4j 的依赖，在根路径 pom.xml 中添加 dependency
```xml
<dependencies>
    <dependency>
        <groupId>net.sf.py4j</groupId>
        <artifactId>py4j</artifactId>
        <version>0.10.8</version>
    </dependency>
</dependencies>
```
接着配置 java [阿里云实时语音转文本](https://help.aliyun.com/document_detail/84430.html?spm=a2c4g.11186623.6.567.1cce40c1G4e49H)主要的功能代码，详见上文所附链接。
再配置 py4j 的接入代码。
```java
package com.alibaba.nls.client.example;

import py4j.GatewayServer;  // 引入 pyfj

import com.alibaba.nls.client.example.SpeechTranscriberWithMicrophoneDemo;

public class SpeechTranscriberWithMicrophoneDemoEntryPoint {
    // 启动函数
    public void run(String appKey, String token) {
        SpeechTranscriberWithMicrophoneDemo demo =
                new SpeechTranscriberWithMicrophoneDemo(appKey, token);
        demo.process();
        demo.shutdown();
    }

    public static void main(String[] args) {

        SpeechTranscriberWithMicrophoneDemoEntryPoint app = new SpeechTranscriberWithMicrophoneDemoEntryPoint();
        // py4j服务
        GatewayServer gatewayServer = new GatewayServer(app);
        gatewayServer.start();
        System.out.println("Gateway Server Started");
    }
}
```

# Conclusion
最后分别启动 java 和 python 相关服务，完成相关功能。