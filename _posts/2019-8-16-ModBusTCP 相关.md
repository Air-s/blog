---
layout: post
title: ModBusTCP 相关
tags: [基础]
excerpt_separator: <!--more-->
---



<!--more-->

线圈=寄存器

线圈状态=寄存器数值

#### 读多个寄存器

**请求**

| MBAP报文头 | 设备地址 | 功能码 | 读取寄存器起始地址 | 读取寄存器个数 |
| :--------: | :------: | :----: | :----------------: | :------------: |
|   6字节    |  1字节   | 1字节  |       2字节        |     2字节      |

**响应**

| MBAP报文头 | 设备地址 | 功能码 | 返回有效字节数 | 数据  |
| :--------: | :------: | :----: | :------------: | :---: |
|   6字节    |  1字节   | 1字节  |     1字节      | N字节 |



### 工具方法

**十六进制字符串转字节数组**

```c#
private byte[] HexStringToByteArray(string hexString)
{
    hexString = hexString.Replace(" ", "");
    if ((hexString.Length % 2) != 0)
        hexString = "0" + hexString;
    byte[] returnBytes = new byte[hexString.Length / 2];
    for (int i = 0; i < returnBytes.Length; i++)
        returnBytes[i] = Convert.ToByte(hexString.Substring(i * 2, 2), 16);
    return returnBytes;
}
```

**字节数组转十六进制字符串**

```c#
/* 输出：01-F5（以"-"分割，可用 Replace("-", "") 方法消除）
 * BitConverter.ToString(new byte[]{ 0x01, 0xF5 });
 */
BitConverter.ToString(BYTE_ARRAY);
```

**十六进制字符串转十进制数**

```c#
/* 输出：501
 * Int32.Parse("01F5", System.Globalization.NumberStyles.HexNumber);
 */
int num = Int32.Parse(HexString, System.Globalization.NumberStyles.HexNumber);
```

**打印字节数组**

```c#
/* 输出：1，11110101
 * PrintByteArray(new byte[]{ 0x01, 0xF5 });
 */
private void PrintByteArray(byte[] byteArray) {
    String byteStr = null;
    foreach (byte b in byteArray)
    {
        if (byteStr == null)
            byteStr = Convert.ToString(b, 2);
        else
            byteStr += "，" + Convert.ToString(b, 2);
    }
    Console.WriteLine(byteStr);
}
```



### 参考列表

- [**【Microsoft】如何在十六进制字符串与数值类型之间转换（C# 编程指南）**](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/types/how-to-convert-between-hexadecimal-strings-and-numeric-types)

- [**【Modbus】 如何看懂 Modbus TCP 通訊協定**](https://dotblogs.com.tw/leo_codespace/2018/12/26/185411)

- [**【CSDN】初识 Modbus TCP/IP——C# 编写 Modbus TCP 客户端程序**](https://blog.csdn.net/thebestleo/article/details/52269999)