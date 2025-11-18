# FeiE Cloud Printing SDK

## Introduction
The FeiE Cloud Printing SDK is a Java client library designed to interact with the FeiE Cloud Printing service. It provides functionalities for device management, print task submission, status queries, and handling scan callbacks.

## Features
- **Device Management**: Add, delete, and modify device information
- **Printing Functionality**: Supports text and QR code printing
- **Status Queries**: Retrieve device status and print task information
- **Callback Handling**: Supports validation and processing of print and scan callbacks
- **Security Mechanisms**: Includes signature verification and data encryption

## Installation
Add the following dependency to your `pom.xml` file:
```xml
<dependency>
    <groupId>com.eeeyou</groupId>
    <artifactId>feie-sdk</artifactId>
    <version>1.0.0</version>
</dependency>
```

## Configuration
Add the following configuration to your `application.yml` or `application.properties` file:
```yaml
fg:
  user: your_username
  uKey: your_api_key
  publicKey: your_public_key
  scanPrivateKey: your_private_key
```

## Usage Examples

### Initialize Client
```java
FlyGooseConfig config = new FlyGooseConfig();
config.setUser("your_username");
config.setUKey("your_api_key");
config.setPublicKey("your_public_key");
config.setScanPrivateKey("your_private_key");

RestTemplateHttpClient httpClient = new RestTemplateHttpClient(config);
FlyGoosePrintClient printClient = new FlyGoosePrintClient(httpClient);
```

### Build Print Content
```java
String content = new FeiEYunPrintBuilder()
    .text(10, 10, 0, 1, 1, 0, "Hello World")
    .qr(10, 50, "L", 5, "https://www.example.com")
    .build();
```

### Submit Print Task
```java
String response = printClient.print("device_sn", content);
System.out.println(response);
```

### Device Management
```java
// Batch add devices
List<Device> devices = new ArrayList<>();
devices.add(new Device().setDeviceCode("SN001").setDeviceId("ID001"));
String addResponse = printClient.batchAddDevice(devices);
System.out.println(addResponse);

// Get device information
ApiBaseResponse<DeviceInfo> deviceInfo = printClient.getDeviceInfo("device_sn");
System.out.println(deviceInfo.getData());
```

### Callback Handling
```java
// Print callback
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

// Scan callback
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

## API Methods

### Printing Related
- `print(String sn, String content)` - Submit a print task
- `printLabel(String sn, String content, Integer times)` - Submit a label print task
- `clearPrintQueue(String sn)` - Clear the print queue
- `getPrinterStatus(String orderId)` - Retrieve printer status

### Device Related
- `batchAddDevice(List<Device> deviceList)` - Batch add devices
- `batchDeleteDevice(List<String> snList)` - Batch delete devices
- `modifyDevice(String sn, String name, String phoneNum)` - Modify device information
- `getDeviceInfo(String sn)` - Retrieve detailed device information
- `getDeviceStatus(String sn)` - Retrieve device status
- `printerSetScanSwitch(String sn, int switchStatus)` - Enable/disable scan function

### Query Related
- `getOrderInfoByDate(String sn, LocalDate localDate)` - Retrieve order information for a specified date

## Utility Classes

### Signing and Encryption
- `SignUtils` - Generates signatures
- `PrintSignatureUtils` - Validates print callback signatures
- `ScanSignatureUtils` - Decrypts scan results

### Data Processing
- `MapUtils` - Handles Map data conversion
- `BeanUtils` - Retrieves Spring Beans

## Exception Handling
- `FlyGooseHttpException` - Thrown when API calls fail

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss.

## License
[MIT](https://choosealicense.com/licenses/mit/) License