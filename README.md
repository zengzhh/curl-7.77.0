https://curl.se/

### 一、编译静态库

https://blog.csdn.net/DaSo_CSDN/article/details/77587916

1.从 [https://curl.se/download.html](https://curl.se/download.html) 下载curl-7.77.0.zip，解压

2.添加以下路径到环境变量path，保证rc.exe可以被找到  

```bash
C:\Program Files (x86)\Windows Kits\10\bin\10.0.19041.0\x64  
C:\Program Files (x86)\Windows Kits\10\bin\10.0.19041.0\x86  
```

3.静态编译  
开始菜单打开 x64 Native Tools Command Prompt for VS 2019，进入winbuild编译64位的静态库

```bash
nmake /f Makefile.vc mode=static VC=16 MACHINE=x64 DEBUG=no  //x64-Release  
nmake /f Makefile.vc mode=static VC=16 MACHINE=x64 DEBUG=yes //x64-Debug  
```

开始菜单打开 x86 Native Tools Command Prompt for VS 2019，进入winbuild编译32位的静态库

```bash
nmake /f Makefile.vc mode=static VC=16 MACHINE=x86 DEBUG=no  //x86-Release  
nmake /f Makefile.vc mode=static VC=16 MACHINE=x86 DEBUG=yes //x86-Debug  
```
```

最终在目录 curl-7.77.0\builds 下可以得到  

```
libcurl-vc16-x64-debug-static-ipv6-sspi-schannel  
libcurl-vc16-x64-release-static-ipv6-sspi-schannel  
libcurl-vc16-x86-debug-static-ipv6-sspi-schannel  
libcurl-vc16-x86-release-static-ipv6-sspi-schannel  
```

### 二、使用curl

新建工程testcurl，将所需的头文件和库拷贝至工程目录，然后配置如下：

```
预处理器定义：CURL_STATICLIB
附加包含目录：libcurl-vc16-x86-release-static-ipv6-sspi-schannel\include
附加库目录：libcurl-vc16-x86-release-static-ipv6-sspi-schannel\lib
附加依赖项：
libcurl_a.lib
Ws2_32.lib
Wldap32.lib
winmm.lib
Crypt32.lib
Normaliz.lib
```

编写测试代码，运行：
```cpp
#include <curl/curl.h>
int main()
{
       CURL* curl = curl_easy_init();
       if (curl != 0) {
              curl_easy_setopt(curl, CURLOPT_URL, "https://www.baidu.com");
              curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
              CURLcode res = curl_easy_perform(curl);
              if (res != CURLE_OK) {
                     fprintf(stderr, "curl_easy_perform() failed: %s\n",  curl_easy_strerror(res));
              }
              curl_easy_cleanup(curl);
       }
}
```