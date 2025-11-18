# 飞鹅云打印SDK

## 简介
飞鹅云打印SDK是一个用于与飞鹅云打印服务进行交互的Java客户端库。它提供了设备管理、打印任务提交、状态查询以及扫码回调处理等功能。

## 特性
- 设备管理：添加、删除和修改设备信息
- 打印功能：支持文本和二维码打印
- 状态查询：获取设备状态和打印任务信息
- 回调处理：支持打印和扫码回调的验证与处理
- 安全机制：包含签名验证和数据加密功能

## 安装
将以下依赖添加到您的`pom.xml`文件中：
```xml
<dependency>
    <groupId>com.eeeyou</groupId>
    <artifactId>feie-sdk</artifactId>
    <version>1.0.0</version>
</dependency>
```

## 配置
在您的`application.yml`或`application.properties`文件中添加以下配置：
```yaml
fg:
  user: your_username
  uKey: your_api_key
  publicKey: your_public_key
  scanPrivateKey: your_private_key
```

## 使用示例

### 初始化客户端
```java
FlyGooseConfig config = new FlyGooseConfig();
config.setUser("your_username");
config.setUKey("your_api_key");
config.setPublicKey("your_public_key");
config.setScanPrivateKey("your_private_key");

RestTemplateHttpClient httpClient = new RestTemplateHttpClient(config);
FlyGoosePrintClient printClient = new FlyGoosePrintClient(httpClient);
```

### 构建打印内容
```java
String content = new FeiEYunPrintBuilder()
    .text(10, 10, 0, 1, 1, 0, "Hello World")
    .qr(10, 50, "L", 5, "https://www.example.com")
    .build();
```

### 提交打印任务
```java
String response = printClient.print("device_sn", content);
System.out.println(response);
```

### 设备管理
```java
// 批量添加设备
List<Device> devices = new ArrayList<>();
devices.add(new Device().setDeviceCode("SN001").setDeviceId("ID001"));
String addResponse = printClient.batchAddDevice(devices);
System.out.println(addResponse);

// 获取设备信息
ApiBaseResponse<DeviceInfo> deviceInfo = printClient.getDeviceInfo("device_sn");
System.out.println(deviceInfo.getData());
```

### 回调处理
```java
// 打印回调
PrintCallbackDispatcher printDispatcher = new PrintCallbackDispatcher(config);
PrintCallbackRequest printRequest = new PrintCallbackRequest();
printRequest.setOrderId("order123");
printRequest.setStatus(1);
printRequest.setStime(System.currentTimeMillis());
printRequest.setSign("signature");

PrintCallbackResult printResult = printDispatcher.handleCallback(printRequest, request -> {
    System.out.println("Print callback received: " + request.getOrderId());
    return PrintCallbackResult.success();
});
System.out.println(printResult);

// 扫码回调
ScanCallbackDispatcher scanDispatcher = new ScanCallbackDispatcher(config);
ScanCallbackRequest scanRequest = new ScanCallbackRequest();
scanRequest.setResult("scanned_data");
scanRequest.setSn("device_sn");

ScanCallbackResult scanResult = scanDispatcher.handleCallback(scanRequest, request -> {
    System.out.println("Scan callback received: " + request.getResult());
    return ScanCallbackResult.success(1);
});
System.out.println(scanResult);
```

## API方法

### 打印相关
- `print(String sn, String content)` - 提交打印任务
- `printLabel(String sn, String content, Integer times)` - 提交标签打印任务
- `clearPrintQueue(String sn)` - 清除打印队列
- `getPrinterStatus(String orderId)` - 获取打印机状态

### 设备相关
- `batchAddDevice(List<Device> deviceList)` - 批量添加设备
- `batchDeleteDevice(List<String> snList)` - 批量删除设备
- `modifyDevice(String sn, String name, String phoneNum)` - 修改设备信息
- `getDeviceInfo(String sn)` - 获取设备详细信息
- `getDeviceStatus(String sn)` - 获取设备状态
- `printerSetScanSwitch(String sn, int switchStatus)` - 设置扫码开关

### 查询相关
- `getOrderInfoByDate(String sn, LocalDate localDate)` - 获取指定日期的订单信息

## 工具类

### 签名与加密
- `SignUtils` - 用于生成签名
- `PrintSignatureUtils` - 用于验证打印回调签名
- `ScanSignatureUtils` - 用于解密扫码结果

### 数据处理
- `MapUtils` - 用于处理Map数据转换
- `BeanUtils` - 用于获取Spring Bean

## 异常处理
- `FlyGooseHttpException` - 当API调用出现错误时抛出

## 贡献
欢迎提交Pull Request。对于重大变更，请先提交Issue讨论。

## 许可证
[MIT](https://choosealicense.com/licenses/mit/) 许可证