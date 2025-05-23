# harmonyLane 快速构建harmony应用的流水线的fastlane脚本
## 1.简介
你只需要一行命令，搞定harmony 应用的构建，签名，上传与内测分发。简化开发流程，提高开发效率。

## 2.功能

- **打包**：自动打出所有的 hap 包。
- **签名 manifest.json5**：读取应用打包数据，自动生成已签名的 `manifest.json5` 文件。
- **生成分发页面**：自动生成分发页面
- **二维码生成**：自动生成应用的二维码，方便内测人员快速下载和安装。
- **cdn/OSS/ftp 上传**：可将打包好的所有文件上传到cnd/oss/ftp 。
- **机器人通知** 以钉钉机器人为例，打包完成后自动发出通知，将分发页面链接自动发送群聊


## 3.基础环境：

JDK 8+： 官方签名工具需要。 

ruby 3.0+ ：二维码生成库需要

Command Line Tools for HarmonyOS ：鸿蒙应用开发官方命令行工具

[manifest-sign-tool](https://gitee.com/arkin-internal-testing/internal-testing#%E7%94%9F%E6%88%90%E7%AD%BE%E5%90%8D)：生成应用签名工具

```sh
# 在你使用的终端中，检查以下工具是否成功安装：

java --version # 生成签名工具需要使用到

ruby --v     # fastlane工具是基于ruby脚本

hvigorw -v   # command-line-tool 配置完即可用

ohpm -v   # 或 command-line-tool 配置完即可用

```

## 4.准备工作
### 4.1知识储备
在使用本脚本之前，建议先阅读一下华为官方给出的HarmonyOS应用内部测试[指导文档](https://developer.huawei.com/consumer/cn/doc/app/agc-help-harmonyos-internaltest-0000001937800101#section91371234112015)
文档里官方给出的流程图如下：
![flow.jpg](img/flow.jpg)

### 4.2证书准备
准备工作，你需要生成这三个文件：

1、发布证书： .cer 格式
2、内部测试 Profile: .p7b 格式
3、私钥文件： .p12 格式

## 5.使用步骤
### 5.1脚本准备

将fastlane文件夹和Gemfile脚本，拖拽到你的代码项目里。例如demo文件夹

### 5.2修改配置
打开fastlane/FastConfig.yaml，根据你的实际情况，修改配置

```ruby

app_name: #app名称

# 编译相关参数 
sign_tool_path: "/manifest-sign-tool" # 从user路径开始 具体存放签名工具的路径

# 钉钉聊天机器人 选填

robot_webhook_url: # 例如钉钉自定义机器人的地址

# cdn-oss上传参数
cdn_param: "param"
cdn_upload_api: "http://your_domain/upload.do"
public_cdn_url: 'https://your_domain/cdn-oss/'
# 域名
deploy_domain: 'your_domain'  #需和cdn保持一致
# 图标素材url
icon_url: 'https:your_domain/path/icon.png'
```

### 5.3脚本适配(可选)

#### 5.3.1 业务参数适配
主要是适配upload_to_server这个子函数的想过参数，不同的服务器，所需参数不同，需要自行根据实际情况适配oss/cdn的业务参数。建议将业务配置，放到FastConfig.yaml配置文件，在upload_to_server这个lane中，将业务参数拼接

```
params = {
    		#业务参数
    		"business_param" =>  "xxxx",
    		"file"	=> Faraday::UploadIO.new(filePath, 'application/hap', fileName),
		}
```

#### 5.3.2 下载页模板适配
temple.html为下载页模板，里面的icon路径使用的相对路径，可视情况考虑写死一个url链接。
模板样式不满意，可自行找其他模板代替，只要保证$开头的变量保持一致即可。



### 5.4执行脚本
脚本里预制了3个环境，分别是sit，uat，pro
那么执行脚本只需要cd到项目目录，直接
```
fastlane sit/uat/pro
```
即可一键构建hap包，生成签名json，上传到服务器，生成下载页面，发送钉钉通知





