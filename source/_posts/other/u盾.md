---
title: U盾项目代码熟悉
category: C
tag: 熟悉项目
abbrlink: 61331
date: 2024-04-10 19:09:00
---
# 代码分析

## natchost-v1

### 原始代码
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

typedef char *(WINAPI *DELFPPROC)(char *a);

typedef char *(WINAPI *VERIFYPROC)(char *a);

// 从标准输入输出接收数据
char g_input[4096];

// 写日志函数
int write_log_file(const char *str);

// 读取标准输入的数据
char *get_input();

// 输出
int out_write(const char *message);

// 处理结束符
int handle_EOF(char *dest, char *src);

// 拼接数组
int arr_cat(char *dest, unsigned int from, const char *src, unsigned int len);

string replaceChar(string src, string sub, string dest);

HINSTANCE WLinst;

// 返回 0 为成功，其他值是失败
int main() {
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

    //char message [] = "{\"text\": \"hello chrome extension\"}"; // 例子
    //char message[] = "{\"command\": \"collFingerprint\", \"res\": {\"code\":\"0\",\"content\":\"collect fingerprint failure!\"}}";


    // 设置数据流模式模式为二进制流
    result = _setmode(_fileno(stdin), _O_BINARY);
    if (result == -1)
        out_write("Cannot set mode\n");

    WLinst = LoadLibrary("natcud.dll");
    if (WLinst == 0)
        return -1;

    CONNECTPROC ffconnect = (CONNECTPROC) GetProcAddress(WLinst, "ffconnect");
    COLLSNPROC collsn = (COLLSNPROC) GetProcAddress(WLinst, "collTerminalSN");
    COLLIDPROC collid = (COLLIDPROC) GetProcAddress(WLinst, "collIdNumber");
    COLLFPRPROC collfp = (COLLFPRPROC) GetProcAddress(WLinst, "collFingerPrint");
    DELFPPROC delfp = (DELFPPROC) GetProcAddress(WLinst, "deleteFingerprints");
    VERIFYPROC verify = (VERIFYPROC) GetProcAddress(WLinst, "verify");


    char *tmpcomm = get_input();
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
    reader.parse(command, jData);
    string comname = (char *) jData["cmd"].asString().c_str();
    strcat(commlog, comname.c_str());
    write_log_file(comname.c_str());

    if (stricmp(comname.c_str(), "connect") == 0) {

        char *rdata = ffconnect();
        // 写日志
        strcat(connlog, rdata);
        write_log_file(connlog);

        Json::Value root;
        Json::Value res;
        root["cmd"] = comname;
        root["res"] = rdata;
        root.toStyledString();
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());

    } else if (stricmp(comname.c_str(), "sn") == 0) {
        char *rdata = collsn();
        // 写日志
        strcat(snlog, rdata);
        write_log_file(snlog);
        Json::Value root;
        Json::Value res;
        root["cmd"] = comname;
        root["res"] = rdata;
        root.toStyledString();
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());

    } else if (stricmp(comname.c_str(), "i") == 0) {

// 读取 json 子结构
        Json::Value reqData = jData["req"];
//		string req=jData["command"].asString().c_str();
        string req = reqData.toStyledString();// 转换成字符串
        char *rdata = collid((char *) req.c_str());
        // 写日志
        strcat(idlog, rdata);
        write_log_file(idlog);

        Json::Value root;
        Json::Value res;
        root["cmd"] = comname;
        root["res"] = rdata;
        root.toStyledString();
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());

    } else if (stricmp(comname.c_str(), "f") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();// 转换成字符串
        //char * req=(char *).asString().c_str();
        char *rdata = collfp((char *) req.c_str());
        // 写日志
        strcat(fplog, rdata);
        write_log_file(fplog);

        Json::Value root;
        Json::Value res;
        root["cmd"] = comname;
        root["res"] = rdata;
        //	root.toStyledString();
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());

    } else if (stricmp(comname.c_str(), "verify") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();// 转换成字符串
        // char * req=(char *)jData["req"].asString().c_str();
        char *rdata = verify((char *) req.c_str());

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
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());

    } else if (stricmp(comname.c_str(), "delf") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();// 转换成字符串
        // char * req=(char *)jData["req"].asString().c_str();
        char *rdata = delfp((char *) req.c_str());
        // 写日志
        strcat(dellog, rdata);
        write_log_file(dellog);

        Json::Value root;
        Json::Value res;
        root["cmd"] = comname;
        root["res"] = rdata;
        root.toStyledString();
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else {
        char msg[] = "command failure, exit";
        write_log_file(msg);
        exit(0);
    }
    out_write("exit program");
    return 0;
}

// 读取标准输入的数据
char *get_input() {
    int len = 0; // 从接收字符串中前 4 个字节读取的数据长度
    char log[MAX_LEN]; // 日志
    char log_len[8]; // 日值输出读取字符串的长度，临时值。
    int i; // 循环初始值
    int l;
    // 读取数据流长度
    for (i = 0; i < 4; i++) {
        //l = char2hex(getchar());
        l = getchar();
        len += l << (i * 4);
    }
    // 失败返回
    if (len == 0) {
        write_log_file("read error,len is 0");
        out_write("read error,len is 0");
        exit(0);
    }

    strcpy(log, "read len is ");
    sprintf(log_len, "%ld", (long) len);
    strcat(log, log_len);
    write_log_file(log);
    // 读取数据流内容
    if (fgets(g_input, len + 1, stdin) != NULL) {
        write_log_file(g_input);
        return g_input;
    }
    write_log_file("read content error");
    out_write("read content error");
    return NULL;
}

// 输出
int out_write(const char *message) {
    unsigned int len; // 将要输出的字符串的长度
    char protocol_len[4]; // 按照交互协调协议转换后，协议中的输出字符串长度
    char out_all[1024]; // 输出的所有字符串
    char log_len[4]; // 日志中输出的字符串长度
    char log[MAX_LEN]; // 日志内容
    int i;

    if (message == NULL) {
        // 输出消息为空
        write_log_file("out_write NULL");
        return 1;
    }
    write_log_file(message);
    len = strlen(message);
//    printf("%zu",len);
    if (len == 0) {
        // 长度为空
        write_log_file("out_write len is 0");
        return 1;
    }

    strcpy(log, "out_write content len is ");
    sprintf(log_len, "%ld", (long) len);
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
           (char) ((len >> 8) & 0xFF),
           (char) ((len >> 16) & 0xFF),
           (char) ((len >> 24) & 0xFF));
    // Now we can output our message
    fwrite(message, sizeof(char), len, stdout);
    //printf("%s", message);
    fflush(stdout);
    write_log_file(message);
    return 0;
    // 刷新输出缓冲区，以确保消息立即被发送
}

int write_log_file(const char *str) {
    FILE *file = fopen("data.txt", "a+");
    if (file == NULL) {
        // exit(1);
    }
    //if(file.isope
    unsigned int len = strlen(str);
    unsigned int wlen = fwrite(str, sizeof(char), len, file);
    fputc('\n', file);
    if (len != wlen) {
        //exit(1);
    }

    fclose(file);
    return 0;
}

// 拼接数组
int arr_cat(char *dest, unsigned int from, const char *src, unsigned int len) {
    int i = 0;
    dest = dest + from;
    while (i < len) {
        *dest = *src;
        dest++;
        src++;
        i++;
    }
    return i;
}

string replaceChar(string src, string sub, string dest) {
    while (1) {
        unsigned int len = src.length();
        int idx = src.find(sub);
        if (idx == -1) {
            return src;
        }
        unsigned int slen = sub.length();
        src = src.replace(idx, slen, dest);
    }
    return NULL;
}

int handle_EOF(char *dest, char *src) {
    char arr[MAX_LEN];
    int idx = 0;
    int i = 0;
    while (i < MAX_LEN) {
        if (*src != '\0') {
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

- 这段代码看起来是一个用于与 USB key 进行通信的程序。我来解释一下每个方法的作用和整体的功能：

1. get_input(): 这个函数负责从标准输入读取数据。它首先读取前四个字节以确定接收到的数据的长度，然后根据长度读取实际的数据内容。
2. out_write(const char *message): 这个函数用于向标准输出写入数据。它将输入的消息长度转换为四个字节的格式，并将消息内容输出到标准输出流中。
3. write_log_file(const char *str): 这个函数用于将字符串写入日志文件 "data.txt" 中。它打开一个文件句柄，将字符串写入文件中，并在每个字符串后添加换行符。
4. arr_cat(char *dest, unsigned int from, const char *src, unsigned int len): 这个函数用于将源数组的内容连接到目标数组的末尾。它接受目标数组的指针、起始位置、源数组的指针以及源数组的长度作为参数，并将源数组的内容复制到目标数组的末尾。
5. replaceChar(string src, string sub, string dest): 这个函数用于在字符串中替换子字符串。它接受源字符串、要替换的子字符串以及替换后的字符串作为参数，并在源字符串中将所有匹配的子字符串替换为指定的字符串。
6. handle_EOF(char *dest, char *src): 这个函数似乎有些问题，因为它试图将源数组的内容复制到目标数组中。然而，它使用了一个局部数组 arr，并将其指针赋给了目标数组，这意味着在函数返回后，arr 将超出作用域并丢失其内容。因此，这个函数可能无法正确工作。
- 整体来说，这段代码是一个基于命令的 USB key 通信程序。它从标准输入读取命令，解析命令并调用相应的函数与 USB key 进行通信，然后将通信结果写入标准输出，并将相关信息记录在日志文件中。



### 整理后的代码
```cpp
#include "stdafx.h"
#include "windows.h"
#include <stdio.h>
#include <fcntl.h>
#include <io.h>
#include <string.h>
#include <json/json.h>
using namespace std;

#define MAX_LEN 4096

typedef char* (WINAPI* CONNECTPROC)();
typedef char* (WINAPI* COLLSNPROC)();
typedef char* (WINAPI* COLLIDPROC)(char* a);
typedef char* (WINAPI* COLLFPRPROC)(char* a);
typedef char* (WINAPI* DELFPPROC)(char* a);
typedef char* (WINAPI* VERIFYPROC)(char* a);

// Function prototypes
char* get_input();
int out_write(const char* message);
int write_log_file(const char* str);
int arr_cat(char* dest, unsigned int from, const char* src, unsigned int len);
string replaceChar(string src, string sub, string dest);
int handle_EOF(char* dest, char* src);

// Global variables
char g_input[MAX_LEN];
HINSTANCE WLinst;

int main() {
    // Variables for logging
    char reclog[] = "Received command: ";
    char commlog[] = "Parsed command: ";
    char castlog[] = "Casted command: ";
    char connlog[] = "USB key connect response: ";
    char snlog[] = "USB key SN response: ";
    char idlog[] = "USB key ID number response: ";
    char fplog[] = "USB key fingerprint response: ";
    char dellog[] = "USB key delete fingerprint response: ";
    char verifylog[] = "USB key verify response: ";

    // Set input/output mode to binary
    int result = _setmode(_fileno(stdin), _O_BINARY);
    if (result == -1)
        out_write("Cannot set mode\n");

    // Load library
    WLinst = LoadLibrary("natcud.dll");
    if (WLinst == 0)
        return -1;

    // Function pointers
    CONNECTPROC ffconnect = (CONNECTPROC)GetProcAddress(WLinst, "ffconnect");
    COLLSNPROC collsn = (COLLSNPROC)GetProcAddress(WLinst, "collTerminalSN");
    COLLIDPROC collid = (COLLIDPROC)GetProcAddress(WLinst, "collIdNumber");
    COLLFPRPROC collfp = (COLLFPRPROC)GetProcAddress(WLinst, "collFingerPrint");
    DELFPPROC delfp = (DELFPPROC)GetProcAddress(WLinst, "deleteFingerprints");
    VERIFYPROC verify = (VERIFYPROC)GetProcAddress(WLinst, "verify");

    // Get input
    char* tmpcomm = get_input();
    strcat(reclog, tmpcomm);
    write_log_file(reclog);

    // Parse command
    std::string command(tmpcomm);
    strcat(castlog, command.c_str());
    write_log_file(castlog);

    // Parse JSON command
    Json::Value jData;
    Json::Reader reader;
    reader.parse(command, jData);
    string comname = (char*)jData["cmd"].asString().c_str();
    strcat(commlog, comname.c_str());
    write_log_file(comname.c_str());

    // Execute command based on its type
    if (stricmp(comname.c_str(), "connect") == 0) {
        char* rdata = ffconnect();
        strcat(connlog, rdata);
        write_log_file(connlog);
        Json::Value root;
        root["cmd"] = comname;
        root["res"] = rdata;
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else if (stricmp(comname.c_str(), "sn") == 0) {
        char* rdata = collsn();
        strcat(snlog, rdata);
        write_log_file(snlog);
        Json::Value root;
        root["cmd"] = comname;
        root["res"] = rdata;
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else if (stricmp(comname.c_str(), "i") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = collid((char*)req.c_str());
        strcat(idlog, rdata);
        write_log_file(idlog);
        Json::Value root;
        root["cmd"] = comname;
        root["res"] = rdata;
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else if (stricmp(comname.c_str(), "f") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = collfp((char*)req.c_str());
        strcat(fplog, rdata);
        write_log_file(fplog);
        Json::Value root;
        root["cmd"] = comname;
        root["res"] = rdata;
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else if (stricmp(comname.c_str(), "verify") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = verify((char*)req.c_str());
        strcat(verifylog, rdata);
        write_log_file(verifylog);
        Json::Value root;
        root["cmd"] = comname;
        root["res"] = rdata;
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else if (stricmp(comname.c_str(), "delf") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = delfp((char*)req.c_str());
        strcat(dellog, rdata);
        write_log_file(dellog);
        Json::Value root;
        root["cmd"] = comname;
        root["res"] = rdata;
        std::string rtnOut = root.toStyledString();
        string rtn = replaceChar(rtnOut, "\\n", "");
        rtn = replaceChar(rtn, "\n", "");
        out_write(rtn.c_str());
    } else {
        char msg[] = "Command failure, exiting";
        write_log_file(msg);
        exit(0);
    }

    out_write("Exit program");
    return 0;
}

char* get_input() {
    int len = 0;
    char log[MAX_LEN];
    char log_len[8];
    int i;
    int l;
    // Read data stream length
    for (i = 0; i < 4; i++) {
        l = getchar();
        len += l << (i * 4);
    }
    // Fail if length is 0
    if (len == 0) {
        write_log_file("Read error, length is 0");
        out_write("Read error, length is 0");
        exit(0);
    }
    strcpy(log, "Read length is ");
    sprintf(log_len, "%ld", (long)len);
    strcat(log, log_len);
    write_log_file(log);
    // Read data stream content
    if (fgets(g_input, len + 1, stdin) != NULL) {
        write_log_file(g_input);
        return g_input;
    }
    write_log_file("Read content error");
    out_write("Read content error");
    return NULL;
}

int out_write(const char* message) {
    unsigned int len;
    char protocol_len[4];
    char out_all[1024];
    char log_len[4];
    char log[MAX_LEN];
    int i;
    if (message == NULL) {
        write_log_file("out_write NULL");
        return 1;
    }
    write_log_file(message);
    len = strlen(message);
    if (len == 0) {
        write_log_file("out_write length is 0");
        return 1;
    }
    strcpy(log, "out_write content length is ");
    sprintf(log_len, "%ld", (long)len);
    strcat(log, log_len);
    write_log_file(log);
    printf("%c%c%c%c", (char)(len & 0xff),
        (char)((len >> 8) & 0xFF),
        (char)((len >> 16) & 0xFF),
        (char)((len >> 24) & 0xFF));
    fwrite(message, sizeof(char), len, stdout);
    fflush(stdout);
    write_log_file(message);
    return 0;
}

int write_log_file(const char* str) {
    FILE* file = fopen("data.txt", "a+");
    if (file == NULL) {
        // exit(1);
    }
    unsigned int len = strlen(str);
    unsigned int wlen = fwrite(str, sizeof(char), len, file);
    fputc('\n', file);
    if (len != wlen) {
        //exit(1);
    }
    fclose(file);
    return 0;
}

int arr_cat(char* dest, unsigned int from, const char* src, unsigned int len) {
    int i = 0;
    dest = dest + from;
    while (i < len) {
        *dest = *src;
        dest++;
        src++;
        i++;
    }
    return i;
}

string replaceChar(string src, string sub, string dest) {
    while (1) {
        unsigned int len = src.length();
        int idx = src.find(sub);
        if (idx == -1) {
            return src;
        }
        unsigned int slen = sub.length();
        src = src.replace(idx, slen, dest);
    }
    return NULL;
}

int handle_EOF(char* dest, char* src) {
    char arr[MAX_LEN];
    int idx = 0;
    int i = 0;
    while (i < MAX_LEN) {
        if (*src != '\0') {
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

- 这段代码是一个与 USB key 进行通信的程序，主要负责从标准输入接收命令，解析命令并调用相应的函数与 USB key 进行交互，然后将结果输出到标准输出并记录日志。以下是每个方法的作用以及可能的优化点：

1. get_input(): 该方法用于从标准输入读取数据。它首先读取前四个字节以确定接收到的数据的长度，然后根据长度读取实际的数据内容。优化点：目前没有看到明显的优化点，但可以考虑增加错误处理机制，以应对无效输入或读取错误。
2. out_write(const char* message): 该方法用于向标准输出写入数据。它将输入的消息长度转换为四个字节的格式，并将消息内容输出到标准输出流中。优化点：可以考虑使用更有效率的方式将消息长度转换为四个字节的格式，例如使用位运算代替 sprintf。
3. write_log_file(const char* str): 该方法用于将字符串写入日志文件 "data.txt" 中。它打开一个文件句柄，将字符串写入文件中，并在每个字符串后添加换行符。优化点：可以考虑批量写入日志，而不是每次写入都打开和关闭文件句柄，以提高效率。
4. arr_cat(char* dest, unsigned int from, const char* src, unsigned int len): 该方法用于将源数组的内容连接到目标数组的末尾。它接受目标数组的指针、起始位置、源数组的指针以及源数组的长度作为参数，并将源数组的内容复制到目标数组的末尾。优化点：目前没有明显的优化点，因为这个函数的作用比较简单，已经比较高效。
5. replaceChar(string src, string sub, string dest): 该方法用于在字符串中替换子字符串。它接受源字符串、要替换的子字符串以及替换后的字符串作为参数，并在源字符串中将所有匹配的子字符串替换为指定的字符串。优化点：目前没有明显的优化点，但可以考虑使用更高效的字符串替换算法，例如 Boyer-Moore 算法。
6. handle_EOF(char* dest, char* src): 这个方法似乎有问题，因为它试图将源数组的内容复制到目标数组中。然而，它使用了一个局部数组 arr，并将其指针赋给了目标数组，这意味着在函数返回后，arr 将超出作用域并丢失其内容。因此，这个方法需要重写以正确工作。
- 至于优化的建议，主要集中在文件操作、内存管理和字符串处理等方面。可以通过减少文件操作次数、使用更有效的内存分配和释放方式以及优化字符串处理算法来提高程序的性能和健壮性。

#### 精简之后的代码
- 这个版本将日志记录的部分移除，只保留了与 USB key 通信相关的功能。

```cpp
#include "stdafx.h"
#include "windows.h"
#include <stdio.h>
#include <fcntl.h>
#include <io.h>
#include <json/json.h>
using namespace std;

#define MAX_LEN 4096

typedef char* (WINAPI* CONNECTPROC)();
typedef char* (WINAPI* COLLSNPROC)();
typedef char* (WINAPI* COLLIDPROC)(char* a);
typedef char* (WINAPI* COLLFPRPROC)(char* a);
typedef char* (WINAPI* DELFPPROC)(char* a);
typedef char* (WINAPI* VERIFYPROC)(char* a);

char g_input[MAX_LEN];
HINSTANCE WLinst;

char* get_input();
int out_write(const char* message);
int arr_cat(char* dest, unsigned int from, const char* src, unsigned int len);
string replaceChar(string src, string sub, string dest);
int handle_EOF(char* dest, char* src);

int main() {
    int result = _setmode(_fileno(stdin), _O_BINARY);
    if (result == -1)
        out_write("Cannot set mode\n");

    WLinst = LoadLibrary("natcud.dll");
    if (WLinst == 0)
        return -1;

    CONNECTPROC ffconnect = (CONNECTPROC)GetProcAddress(WLinst, "ffconnect");
    COLLSNPROC collsn = (COLLSNPROC)GetProcAddress(WLinst, "collTerminalSN");
    COLLIDPROC collid = (COLLIDPROC)GetProcAddress(WLinst, "collIdNumber");
    COLLFPRPROC collfp = (COLLFPRPROC)GetProcAddress(WLinst, "collFingerPrint");
    DELFPPROC delfp = (DELFPPROC)GetProcAddress(WLinst, "deleteFingerprints");
    VERIFYPROC verify = (VERIFYPROC)GetProcAddress(WLinst, "verify");

    char* tmpcomm = get_input();

    Json::Value jData;
    Json::Reader reader;
    reader.parse(tmpcomm, jData);
    string comname = (char*)jData["cmd"].asString().c_str();

    if (stricmp(comname.c_str(), "connect") == 0) {
        char* rdata = ffconnect();
        out_write(rdata);
    } else if (stricmp(comname.c_str(), "sn") == 0) {
        char* rdata = collsn();
        out_write(rdata);
    } else if (stricmp(comname.c_str(), "i") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = collid((char*)req.c_str());
        out_write(rdata);
    } else if (stricmp(comname.c_str(), "f") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = collfp((char*)req.c_str());
        out_write(rdata);
    } else if (stricmp(comname.c_str(), "verify") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = verify((char*)req.c_str());
        out_write(rdata);
    } else if (stricmp(comname.c_str(), "delf") == 0) {
        Json::Value reqData = jData["req"];
        string req = reqData.toStyledString();
        char* rdata = delfp((char*)req.c_str());
        out_write(rdata);
    } else {
        exit(0);
    }

    out_write("Exit program");
    return 0;
}

char* get_input() {
    int len = 0;
    for (int i = 0; i < 4; i++) {
        int l = getchar();
        len += l << (i * 4);
    }
    if (len == 0)
        exit(0);

    fgets(g_input, len + 1, stdin);
    return g_input;
}

int out_write(const char* message) {
    unsigned int len = strlen(message);
    printf("%c%c%c%c", (char)(len & 0xff),
        (char)((len >> 8) & 0xFF),
        (char)((len >> 16) & 0xFF),
        (char)((len >> 24) & 0xFF));
    fwrite(message, sizeof(char), len, stdout);
    fflush(stdout);
    return 0;
}

int arr_cat(char* dest, unsigned int from, const char* src, unsigned int len) {
    int i = 0;
    dest = dest + from;
    while (i < len) {
        *dest = *src;
        dest++;
        src++;
        i++;
    }
    return i;
}

string replaceChar(string src, string sub, string dest) {
    while (1) {
        unsigned int len = src.length();
        int idx = src.find(sub);
        if (idx == -1) {
            return src;
        }
        unsigned int slen = sub.length();
        src = src.replace(idx, slen, dest);
    }
    return NULL;
}

int handle_EOF(char* dest, char* src) {
    char arr[MAX_LEN];
    int idx = 0;
    int i = 0;
    while (i < MAX_LEN) {
        if (*src != '\0') {
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

- 这段代码是一个简单的与外部库进行交互的程序，它通过标准输入接收命令，解析命令后调用外部库的相应函数，并将结果输出到标准输出。以下是每个方法的作用以及可能的优化点：

1. get_input(): 该方法用于从标准输入读取数据。它首先读取前四个字节以确定接收到的数据的长度，然后根据长度读取实际的数据内容。优化点：可以增加错误处理机制，以应对无效输入或读取错误。
2. out_write(const char* message): 该方法用于向标准输出写入数据。它将输入的消息长度转换为四个字节的格式，并将消息内容输出到标准输出流中。优化点：可以考虑使用更有效率的方式将消息长度转换为四个字节的格式，例如使用位运算代替 sprintf。
3. arr_cat(char* dest, unsigned int from, const char* src, unsigned int len): 该方法用于将源数组的内容连接到目标数组的末尾。优化点：目前没有明显的优化点，因为这个函数的作用比较简单，已经比较高效。
4. replaceChar(string src, string sub, string dest): 该方法用于在字符串中替换子字符串。优化点：目前没有明显的优化点，但可以考虑使用更高效的字符串替换算法，例如 Boyer-Moore 算法。
5. handle_EOF(char* dest, char* src): 该方法似乎有问题，因为它试图将源数组的内容复制到目标数组中。然而，它使用了一个局部数组 arr，并将其指针赋给了目标数组，这意味着在函数返回后，arr 将超出作用域并丢失其内容。因此，这个方法需要重写以正确工作。
- 针对这段代码的改进方向：

1. 错误处理：增加对输入数据的错误处理，例如长度为零或无效数据。
2. 内存管理：检查所有的内存分配和释放，确保没有内存泄漏或悬空指针。
3. 效率优化：尽可能减少文件操作次数，例如批量写入日志文件，以及优化字符串处理算法的性能。
4. 代码清晰度：提高代码的可读性和可维护性，例如使用更有意义的变量名和添加注释来解释代码的功能和逻辑。

- 对于一个新手来说，可以逐步改进代码，首先着重处理错误情况，然后关注内存管理和效率优化，最后提高代码的可读性和清晰度。通过逐步改进，新手可以更好地理解代码，并逐渐掌握更高级的优化技巧和编程实践。


#### 提高代码清晰度
```cpp
#include "stdafx.h"
#include "windows.h"
#include <stdio.h>
#include <fcntl.h>
#include <io.h>
#include <json/json.h>
using namespace std;

#define MAX_INPUT_LEN 4096

// Function pointers for external library functions
typedef char* (WINAPI* CONNECTPROC)();
typedef char* (WINAPI* COLLSNPROC)();
typedef char* (WINAPI* COLLIDPROC)(char* a);
typedef char* (WINAPI* COLLFPRPROC)(char* a);
typedef char* (WINAPI* DELFPPROC)(char* a);
typedef char* (WINAPI* VERIFYPROC)(char* a);

// Global variables
char g_input[MAX_INPUT_LEN];
HINSTANCE libraryInstance;

// Function prototypes
char* getInput();
int writeToOutput(const char* message);

int main() {
    // Set input mode to binary
    int result = _setmode(_fileno(stdin), _O_BINARY);
    if (result == -1)
        writeToOutput("Cannot set input mode\n");

    // Load library
    libraryInstance = LoadLibrary("natcud.dll");
    if (libraryInstance == 0)
        return -1;

    // Function pointers for library functions
    CONNECTPROC connectFunction = (CONNECTPROC)GetProcAddress(libraryInstance, "ffconnect");
    COLLSNPROC collsnFunction = (COLLSNPROC)GetProcAddress(libraryInstance, "collTerminalSN");
    COLLIDPROC collidFunction = (COLLIDPROC)GetProcAddress(libraryInstance, "collIdNumber");
    COLLFPRPROC collfpFunction = (COLLFPRPROC)GetProcAddress(libraryInstance, "collFingerPrint");
    DELFPPROC delfpFunction = (DELFPPROC)GetProcAddress(libraryInstance, "deleteFingerprints");
    VERIFYPROC verifyFunction = (VERIFYPROC)GetProcAddress(libraryInstance, "verify");

    // Get input from stdin
    char* receivedInput = getInput();

    // Parse JSON input
    Json::Value jsonData;
    Json::Reader jsonReader;
    jsonReader.parse(receivedInput, jsonData);
    string commandName = jsonData["cmd"].asString();

    // Execute command based on its type
    if (stricmp(commandName.c_str(), "connect") == 0) {
        char* responseData = connectFunction();
        writeToOutput(responseData);
    } else if (stricmp(commandName.c_str(), "sn") == 0) {
        char* responseData = collsnFunction();
        writeToOutput(responseData);
    } else if (stricmp(commandName.c_str(), "i") == 0) {
        Json::Value requestData = jsonData["req"];
        string request = requestData.toStyledString();
        char* responseData = collidFunction((char*)request.c_str());
        writeToOutput(responseData);
    } else if (stricmp(commandName.c_str(), "f") == 0) {
        Json::Value requestData = jsonData["req"];
        string request = requestData.toStyledString();
        char* responseData = collfpFunction((char*)request.c_str());
        writeToOutput(responseData);
    } else if (stricmp(commandName.c_str(), "verify") == 0) {
        Json::Value requestData = jsonData["req"];
        string request = requestData.toStyledString();
        char* responseData = verifyFunction((char*)request.c_str());
        writeToOutput(responseData);
    } else if (stricmp(commandName.c_str(), "delf") == 0) {
        Json::Value requestData = jsonData["req"];
        string request = requestData.toStyledString();
        char* responseData = delfpFunction((char*)request.c_str());
        writeToOutput(responseData);
    } else {
        // Unknown command
        exit(0);
    }

    // Inform about program exit
    writeToOutput("Exit program");
    return 0;
}

// Read input from stdin
char* getInput() {
    int inputLength = 0;
    for (int i = 0; i < 4; i++) {
        int byte = getchar();
        inputLength += byte << (i * 4);
    }
    if (inputLength == 0)
        exit(0);

    fgets(g_input, inputLength + 1, stdin);
    return g_input;
}

// Write output to stdout
int writeToOutput(const char* message) {
    unsigned int messageLength = strlen(message);
    printf("%c%c%c%c", (char)(messageLength & 0xff),
        (char)((messageLength >> 8) & 0xFF),
        (char)((messageLength >> 16) & 0xFF),
        (char)((messageLength >> 24) & 0xFF));
    fwrite(message, sizeof(char), messageLength, stdout);
    fflush(stdout);
    return 0;
}

```
## demo1 
### 原始代码

```cpp
// demo1.cpp : Defines the initialization routines for the DLL.

#include "stdafx.h"
#include "demo1.h"
#include <json/json.h>

// YSIDObjBB.cpp : Implementation of CYSIDObjBB
#include "afxinet.h"
#include "usb_thread.h"
#include "TCPClient_FT.h"
#include "global.h"
#include "ZBase64.h"
#include "GSSocket.h"

using namespace std;
bool bExit;
char DataB[100];
Json::Value root;
std::string rtnOut;

CString SERVERIPARRAY[16];//ip组
CString SERVERIP = "";//实际连上的IP
UINT SERVERCOUNT = 0;
char mip[30];
char sID[30];

//CString SERVERIP ;
char CODEHEAD[4] = {'0', '1', 'T', 'Y'};
CWinThread *usbread_thread;
CWinThread *checkThread;

unsigned int DelayTime;
UINT nEndCode;
HINSTANCE WLinst;
typedef int(WINAPI
*GETBMPPROC)(
LPCSTR a,
int b
);
GETBMPPROC AAAGetBmp;
int bEncrypt;
extern char namebuf[30];
extern UINT ClientPort;

char AreaCode[8] = {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05};
char AreaCodeZ[][8] = {{0xa1, 0xb2, 0xc3, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa1, 0xb2, 0xc3, 0xd5, 0x00, 0x01, 0x06, 0x05},
                       {0xa1, 0xb2, 0xc3, 0xd5, 0x00, 0x02, 0x06, 0x05},
                       {0xa1, 0xb2, 0xc3, 0xd5, 0x00, 0x03, 0x06, 0x05},
                       {0xa1, 0xb2, 0xc3, 0xd5, 0x00, 0x04, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05},
                       {0xa3, 0xb2, 0xc1, 0xd5, 0x00, 0x00, 0x06, 0x05}
};

char ID[8] = {0x01, 0x02, 0x03, 0x05, 0x06, 0x00, 0x00, 0x01};
//	char Key[8]={0x2E,0x16,09,0xc0,0x79,0x3f,0x5f,0x00};
unsigned int key1[4] = {0x00, 0x00, 0x2E1609C7, 0x793F5F08};
unsigned int key2[4] = {0x00, 0x00, 0xB83D9A75, 0x53A8C40B};
unsigned int keys[4] = {0x00, 0xE76AB896, 0x00, 0x530C8D24};
//char minzu[58][10];
char minzu[][11] = {"未定义", "汉", "蒙古", "回", "藏", "维吾尔", "苗", "彝", "壮", "布依", "朝鲜", "满", "侗", "瑶", "白", "土家", "哈尼", "哈萨克",
                    "傣", "黎", "傈僳", "佤", "畲", "高山", "拉祜", "水", "东乡", "纳西", "景颇", "柯尔克孜", "土", "达斡尔", "仫佬", "羌", "布朗",
                    "撒拉", "毛南", "仡佬", "锡伯", "阿昌", "普米", "塔吉克", "怒", "乌孜别克", "俄罗斯", "鄂温克", "德昂", "保安", "裕固", "京", "塔塔尔",
                    "独龙", "鄂伦春", "赫哲", "门巴", "珞巴", "基诺", "其它"};

char gender[][7] = {"未知", "男", "女", "未说明"};

int TestConnect(char *ip) {
    WSADATA wsd;
    SOCKET cClient;
    int ret;
    struct sockaddr_in server;
    hostent *host = NULL;

    if (WSAStartup(MAKEWORD(2, 0), &wsd)) { return 0; }
    cClient = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    if (cClient == INVALID_SOCKET) { return 0; }
    //set Recv and Send time out
    DWORD TimeOut = 6000; //设置发送超时6秒
    if (::setsockopt(cClient, SOL_SOCKET, SO_SNDTIMEO, (char *) &TimeOut, sizeof(TimeOut)) == SOCKET_ERROR) {
        return 0;
    }
    TimeOut = 6000;//设置接收超时6秒
    if (::setsockopt(cClient, SOL_SOCKET, SO_RCVTIMEO, (char *) &TimeOut, sizeof(TimeOut)) == SOCKET_ERROR) {
        return 0;
    }
    //设置非阻塞方式连接
    unsigned long ul = 1;
    ret = ioctlsocket(cClient, FIONBIO, (unsigned long *) &ul);
    if (ret == SOCKET_ERROR)return 0;

    //连接
    server.sin_family = AF_INET;
    server.sin_port = htons(9007);
    server.sin_addr.s_addr = inet_addr(ip);
    //	    client.sin_addr.s_addr=inet_addr("192.168.1.106");
    if (server.sin_addr.s_addr == INADDR_NONE) { return 0; }

    connect(cClient, (const struct sockaddr *) &server, sizeof(server)); //立即返回


    struct timeval timeout;
    fd_set r;

    FD_ZERO(&r);
    FD_SET(cClient, &r);
    timeout.tv_sec = 1; //连接超时15秒
    timeout.tv_usec = 200000;
    ret = select(0, 0, &r, 0, &timeout);
    if (ret <= 0) {
        ::closesocket(cClient);

        return 0;
    } else {
        ::closesocket(cClient);

        return 1;
    }
}


void CleanFile() {
    CString strFileTitle;

    CFileFind finder;
    char curpath[256];

    GetModuleFileName(NULL, curpath, MAX_PATH);
    (_tcsrchr(curpath, _T('\\')))[1] = 0;//

    BOOL bWorking = finder.FindFile("P*.bmp");
    while (bWorking) {

        bWorking = finder.FindNextFile();
        strFileTitle = finder.GetFileName();

        CFile::Remove((LPCSTR) strFileTitle);

    }
    bWorking = finder.FindFile("P*.txt");
    while (bWorking) {

        bWorking = finder.FindNextFile();
        strFileTitle = finder.GetFileName();

        CFile::Remove((LPCSTR) strFileTitle);

    }

    bWorking = finder.FindFile("wz.txt");
    while (bWorking) {

        bWorking = finder.FindNextFile();
        strFileTitle = finder.GetFileName();

        CFile::Remove((LPCSTR) strFileTitle);

    }

}


int GetCheck() {
    srand(time(NULL));
    int check = rand() * 0xFFFF;

    return check;
}

WritePrivateProfileInt(   LPCTSTR
lpAppName,
LPCTSTR lpKeyName, INT
Value,
LPCTSTR lpFileName
)
{
TCHAR ValBuf[16];
sprintf(ValBuf, TEXT("%i"), Value
);
return(
WritePrivateProfileString(   lpAppName, lpKeyName, ValBuf, lpFileName
));
}


CString EncodeImage(char *jpgname) {//对图片进行Base64编码
    ZBase64 zBase;
    //图片编码
    CFile cbfBmp;
    cbfBmp.Open(jpgname, 0, 0);
    int iBmpSize = cbfBmp.GetLength();

    HGLOBAL hMemBmp = GlobalAlloc(GMEM_FIXED, iBmpSize);

    if (hMemBmp == NULL) return "";
    IStream *pStmBmp = NULL;

    CreateStreamOnHGlobal(hMemBmp, FALSE, &pStmBmp);

    BYTE *pbyBmp = (BYTE *) GlobalLock(hMemBmp);
    cbfBmp.SeekToBegin();
    cbfBmp.Read(pbyBmp, iBmpSize);

    string strTmpResult = zBase.Encode(pbyBmp, iBmpSize);

    CString result;
    result = strTmpResult.c_str();
    return result;
}


char *__stdcall ReadCard() {
    AFX_MANAGE_STATE(AfxGetStaticModuleState())

#ifndef _AFXDLL
#define _AFX_SOCK_THREAD_STATE AFX_MODULE_THREAD_STATE
#define _afxSockThreadState AfxGetModuleThreadState()

    _AFX_SOCK_THREAD_STATE *pState = _afxSockThreadState;
    if (pState->m_pmapSocketHandle == NULL)
        pState->m_pmapSocketHandle = new CMapPtrToPtr;
    if (pState->m_pmapDeadSockets == NULL)
        pState->m_pmapDeadSockets = new CMapPtrToPtr;
    if (pState->m_plistSocketNotifications == NULL)
        pState->m_plistSocketNotifications = new CPtrList;

#endif
    char *retString;

    if (ReadThreadFindHID()) {

    }
    retString = "net failure";

    return retString;


}

char *__stdcall ffconnect() {
    AFX_MANAGE_STATE(AfxGetStaticModuleState())

    char *retString;


    CString strResult;


    if (ReadThreadFindHID()) {
        root["code"] = "1";
        root["content"] = "conect device success";
    } else {
        root["code"] = "0";
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();

    return (char *) rtnOut.c_str();

}


char *__stdcall collTerminalSN() {
    write_log_file("collTerminalSN 1");
    AFX_MANAGE_STATE(AfxGetStaticModuleState())

    unsigned char code_data[60];
    memset(code_data, 0, 60);

    write_log_file("collTerminalSN 2");
    if (ReadThreadFindHID()) {
        write_log_file("collTerminalSN 3");
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            root["code"] = "0";
            root["content"] = "get SN failure";
            //root[""] =
        } else {
            write_log_file("collTerminalSN 4");
            memcpy(code_data, Data, 60);

            root["code"] = "1";
            char sn[9];
            memset(sn, 0, 9);
            memcpy(sn, code_data + 1, 8);
            root["content"] = sn;

            write_log_file("collTerminalSN 5");
        }
    } else {
        root["code"] = "0";
        root["content"] = "connect device failure";
    }
    write_log_file("collTerminalSN 6");
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str();
}

char *__stdcall collIdNumber(char *inData) {
    AFX_MANAGE_STATE(AfxGetStaticModuleState())
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);
    unsigned char code_data[60];
    memset(code_data, 0, 60);

    string inID = (char *) jData["i"].asString().c_str();
    string inSN = (char *) jData["s"].asString().c_str();
    if (ReadThreadFindHID()) {
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            root["code"] = "2";
            root["content"] = "get SN failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                root["code"] = "3";
                root["content"] = "SN not match";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            }

        }

        Data = DEL_ID();

        Data = ADD_ID((char *) inID.c_str());
        if (Data == NULL) {
            root["code"] = "0";
            root["content"] = "collect id number failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            if (code_data[0] == 0x00) {
                root["code"] = "0";
                root["content"] = "collect id number failure!";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            } else {
                root["code"] = "1";

                inSN.append("|");
                inSN.append(inID);
                root["content"] = inSN;

                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();

            }

        }
    } else {
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str();
}

char *__stdcall collFingerPrint(char *inData) {
    write_log_file("collFingerPrint 0"); // 写日志
    AFX_MANAGE_STATE(AfxGetStaticModuleState())
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);
    unsigned char code_data[60];
    memset(code_data, 0, 60);
    int index = -1;
    write_log_file("collFingerPrint 1"); // 写日志
    if (!(jData["f"].isNull())) {
        if (jData["f"].isString()) {
            string strindex = jData["f"].asString();
            if (!strindex.empty()) {
                index = atoi((char *) strindex.c_str());
            }

        } else {
            index = (int) jData["f"].asInt();
        }
    }
    write_log_file("collFingerPrint 2");
    string inSN = (char *) jData["s"].asString().c_str();
    if (ReadThreadFindHID()) {
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            root["code"] = "2";
            root["content"] = "get SN failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                root["code"] = "3";
                root["content"] = "SN not match";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            }

        }
        if (index != -1) {
            write_log_file("collFingerPrint 3 del");
            Data = FP_DEL(index, index);
        } else {
            index = 0;
        }

        Data = FP_REG(index);
        if (Data == NULL) {
            root["code"] = "0";
            root["content"] = "collect fingerprint failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            if (code_data[2] == 0x00) {
                root["code"] = "0";
                root["content"] = "collect fingerprint failure!";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            } else {
                root["code"] = "1";
                index = code_data[3] * 256 + code_data[4];
                char idx[4];
                itoa(index, idx, 10);
                inSN.append("|");
                inSN.append(idx);
                root["content"] = inSN;

                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            }

        }
    } else {
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str();
}

char *__stdcall deleteFingerprints(char *inData) {
    AFX_MANAGE_STATE(AfxGetStaticModuleState())
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);
    unsigned char code_data[60];
    memset(code_data, 0, 60);

    string inSN = (char *) jData["s"].asString().c_str();
    if (ReadThreadFindHID()) {
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            root["code"] = "2";
            root["content"] = "get SN failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                root["code"] = "3";
                root["content"] = "SN not match";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            }
        }

        Data = FP_DEL(1, 500);
        if (Data == NULL) {
            root["code"] = "0";
            root["content"] = "delete fingerprint failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {

            root["code"] = "1";
            //root["content"] = inSN;

            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        }
    } else {
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str();
}


char *__stdcall verify(char *inData) {
    AFX_MANAGE_STATE(AfxGetStaticModuleState())
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);
    unsigned char code_data[60];
    memset(code_data, 0, 60);
    //Json::Value root;
    int index;

    string inSN = (char *) jData["s"].asString().c_str();
    if (ReadThreadFindHID()) {
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            root["code"] = "2";
            root["content"] = "get SN failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                root["code"] = "3";
                root["content"] = "SN not match";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            }

        }

        Data = FP_CHECH();
        if (Data == NULL) {
            root["code"] = "0";
            root["content"] = "verify failure";
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str();
        } else {
            memcpy(code_data, Data, 60);
            if ((code_data[1] == 0x50) && (code_data[2] == 0x11) && (code_data[3] == 0x01)) //指紋成功
            {
                root["code"] = "6";

                char *tmpdata = (char *) code_data + 6;
                char tmp_code_data[19];
                memset(tmp_code_data, 0, 19);
                memcpy(tmp_code_data, tmpdata, 18);

                index = code_data[4] * 256 + code_data[5];
                char idx[4];
                itoa(index, idx, 10);

                inSN.append("|");;
                inSN.append(idx);

                root["content"] = inSN;

                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            } else if ((code_data[0] == 0x16) && (code_data[1] == 0x02) && (code_data[2] == 0x01))//身分証成功
            {
                root["code"] = "5";
                char *tmpdata = (char *) code_data + 3;
                char tmp_code_data[19];
                memset(tmp_code_data, 0, 19);
                memcpy(tmp_code_data, tmpdata, 18);

                inSN.append("|");
                inSN.append(tmp_code_data);
                root["content"] = inSN;

                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();

            } else//失敗
            {
                root["code"] = "0";
                root["content"] = "verify failure!";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            }

        }
    } else {
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str();
}


#ifdef _DEBUG
#define new DEBUG_NEW
#undef THIS_FILE
static char THIS_FILE[] = __FILE__;
#endif

BEGIN_MESSAGE_MAP(CDemo1App, CWinApp
)

END_MESSAGE_MAP()


CDemo1App::CDemo1App() {

}

CDemo1App theApp;


BOOL CDemo1App::InitInstance() {
    if (!AfxSocketInit()) {
        AfxMessageBox(IDP_SOCKETS_INIT_FAILED);
        return FALSE;
    }

    return TRUE;
}
```

### 代码结构

```cpp
// TestConnect: 测试连接到指定IP地址的特定端口。
int TestConnect(char *ip) {
    // ... 实现代码 ...
}

// CleanFile: 删除当前目录中特定类型的文件。
void CleanFile() {
    // ... 实现代码 ...
}

// GetCheck: 生成一个随机检查值。
int GetCheck() {
    // ... 实现代码 ...
}

// WritePrivateProfileInt: 将整数值写入INI文件。
WritePrivateProfileInt(LPCTSTR lpAppName, LPCTSTR lpKeyName, INT Value, LPCTSTR lpFileName) {
    // ... 实现代码 ...
}

// EncodeImage: 将图像文件编码为Base64格式。
CString EncodeImage(char *jpgname) {
    // ... 实现代码 ...
}

// ReadCard: 占位符函数，当前不执行任何操作。
char *__stdcall ReadCard() {
    // ... 实现代码 ...
}

// ffconnect: 占位符函数，当前不执行任何操作。
char *__stdcall ffconnect() {
    // ... 实现代码 ...
}

// collTerminalSN: 收集终端设备的序列号。
char *__stdcall collTerminalSN() {
    // ... 实现代码 ...
}

// collIdNumber: 收集与终端设备关联的身份证号码。
char *__stdcall collIdNumber(char *inData) {
    // ... 实现代码 ...
}

// collFingerPrint: 收集与终端设备关联的指纹。
char *__stdcall collFingerPrint(char *inData) {
    // ... 实现代码 ...
}

// deleteFingerprints: 从终端设备中删除存储的指纹。
char *__stdcall deleteFingerprints(char *inData) {
    // ... 实现代码 ...
}

// verify: 使用终端设备验证指纹或身份证号码。
char *__stdcall verify(char *inData) {
    // ... 实现代码 ...
}

// 应用程序的初始化。
BOOL CDemo1App::InitInstance() {
    // 初始化Winsock。
    if (!AfxSocketInit()) {
        AfxMessageBox(IDP_SOCKETS_INIT_FAILED);
        return FALSE;
    }

    return TRUE;
}
```
#### TestConnect
```cpp
/*
 * 这个函数的作用是测试连接到指定 IP 地址的特定端口是否成功
 * 如果连接成功则返回 1，否则返回 0
 */
int TestConnect(char *ip) {
    // 初始化 Winsock
    WSADATA wsd;
    if (WSAStartup(MAKEWORD(2, 0), &wsd)) { return 0; }

    // 创建套接字
    SOCKET cClient;
    cClient = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    if (cClient == INVALID_SOCKET) { return 0; }

    // 设置发送和接收超时时间为 6 秒
    DWORD TimeOut = 6000;
    if (::setsockopt(cClient, SOL_SOCKET, SO_SNDTIMEO, (char *) &TimeOut, sizeof(TimeOut)) == SOCKET_ERROR) {
        return 0;
    }
    TimeOut = 6000;
    if (::setsockopt(cClient, SOL_SOCKET, SO_RCVTIMEO, (char *) &TimeOut, sizeof(TimeOut)) == SOCKET_ERROR) {
        return 0;
    }

    // 设置非阻塞方式连接
    unsigned long ul = 1;
    int ret = ioctlsocket(cClient, FIONBIO, (unsigned long *) &ul);
    if (ret == SOCKET_ERROR) return 0;

    // 连接到指定 IP 地址和端口
    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(9007);
    server.sin_addr.s_addr = inet_addr(ip);
    if (server.sin_addr.s_addr == INADDR_NONE) { return 0; }
    connect(cClient, (const struct sockaddr *) &server, sizeof(server)); // 立即返回

    // 使用 select 函数检测连接是否建立
    struct timeval timeout;
    fd_set r;
    FD_ZERO(&r);
    FD_SET(cClient, &r);
    timeout.tv_sec = 1; // 连接超时 1 秒
    timeout.tv_usec = 200000;
    ret = select(0, 0, &r, 0, &timeout);
    if (ret <= 0) {
        ::closesocket(cClient); // 关闭套接字
        return 0; // 连接失败
    } else {
        ::closesocket(cClient); // 关闭套接字
        return 1; // 连接成功
    }
}

```
#### CleanFile
```cpp
/**
 * 函数的作用是清理指定目录下的特定文件
 * 该函数用于删除指定目录下以 "P*.bmp"、"P*.txt" 和 "wz.txt" 格式命名的文件
 */
void CleanFile() {
    CString strFileTitle; // 用于存储文件名的字符串

    CFileFind finder; // 文件查找器对象
    char curpath[256]; // 存储当前执行文件路径的字符数组

    // 获取当前执行文件的路径
    GetModuleFileName(NULL, curpath, MAX_PATH);
    (_tcsrchr(curpath, _T('\\')))[1] = 0; // 将路径最后的文件名部分截断

    // 查找并删除所有以 "P*.bmp" 格式命名的文件
    BOOL bWorking = finder.FindFile("P*.bmp");
    while (bWorking) {
        bWorking = finder.FindNextFile(); // 继续查找下一个匹配的文件
        strFileTitle = finder.GetFileName(); // 获取文件名
        CFile::Remove((LPCSTR) strFileTitle); // 删除文件
    }

    // 查找并删除所有以 "P*.txt" 格式命名的文件
    bWorking = finder.FindFile("P*.txt");
    while (bWorking) {
        bWorking = finder.FindNextFile(); // 继续查找下一个匹配的文件
        strFileTitle = finder.GetFileName(); // 获取文件名
        CFile::Remove((LPCSTR) strFileTitle); // 删除文件
    }

    // 查找并删除所有名为 "wz.txt" 的文件
    bWorking = finder.FindFile("wz.txt");
    while (bWorking) {
        bWorking = finder.FindNextFile(); // 继续查找下一个匹配的文件
        strFileTitle = finder.GetFileName(); // 获取文件名
        CFile::Remove((LPCSTR) strFileTitle); // 删除文件
    }
}
```

#### GetCheck
```cpp
/**
 * 这个函数的作用是生成一个用于校验的随机数
 * 该函数利用当前时间作为随机数的种子，通过调用 rand() 函数生成一个随机数，并将其乘以 0xFFFF 以增加其随机性，最后返回生成的随机数作为校验值
 * @return
 */

int GetCheck() {
    srand(time(NULL)); // 使用当前时间作为随机数种子，以确保每次生成的随机数都不同
    int check = rand() * 0xFFFF; // 生成一个随机数，并乘以 0xFFFF（十六进制中最大的两字节值），以增加随机性

    return check; // 返回生成的随机数作为校验值
}

```
#### WritePrivateProfileInt
```cpp WritePrivateProfileInt
/**
 * 这段代码定义了一个名为 WritePrivateProfileInt 的函数，用于向 INI 文件中写入整数值
 * 这个函数的参数包括要写入的 INI 文件的节名 (lpAppName)、键名 (lpKeyName)，以及要写入的整数值 (Value)。函数将整数值转换为字符串格式，
 * 并通过 WritePrivateProfileString 函数写入指定的 INI 文件中的相应部分
 */

WritePrivateProfileInt(LPCTSTR lpAppName, LPCTSTR lpKeyName, INT Value, LPCTSTR lpFileName)
{
TCHAR ValBuf[16]; // 用于存储整数值的字符缓冲区，大小为 16 字节
sprintf(ValBuf, TEXT("%i"), Value); // 将整数值转换为字符串格式，存储在 ValBuf 中

// 调用 WritePrivateProfileString 函数写入 INI 文件，并返回其结果
return WritePrivateProfileString(lpAppName, lpKeyName, ValBuf, lpFileName);
}
```

#### EncodeImage
```cpp
/**
 * 这个函数是用来对图片进行Base64编码的
 * 该函数的作用是接受一个图片文件的路径，然后对该图片进行Base64编码，并返回编码后的字符串。
 * @param jpgname
 * @return
 */

CString EncodeImage(char *jpgname) {// 对图片进行Base64编码的函数
    ZBase64 zBase; // 创建一个ZBase64对象，用于Base64编码
    // 打开图片文件
    CFile cbfBmp;
    cbfBmp.Open(jpgname, 0, 0);
    // 获取图片文件大小
    int iBmpSize = cbfBmp.GetLength();

    // 分配内存
    HGLOBAL hMemBmp = GlobalAlloc(GMEM_FIXED, iBmpSize);
    // 如果内存分配失败，返回空字符串
    if (hMemBmp == NULL) return "";
    // 创建内存流对象
    IStream *pStmBmp = NULL;
    CreateStreamOnHGlobal(hMemBmp, FALSE, &pStmBmp);

    // 锁定内存并读取图片数据
    BYTE *pbyBmp = (BYTE *) GlobalLock(hMemBmp);
    cbfBmp.SeekToBegin();
    cbfBmp.Read(pbyBmp, iBmpSize);

    // 使用ZBase64对象对图片数据进行Base64编码
    string strTmpResult = zBase.Encode(pbyBmp, iBmpSize);

    // 将Base64编码后的字符串转换为CString类型并返回
    CString result;
    result = strTmpResult.c_str();
    return result;
}
```

#### ReadCard
```cpp
/**
 * 这段代码似乎是用于读取身份证信息的，但是其中缺少了具体的读取身份证信息的逻辑
 * 这段代码中，ReadCard 函数似乎是用于读取身份证信息的，但其中的具体逻辑在 ReadThreadFindHID 函数内部实现。在当前的代码中，
 * 没有提供 ReadThreadFindHID 函数的实现，因此无法准确判断 ReadCard 函数的功能
 * @return
 */
char *__stdcall ReadCard() {
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

#ifndef _AFXDLL
#define _AFX_SOCK_THREAD_STATE AFX_MODULE_THREAD_STATE
#define _afxSockThreadState AfxGetModuleThreadState()

    _AFX_SOCK_THREAD_STATE *pState = _afxSockThreadState;
    // 检查线程状态，如果为空，则初始化一些Socket相关的数据结构
    if (pState->m_pmapSocketHandle == NULL)
        pState->m_pmapSocketHandle = new CMapPtrToPtr;
    if (pState->m_pmapDeadSockets == NULL)
        pState->m_pmapDeadSockets = new CMapPtrToPtr;
    if (pState->m_plistSocketNotifications == NULL)
        pState->m_plistSocketNotifications = new CPtrList;

#endif

    char *retString;

    // 检查是否找到了读取身份证信息的线程
    if (ReadThreadFindHID()) {
        // 如果找到了读取身份证信息的线程，则进行相关操作
    }

    // 如果未找到读取身份证信息的线程，则返回 "net failure" 字符串
    retString = "net failure";

    return retString;
}
```

#### ffconnect
```cpp
/**
 * 这段代码似乎是一个用于连接设备的函数，同时返回一个包含连接状态信息的 JSON 字符串
 * 在这个函数中，ReadThreadFindHID() 函数似乎用于检查是否成功找到了读取设备信息的线程。如果找到了，
 * 函数会返回一个包含成功连接信息的 JSON 字符串；如果未找到，函数会返回一个包含连接失败信息的 JSON 字符串。
 * @return
 */
char *__stdcall ffconnect() {
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

    char *retString; // 返回的字符串指针

    CString strResult; // 存储连接状态信息的字符串

    if (ReadThreadFindHID()) {
        // 如果找到了读取身份证信息的线程，则表示连接设备成功
        root["code"] = "1"; // 设定状态码为1，表示成功
        root["content"] = "conect device success"; // 设置内容为连接设备成功
    } else {
        // 如果未找到读取身份证信息的线程，则表示连接设备失败
        root["code"] = "0"; // 设定状态码为0，表示失败
        root["content"] = "connect device failure"; // 设置内容为连接设备失败
    }

    // 将 JSON 对象转换为格式化的字符串
    root.toStyledString();
    rtnOut = root.toStyledString();

    return (char *) rtnOut.c_str(); // 返回连接状态信息的字符串指针
}
```

#### collTerminalSN
```cpp
/**
 * 这段代码是一个用于收集终端序列号的函数。它似乎会尝试从设备中读取序列号，并返回一个包含序列号信息的 JSON 字符串
 * 在这个函数中，首先尝试找到读取设备信息的线程。如果成功找到，就尝试从设备中读取序列号数据，并根据读取结果返回相应的 JSON 字符串
 * @return
 */
char *__stdcall collTerminalSN() {
    write_log_file("collTerminalSN 1"); // 记录日志：开始收集终端序列号
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

    unsigned char code_data[60]; // 存储序列号数据的缓冲区
    memset(code_data, 0, 60); // 将缓冲区清零

    write_log_file("collTerminalSN 2"); // 记录日志：准备读取序列号
    if (ReadThreadFindHID()) { // 如果成功找到读取设备信息的线程
        write_log_file("collTerminalSN 3"); // 记录日志：找到读取设备信息的线程

        unsigned char *Data = GET_SN(); // 获取序列号数据
        if (Data == NULL) {
            // 如果未成功获取序列号数据
            root["code"] = "0"; // 设定状态码为0，表示失败
            root["content"] = "get SN failure"; // 设置内容为获取序列号失败
        } else {
            write_log_file("collTerminalSN 4"); // 记录日志：成功获取序列号数据

            // 将获取的序列号数据拷贝到缓冲区中
            memcpy(code_data, Data, 60);

            root["code"] = "1"; // 设定状态码为1，表示成功
            char sn[9]; // 存储序列号的数组
            memset(sn, 0, 9); // 清零序列号数组
            memcpy(sn, code_data + 1, 8); // 从序列号数据中提取序列号信息
            root["content"] = sn; // 将序列号信息设置为内容

            write_log_file("collTerminalSN 5"); // 记录日志：序列号处理完成
        }
    } else {
        // 如果未找到读取设备信息的线程
        root["code"] = "0"; // 设定状态码为0，表示失败
        root["content"] = "connect device failure"; // 设置内容为连接设备失败
    }
    write_log_file("collTerminalSN 6"); // 记录日志：序列号收集完成

    root.toStyledString(); // 将 JSON 对象转换为格式化的字符串
    rtnOut = root.toStyledString(); // 将格式化的字符串赋值给返回值

    return (char *) rtnOut.c_str(); // 返回序列号信息的字符串指针
}
```


#### collIdNumber
```cpp
/**
 * 这是一个用于收集身份证号码的函数。它接收一个 JSON 字符串作为输入，解析其中的身份证号码和设备序列号，并尝试将身份证号码写入设备中
 * 这个函数首先从输入的 JSON 字符串中解析身份证号码和设备序列号。然后它尝试从设备中获取序列号数据，并与输入的设备序列号进行匹配。
 * 如果匹配成功，它会尝试将身份证号码写入设备中。最后，它会根据操作的结果返回相应的 JSON 字符串
 * @param inData
 * @return
 */
char *__stdcall collIdNumber(char *inData) {
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

    // 解析输入的JSON数据
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);

    unsigned char code_data[60]; // 存储设备数据的缓冲区
    memset(code_data, 0, 60); // 将缓冲区清零

    // 从JSON中获取身份证号码和设备序列号
    string inID = (char *) jData["i"].asString().c_str(); // 身份证号码
    string inSN = (char *) jData["s"].asString().c_str(); // 设备序列号

    // 如果成功找到读取设备信息的线程
    if (ReadThreadFindHID()) {
        // 尝试从设备中获取序列号数据
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            // 如果未成功获取序列号数据，则返回错误信息
            root["code"] = "2"; // 设置状态码为2，表示获取序列号失败
            root["content"] = "get SN failure"; // 设置内容为获取序列号失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            // 如果成功获取序列号数据，则检查序列号是否匹配
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                // 如果序列号不匹配，则返回错误信息
                root["code"] = "3"; // 设置状态码为3，表示序列号不匹配
                root["content"] = "SN not match"; // 设置内容为序列号不匹配
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
            }
        }

        // 从设备中删除原有的身份证号码，并尝试写入新的身份证号码
        Data = DEL_ID();
        Data = ADD_ID((char *) inID.c_str());

        if (Data == NULL) {
            // 如果写入身份证号码失败，则返回错误信息
            root["code"] = "0"; // 设置状态码为0，表示写入失败
            root["content"] = "collect id number failure"; // 设置内容为写入身份证号码失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            // 如果写入身份证号码成功，则返回成功信息及新的身份证号码
            memcpy(code_data, Data, 60);
            if (code_data[0] == 0x00) {
                root["code"] = "0";
                root["content"] = "collect id number failure!";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            } else {
                root["code"] = "1"; // 设置状态码为1，表示写入成功
                // 将新的身份证号码和设备序列号拼接成字符串
                inSN.append("|");
                inSN.append(inID);
                root["content"] = inSN; // 设置内容为拼接后的字符串

                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回成功信息的字符串指针
            }
        }
    } else {
        // 如果未找到读取设备信息的线程，则返回连接设备失败的错误信息
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
}

```

#### collFingerPrint

```cpp
/**
 * 这段代码是一个用于收集指纹数据的函数
 * 这个函数的作用是收集指纹数据，并返回操作结果
 * @param inData
 * @return
 */
char *__stdcall collFingerPrint(char *inData) {
    write_log_file("collFingerPrint 0"); // 写入日志，表示函数开始执行
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

    // 解析输入的JSON数据
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);

    unsigned char code_data[60]; // 存储设备数据的缓冲区
    memset(code_data, 0, 60); // 将缓冲区清零
    int index = -1; // 指纹索引，默认为-1

    write_log_file("collFingerPrint 1"); // 写入日志，表示进入函数的第一步

    // 从输入的JSON中获取指纹索引
    if (!(jData["f"].isNull())) { // 如果JSON中包含"f"字段
        if (jData["f"].isString()) { // 如果"f"字段的值是字符串类型
            string strindex = jData["f"].asString(); // 将"f"字段的值转换为字符串
            if (!strindex.empty()) {
                index = atoi((char *) strindex.c_str()); // 将字符串转换为整数作为指纹索引
            }
        } else { // 如果"f"字段的值不是字符串类型
            index = (int) jData["f"].asInt(); // 直接获取整数作为指纹索引
        }
    }

    write_log_file("collFingerPrint 2"); // 写入日志，表示进入函数的第二步

    string inSN = (char *) jData["s"].asString().c_str(); // 获取设备序列号

    // 如果成功找到读取设备信息的线程
    if (ReadThreadFindHID()) {
        // 尝试从设备中获取序列号数据
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            // 如果未成功获取序列号数据，则返回错误信息
            root["code"] = "2"; // 设置状态码为2，表示获取序列号失败
            root["content"] = "get SN failure"; // 设置内容为获取序列号失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            // 如果成功获取序列号数据，则检查序列号是否匹配
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                // 如果序列号不匹配，则返回错误信息
                root["code"] = "3"; // 设置状态码为3，表示序列号不匹配
                root["content"] = "SN not match"; // 设置内容为序列号不匹配
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
            }
        }

        // 如果指纹索引不为-1，则删除指定索引的指纹数据
        if (index != -1) {
            write_log_file("collFingerPrint 3 del"); // 写入日志，表示删除指纹数据
            Data = FP_DEL(index, index);
        } else {
            index = 0;
        }

        // 注册新的指纹数据
        Data = FP_REG(index);
        if (Data == NULL) {
            // 如果注册指纹失败，则返回错误信息
            root["code"] = "0"; // 设置状态码为0，表示注册失败
            root["content"] = "collect fingerprint failure"; // 设置内容为注册指纹失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            // 如果注册指纹成功，则返回成功信息及指纹索引
            memcpy(code_data, Data, 60);
            if (code_data[2] == 0x00) {
                root["code"] = "0";
                root["content"] = "collect fingerprint failure!";
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str();
            } else {
                root["code"] = "1"; // 设置状态码为1，表示注册成功
                index = code_data[3] * 256 + code_data[4]; // 获取指纹索引
                char idx[4];
                itoa(index, idx, 10);
                inSN.append("|");
                inSN.append(idx); // 将指纹索引拼接到设备序列号后面
                root["content"] = inSN;

                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回成功信息的字符串指针
            }
        }
    } else {
        // 如果未找到读取设备信息的线程，则返回连接设备失败的错误信息
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
}
```

#### deleteFingerprints

```cpp
/**
 * 这段代码是一个用于删除设备中指纹数据的函数
 * 这个函数的作用是接收一个包含设备序列号的JSON数据，并尝试从设备中获取序列号数据。
 * 如果成功获取序列号数据并且与输入的序列号匹配，则尝试删除设备中的指纹数据。
 * 如果删除成功，则返回成功信息；
 * 如果删除失败，则返回失败信息。
 * 如果无法找到读取设备信息的线程，则返回连接设备失败的错误信息。

代码中使用了MFC模块来管理状态，并且依赖了JSON库来解析JSON数据。
 在函数中，还使用了一些未定义的函数 ReadThreadFindHID()、GET_SN() 和 FP_DEL()，
 这些函数可能是外部定义的，用于设备操作和数据处理。

总的来说，这个函数的作用是在设备中删除指定范围内的指纹数据，并根据操作结果返回相应的状态信息
 * @param inData
 * @return
 */

char *__stdcall deleteFingerprints(char *inData) {
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

    // 解析输入的JSON数据
    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);

    unsigned char code_data[60]; // 存储设备数据的缓冲区
    memset(code_data, 0, 60); // 将缓冲区清零

    string inSN = (char *) jData["s"].asString().c_str(); // 获取设备序列号

    // 如果成功找到读取设备信息的线程
    if (ReadThreadFindHID()) {
        // 尝试从设备中获取序列号数据
        unsigned char *Data = GET_SN();
        if (Data == NULL) {
            // 如果未成功获取序列号数据，则返回错误信息
            root["code"] = "2"; // 设置状态码为2，表示获取序列号失败
            root["content"] = "get SN failure"; // 设置内容为获取序列号失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            // 如果成功获取序列号数据，则检查序列号是否匹配
            memcpy(code_data, Data, 60);
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8);
            if (ret != 0) {
                // 如果序列号不匹配，则返回错误信息
                root["code"] = "3"; // 设置状态码为3，表示序列号不匹配
                root["content"] = "SN not match"; // 设置内容为序列号不匹配
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
            }
        }

        // 删除设备中的指纹数据
        Data = FP_DEL(1, 500); // 删除指定范围内的指纹数据
        if (Data == NULL) {
            // 如果删除指纹数据失败，则返回错误信息
            root["code"] = "0"; // 设置状态码为0，表示删除失败
            root["content"] = "delete fingerprint failure"; // 设置内容为删除指纹失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            // 如果成功删除指纹数据，则返回成功信息
            root["code"] = "1"; // 设置状态码为1，表示删除成功
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回成功信息的字符串指针
        }
    } else {
        // 如果未找到读取设备信息的线程，则返回连接设备失败的错误信息
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
}
```
#### verify

```cpp
/**
 * 这段代码是一个用于验证身份信息的函数
 * 这个函数的作用是验证设备中的指纹或身份证信息，并返回验证结果。
 * 函数首先从输入的JSON数据中获取设备序列号，并与设备中的序列号进行比对。
 * 然后尝试验证指纹或身份证信息，并根据验证结果返回相应的状态码和内容。

需要注意的是，函数中使用了一些未定义的函数 ReadThreadFindHID()、GET_SN() 和 FP_CHECH()，
 这些函数可能是外部定义的，用于设备操作和数据处理。
 * @param inData
 * @return
 */
char *__stdcall verify(char *inData) {
    AFX_MANAGE_STATE(AfxGetStaticModuleState()) // 管理MFC模块状态

    Json::Value jData;
    Json::Reader reader;
    string sinData = inData;
    reader.parse(sinData, jData);

    unsigned char code_data[60]; // 存储设备数据的缓冲区
    memset(code_data, 0, 60); // 将缓冲区清零

    int index; // 用于存储索引值

    string inSN = (char *) jData["s"].asString().c_str(); // 获取设备序列号

    // 如果成功找到读取设备信息的线程
    if (ReadThreadFindHID()) {
        unsigned char *Data = GET_SN(); // 尝试从设备中获取序列号数据
        if (Data == NULL) {
            // 如果未成功获取序列号数据，则返回错误信息
            root["code"] = "2"; // 设置状态码为2，表示获取序列号失败
            root["content"] = "get SN failure"; // 设置内容为获取序列号失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            memcpy(code_data, Data, 60); // 复制设备数据到缓冲区中
            int ret = strncmp(inSN.c_str(), (char *) code_data + 1, 8); // 检查序列号是否匹配
            if (ret != 0) {
                // 如果序列号不匹配，则返回错误信息
                root["code"] = "3"; // 设置状态码为3，表示序列号不匹配
                root["content"] = "SN not match"; // 设置内容为序列号不匹配
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
            }
        }

        // 尝试验证指纹或身份证信息
        Data = FP_CHECH(); // 获取设备验证结果数据
        if (Data == NULL) {
            // 如果验证失败，则返回错误信息
            root["code"] = "0"; // 设置状态码为0，表示验证失败
            root["content"] = "verify failure"; // 设置内容为验证失败
            root.toStyledString();
            rtnOut = root.toStyledString();
            return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
        } else {
            memcpy(code_data, Data, 60); // 复制设备数据到缓冲区中
            if ((code_data[1] == 0x50) && (code_data[2] == 0x11) && (code_data[3] == 0x01)) {
                // 如果指纹验证成功
                root["code"] = "6"; // 设置状态码为6，表示指纹验证成功

                // 获取指纹索引和数据
                char *tmpdata = (char *) code_data + 6;
                char tmp_code_data[19];
                memset(tmp_code_data, 0, 19);
                memcpy(tmp_code_data, tmpdata, 18);

                index = code_data[4] * 256 + code_data[5]; // 计算索引值
                char idx[4];
                itoa(index, idx, 10); // 将索引值转换为字符串

                inSN.append("|");
                inSN.append(idx); // 将索引值添加到设备序列号后面

                root["content"] = inSN; // 设置内容为设备序列号和指纹索引
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回成功信息的字符串指针
            } else if ((code_data[0] == 0x16) && (code_data[1] == 0x02) && (code_data[2] == 0x01)) {
                // 如果身份证验证成功
                root["code"] = "5"; // 设置状态码为5，表示身份证验证成功

                // 获取身份证数据
                char *tmpdata = (char *) code_data + 3;
                char tmp_code_data[19];
                memset(tmp_code_data, 0, 19);
                memcpy(tmp_code_data, tmpdata, 18);

                inSN.append("|");
                inSN.append(tmp_code_data); // 将身份证数据添加到设备序列号后面

                root["content"] = inSN; // 设置内容为设备序列号和身份证数据
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回成功信息的字符串指针
            } else {
                // 如果验证失败，则返回错误信息
                root["code"] = "0"; // 设置状态码为0，表示验证失败
                root["content"] = "verify failure!"; // 设置内容为验证失败
                root.toStyledString();
                rtnOut = root.toStyledString();
                return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
            }
        }
    } else {
        // 如果未找到读取设备信息的线程，则返回连接设备失败的错误信息
        root["code"] = 0;
        root["content"] = "connect device failure";
    }
    root.toStyledString();
    rtnOut = root.toStyledString();
    return (char *) rtnOut.c_str(); // 返回错误信息的字符串指针
}
```

#### InitInstance

```cpp
/**
 *  MFC 应用程序的初始化函数 InitInstance()
 */
#ifdef _DEBUG
/*  这是一个常见的技巧，用于在调试模式下跟踪内存泄漏。
    在 MFC 应用程序中，DEBUG_NEW 是一个宏，用于替换默认的 new 操作符，
    从而跟踪动态分配的内存，并在程序退出时报告未释放的内存。通过定义 new DEBUG_NEW，可以在调试模式下使用这个特性。
*/
#define new DEBUG_NEW
/**
 * 这是 MFC 的一种用法，用于跟踪文件名。__FILE__ 是一个预定义的宏，它展开为当前源文件的文件名。
 * 通过定义 THIS_FILE 字符数组，并将其设置为 __FILE__ 的值，可以在调试时轻松地查看代码所在的文件。
 */
#undef THIS_FILE
static char THIS_FILE[] = __FILE__;
#endif

// CDemo1App 应用程序类的初始化函数
BOOL CDemo1App::InitInstance() {
    // 初始化 MFC 套接字支持
    if (!AfxSocketInit()) {
        // 如果套接字初始化失败，弹出错误消息框
        AfxMessageBox(IDP_SOCKETS_INIT_FAILED);
        return FALSE; // 返回初始化失败
    }

    return TRUE; // 返回初始化成功
}
```

## usb_thread

### 原始代码

```cpp

#include <stdafx.h>
#include "usb_thread.h"
#include "global.h"
//#include "GSIDClientDlg.h"
#include "idcardbin.h"

DWORD								ActualBytesRead;
DWORD								BytesRead;
HIDP_CAPS							Capabilities;
DWORD								cbBytesRead;
PSP_DEVICE_INTERFACE_DETAIL_DATA	detailData;
HANDLE								DeviceHandle;
DWORD								dwError;
char								FeatureReport[256];
HANDLE								hEventObject;
HANDLE								hDevInfo;
GUID								HidGuid;
OVERLAPPED							HIDOverlapped;
unsigned char						InputReport[256];
ULONG								Length;
LPOVERLAPPED						lpOverLap;
bool								MyDeviceDetected = FALSE;
CString								MyDevicePathName;
DWORD								NumberOfBytesRead;
unsigned char						OutputReport[256];
HANDLE								ReadHandle;
DWORD								ReportType;
ULONG								Required;
CString								ValueToDisplay;
HANDLE								WriteHandle;


CString my_usb_detail;
int VendorID=0x9573;
int ProductID= 0x4850;


static DWORD start_time=0;
static DWORD end_time=0;
static temp_time=0;

unsigned char sndbuf[100][65];
int bufnumb=0;
int seq=1;
int circlenumb=0;

char p390f88[60];
char p670b1[60];
char p670b2[60];
char p390f82[60];
char namebuf[30];

extern	UINT nEndCode;


static unsigned short UpdateCrc(unsigned char ch, unsigned short *lpwCrc)
{
    ch = (ch^(unsigned char)((*lpwCrc) & 0x00FF));
    ch = (ch^(ch<<4));
    *lpwCrc = (*lpwCrc >> 8)^((unsigned short)ch << 8)^
              ((unsigned short)ch<<3)^((unsigned short)ch>>4);
    return(*lpwCrc);
}
static void ComputeCrc(unsigned char *Data,unsigned short Length,
                       unsigned char *TransmitFirst, unsigned char *TransmitSecond)
{
    unsigned char chBlock;
    unsigned short wCrc = 0x6363;
    do {
        chBlock = *Data++;
        UpdateCrc(chBlock, &wCrc);
    } while (--Length);
    *TransmitFirst = (unsigned char) ((wCrc >> 8) & 0xFF);
    *TransmitSecond = (unsigned char) (wCrc & 0xFF);
    return;
}
void crc_calc(void *data,unsigned short len)
{
    unsigned char *p = (unsigned char *)data;
    if(len<2)
        return;
    ComputeCrc(p,len-2,&p[len-2],&p[len-1]);
}

void GetDeviceCapabilities()
{
    //Get the Capabilities structure for the device.

    PHIDP_PREPARSED_DATA	PreparsedData;

    /*
    API function: HidD_GetPreparsedData
    Returns: a pointer to a buffer containing the information about the device's capabilities.
    Requires: A handle returned by CreateFile.
    There's no need to access the buffer directly,
    but HidP_GetCaps and other API functions require a pointer to the buffer.
    */

    HidD_GetPreparsedData
            (DeviceHandle,
             &PreparsedData);

    /*
    API function: HidP_GetCaps
    Learn the device's capabilities.
    For standard devices such as joysticks, you can find out the specific
    capabilities of the device.
    For a custom device, the software will probably know what the device is capable of,
    and the call only verifies the information.
    Requires: the pointer to the buffer returned by HidD_GetPreparsedData.
    Returns: a Capabilities structure containing the information.
    */

    HidP_GetCaps
            (PreparsedData,
             &Capabilities);

    //No need for PreparsedData any more, so free the memory it's using.
    HidD_FreePreparsedData(PreparsedData);
}

void PrepareForOverlappedTransfer()
{
    //Get a handle to the device for the overlapped ReadFiles.
    ReadHandle=CreateFile
            (detailData->DevicePath,
             GENERIC_READ,
             FILE_SHARE_READ|FILE_SHARE_WRITE,
             (LPSECURITY_ATTRIBUTES)NULL,
             OPEN_EXISTING,
             FILE_ATTRIBUTE_NORMAL|FILE_FLAG_OVERLAPPED,
             NULL);

    if (hEventObject == 0)
    {
        hEventObject = CreateEvent
                (NULL,
                 TRUE,
                 TRUE,
                 "");
        //Set the members of the overlapped structure.

        HIDOverlapped.hEvent = hEventObject;
        HIDOverlapped.Offset = 0;
        HIDOverlapped.OffsetHigh = 0;
    }
}
void CloseHandles()
{
    //Close open handles.

    if (DeviceHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(DeviceHandle);
        DeviceHandle = INVALID_HANDLE_VALUE;
    }

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(ReadHandle);
        ReadHandle = INVALID_HANDLE_VALUE;
    }

    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(WriteHandle);
        WriteHandle = INVALID_HANDLE_VALUE;
    }
    SetupDiDestroyDeviceInfoList(hDevInfo);

}

int WriteOutputReport()
{
    //Send a report to the device.

    DWORD	BytesWritten = 0;
    INT		Index =0;
    ULONG	Result=0;;
//	OVERLAPPED overlapped;

    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        Result = WriteFile
                (WriteHandle,
                 InputReport,
                 Capabilities.OutputReportByteLength,
                 &BytesWritten,
                 NULL);
    }

    if (!Result)
    {
        //The WriteFile failed, so close the handles, display a message,
        //and set MyDeviceDetected to FALSE so the next attempt will look for the device.
        BytesWritten = GetLastError();
        //CloseHandles();
        return -1;
    }else //writefile success
    {
        return 0;
    }
}

int WriteOutputReport(char * buf)
{
    //Send a report to the device.

    DWORD	BytesWritten = 0;
    INT		Index =0;
    ULONG	Result=0;;
//	OVERLAPPED overlapped;
    memcpy(InputReport,buf,256);
    if ((WriteHandle != INVALID_HANDLE_VALUE)&&(WriteHandle!=NULL))
    {
        Result = WriteFile
                (WriteHandle,
                 InputReport,
                 Capabilities.OutputReportByteLength,
                 &BytesWritten,
                 NULL);
    }
    WritebinErrLogFile((char *)InputReport,65);
    if (!Result)
    {
        //The WriteFile failed, so close the handles, display a message,
        //and set MyDeviceDetected to FALSE so the next attempt will look for the device.
        BytesWritten = GetLastError();
        //CloseHandles();
        WriteLogFile("WriteOutputReport FAIL");
//TRACE( "WriteOutputReport fail\n");
        return -1;
    }else //writefile success
    {
        WriteLogFile("WriteOutputReport success");
//TRACE( "WriteOutputReport success\n");
        return 0;
    }
}
int WriteOutputReport2()
{
    //Send a report to the device.

    DWORD	BytesWritten = 0;
    INT		Index =0;
    ULONG	Result=0;;
//	OVERLAPPED overlapped;


    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        memset(InputReport,0,256);
        InputReport[1]= 0x39;
        InputReport[2]= 0x03;

        InputReport[4]= 0x03;
        InputReport[5]= 0x05;

        Result = WriteFile
                (WriteHandle,
                 InputReport,
                 Capabilities.OutputReportByteLength,
                 &BytesWritten,
                 NULL);
    }

    if (!Result)
    {
        //The WriteFile failed, so close the handles, display a message,
        //and set MyDeviceDetected to FALSE so the next attempt will look for the device.
        BytesWritten = GetLastError();
        //CloseHandles();
        return -1;
    }else //writefile success
    {
        return 0;
    }
}


bool ReadThreadFindHID()
{
    //Use a series of API calls to find a HID with a specified Vendor IF and Product ID.

    HIDD_ATTRIBUTES						Attributes;
    DWORD								DeviceUsage;
    SP_DEVICE_INTERFACE_DATA			devInfoData;
    bool								LastDevice = FALSE;
    int									MemberIndex = 0;
    LONG								Result;
    CString								UsageDescription;

    Length = 0;
    detailData = NULL;
    DeviceHandle=NULL;

    /*
    API function: HidD_GetHidGuid
    Get the GUID for all system HIDs.
    Returns: the GUID in HidGuid.
    */

    HidD_GetHidGuid(&HidGuid);

    /*
    API function: SetupDiGetClassDevs
    Returns: a handle to a device information set for all installed devices.
    Requires: the GUID returned by GetHidGuid.
    */

    hDevInfo=SetupDiGetClassDevs
            (&HidGuid,
             NULL,
             NULL,
             DIGCF_PRESENT|DIGCF_INTERFACEDEVICE);

    devInfoData.cbSize = sizeof(devInfoData);

    //Step through the available devices looking for the one we want.
    //Quit on detecting the desired device or checking all available devices without success.

    MemberIndex = 0;
    LastDevice = FALSE;

    do
    {


        Result=SetupDiEnumDeviceInterfaces
                (hDevInfo,
                 0,
                 &HidGuid,
                 MemberIndex,
                 &devInfoData);

        if (Result != 0)
        {


            Result = SetupDiGetDeviceInterfaceDetail
                    (hDevInfo,
                     &devInfoData,
                     NULL,
                     0,
                     &Length,
                     NULL);

            //Allocate memory for the hDevInfo structure, using the returned Length.

            detailData = (PSP_DEVICE_INTERFACE_DETAIL_DATA)malloc(Length);

            //Set cbSize in the detailData structure.

            detailData -> cbSize = sizeof(SP_DEVICE_INTERFACE_DETAIL_DATA);

            //Call the function again, this time passing it the returned buffer size.

            Result = SetupDiGetDeviceInterfaceDetail
                    (hDevInfo,
                     &devInfoData,
                     detailData,
                     Length,
                     &Required,
                     NULL);



            DeviceHandle=CreateFile
                    (detailData->DevicePath,
                     0,
                     FILE_SHARE_READ|FILE_SHARE_WRITE,
                     (LPSECURITY_ATTRIBUTES)NULL,
                     OPEN_EXISTING,
                     0,
                     NULL);

            //Set the Size to the number of bytes in the structure.

            Attributes.Size = sizeof(Attributes);

            Result = HidD_GetAttributes
                    (DeviceHandle,
                     &Attributes);

            //Is it the desired device?

            MyDeviceDetected = FALSE;


            if (Attributes.VendorID == VendorID)
            {
                if (Attributes.ProductID == ProductID)
                {
                    //Both the Vendor ID and Product ID match.

                    MyDeviceDetected = TRUE;
                    MyDevicePathName = detailData->DevicePath;

                    //Register to receive device notifications.

                    //RegisterForDeviceNotifications();

                    //Get the device's capablities.

                    GetDeviceCapabilities();

                    // Find out if the device is a system mouse or keyboard.

                    DeviceUsage = (Capabilities.UsagePage * 256) + Capabilities.Usage;

                    if (DeviceUsage == 0x102)
                    {
                        UsageDescription = "mouse";
                    }

                    if (DeviceUsage == 0x106)
                    {
                        UsageDescription = "keyboard";
                    }

                    ReadHandle=CreateFile
                            (detailData->DevicePath,
                             GENERIC_READ,
                             FILE_SHARE_READ|FILE_SHARE_WRITE,
                             (LPSECURITY_ATTRIBUTES)NULL,
                             OPEN_EXISTING,
                             FILE_ATTRIBUTE_NORMAL|FILE_FLAG_OVERLAPPED,
                             NULL);

                    if (hEventObject == 0)
                    {
                        hEventObject = CreateEvent
                                (NULL,
                                 TRUE,
                                 TRUE,
                                 "");
                        //Set the members of the overlapped structure.

                        HIDOverlapped.hEvent = hEventObject;
                        HIDOverlapped.Offset = 0;
                        HIDOverlapped.OffsetHigh = 0;
                    }
                    // Get a handle for writing Output reports.

                    WriteHandle=CreateFile
                            (detailData->DevicePath,
                             GENERIC_WRITE,
                             FILE_SHARE_READ|FILE_SHARE_WRITE,
                             (LPSECURITY_ATTRIBUTES)NULL,
                             OPEN_EXISTING,
                             FILE_ATTRIBUTE_NORMAL,
                             NULL);
                    // Prepare to read reports using Overlapped I/O.

                    //PrepareForOverlappedTransfer();

                } //if (Attributes.ProductID == ProductID)
                else
                    //The Product ID doesn't match.

                    CloseHandle(DeviceHandle);
            } //if (Attributes.VendorID == VendorID)

            else
                //The Vendor ID doesn't match.
                CloseHandle(DeviceHandle);

            //Free the memory used by the detailData structure (no longer needed).

            free(detailData);

        }  //if (Result != 0)

        else
            //SetupDiEnumDeviceInterfaces returned 0, so there are no more devices to check.

            LastDevice=TRUE;

        //If we haven't found the device yet, and haven't tried every available device,
        //try the next one.

        MemberIndex = MemberIndex + 1;

    } //do

    while ((LastDevice == FALSE) && (MyDeviceDetected == FALSE));


    //Free the memory reserved for hDevInfo by SetupDiClassDevs.

    SetupDiDestroyDeviceInfoList(hDevInfo);

    return MyDeviceDetected;

}

void TeaEncrypt(unsigned int *v,unsigned int n,unsigned int *k)
{
    unsigned int z, y, sum=0, e, DELTA=0x9e3779b9;
    long p,q;

    z=v[n-1];
    y=v[0];
    q = 6 + 52/n;
    while(q-- > 0)
    {
        sum += DELTA;
        e = (sum >> 2)&3;
        for(p=0;p< n-1; p++)
            y = v[p+1], z = v[p] += MX;
        y = v[0];
        z = v[n-1] += MX;
    }
}


void TeaDecrypt(unsigned int *v,unsigned int n,unsigned int *k)
{
    //n>1
    unsigned int z, y, sum=0, e, DELTA=0x9e3779b9;
    long p,q;

    y=v[0];
    q = 6 + 52/n;
    sum = q*DELTA;
    while(sum != 0)
    {
        e = (sum >> 2)&3;
        for(p=n-1;p>0; p--)
            z = v[p-1],y = v[p] -= MX;
        z = v[n-1];
        y = v[0] -= MX;
        sum -= DELTA;
    }
}


unsigned char * FP_CHECH()
{
    DWORD	Result;

    //device
    char buf[65];
    memset(buf,0,65);
    buf[0]=0x00;
    buf[1]=0x55;
    buf[2]=0x7A;
    //buf[4]=0x10;
    buf[4]=0x04;
    buf[5]=0x50;
    buf[6]=0x11;
    crc_calc(buf+3,6);

    WriteOutputReport(buf);

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile
                (ReadHandle,
                 InputReport,
                 Capabilities.InputReportByteLength,
                 &NumberOfBytesRead,
                 (LPOVERLAPPED) &HIDOverlapped);
//			if (Result) break;
    }else
    {
        //STATE_USB_BROKEN;
        return NULL;
    }
//		Sleep(5);
//		}
    Result = WaitForSingleObject (hEventObject,	5000);

    switch (Result)
    {
        case WAIT_OBJECT_0: //ok, received inputreport
        {
            ResetEvent(hEventObject);
            CloseHandles();
            return (unsigned char *)(InputReport+4);
            //WriteOutputReport();
        }
            break;
        case WAIT_TIMEOUT:
            ResetEvent(hEventObject);
//			Sleep(5);
//			TRACE("noting readed");
            break;
        default:  //Undefined error
            break;
    }

//	CloseHandles();
    return 0;
}


unsigned char * FP_REG(int index )
{
    DWORD	Result;
    char off1=index/256;
    char off2=index%256;
    //device
    char buf[65];
    memset(buf,0,65);
    buf[0]=0x00;
    buf[1]=0x55;
    buf[2]=0x7A;
    //buf[4]=0x10;
    buf[4]=0x06;
    buf[5]=0x50;
    buf[6]=0x12;
    buf[7]=off1;
    buf[8]=off2;
    crc_calc(buf+3,8);//len2，crc2
//	memcpy
//	memcpy(buf+9,data,60);
    WriteOutputReport(buf);

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile
                (ReadHandle,
                 InputReport,
                 Capabilities.InputReportByteLength,
                 &NumberOfBytesRead,
                 (LPOVERLAPPED) &HIDOverlapped);
//			if (Result) break;
    }else
    {
        //STATE_USB_BROKEN;
        return NULL;
    }
//		Sleep(5);
//		}
    Result = WaitForSingleObject (hEventObject,	5000);

    switch (Result)
    {
        case WAIT_OBJECT_0: //ok, received inputreport
        {
            ResetEvent(hEventObject);
            CloseHandles();
            return (unsigned char *)(InputReport+5);
            //WriteOutputReport();
        }
            break;
        case WAIT_TIMEOUT:
            ResetEvent(hEventObject);
//			Sleep(5);
//			TRACE("noting readed");
            break;
        default:  //Undefined error
            break;
    }

//	CloseHandles();
    return 0;
}

unsigned char * FP_DEL(int index1,int index2)
{
    DWORD	Result;

    //device
    char buf[65];
    memset(buf,0,65);
    buf[0]=0x00;
    buf[1]=0x55;
    buf[2]=0x7A;
    //buf[4]=0x10;
    buf[4]=0x08;
    buf[5]=0x50;
    buf[6]=0x13;
    buf[7]=index1/256;
    buf[8]=index1%256;
    buf[9]=index2/256;
    buf[10]=index2%256;
    crc_calc(buf+3,10);//len2，crc2

//	memcpy
//	memcpy(buf+9,data,60);
    WriteOutputReport(buf);

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile
                (ReadHandle,
                 InputReport,
                 Capabilities.InputReportByteLength,
                 &NumberOfBytesRead,
                 (LPOVERLAPPED) &HIDOverlapped);
//			if (Result) break;
    }else
    {
        //STATE_USB_BROKEN;
        return NULL;
    }
//		Sleep(5);
//		}
    Result = WaitForSingleObject (hEventObject,	500);

    switch (Result)
    {
        case WAIT_OBJECT_0: //ok, received inputreport
        {
            ResetEvent(hEventObject);
            //CloseHandles();
            return (unsigned char *)(InputReport+5);
            //WriteOutputReport();
        }
            break;
        case WAIT_TIMEOUT:
            ResetEvent(hEventObject);
//			Sleep(5);
//			TRACE("noting readed");
            break;
        default:  //Undefined error
            break;
    }

//	CloseHandles();
    return 0;
}


unsigned char * GET_SN()
{
    DWORD	Result;

    //device
    char buf[65];
    memset(buf,0,65);
    buf[0]=0x00;
    buf[1]=0x55;
    buf[2]=0x7A;
    //buf[4]=0x10;
    buf[4]=0x05;
    buf[5]=0x7c;
    buf[6]=0x00;
    buf[7]=0x08;
    crc_calc(buf+3,7);//len2，crc2
//	memcpy
//	memcpy(buf+9,data,60);
    WriteOutputReport(buf);

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile
                (ReadHandle,
                 InputReport,
                 Capabilities.InputReportByteLength,
                 &NumberOfBytesRead,
                 (LPOVERLAPPED) &HIDOverlapped);
//			if (Result) break;
    }else
    {
        //STATE_USB_BROKEN;
        return NULL;
    }
//		Sleep(5);
//		}
    Result = WaitForSingleObject (hEventObject,	500);

    switch (Result)
    {
        case WAIT_OBJECT_0: //ok, received inputreport
        {
            ResetEvent(hEventObject);
            //	CloseHandles();
            return (unsigned char *)(InputReport+6);
            //WriteOutputReport();
        }
            break;
        case WAIT_TIMEOUT:
            ResetEvent(hEventObject);
//			Sleep(5);
//			TRACE("noting readed");
            break;
        default:  //Undefined error
            break;
    }

//	CloseHandles();
    return 0;
}

unsigned char * DEL_ID()
{
    DWORD	Result;

    //device
    char buf[65];
    memset(buf,0,65);
    buf[0]=0x00;
    buf[1]=0x55;
    buf[2]=0x7A;
    //buf[4]=0x10;
    buf[4]=0x04;
    buf[5]=0x7d;
    buf[6]=0x00;
    crc_calc(buf+3,6);//len2，crc2
//	memcpy
//	memcpy(buf+9,data,60);
    WriteOutputReport(buf);

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile
                (ReadHandle,
                 InputReport,
                 Capabilities.InputReportByteLength,
                 &NumberOfBytesRead,
                 (LPOVERLAPPED) &HIDOverlapped);
//			if (Result) break;
    }else
    {
        //STATE_USB_BROKEN;
        return NULL;
    }
//		Sleep(5);
//		}
    Result = WaitForSingleObject (hEventObject,	500);

    switch (Result)
    {
        case WAIT_OBJECT_0: //ok, received inputreport
        {
            ResetEvent(hEventObject);
            //CloseHandles();
            return (unsigned char *)(InputReport+5);
            //WriteOutputReport();
        }
            break;
        case WAIT_TIMEOUT:
            ResetEvent(hEventObject);
//			Sleep(5);
//			TRACE("noting readed");
            break;
        default:  //Undefined error
            break;
    }

//	CloseHandles();
    return 0;
}



unsigned char * ADD_ID(char * data)
{
    DWORD	Result;

    //device
    char buf[65];
    memset(buf,0,65);
    buf[0]=0x00;
    buf[1]=0x55;
    buf[2]=0x7A;
    //buf[4]=0x10;
    buf[4]=0x15;
    buf[5]=0x7e;
//	buf[6]=0x00;
//	memcpy
    memcpy(buf+6,data,18);
    crc_calc(buf+3,0x17);//len2，crc2
    WriteOutputReport(buf);

    while (1)
    {
        if (ReadHandle != INVALID_HANDLE_VALUE)
        {
            Result = ReadFile
                    (ReadHandle,
                     InputReport,
                     Capabilities.InputReportByteLength,
                     &NumberOfBytesRead,
                     (LPOVERLAPPED) &HIDOverlapped);
//			if (Result) break;
        }else
        {
            //STATE_USB_BROKEN;
            return NULL;
        }
//		Sleep(5);
//		}


        Result = WaitForSingleObject (hEventObject,	5000);
        //write_log_file("add id  6");
        switch (Result)
        {
            case WAIT_OBJECT_0: //ok, received inputreport
            {
                //write_log_file("add id  7");
                if (InputReport[5]!=0x7E)
                {
                    break;
                }
                else
                {
                    ResetEvent(hEventObject);
                    CloseHandles();
                    return (unsigned char *)(InputReport+5);
                }
                //WriteOutputReport();
            }
                break;
            case WAIT_TIMEOUT:
                write_log_file("add id  8");
                ResetEvent(hEventObject);
                return 0;
//			break;
//			Sleep(5);
//			TRACE("noting readed");
                break;
            default:  //Undefined error
                return 0;
                break;
        }
        continue;
    }
//	CloseHandles();
    return 0;
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

```

### 精简后代码结构

```cpp
// 包含标准头文件和自定义头文件
#include <stdafx.h>
#include "usb_thread.h"
#include "global.h"
#include "idcardbin.h"

// 定义全局变量
DWORD								ActualBytesRead;
DWORD								BytesRead;
HIDP_CAPS							Capabilities;
DWORD								cbBytesRead;
PSP_DEVICE_INTERFACE_DETAIL_DATA	detailData;
HANDLE								DeviceHandle;
DWORD								dwError;
char								FeatureReport[256];
HANDLE								hEventObject;
HANDLE								hDevInfo;
GUID								HidGuid;
OVERLAPPED							HIDOverlapped;
unsigned char						InputReport[256];
ULONG								Length;
LPOVERLAPPED						lpOverLap;
bool								MyDeviceDetected = FALSE;
CString								MyDevicePathName;
DWORD								NumberOfBytesRead;
unsigned char						OutputReport[256];
HANDLE								ReadHandle;
DWORD								ReportType;
ULONG								Required;
CString								ValueToDisplay;
HANDLE								WriteHandle;

// 定义其他变量
CString my_usb_detail;
int VendorID=0x9573;
int ProductID= 0x4850;

// 函数：更新 CRC 校验值
static unsigned short UpdateCrc(unsigned char ch, unsigned short *lpwCrc)
{
    ch = (ch^(unsigned char)((*lpwCrc) & 0x00FF));
    ch = (ch^(ch<<4));
    *lpwCrc = (*lpwCrc >> 8)^((unsigned short)ch << 8)^
              ((unsigned short)ch<<3)^((unsigned short)ch>>4);
    return(*lpwCrc);
}

// 函数：计算 CRC 校验值
static void ComputeCrc(unsigned char *Data, unsigned short Length,
                       unsigned char *TransmitFirst, unsigned char *TransmitSecond)
{
    unsigned char chBlock;
    unsigned short wCrc = 0x6363;
    do {
        chBlock = *Data++;
        UpdateCrc(chBlock, &wCrc);
    } while (--Length);
    *TransmitFirst = (unsigned char) ((wCrc >> 8) & 0xFF);
    *TransmitSecond = (unsigned char) (wCrc & 0xFF);
    return;
}

// 函数：计算 CRC 校验值（简化版）
void crc_calc(void *data, unsigned short len)
{
    unsigned char *p = (unsigned char *)data;
    if(len < 2)
        return;
    ComputeCrc(p, len - 2, &p[len - 2], &p[len - 1]);
}

// 函数：获取设备能力
void GetDeviceCapabilities()
{
    // 获取预处理数据
    PHIDP_PREPARSED_DATA PreparsedData;
    HidD_GetPreparsedData(DeviceHandle, &PreparsedData);
    
    // 获取设备能力信息
    HidP_GetCaps(PreparsedData, &Capabilities);

    // 释放预处理数据
    HidD_FreePreparsedData(PreparsedData);
}

// 函数：准备进行重叠传输
void PrepareForOverlappedTransfer()
{
    // 为重叠读取操作获取设备句柄
    ReadHandle = CreateFile(detailData->DevicePath, GENERIC_READ,
                            FILE_SHARE_READ | FILE_SHARE_WRITE,
                            (LPSECURITY_ATTRIBUTES)NULL, OPEN_EXISTING,
                            FILE_ATTRIBUTE_NORMAL | FILE_FLAG_OVERLAPPED, NULL);

    if (hEventObject == 0)
    {
        // 创建事件对象
        hEventObject = CreateEvent(NULL, TRUE, TRUE, "");

        // 设置重叠结构的成员
        HIDOverlapped.hEvent = hEventObject;
        HIDOverlapped.Offset = 0;
        HIDOverlapped.OffsetHigh = 0;
    }
}

// 函数：关闭句柄
void CloseHandles()
{
    // 关闭打开的句柄
    if (DeviceHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(DeviceHandle);
        DeviceHandle = INVALID_HANDLE_VALUE;
    }

    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(ReadHandle);
        ReadHandle = INVALID_HANDLE_VALUE;
    }

    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(WriteHandle);
        WriteHandle = INVALID_HANDLE_VALUE;
    }

    // 销毁设备信息列表
    SetupDiDestroyDeviceInfoList(hDevInfo);
}

```


#### UpdateCrc

```cpp
// 函数：更新 CRC 校验值
// 参数：
//   ch: 待处理的字节
//   lpwCrc: CRC 校验值指针
// 返回值：更新后的 CRC 校验值
static unsigned short UpdateCrc(unsigned char ch, unsigned short *lpwCrc)
{
    // 将待处理的字节与低位 CRC 校验值进行异或运算
    ch = (ch ^ (unsigned char)((*lpwCrc) & 0x00FF));
    
    // 左移 4 位，并再次与原字节进行异或运算
    ch = (ch ^ (ch << 4));
    
    // 更新 CRC 校验值
    *lpwCrc = (*lpwCrc >> 8) ^ ((unsigned short)ch << 8) ^
              ((unsigned short)ch << 3) ^ ((unsigned short)ch >> 4);
    
    // 返回更新后的 CRC 校验值
    return (*lpwCrc);
}

```


#### ComputeCrc

```cpp
// 函数：计算 CRC 校验值
// 参数：
//   Data: 待计算 CRC 的数据数组指针
//   Length: 数据长度
//   TransmitFirst: 存储 CRC 高位字节的指针
//   TransmitSecond: 存储 CRC 低位字节的指针
// 返回值：无
static void ComputeCrc(unsigned char *Data, unsigned short Length,
                       unsigned char *TransmitFirst, unsigned char *TransmitSecond)
{
    unsigned char chBlock; // 用于存储每次处理的字节
    unsigned short wCrc = 0x6363; // 初始 CRC 校验值

    // 遍历数据数组，更新 CRC 校验值
    do {
        chBlock = *Data++; // 获取当前字节
        UpdateCrc(chBlock, &wCrc); // 更新 CRC 校验值
    } while (--Length); // 继续直到处理完所有数据

    // 将 CRC 校验值的高位字节和低位字节存储到对应的指针中
    *TransmitFirst = (unsigned char)((wCrc >> 8) & 0xFF); // 存储高位字节
    *TransmitSecond = (unsigned char)(wCrc & 0xFF); // 存储低位字节

    return; // 结束函数
}

```


#### crc_calc
```cpp
// 函数：计算 CRC 校验值
// 参数：
//   data: 待计算 CRC 的数据指针
//   len: 数据长度
// 返回值：无
void crc_calc(void *data, unsigned short len)
{
    unsigned char *p = (unsigned char *)data; // 将数据指针转换为无符号字符指针

    // 如果数据长度小于 2，则直接返回，因为 CRC 校验需要至少两个字节的数据
    if (len < 2)
        return;

    // 调用 ComputeCrc 函数计算 CRC 校验值，并将结果存储在数据的倒数第二个字节和最后一个字节中
    ComputeCrc(p, len - 2, &p[len - 2], &p[len - 1]);
}

```

#### GetDeviceCapabilities
```cpp
// 函数：获取设备的功能信息
// 参数：无
// 返回值：无
void GetDeviceCapabilities()
{
    PHIDP_PREPARSED_DATA PreparsedData; // 预解析数据结构指针

    // 获取设备的预解析数据
    HidD_GetPreparsedData(DeviceHandle, &PreparsedData);

    /*
    函数：HidP_GetCaps
    说明：获取设备的功能信息。对于标准设备（如游戏手柄），可以了解设备的具体功能。
         对于自定义设备，软件可能已经知道设备的功能，此调用仅用于验证信息。
    参数：PreparsedData - 由 HidD_GetPreparsedData 返回的缓冲区指针
    返回值：包含设备信息的 Capabilities 结构体
    */
    HidP_GetCaps(PreparsedData, &Capabilities);

    // 不再需要 PreparsedData，释放内存
    HidD_FreePreparsedData(PreparsedData);
}

```

#### PrepareForOverlappedTransfer
```cpp
// 函数：准备进行重叠传输
// 参数：无
// 返回值：无
void PrepareForOverlappedTransfer()
{
    // 获取用于重叠读取文件的设备句柄
    ReadHandle = CreateFile(
        detailData->DevicePath,                           // 设备路径
        GENERIC_READ,                                     // 读取权限
        FILE_SHARE_READ | FILE_SHARE_WRITE,               // 共享权限
        (LPSECURITY_ATTRIBUTES)NULL,                      // 安全属性
        OPEN_EXISTING,                                    // 打开已存在的文件
        FILE_ATTRIBUTE_NORMAL | FILE_FLAG_OVERLAPPED,     // 属性标志：正常文件 + 重叠标志
        NULL                                              // 模板句柄
    );

    // 如果事件对象未创建，创建一个事件对象
    if (hEventObject == 0)
    {
        hEventObject = CreateEvent(
            NULL,     // 默认安全属性
            TRUE,     // 手动重置
            TRUE,     // 初始状态为有信号
            ""        // 无名称
        );

        // 设置重叠结构的成员
        HIDOverlapped.hEvent = hEventObject;   // 事件句柄
        HIDOverlapped.Offset = 0;               // 偏移量
        HIDOverlapped.OffsetHigh = 0;           // 高位偏移量
    }
}

```

#### CloseHandles
```cpp
// 函数：关闭句柄
// 参数：无
// 返回值：无
void CloseHandles()
{
    // 关闭打开的句柄

    // 如果设备句柄有效，关闭设备句柄并将其置为无效
    if (DeviceHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(DeviceHandle);
        DeviceHandle = INVALID_HANDLE_VALUE;
    }

    // 如果读取句柄有效，关闭读取句柄并将其置为无效
    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(ReadHandle);
        ReadHandle = INVALID_HANDLE_VALUE;
    }

    // 如果写入句柄有效，关闭写入句柄并将其置为无效
    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        CloseHandle(WriteHandle);
        WriteHandle = INVALID_HANDLE_VALUE;
    }

    // 销毁设备信息列表
    SetupDiDestroyDeviceInfoList(hDevInfo);
}

```

#### WriteOutputReport
```cpp
// 函数：写入输出报告
// 参数：无
// 返回值：写入结果，成功返回0，失败返回-1
int WriteOutputReport()
{
    // 向设备发送报告

    DWORD	BytesWritten = 0;
    ULONG	Result = 0;

    // 如果写入句柄有效，则进行写入操作
    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        // 调用WriteFile函数向设备写入数据
        Result = WriteFile(
            WriteHandle,                // 写入句柄
            InputReport,                // 待写入的数据
            Capabilities.OutputReportByteLength, // 写入数据的字节长度
            &BytesWritten,              // 实际写入的字节数
            NULL                        // 不使用重叠操作
        );
    }

    // 如果写入失败，则处理失败情况
    if (!Result)
    {
        // 获取错误信息
        BytesWritten = GetLastError();
        // 返回-1表示写入失败
        return -1;
    }
    else
    {
        // 写入成功，返回0表示成功
        return 0;
    }
}

```

#### WriteOutputReport
```cpp
// 函数：写入输出报告
// 参数：buf - 待发送的报告数据
// 返回值：写入结果，成功返回0，失败返回-1
int WriteOutputReport(char *buf)
{
    // 将待发送的报告数据复制到InputReport缓冲区中
    memcpy(InputReport, buf, 256);

    // 定义变量用于记录写入操作结果和实际写入的字节数
    DWORD	BytesWritten = 0;
    ULONG	Result = 0;

    // 如果写入句柄有效，则进行写入操作
    if (WriteHandle != INVALID_HANDLE_VALUE && WriteHandle != NULL)
    {
        // 调用WriteFile函数向设备写入数据
        Result = WriteFile(
            WriteHandle,                        // 写入句柄
            InputReport,                        // 待写入的数据
            Capabilities.OutputReportByteLength,// 写入数据的字节长度
            &BytesWritten,                      // 实际写入的字节数
            NULL                                // 不使用重叠操作
        );
    }

    // 记录写入操作结果到日志文件
    WritebinErrLogFile((char *)InputReport, 65);

    // 如果写入失败，则处理失败情况
    if (!Result)
    {
        // 获取错误信息
        BytesWritten = GetLastError();
        // 记录失败信息到日志文件
        WriteLogFile("WriteOutputReport FAIL");
        // 返回-1表示写入失败
        return -1;
    }
    else
    {
        // 记录成功信息到日志文件
        WriteLogFile("WriteOutputReport success");
        // 写入成功，返回0表示成功
        return 0;
    }
}

```

#### WriteOutputReport2
```cpp
// 函数：写入输出报告2
// 描述：向设备发送报告数据
// 返回值：写入结果，成功返回0，失败返回-1
int WriteOutputReport2()
{
    // 定义变量用于记录写入操作结果和实际写入的字节数
    DWORD	BytesWritten = 0;
    ULONG	Result = 0;

    // 如果写入句柄有效，则进行写入操作
    if (WriteHandle != INVALID_HANDLE_VALUE)
    {
        // 清空InputReport缓冲区，并设置报告数据
        memset(InputReport, 0, 256);
        InputReport[1] = 0x39;
        InputReport[2] = 0x03;
        InputReport[4] = 0x03;
        InputReport[5] = 0x05;

        // 调用WriteFile函数向设备写入数据
        Result = WriteFile(
            WriteHandle,                        // 写入句柄
            InputReport,                        // 待写入的数据
            Capabilities.OutputReportByteLength,// 写入数据的字节长度
            &BytesWritten,                      // 实际写入的字节数
            NULL                                // 不使用重叠操作
        );
    }

    // 如果写入失败，则处理失败情况
    if (!Result)
    {
        // 获取错误信息
        BytesWritten = GetLastError();
        // 返回-1表示写入失败
        return -1;
    }
    else
    {
        // 写入成功，返回0表示成功
        return 0;
    }
}

```

#### ReadThreadFindHID
```cpp
// 函数：读取线程查找HID设备
// 描述：使用一系列的API调用查找具有指定供应商ID和产品ID的HID设备
// 返回值：如果找到目标设备返回true，否则返回false
bool ReadThreadFindHID()
{
    // 定义变量
    HIDD_ATTRIBUTES Attributes;
    DWORD DeviceUsage;
    SP_DEVICE_INTERFACE_DATA devInfoData;
    bool LastDevice = FALSE;
    int MemberIndex = 0;
    LONG Result;
    CString UsageDescription;

    // 重置长度和设备信息数据
    Length = 0;
    detailData = NULL;
    DeviceHandle = NULL;

    // 获取系统中所有HID设备的GUID
    HidD_GetHidGuid(&HidGuid);

    // 获取设备信息集句柄
    hDevInfo = SetupDiGetClassDevs(
        &HidGuid,
        NULL,
        NULL,
        DIGCF_PRESENT | DIGCF_INTERFACEDEVICE
    );

    devInfoData.cbSize = sizeof(devInfoData);

    // 逐个检查可用设备，直到找到目标设备或检查完所有设备
    MemberIndex = 0;
    LastDevice = FALSE;

    do
    {
        // 枚举设备接口
        Result = SetupDiEnumDeviceInterfaces(
            hDevInfo,
            0,
            &HidGuid,
            MemberIndex,
            &devInfoData
        );

        if (Result != 0)
        {
            Result = SetupDiGetDeviceInterfaceDetail(
                hDevInfo,
                &devInfoData,
                NULL,
                0,
                &Length,
                NULL
            );

            // 分配内存给设备信息数据结构
            detailData = (PSP_DEVICE_INTERFACE_DETAIL_DATA)malloc(Length);
            detailData->cbSize = sizeof(SP_DEVICE_INTERFACE_DETAIL_DATA);

            // 获取设备接口详细信息
            Result = SetupDiGetDeviceInterfaceDetail(
                hDevInfo,
                &devInfoData,
                detailData,
                Length,
                &Required,
                NULL
            );

            // 打开设备文件句柄
            DeviceHandle = CreateFile(
                detailData->DevicePath,
                0,
                FILE_SHARE_READ | FILE_SHARE_WRITE,
                (LPSECURITY_ATTRIBUTES)NULL,
                OPEN_EXISTING,
                0,
                NULL
            );

            // 获取设备属性
            Attributes.Size = sizeof(Attributes);
            Result = HidD_GetAttributes(DeviceHandle, &Attributes);

            // 检查是否为目标设备
            MyDeviceDetected = FALSE;
            if (Attributes.VendorID == VendorID)
            {
                if (Attributes.ProductID == ProductID)
                {
                    // 供应商ID和产品ID都匹配
                    MyDeviceDetected = TRUE;
                    MyDevicePathName = detailData->DevicePath;

                    // 获取设备能力
                    GetDeviceCapabilities();

                    // 检查设备是否为系统鼠标或键盘
                    DeviceUsage = (Capabilities.UsagePage * 256) + Capabilities.Usage;
                    if (DeviceUsage == 0x102)
                    {
                        UsageDescription = "mouse";
                    }
                    if (DeviceUsage == 0x106)
                    {
                        UsageDescription = "keyboard";
                    }

                    // 创建读取句柄
                    ReadHandle = CreateFile(
                        detailData->DevicePath,
                        GENERIC_READ,
                        FILE_SHARE_READ | FILE_SHARE_WRITE,
                        (LPSECURITY_ATTRIBUTES)NULL,
                        OPEN_EXISTING,
                        FILE_ATTRIBUTE_NORMAL | FILE_FLAG_OVERLAPPED,
                        NULL
                    );

                    // 创建事件对象
                    if (hEventObject == 0)
                    {
                        hEventObject = CreateEvent(
                            NULL,
                            TRUE,
                            TRUE,
                            ""
                        );
                        // 设置Overlapped结构的成员
                        HIDOverlapped.hEvent = hEventObject;
                        HIDOverlapped.Offset = 0;
                        HIDOverlapped.OffsetHigh = 0;
                    }

                    // 创建写入句柄
                    WriteHandle = CreateFile(
                        detailData->DevicePath,
                        GENERIC_WRITE,
                        FILE_SHARE_READ | FILE_SHARE_WRITE,
                        (LPSECURITY_ATTRIBUTES)NULL,
                        OPEN_EXISTING,
                        FILE_ATTRIBUTE_NORMAL,
                        NULL
                    );
                }
                else
                {
                    // 产品ID不匹配，关闭设备句柄
                    CloseHandle(DeviceHandle);
                }
            }
            else
            {
                // 供应商ID不匹配，关闭设备句柄
                CloseHandle(DeviceHandle);
            }

            // 释放detailData结构占用的内存
            free(detailData);
        }
        else
        {
            // 枚举设备接口失败，没有更多设备可用
            LastDevice = TRUE;
        }

        // 如果还未找到目标设备，并且还有可用设备，尝试下一个
        MemberIndex = MemberIndex + 1;

    } while ((LastDevice == FALSE) && (MyDeviceDetected == FALSE));

    // 释放SetupDiClassDevs保留的内存
    SetupDiDestroyDeviceInfoList(hDevInfo);

    return MyDeviceDetected;
}

```


#### TeaEncrypt
```cpp
// 函数：TEA加密
// 描述：使用Tiny Encryption Algorithm（TEA）对数据进行加密
// 参数：
// - v: 待加密的数据数组
// - n: 数据数组的长度
// - k: 加密密钥数组（4个无符号整数）
void TeaEncrypt(unsigned int *v, unsigned int n, unsigned int *k)
{
    unsigned int z, y, sum = 0, e, DELTA = 0x9e3779b9;
    long p, q;

    z = v[n - 1];
    y = v[0];
    q = 6 + 52 / n;
    while (q-- > 0)
    {
        sum += DELTA;
        e = (sum >> 2) & 3;
        for (p = 0; p < n - 1; p++)
        {
            y = v[p + 1];
            z = v[p] += (z >> 5 ^ y << 2) + (y >> 3 ^ z << 4) ^ (sum ^ y) + (k[p & 3 ^ e] ^ z);
        }
        y = v[0];
        z = v[n - 1] += (z >> 5 ^ y << 2) + (y >> 3 ^ z << 4) ^ (sum ^ y) + (k[p & 3 ^ e] ^ z);
    }
}

```


#### TeaDecrypt
```cpp
// 函数：TEA解密
// 描述：使用Tiny Encryption Algorithm（TEA）对数据进行解密
// 参数：
// - v: 待解密的数据数组
// - n: 数据数组的长度
// - k: 解密密钥数组（4个无符号整数）
void TeaDecrypt(unsigned int *v, unsigned int n, unsigned int *k)
{
    // n > 1
    unsigned int z, y, sum = 0, e, DELTA = 0x9e3779b9;
    long p, q;

    y = v[0];
    q = 6 + 52 / n;
    sum = q * DELTA;
    while (sum != 0)
    {
        e = (sum >> 2) & 3;
        for (p = n - 1; p > 0; p--)
        {
            z = v[p - 1];
            y = v[p] -= (z >> 5 ^ y << 2) + (y >> 3 ^ z << 4) ^ (sum ^ y) + (k[p & 3 ^ e] ^ z);
        }
        z = v[n - 1];
        y = v[0] -= (z >> 5 ^ y << 2) + (y >> 3 ^ z << 4) ^ (sum ^ y) + (k[p & 3 ^ e] ^ z);
        sum -= DELTA;
    }
}

```


#### FP_CHECH
```cpp
// 函数：FP_CHECH
// 描述：进行指纹检查，向设备发送指纹检查命令并读取响应
// 返回值：指向响应数据的指针，如果发生错误则返回空指针
unsigned char *FP_CHECH()
{
    DWORD Result;

    // 准备发送给设备的数据
    char buf[65];
    memset(buf, 0, 65);
    buf[0] = 0x00;
    buf[1] = 0x55;
    buf[2] = 0x7A;
    buf[4] = 0x04; // 命令长度
    buf[5] = 0x50;
    buf[6] = 0x11;
    crc_calc(buf + 3, 6); // 计算 CRC 校验码

    // 向设备发送数据
    WriteOutputReport(buf);

    // 读取设备响应
    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile(ReadHandle, InputReport, Capabilities.InputReportByteLength, &NumberOfBytesRead, (LPOVERLAPPED) &HIDOverlapped);
    }
    else
    {
        // 设备句柄无效，返回空指针
        return nullptr;
    }

    // 等待读取完成或超时
    Result = WaitForSingleObject(hEventObject, 5000);

    switch (Result)
    {
        case WAIT_OBJECT_0: // 读取完成
        {
            ResetEvent(hEventObject);
            CloseHandles();
            // 返回响应数据指针
            return (unsigned char *)(InputReport + 4);
        }
        break;
        case WAIT_TIMEOUT: // 超时
        {
            ResetEvent(hEventObject);
        }
        break;
        default: // 其他错误
        {
        }
        break;
    }

    // 关闭句柄并返回空指针
    return nullptr;
}

```

#### FP_REG
```cpp
// 函数：FP_REG
// 描述：进行指纹注册，向设备发送指纹注册命令并读取响应
// 参数：index - 指纹索引
// 返回值：指向响应数据的指针，如果发生错误则返回空指针
unsigned char *FP_REG(int index)
{
    DWORD Result;

    // 计算索引偏移量
    char off1 = index / 256;
    char off2 = index % 256;

    // 准备发送给设备的数据
    char buf[65];
    memset(buf, 0, 65);
    buf[0] = 0x00;
    buf[1] = 0x55;
    buf[2] = 0x7A;
    buf[4] = 0x06; // 命令长度
    buf[5] = 0x50;
    buf[6] = 0x12;
    buf[7] = off1;
    buf[8] = off2;
    crc_calc(buf + 3, 8); // 计算 CRC 校验码

    // 向设备发送数据
    WriteOutputReport(buf);

    // 读取设备响应
    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile(ReadHandle, InputReport, Capabilities.InputReportByteLength, &NumberOfBytesRead, (LPOVERLAPPED) &HIDOverlapped);
    }
    else
    {
        // 设备句柄无效，返回空指针
        return nullptr;
    }

    // 等待读取完成或超时
    Result = WaitForSingleObject(hEventObject, 5000);

    switch (Result)
    {
        case WAIT_OBJECT_0: // 读取完成
        {
            ResetEvent(hEventObject);
            CloseHandles();
            // 返回响应数据指针
            return (unsigned char *)(InputReport + 5);
        }
        break;
        case WAIT_TIMEOUT: // 超时
        {
            ResetEvent(hEventObject);
        }
        break;
        default: // 其他错误
        {
        }
        break;
    }

    // 关闭句柄并返回空指针
    return nullptr;
}

```

#### FP_DEL
```cpp
// 函数：FP_DEL
// 描述：删除指定索引范围内的指纹数据，向设备发送删除命令并读取响应
// 参数：index1 - 起始索引，index2 - 结束索引
// 返回值：指向响应数据的指针，如果发生错误则返回空指针
unsigned char *FP_DEL(int index1, int index2)
{
    DWORD Result;

    // 准备发送给设备的数据
    char buf[65];
    memset(buf, 0, 65);
    buf[0] = 0x00;
    buf[1] = 0x55;
    buf[2] = 0x7A;
    buf[4] = 0x08; // 命令长度
    buf[5] = 0x50;
    buf[6] = 0x13;
    buf[7] = index1 / 256;
    buf[8] = index1 % 256;
    buf[9] = index2 / 256;
    buf[10] = index2 % 256;
    crc_calc(buf + 3, 10); // 计算 CRC 校验码

    // 向设备发送数据
    WriteOutputReport(buf);

    // 读取设备响应
    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile(ReadHandle, InputReport, Capabilities.InputReportByteLength, &NumberOfBytesRead, (LPOVERLAPPED) &HIDOverlapped);
    }
    else
    {
        // 设备句柄无效，返回空指针
        return nullptr;
    }

    // 等待读取完成或超时
    Result = WaitForSingleObject(hEventObject, 500);

    switch (Result)
    {
        case WAIT_OBJECT_0: // 读取完成
        {
            ResetEvent(hEventObject);
            // 返回响应数据指针
            return (unsigned char *)(InputReport + 5);
        }
        break;
        case WAIT_TIMEOUT: // 超时
        {
            ResetEvent(hEventObject);
        }
        break;
        default: // 其他错误
        {
        }
        break;
    }

    // 返回空指针
    return nullptr;
}

```

#### GET_SN
```cpp
// 函数：GET_SN
// 描述：获取设备的序列号，向设备发送获取序列号命令并读取响应
// 返回值：指向设备序列号的指针，如果发生错误则返回空指针
unsigned char *GET_SN()
{
    DWORD Result;

    // 准备发送给设备的数据
    char buf[65];
    memset(buf, 0, 65);
    buf[0] = 0x00;
    buf[1] = 0x55;
    buf[2] = 0x7A;
    buf[4] = 0x05; // 命令长度
    buf[5] = 0x7c;
    buf[6] = 0x00;
    buf[7] = 0x08;
    crc_calc(buf + 3, 7); // 计算 CRC 校验码

    // 向设备发送数据
    WriteOutputReport(buf);

    // 读取设备响应
    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile(ReadHandle, InputReport, Capabilities.InputReportByteLength, &NumberOfBytesRead, (LPOVERLAPPED) &HIDOverlapped);
    }
    else
    {
        // 设备句柄无效，返回空指针
        return nullptr;
    }

    // 等待读取完成或超时
    Result = WaitForSingleObject(hEventObject, 500);

    switch (Result)
    {
        case WAIT_OBJECT_0: // 读取完成
        {
            ResetEvent(hEventObject);
            // 返回序列号数据指针
            return (unsigned char *)(InputReport + 6);
        }
        break;
        case WAIT_TIMEOUT: // 超时
        {
            ResetEvent(hEventObject);
        }
        break;
        default: // 其他错误
        {
        }
        break;
    }

    // 返回空指针
    return nullptr;
}

```

#### DEL_ID
```cpp
// 函数：DEL_ID
// 描述：从设备中删除指纹 ID
// 返回值：指向删除操作结果的指针，如果发生错误则返回空指针
unsigned char *DEL_ID()
{
    DWORD Result;

    // 准备发送给设备的数据
    char buf[65];
    memset(buf, 0, 65);
    buf[0] = 0x00;
    buf[1] = 0x55;
    buf[2] = 0x7A;
    buf[4] = 0x04; // 命令长度
    buf[5] = 0x7d;
    buf[6] = 0x00;
    crc_calc(buf + 3, 6); // 计算 CRC 校验码

    // 向设备发送数据
    WriteOutputReport(buf);

    // 读取设备响应
    if (ReadHandle != INVALID_HANDLE_VALUE)
    {
        Result = ReadFile(ReadHandle, InputReport, Capabilities.InputReportByteLength, &NumberOfBytesRead, (LPOVERLAPPED) &HIDOverlapped);
    }
    else
    {
        // 设备句柄无效，返回空指针
        return nullptr;
    }

    // 等待读取完成或超时
    Result = WaitForSingleObject(hEventObject, 500);

    switch (Result)
    {
        case WAIT_OBJECT_0: // 读取完成
        {
            ResetEvent(hEventObject);
            // 返回删除操作结果指针
            return (unsigned char *)(InputReport + 5);
        }
        break;
        case WAIT_TIMEOUT: // 超时
        {
            ResetEvent(hEventObject);
        }
        break;
        default: // 其他错误
        {
        }
        break;
    }

    // 返回空指针
    return nullptr;
}

```

#### ADD_ID
```cpp
// 函数：ADD_ID
// 描述：向设备中添加指纹 ID
// 参数：
//   - data: 指向包含要添加的指纹 ID 数据的指针
// 返回值：指向添加操作结果的指针，如果发生错误则返回空指针
unsigned char *ADD_ID(char *data)
{
    DWORD Result;

    // 准备发送给设备的数据
    char buf[65];
    memset(buf, 0, 65);
    buf[0] = 0x00;
    buf[1] = 0x55;
    buf[2] = 0x7A;
    buf[4] = 0x15; // 命令长度
    buf[5] = 0x7e;
    memcpy(buf + 6, data, 18); // 将指纹 ID 数据复制到缓冲区中
    crc_calc(buf + 3, 0x17); // 计算 CRC 校验码
    WriteOutputReport(buf);

    // 循环读取设备响应
    while (1)
    {
        // 读取设备响应
        if (ReadHandle != INVALID_HANDLE_VALUE)
        {
            Result = ReadFile(ReadHandle, InputReport, Capabilities.InputReportByteLength, &NumberOfBytesRead, (LPOVERLAPPED) &HIDOverlapped);
        }
        else
        {
            // 设备句柄无效，返回空指针
            return nullptr;
        }

        Result = WaitForSingleObject(hEventObject, 5000); // 等待读取完成或超时

        switch (Result)
        {
            case WAIT_OBJECT_0: // 读取完成
            {
                if (InputReport[5] != 0x7E) // 检查响应是否为添加成功的标志
                {
                    break;
                }
                else
                {
                    ResetEvent(hEventObject);
                    CloseHandles();
                    return (unsigned char *)(InputReport + 5); // 返回添加操作结果指针
                }
            }
            break;
            case WAIT_TIMEOUT: // 超时
            {
                ResetEvent(hEventObject);
                return nullptr;
            }
            break;
            default: // 其他错误
            {
                return nullptr;
            }
            break;
        }
        continue;
    }
    return nullptr;
}

```

#### write_log_file
```cpp
// 函数：write_log_file
// 描述：向日志文件中写入字符串
// 参数：
//   - str: 要写入的字符串
// 返回值：写入操作的状态，0 表示成功，-1 表示失败
int write_log_file(const char *str)
{
    FILE *file = fopen("data.txt", "a+"); // 打开日志文件，以追加方式写入
    if (file == NULL)
    {
        // 文件打开失败，可以选择退出或者其他处理方式
        return -1;
    }

    unsigned int len = strlen(str);
    unsigned int wlen = fwrite(str, sizeof(char), len, file); // 将字符串写入文件
    fputc('\n', file); // 写入换行符

    if (len != wlen)
    {
        // 写入长度不匹配，可能发生了写入错误，可以选择退出或者其他处理方式
        fclose(file);
        return -1;
    }

    fclose(file); // 关闭文件
    return 0; // 返回写入成功状态
}
```
