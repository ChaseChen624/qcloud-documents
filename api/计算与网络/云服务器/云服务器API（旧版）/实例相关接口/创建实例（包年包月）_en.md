## 1. API Description
 
This API (RunInstances) is used to create one or more instances with an annual or monthly plan with specified configuration.

Domain name for API request: <font style="color:red">cvm.api.qcloud.com</font>


* The instances created by API are subject to the number limit described in the [Restrictions on CVM Instance Purchase](https://cloud.tencent.com/doc/product/213/CVM%E5%AE%9E%E4%BE%8B%E8%B4%AD%E4%B9%B0%E9%99%90%E5%88%B6), and share the quota with instances created on Console.
* For **limitations on the ratio** of CPU to memory, refer to [CVM Instance Configuration](/document/product/213/2177).
* The creation of an instance will take some time, so this API will not immediately return the instance results. Instead, an InstanceId will be returned, with which you can query the status of the instance through the [DescribeInstances](/doc/api/229/831) API. If the status changes from "Creating" to "Running", the creation is successful.   
* This API is only available for prepaid users now. Please make sure your balance is sufficient before calling the API.
* Instances created using this API will be in "Running" status automatically. You don't need to call [StartInstances](/doc/api/229/1249) again to start it.
* If you need to change the bandwidth, change it using the [UpdateInstanceBandwidth](https://cloud.tencent.com/doc/api/229/1251) API. <font style="color:red">**The bandwidth of public network is 0 by default if not specified**</font>.
* Supported instance types:

| Model | Series 1 | Series 2 |
|---|---|---|---|
| Standard | CVM.S1 | CVM.S2
| High IO | CVM.I1 | CVM.I2
| Memory | CVM.M1 | CVM.M2
| Computational | - | CVM.C2 

## 2. Input Parameters

The following list only provides request parameters for this API. For other parameters, refer to [Common Request Parameters](/document/api/213/6976).

| Parameter | Required | Type | Description |
|---------|---------|---------|---------|
| cpu | Yes | Int | The number of CPU cores. |
| mem | Yes | Int | Memory (GB). |
| imageId | Yes | String | Image ID, which can be obtained from "unImgId" from the result of [DescribeImages](http://cloud.tencent.com/doc/api/229/查询可用的镜像列表) API. |
| period | Yes | Int | Length of purchase period (unit: month). 1 month at least and 36 months at most. |
| storageSize | Yes | Int | Data disk size (GB). The minimum increments in which the adjustment is made is 10G. The default value of this parameter is 0, which means that data disk is not purchased. The type of data disk assigned is the same as specified by `storageType`. For the characteristics and capacity limits of different types of data disks, refer to the [Overview of Hard Disk Products](https://cloud.tencent.com/doc/product/213/498). |
| zoneId | No | Int | [Availability zone](https://cloud.tencent.com/doc/product/213/497#2.-.E5.8F.AF.E7.94.A8.E5.8C.BA) ID. It defaults to the first availability zone of each region. |
| instanceType | No | String | Instance type (e.g. High IO model). The default is `CVM.S1`. For more instance types, see the "Instance types" section above.
| imageType | No | Int | Image type. 1: Private Image, 2: Public Image, 3: Image Marketplace, 4: Shared Image. The default is 2. If the image specifed in `ImageId` is a non-public image, you must also specify the `ImageType`. Otherwise the ` ImageType` parameter can be ignored. |
| bandwidthType | No | String | Bandwidth type. `PayByTraffic`: charge by traffic; `PayByBandwidth`: charge by bandwidth; charge by bandwidth by default. The difference between the network billing modes can be found in [Purchase Network Bandwidth](https://cloud.tencent.com/doc/product/213/509). |
| bandwidth | No | Int | Public network bandwidth (Mbps), or the peak public network bandwidth for charge by traffic mode. The default is 0. |
| wanIp | No | Int | Whether to enable the Public IP. 1: Enable, 0: Not enable. If `bandwidth` is greater than 0, you're free to choose whether to enable the Public IP ("Enable" by default); if `bandwidth` is 0, the Public IP will not be allocated. |
| vpcId | No | String | [Virtual Private Cloud](https://cloud.tencent.com/doc/product/215/535) ID. Required in VPC, not required in basic network.
| subnetId | No | String | [Subnet](/doc/product/215/4927#.E5.AD.90.E7.BD.91) ID, required in VPC (The `subnetId` must be guaranteed to be within this availability zone). |
| isVpcGateway | No | Int | Whether it is the [Public Network Gateway](https://cloud.tencent.com/doc/product/215/3089#3.-.E5.90.91.E7.A7.81.E6.9C.89.E7.BD.91.E7.BB.9C.E4.B8.AD.E6.B7.BB.E5.8A.A0.E5.85.AC.E7.BD.91.E7.BD.91.E5.85.B3.) or not. 0: Non-public network gateway; 1: public network gateway; the default is 0. The public network gateway can be used only when the instance has a Public IP and is in VPC.
| storageType | No | Int | Storage type. 1: Local disk; 2: HHD block storage; 3: SSD local disk; 4: SSD cloud disk. The default is 1. For the selection of hard disk type, refer to [Overview of Storage Products](https://cloud.tencent.com/doc/product/213/498). The optional hard disk type is limited by the InstanceType. In addition, the maximum capacity available for purchase varies with different types of hard disks. |
| rootSize | No | Int | System disk size (GB). <br>The adjustment range for Linux/BSD system is 20~50G, with the minimum adjustable increment of 10G. A fee-free capacity of 20G is allocated by default. Adjustment is not supported for Windows. A fee-free capacity of 50G is allocated by default. The type of system disk must be the same as specified by storageType. |
| password | No | String | Instance password. It will be generated randomly if not set, and be sent via internal message. Linux instance's password should be a combination of 8 - 16 characters comprised of at least two of the following types: letters [a-z, A-Z], numbers [0-9], and special symbols [( ) &#96; ~ ! @ # $ % ^ & * - + = &#124; { } [ ] : ; ' , . ? / ]. Windows instance's password should be a combination of 12 - 16 characters comprised of at least three of the following types: lowercase letters [a-z], uppercase letters [A-Z], numbers [0-9] and special symbols [( ) &#96; ~ ! @ # $ % ^ & * - + = { } [ ] : ; ' , . ? /]. |
| keyId | No | String | [Key](https://cloud.tencent.com/doc/product/213/503) ID. You can use the key to log in to the instance after the key is associated. `keyId` can be obtained through API [DescribeKeyPairs](http://cloud.tencent.com/doc/api/229/%E6%9F%A5%E8%AF%A2%E5%AF%86%E9%92%A5). Key and password cannot both be specified, and specifying key is not supported by Windows operating systems. |
| needSecurityAgent | No | Int | Activate [Cloud security service](https://cloud.tencent.com/doc/product/296/2222). Activate: 1, Not activate: 0. "Activate" by default. |
| needMonitorAgent | No | Int | Activate [Cloud Monitor service](https://cloud.tencent.com/doc/product/248/967). Activate: 1, Not activate: 0. "Activate" by default. |
| projectId | No | Int| [Project ID](https://cloud.tencent.com/doc/api/403/4398). Default project if not filled in. |
| goodsNum | No | Int | Number of purchased instances. The default is 1 and the maximum is 100. |
| clientToken | No | String | `Token` that guarantees the idempotent of this API, up to 64 characters. Different API requests with the same `Token` are deemed as the same request, which can prevent repeated creation of instances resulted from the client's retries in case of network anomaly when requesting an API.
## 3. Output Parameters

| Parameter Name | Type | Description |
|---------|---------|---------|
| code | Int | Common error code. A value of 0 indicates success, and other values indicate failure. For more information, refer to [Common Error Codes](https://cloud.tencent.com/doc/api/372/%E9%94%99%E8%AF%AF%E7%A0%81#1.E3.80.81.E5.85.AC.E5.85.B1.E9.94.99.E8.AF.AF.E7.A0.81) on Error Code page. |
| message | String | Module error message description depending on API. For more information, refer to [Module Error Codes](https://cloud.tencent.com/doc/api/372/%E9%94%99%E8%AF%AF%E7%A0%81#2.E3.80.81.E6.A8.A1.E5.9D.97.E9.94.99.E8.AF.AF.E7.A0.81) on Error Code page. |
| dealIds | Array | The order number generated, which can be used to query the fulfillment status through the [DescribeDeals](/doc/api/403/4392) API. |
| unInstanceIds | Array | ID of an instance generated automatically by the system in the format "ins-xxxxxxxx", which is used to query the details of the instance through [DescribeInstances](/doc/api/229/831) API. |

## 4. Error Codes
The following list only provides the business logic error codes for this API. For additional common error codes, refer to [CVM Error Codes](/document/product/213/6982).

| Error Code | Description |
|---|---|
| InvalidParameter.InvaildZoneId | The zoneId is invalid
| OperationFail.SystemBusy | System is busy with resource purchase
| InvalidParameter.PasswordNotConformSpecs | The password does not conform to the standard
| InvalidParameter.InvaildZoneId | The zoneId is invalid
| OperationConstraints.AccountBalanceNotEnough | Your balance is insufficient, please top up first
| OperationFail.SystemBusy | System is busy with resource purchase


## 5. Example

Input

<pre>
  https://cvm.api.qcloud.com/v2/index.php?Action=RunInstances
  &imageId=img-lkxqa4kj
  &imageType=2
  &bandwidth=1
  &cpu=1
  &mem=2
  &storageType=1
  &storageSize=50
  &period=12
  &goodsNum=1
  &<<a href="https://cloud.tencent.com/doc/api/229/6976">Common request parameters</a>>
</pre>

Output

```
  {
      "code" : 0,
      "message" : "ok",
      "dealIds":[
          121
      ],
     "unInstanceIds": {
        "121": [
            "ins-npphstest"
        ]
    },
  }
```





