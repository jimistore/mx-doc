# MX链接口文档

## 1.调用约定

### 1.1 请求
 > * 接口协议：http
 > * 请求方式：post
 > * 消息格式：application/json
 > * 消息编码：UTF-8
 
请求参数样例：
```
{
    "id":"517080601011",
    "appid":"jimi",
    "name":"xx",
    "sign":""
}
```

### 1.2 响应
 > * 消息格式：application/json
 > * 消息编码：UTF-8

成功响应参数结构：
```
{
    "code":"200",
    "data":{
        "age":"20"
    }
}
```
失败响应参数结构：
```
{
    "code":"501",
    "message":"错误消息详情"
}
```
 
### 1.3 签名算法
为确保接口访问安全，接口的请求和响应应对所有参数使用对称加密算法做签名校验。请求和响应均在header中加入签名参数。
Header参数：

| 名称    | 含义   |  类型  | 是否必填 | 备注            |
| :----   | :----  | :----  | :--      | :-------------  |
| appId |    应用唯一标识    |  varchar(15)  | Y | - |
| timestamp |    时间戳    |  varchar(15)  | Y | 时区GMT+8以秒为单位的时间戳 |
| sign    |    签名    |  varchar(15)  | Y | - |

签名算法：
 > * step1：把所有参数（包括appId、secret、timestamp、body）的key和值拼成字符串放入到数组，得到 array = ['key2=value2','key1=value1']
 > * step2：把数组按照ascii码进行升序排序，得到 array = ['key1=value1','key2=value2']
 > * step3：把数组的元素用&拼成一个字符串，得到 source = 'key1=value1&key2=value2'
 > * step4：根据step3得到的source生成MD5加密值，并转成大写，生成签名。sign=toUpperCase(Md5(source))
 

### 1.4 加密算法
暂略
  
## 2 接口列表
### 2.1 数据上送
接口地址：${api_domain}/api/chain/put/v1 
请求参数：

| 名称    | 含义   |  类型  | 是否必填 | 备注            |
| :----   | :----  | :----  | :--      | :--------  |
| uploadTime | 上传时间 | varchar(10) | Y | 以秒为单位的时间戳 |
| txHash | 链上签名位置 | varchar(10) | Y | 对应的链上签名位置 |
| clientId | 数据唯一标识 | varchar(10) | Y | 幂等控制，同一个clientId只能上传一次 |
| metaData | 信用主体 | {} | Y | - |
| -idcardNum | 用户身份证号码 | varchar(20) | Y | - |
| -idcardName | 用户身份证姓名 | varchar(40) | Y | - |
| -phone | 用户手机号 | varchar(20) | N | - |
| -email | 用户电子邮箱 | varchar(50) | N | - |
| -bankCard | 用户银行卡号 | varchar(30) | N | - |
| -mac | 用户设备物理地址 | varchar(20) | N | - |
| -wifimac | 用户设备WiFi的物理地址 | varchar(20) | N | - |
| -imei | 用户设备标识 | varchar(32) | N | 国际移动设备标识 |
| -ip | 用户设备IP地址 | varchar(24) | N | - |
| creditData | 信用数据 | {} | N | - |
| -source | 数据来源 | varchar(10) | N | self/outside，self为本单位产生，outside为外部数据源产生 |
| -createTime | 下单时间 | varchar(10) | N | 以秒为单位的时间戳 |
| -overdueTime | 逾期时间 | varchar(10) | N | 以秒为单位的时间戳 |
| -level | 黑等级 | varchar(10) | N | M1,M2,M3......M12,M12+；逾期一个月为M1,两个月为M2,依次类推，超过12个月为M12+ |
| -goodsName | 商品名称 | varchar(10) | N | - |
| -price | 价格 | varchar(12) | N | 单位为分 |
| extend | 补充数据 | {} | N | json格式；自由扩展 |

 
请求示例：
```javascript
{
    "clientId":"001",
    "uploadTime":"1564042364",
    "txHash":"XXXXXXXXXXX",
    "metaData":{
        "idcardNum":"111",
        "idcardName":"XXX",
        "phone":"XXX",
        "email":"XXX",
        "bankCard":"XXX",
        "mac":"XXX",
        "wifimac":"XXX",
        "imei":"XXX",
        "ip":"10.1.1.1"
    },
    "creditData":{
        "source":"slef",
        "createTime":"1564042364",
        "overdueTime":"1564042364",
        "level":"M12+",
        "goodsName":"iphone x",
        "price":"600000"
    },
    "extend":{
    	"other":"xxxx"
    }
}
```

成功响应示例：
```
{
    "code": "200",
    "data": {
        "id": "1",
        "clientId": "001"
    }
}
```

异常响应示例：
```
{
    "code": "500503",
    "message": "逾期时间不能小于下单时间"
}
```
### 2.2 数据查询
接口地址：${api_domain}/api/chain/query/v1 
请求参数：

| 名称    | 含义   |  类型  | 是否必填 | 备注            |
| :----   | :----  | :----  | :--      | :--------  |
| id | 上传返回的唯一标识 | varchar(32) | N | - |
| clientId | 数据唯一标识 | varchar(32) | N | - |
| idcardNum | 用户身份证号码 | varchar(20) | N | - |
| idcardName | 用户身份证姓名 | varchar(40) | N | - |
| phone | 用户手机号 | varchar(20) | N | - |

响应参数：

| 名称    | 含义   |  类型  | 是否必填 | 备注            |
| :----   | :----  | :----  | :--      | :--------  |
| uploadTime | 上传时间 | varchar(10) | Y | 以秒为单位的时间戳 |
| txHash | 链上签名位置 | varchar(10) | Y | 对应的链上签名位置 |
| appId | 商户应用唯一标识 | varchar(10) | Y | 每个节点对应的商户应用的唯一标识 |
| clientId | 数据唯一标识 | varchar(10) | Y | 幂等控制，同一个clientId只能上传一次 |
| metaData | 信用主体 | {} | Y | - |
| -idcardNum | 用户身份证号码 | varchar(20) | Y | - |
| -idcardName | 用户身份证姓名 | varchar(40) | Y | - |
| -phone | 用户手机号 | varchar(20) | N | - |
| -email | 用户电子邮箱 | varchar(50) | N | - |
| -bankCard | 用户银行卡号 | varchar(30) | N | - |
| -mac | 用户设备物理地址 | varchar(20) | N | - |
| -wifimac | 用户设备WiFi的物理地址 | varchar(20) | N | - |
| -imei | 用户设备标识 | varchar(32) | N | 国际移动设备标识 |
| -ip | 用户设备IP地址 | varchar(24) | N | - |
| creditData | 信用数据 | {} | N | - |
| -source | 数据来源 | varchar(10) | N | self/outside，self为本单位产生，outside为外部数据源产生 |
| -createTime | 下单时间 | varchar(10) | N | 以秒为单位的时间戳 |
| -overdueTime | 逾期时间 | varchar(10) | N | 以秒为单位的时间戳 |
| -level | 黑等级 | varchar(10) | N | M1,M2,M3......M12,M12+；逾期一个月为M1,两个月为M2,依次类推，超过12个月为M12+ |
| -goodsName | 商品名称 | varchar(10) | N | - |
| -price | 价格 | varchar(12) | N | 单位为分 |
| extend | 补充数据 | {} | N | json格式；自由扩展 |


请求示例：
```
{
    "id": "1",
    "clientId": "001",
    "idcardNum":"111",
    "idcardName":"XXX",
    "phone":"XXX"
}
```
 
成功响应示例：
```javascript
{
    "code":"200",
    "data":[{
        "appId":"001",
        "uploadTime":"1564042364",
        "txHash":"XXXXXXXXXXX",
        "metaData":{
            "idcardNum":"111",
            "idcardName":"XXX",
            "phone":"XXX",
            "email":"XXX",
            "bankCard":"XXX",
            "mac":"XXX",
            "wifimac":"XXX",
            "imei":"XXX",
            "ip":"10.1.1.1"
        },
        "creditData":{
            "source":"slef",
            "createTime":"1564042364",
            "overdueTime":"1564042364",
            "level":"M12+",
            "goodsName":"iphone x",
            "price":"600000"
        },
        "extend":{
            "other":"xxxx"
        }
    }]
    
}
```

异常响应示例：
```
{
    "code": "500505",
    "message": "必填参数{param}不能为空"
}
```
## 3 异常代码

| 代码    | 含义 |
| :----   | :--------  |
| 500501 | 接口权限校验失败 |
| 500502 | 数据格式校验异常 |
| 500503 | 数据逻辑校验异常 |
| 500504 | 数据重复上传 |
| 500505 | 必填参数不能为空 |


