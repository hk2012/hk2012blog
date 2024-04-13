---
title: U盾项目代码熟悉
category: C
tag: 熟悉项目
abbrlink: 61331
date: 2024-04-10 19:09:00
---


# demo1 - nativehost_2_v1



# natchost-v1

## StdAfx.h 文件

- StdAfx.h 头文件代码
    ```c
    #if !defined(AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_)
    #define AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_
    #if _MSC_VER > 1000
    #pragma once
    #endif // _MSC_VER > 1000
    // TODO: reference additional headers your program requires here
    // AFX_INSERT_LOCATION
    #endif // !defined(AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_)
    ```
- 代码解释
    这个头文件是一个预编译头文件（Precompiled Header File），通常在使用 Microsoft Visual C++ 编译器编译的项目中会看到这样的文件。这个头文件的作用是包含一些系统标准头文件或者项目特定的头文件，这些头文件在整个项目中被频繁使用，但是不经常修改。通过将它们包含在预编译头文件中，可以提高编译速度。

- 下面是对这个头文件中的各部分的解释：

    1. #if !defined(AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_)：
    这行代码是一个条件预处理指令，它检查在编译时是否定义了宏 AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_。如果这个宏没有被定义，就执行 #define AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_ 这行代码，防止头文件被多次包含。

    2. #if _MSC_VER > 1000：
    这行代码是针对 Microsoft Visual C++ 编译器的条件预处理指令。它检查编译器的版本是否大于 1000，如果是，则执行 #pragma once，这是一种防止头文件被多次包含的更现代的方法。这样做可以提高编译效率，避免重复编译。

    3. // TODO: reference additional headers your program requires here：
    这是一个注释，提示程序员在这里引用项目中需要的其他头文件。

    4. AFX_INSERT_LOCATION：
    这两行标记了 Visual C++ 的编辑器在向此文件插入其他标记之前应插入额外的声明的位置。这样做是为了确保预编译头文件之后仍然能够正确地引入其他声明。

    5. #endif 表示: !defined (AFX_STDAFX_H__A2E71064_82CE_4D09_9427_F957E26039E0__INCLUDED_)：
    这行代码结束了条件预处理指令的块，并且标记着这个头文件的结尾。


## natchost files Source Files
### natchost.cpp
#### 源代码
```cpp
#include "stdafx.h"
#include "windows.h"

#include <stdio.h>
#include <fcntl.h>
#include <io.h>
#include<string.h>
#include <json/json.h>
using namespace std;
#define MAX_LEN 4096
typedef char *(WINAPI *CONNECTPROC)();
typedef char *(WINAPI *COLLSNPROC)();
typedef char *(WINAPI *COLLIDPROC)(char *a);
typedef char *(WINAPI *COLLFPRPROC)(char *a);
typedef char *(WINAPI *DELFPPROC)(char * a);
typedef char *(WINAPI *VERIFYPROC)(char * a);
// 从标准输入输出接收数据
char g_input[4096];
// 写日志函数
int write_log_file(const char *str);
// 读取标准输入的数据
char * get_input();
// 输出
int out_write(const char *message);
// 处理结束符
int handle_EOF(char *dest, char *src);
// 拼接数组
int arr_cat(char * dest, unsigned int from, const char * src, unsigned int len);
string replaceChar(string src, string sub, string dest);
HINSTANCE WLinst;

// 返回0为成功，其他值是失败
int main()
{
    int result; // 设置数据流模式结果
    char reclog[] = "recieve command: ";
	char commlog[] = "parsed command: ";
	char castlog[] = "cast command: ";
	char connlog[] = "usbkey connect response: ";
	char snlog[] = "usbkey sn response: ";
	char idlog[] = "usbkey id number response: ";
	char fplog[] = "usbkey fingerprint response: ";
	char dellog[] = "usbkey delete fingerprint response: ";
	char verifylog[] = "usbkey verify response: ";
	char inlen = 0; // 接收字符串长度

	//char message[] = "{\"text\": \"hello chrome extension\"}"; // 例子
	//char message[] = "{\"command\": \"collFingerprint\", \"res\": {\"code\":\"0\",\"content\":\"collect fingerprint failure!\"}}";
	

    // 设置数据流模式模式为二进制流
    result = _setmode( _fileno( stdin ), _O_BINARY );
    if( result == -1 )
        out_write("Cannot set mode\n");

	WLinst = LoadLibrary("natcud.dll");
    if (WLinst == 0)
		return -1;

	CONNECTPROC ffconnect=(CONNECTPROC)GetProcAddress(WLinst,"ffconnect");
	COLLSNPROC collsn=(COLLSNPROC)GetProcAddress(WLinst,"collTerminalSN");
	COLLIDPROC collid=(COLLIDPROC)GetProcAddress(WLinst,"collIdNumber");
	COLLFPRPROC collfp=(COLLFPRPROC)GetProcAddress(WLinst,"collFingerPrint");
	DELFPPROC delfp=(DELFPPROC)GetProcAddress(WLinst,"deleteFingerprints");
	VERIFYPROC verify=(VERIFYPROC)GetProcAddress(WLinst,"verify");
	
	
	char * tmpcomm = get_input();
	strcat(reclog, tmpcomm);
	write_log_file(reclog);
	
	std::string command(tmpcomm);
	strcat(castlog, command.c_str());
    write_log_file(castlog);
	

	//std::string command = "{\"command\":\"collTerminalSN\"}";
//	std::string command = "{\"command\": \"collTerminalSN\"}";
	//std::string command = "{\"command\": \"collIdNumber\",\"req\": {\"serial\": \"32016225\",\"idNumber\": \"411481199110128450\"}}";
	//std::string command = "{\"command\": \"collFingerprint\",\"req\": {\"serial\": \"32016225\"}}";
	//std::string command = "{\"command\": \"collFingerprint\",\"req\": {\"serial\": \"32016225\",\"fingerNo\": \"\"}}";
	//std::string command = "{\"command\": \"deleteFingerprints\",\"req\": {\"serial\": \"32016225\"}}";
    // 指令判断处理 ..........................
	//std::string command = "{\"command\": \"verify\",\"req\": {\"serial\": \"30077052\"}}";

	 //std::string command = "{\"cmd\": \"f\",\"req\": {\"s\": \"30077052\",\"f\": 1}}";

	Json::Value jData;
	Json::Reader reader;
	reader.parse(command,jData);
	string comname = (char *)jData["cmd"].asString().c_str();
	strcat(commlog, comname.c_str());
	write_log_file(comname.c_str());

	if (stricmp(comname.c_str(),"connect")==0)
	{	
		
		char * rdata = ffconnect();
		// 写日志
		strcat(connlog, rdata);
		write_log_file(connlog);

		Json::Value root;
		Json::Value res;
		root["cmd"] = comname;
		root["res"] = rdata;
		root.toStyledString();
		std::string rtnOut = root.toStyledString();
		string rtn = replaceChar(rtnOut,"\\n","");
		rtn = replaceChar(rtn,"\n","");
		out_write(rtn.c_str());

	}
	else if (stricmp(comname.c_str(),"sn")==0)
	{
		char * rdata = collsn();
		// 写日志
		strcat(snlog, rdata);
		write_log_file(snlog);
		Json::Value root;
		Json::Value res;
		root["cmd"] = comname;
		root["res"] = rdata;
		root.toStyledString();
		std::string rtnOut = root.toStyledString();
		string rtn = replaceChar(rtnOut,"\\n","");
		rtn = replaceChar(rtn,"\n","");
		out_write(rtn.c_str());
		
	}
	else if (stricmp(comname.c_str(),"i")==0)
	{
		
//读取json子结构
		Json::Value reqData=jData["req"];
//		string req=jData["command"].asString().c_str();
		string req = reqData.toStyledString();//转换成字符串
		char * rdata = collid((char *)req.c_str());
		// 写日志
		strcat(idlog, rdata);
		write_log_file(idlog);

		Json::Value root;
		Json::Value res;
		root["cmd"] = comname;
		root["res"] = rdata;
		root.toStyledString();
		std::string rtnOut = root.toStyledString();
		string rtn = replaceChar(rtnOut,"\\n","");
		rtn = replaceChar(rtn,"\n","");
		out_write(rtn.c_str());
		
	}
	else if (stricmp(comname.c_str(),"f")==0)
	{
		Json::Value reqData = jData["req"];
		string req = reqData.toStyledString();//转换成字符串
		//char * req=(char *).asString().c_str();
		char * rdata = collfp((char *)req.c_str());
		// 写日志
		strcat(fplog, rdata);
		write_log_file(fplog);

		Json::Value root;
		Json::Value res;
		root["cmd"] = comname;
		root["res"] = rdata;
	//	root.toStyledString();
		std::string rtnOut = root.toStyledString();
		string rtn = replaceChar(rtnOut,"\\n","");
		rtn = replaceChar(rtn,"\n","");
		out_write(rtn.c_str());
		
	}
	else if (stricmp(comname.c_str(),"verify")==0)
	{
		Json::Value reqData = jData["req"];
		string req = reqData.toStyledString();//转换成字符串
		// char * req=(char *)jData["req"].asString().c_str();
		char * rdata = verify((char *)req.c_str());

		Json::Value rtnData = rdata;
		string _rtnData = rtnData.toStyledString();
		
		// 写日志
		strcat(verifylog, rdata);
		write_log_file(verifylog);

		Json::Value root;
		Json::Value res;
		root["cmd"] = comname;
		root["res"] = rdata;
		root.toStyledString();
		std::string rtnOut = root.toStyledString();
		string rtn = replaceChar(rtnOut,"\\n","");
		rtn = replaceChar(rtn,"\n","");
		out_write(rtn.c_str());
		
	}
	else if (stricmp(comname.c_str(),"delf")==0)
	{
		Json::Value reqData = jData["req"];
		string req = reqData.toStyledString();//转换成字符串
		// char * req=(char *)jData["req"].asString().c_str();
		char * rdata = delfp((char *)req.c_str());
		// 写日志
		strcat(dellog, rdata);
		write_log_file(dellog);

		Json::Value root;
		Json::Value res;
		root["cmd"] = comname;
		root["res"] = rdata;
		root.toStyledString();
		std::string rtnOut = root.toStyledString();
		string rtn = replaceChar(rtnOut,"\\n","");
		rtn = replaceChar(rtn,"\n","");
		out_write(rtn.c_str());	
	}
	else
	{
		char msg[] = "command failure, exit";
		write_log_file(msg);
		exit(0);
	}
	out_write("exit program");	
    return 0;
}

// 读取标准输入的数据
char * get_input()
{
    int len = 0; // 从接收字符串中前4个字节读取的数据长度
    char log[MAX_LEN]; // 日志
    char log_len[8]; // 日值输出读取字符串的长度，临时值。
    int i; // 循环初始值
    int l;
    // 读取数据流长度
    for (i = 0; i < 4; i++){
        //l = char2hex(getchar());
        l = getchar();
        len += l<<(i*4);
    }
    // 失败返回
    if(len == 0){
        write_log_file("read error,len is 0");
        out_write("read error,len is 0");
        exit(0);
    }

    strcpy(log, "read len is ");
    sprintf(log_len, "%ld", (long)len);
    strcat(log, log_len);
    write_log_file(log);
    // 读取数据流内容
    if(fgets(g_input, len + 1, stdin) != NULL)
    {
        write_log_file(g_input);
        return g_input;
    }
    write_log_file("read content error");
    out_write("read content error");
    return NULL;
}

// 输出
int out_write(const char * message)
{
    unsigned int len; // 将要输出的字符串的长度
    char protocol_len[4]; // 按照交互协调协议转换后，协议中的输出字符串长度
    char out_all[1024]; // 输出的所有字符串
    char log_len[4]; // 日志中输出的字符串长度
    char log[MAX_LEN]; // 日志内容
    int i;

    if(message == NULL){
        // 输出消息为空
        write_log_file("out_write NULL");
        return 1;
    }
	write_log_file(message);
    len = strlen(message);
//    printf("%zu",len);
    if(len == 0){
        // 长度为空
        write_log_file("out_write len is 0");
        return 1;
    }

    strcpy(log, "out_write content len is ");
    sprintf(log_len, "%ld", (long)len );
    strcat(log, log_len);
    write_log_file(log);
/*
    for(i = 0; i < 4; i++){
        protocol_len[i] = (char)((len >> (i * 8)) & 0xFF);
    }
    strcpy(out_all,protocol_len);
    arr_cat(out_all,4,message, len);
    fwrite(out_all, sizeof(char),  4+len,  stdout);
*/
  
    // We need to send the 4 bytes of length information
    printf("%c%c%c%c", (char) (len & 0xff),
           (char) ((len>>8) & 0xFF),
           (char) ((len>>16) & 0xFF),
           (char) ((len>>24) & 0xFF));
    // Now we can output our message
	fwrite(message, sizeof(char),  len,  stdout);
    //printf("%s", message);
    fflush(stdout);
    write_log_file(message);
    return 0;
    // 刷新输出缓冲区，以确保消息立即被发送
}

int write_log_file(const char * str){
    FILE *file = fopen("data.txt", "a+");
	if(file == NULL){
       // exit(1);
    }
	//if(file.isope
    unsigned int len = strlen(str);
    unsigned int wlen = fwrite(str, sizeof(char), len, file);
	fputc('\n', file);
    if(len != wlen){
        //exit(1);
    }
    
    fclose(file);
    return 0;
}
// 拼接数组
int arr_cat(char * dest, unsigned int from, const char * src, unsigned int len){
    int i = 0;
    dest = dest + from;
    while(i < len){
        *dest = *src;
        dest++;
        src++;
        i++;
    }
    return i;
}

string replaceChar(string src, string sub, string dest)
{
	while(1){
		unsigned int len = src.length();
		int idx = src.find(sub);
		if(idx == -1)
		{
			return src;
		}
		unsigned int slen = sub.length();
		src = src.replace(idx, slen, dest);
	}
	return NULL;
}

int handle_EOF(char *dest, char *src)
{
	char arr[MAX_LEN];
	int idx = 0;
	int i = 0;
	while(i < MAX_LEN)
	{
		if(*src != '\0'){
			arr[idx] = *src;
			idx++;
		}
		src++;
		i++;
	}
	arr[idx + 1] = '\0';
	dest = arr;
	return 0;
}

```

#### 解释
- 这段代码是一个使用C++编写的Windows控制台应用程序，使用了Windows API和一个处理JSON数据的库（jsoncpp）。让我们逐个部分进行解释
    1. 包含和定义：
    代码包含了标准的C/C++库（stdio.h、string.h等）、Windows特定的头文件（windows.h）以及JSON库的头文件（json/json.h）。
    还有一些#define语句定义了常量，如MAX_LEN。
    2. 函数原型：
    使用typedef声明了各种Windows API函数的原型。
    3. 全局变量：
    g_input是一个全局数组，用于存储输入数据。
    WLinst是一个全局变量，用于保存已加载DLL的句柄。
    4. 函数实现：
    main():
    这是程序的入口点。
    将输入模式设置为二进制。
    使用LoadLibrary加载了一个DLL（natcud.dll）。
    解析JSON输入，根据接收到的命令执行相应的函数，并生成适当的响应。
    写入日志并发送响应到标准输出。
    5. 实用函数：

    get_input()：从标准输入读取输入数据，解析长度信息，然后相应地读取数据。
    out_write()：将输出数据写入标准输出，并附带长度信息。
    write_log_file()：将日志追加到名为“data.txt”的文件中。
    arr_cat()：连接数组。
    replaceChar()：替换字符串中的字符。
    handle_EOF()：处理文件结束符（在代码中未使用）。
    6. 总结：
    业务逻辑：
    程序以JSON格式接收命令，并执行从动态加载的DLL（natcud.dll）中相应的函数。
    命令包括连接、收集序列号、收集身份证号码、收集指纹、验证和删除指纹等操作。
    7. 技术细节：
    使用了Windows特定的函数和数据类型来进行输入/输出操作和动态加载库。
    使用jsoncpp库进行JSON解析。
    输入和输出以二进制模式处理。
    日志写入到名为“data.txt”的文件中。
    8. 改进的方向：
    错误处理较为简单。可以实现更健壮的错误处理。
    应考虑内存管理，特别是动态分配的内存。
    代码结构可以改进，以提高可读性和可维护性。
    可以添加单元测试，以确保功能的正确性。


## 交互方式

1. 连接U盾
    -  
2. 采集SN

3. 采集身份证号

4. 采集指纹

5. 删除指纹

6. 安全认证 (比对)

7. 删除身份证号


## 遇到问题

1. C++ 解决报错无法打开源文件 #include <json/json.h>
    - 问题截图 
    ![json/json.h](/img/能源互联网/U盾/json.h头文件问题.png)
    - 解决步骤
        1. 安装vcpkg
            - git clone https://github.com/microsoft/vcpkg.git
            - cd vcpkg && bootstrap-vcpkg.bat
        2. 安装报错依赖包
        3. 在Visual Studio中添加刚才生成的目录