---
author: Yuuna Chang
title: "Spring Framework 使用 Jasypt 加密 config 檔案"
date: 2021-03-14
draft: false
image: image.jpeg
categories:
    - 加密
    - Spring-Framework
tags:
    - Spring-Framework
    - JAVA
    - 加密
---

## Jasypt 介紹

[Jasypt](http://www.jasypt.org/index.html) 為 Java 的套件，提供開發人員用簡單的方法將加解密功能加入專案裡，而開發人員也不需要對密碼學有深入的理解。

### 特點

- 單向與雙向加密的安全性，使用標準的加密技術。
- 提供了 DIGEST 演算法與 PBE(Password-Based Encryption) 演算法，Ex. MD5、SHA、SHA512、PBEWithMD5AndDES...。
- 提高使用者密碼的安全性。
- 於 Spring 簡單使用加解密技術。
- [更多其他特點...](http://www.jasypt.org/features.html)

### 使用 Jasypt CLI Tools 加解密資料

要使用 Jasypt CLI Tools 需先於網站中下載檔案：[Download](http://www.jasypt.org/download.html)

#### 演算法的選擇

​使用 `bin/listAlgorithms.sh` 查看可使用的演算法清單。

{{< highlight shell "lineNos=false" >}}
$ sh ./listAlgorithms.sh  
   
DIGEST ALGORITHMS:  [MD2, MD5, SHA, SHA-224, SHA-256, SHA-384, SHA-512, SHA-512/224, SHA-512/256]  
   
PBE ALGORITHMS:   [PBEWITHHMACSHA1ANDAES_128, PBEWITHHMACSHA1ANDAES_256, PBEWITHHMACSHA224ANDAES_128, PBEWITHHMACSHA224ANDAES_256, PBEWITHHMACSHA256ANDAES_128, PBEWITHHMACSHA256ANDAES_256, PBEWITHHMACSHA384ANDAES_128, PBEWITHHMACSHA384ANDAES_256, PBEWITHHMACSHA512ANDAES_128, PBEWITHHMACSHA512ANDAES_256, PBEWITHMD5ANDDES, PBEWITHMD5ANDTRIPLEDES, PBEWITHSHA1ANDDESEDE, PBEWITHSHA1ANDRC2_128, PBEWITHSHA1ANDRC2_40, PBEWITHSHA1ANDRC4_128, PBEWITHSHA1ANDRC4_40]  
{{< / highlight >}}

#### 加密

使用 `bin/encrypt.sh` 來進行加密，執行此檔案可看到應填入的欄位有哪些。

{{< highlight shell "lineNos=false" >}}
$ sh ./encrypt.sh

USAGE: encrypt.sh [ARGUMENTS]

  * Arguments must apply to format:

      "arg1=value1 arg2=value2 arg3=value3 ..."

  * Required arguments:

      input
      password

  * Optional arguments:

      verbose
      algorithm
      keyObtentionIterations
      saltGeneratorClassName
      providerName
      providerClassName
      stringOutputType
      ivGeneratorClassName
{{< / highlight >}}

​可觀察到必要填入有 `input` 與 `password` 兩個欄位。

- `input` 為要加密的文字。
- `password` 為加解密使用的 Key。
- ​​​​[可選] `algorithm` 為使用哪種演算法來加密。

那現在以 input 為 password123，password 為 CAT，而演算法選擇 PBEWithMD5AndDES 來進行加密。

{{< highlight shell "lineNos=false" >}}
$ sh ./encrypt.sh input=password123 password=CAT algorithm=PBEWithMD5AndDES

----ENVIRONMENT-----------------

Runtime: Azul Systems, Inc. OpenJDK 64-Bit Server VM 25.172-b01

----ARGUMENTS-------------------

algorithm: PBEWithMD5AndDES
input: password123
password: CAT

----OUTPUT----------------------

31wLpRHWtqh0XQOSLgLBLo2Rt2wDz4zj
{{< / highlight >}}

這樣就完成了加密，而加密過後的密碼為 `31wLpRHWtqh0XQOSLgLBLo2Rt2wDz4zj`，網站中就可以使用這組已加密的密碼。

#### 解密

可使用 `bin/decrypt.sh` 進行解密，執行此檔案可看到應填入的欄位有哪些，基本上與加密相同。

{{< highlight shell "lineNos=false" >}}
$ sh ./decrypt.sh

USAGE: decrypt.sh [ARGUMENTS]

  * Arguments must apply to format:

      "arg1=value1 arg2=value2 arg3=value3 ..."

  * Required arguments:

      input
      password

  * Optional arguments:

      verbose
      algorithm
      keyObtentionIterations
      saltGeneratorClassName
      providerName
      providerClassName
      stringOutputType
      ivGeneratorClassName
{{< / highlight >}}

那現在將剛剛加密過後的密碼進行解密，input 為 31wLpRHWtqh0XQOSLgLBLo2Rt2wDz4zj，password 為 CAT，演算法為 PBEWithMD5AndDES。


{{< highlight shell "lineNos=false" >}}
$ sh ./decrypt.sh input=31wLpRHWtqh0XQOSLgLBLo2Rt2wDz4zj password=CAT algorithm=PBEWithMD5AndDES

----ENVIRONMENT-----------------

Runtime: Azul Systems, Inc. OpenJDK 64-Bit Server VM 25.172-b01

----ARGUMENTS-------------------

algorithm: PBEWithMD5AndDES
input: 31wLpRHWtqh0XQOSLgLBLo2Rt2wDz4zj
password: CAT

----OUTPUT----------------------

password123
{{< / highlight >}}

​輸出結果得到 password123，成功進行解密。

## Spring Framework 使用 Jasypt 套件

###  匯入 Jasypt 套件

於 `pom.xml` 新增套件，  
套件版本的選擇若為 Sprint 3.0 或 3.1 使用 `jasypt-spring31` 版本，  
若為 Sprint 4.0 使用 `jasypt-spring4` 版本，本文以使用 4.0 版本。  
其他版本可於網站中尋找：[網站](http://www.jasypt.org/spring31.html)

{{< highlight xml >}}
<dependency>
    <groupId>org.jasypt</groupId>
    <artifactId>jasypt</artifactId>
    <version>1.9.3</version>
</dependency>
<dependency>
    <groupId>org.jasypt</groupId>
    <artifactId>jasypt-spring4</artifactId>
    <version>1.9.3</version>
</dependency>
{{< / highlight >}}

### 調整 Spring 配置

設定環境變數與套件相關配置。

這邊設定 Key 的方法有兩種，  
第一種是直接寫在設定檔案裡，name 設定 password，value 給予 Key，  
第二種是設定在 Server 的環境變數裡，name 設定 passwordEnvName，value 給予環境變數名稱。

{{< highlight xml >}}
<!-- 設定環境變數 -->
<bean id="environmentVariablesConfiguration" class="org.jasypt.encryption.pbe.config.EnvironmentStringPBEConfig">
    <property name="algorithm" value="PBEWithMD5AndDES" />
    
    <!-- 方法一：將 key 直接設定在這裡 -->
    <property name="password" value="CAT" />
    <!-- 方法二：將 key 設定在 Server 的環境變數 -->
    <property name="passwordEnvName" value="WEB_PASSWORD" />
</bean>

<!-- 解密配置值設定 -->
<bean id="configurationEncryptor" class="org.jasypt.encryption.pbe.StandardPBEStringEncryptor">
    <property name="config" ref="environmentVariablesConfiguration" />
</bean>

<!-- Spring Properties 設定 -->
<bean class="org.jasypt.spring4.properties.EncryptablePropertyPlaceholderConfigurer">
    <constructor-arg ref="configurationEncryptor" />
    <property name="locations">
        <list>
            <value>classpath:web.properties</value>
        </list>
    </property>
</bean>
{{< / highlight >}}

> 在 Spring Properties 設定中的 class 需要依照版本設定不同設定檔，  
> 而本文是使用 Spring 4.0，所以使用 org.jasypt.spring4.properties.EncryptablePropertyPlaceholderConfigurer，  
> 若為 Spring 3.1，則使用 org.jasypt.spring31.properties.EncryptablePropertyPlaceholderConfigurer，  
> 其他版本設定於網站中確認：[網站](http://www.jasypt.org/spring31.html)

最後將 properties 檔案裡面需要加密的部分使用 `ENC(<已加密的密碼>)` 包起來，就大功告成啦！

{{< highlight xml >}}
web.user         = webuser
web.password     = ENC(31wLpRHWtqh0XQOSLgLBLo2Rt2wDz4zj)
{{< / highlight >}}

#### Key 設定在環境變數

若將 Key 直接設定在 xml 裡，這樣假若系統程式被駭，Key 會直接也被取得的話根本功虧一簣，
這邊講解在配置中使用 passwordEnvName 來把 Key 設定在環境變數，伺服器應該怎麼做設定。

設定環境變數其實不難，假若是設定 passwordEnvName = WEB_PASSWORD，環境變數設定為 WEB_PASSWORD，
那只要在 `/<tomcat>/bin/setenv.sh` 檔案中新增一行：

{{< highlight shell "lineNos=false" >}}
export WEB_PASSWORD=CAT
{{< / highlight >}}

可能會覺得這樣不是只是換個地方放嗎，若伺服器檔案都被竊取了，這個環境變數也是會被知道的？ 對的

但是​讀取環境變數僅會在伺服器啟動時讀取一次，所以可以啟動伺服器後將檔案移除，  
下次要再重啟時再將檔案放置回去，重啟完再移除檔案，  
雖然動作會較繁瑣，但可以確保密碼不被竊取。

## 常見問題

### 加密時發生 java.lang.ExceptionInInitializerError

執行 encrypt.sh 時若發生 java.lang.ExceptionInInitializerError，原因可能為 JDK 版本過新，可參考此[回答](https://github.com/jasypt/jasypt/issues/58)。

{{< highlight shell "lineNos=false" >}}
$ sh ./encrypt.sh input=password123 password=CAT

----ENVIRONMENT-----------------

Runtime: Oracle Corporation Java HotSpot(TM) 64-Bit Server VM 25.281-b09

----ARGUMENTS-------------------

input: password123
password: CAT

----ERROR-----------------------

java.lang.ExceptionInInitializerError
{{< / highlight >}}

​此時可確認一下本機是否有舊版 JDK 使用，使用以下指令查看，若沒有可去下載 JKD 8 261 版本(或更舊)。

{{< highlight shell "lineNos=false" >}}
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (3):
    1.8.281.09 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    1.8.0_172-zulu-8.30.0.1 (x86_64) "Azul Systems, Inc." - "Zulu 8" /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home
/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
{{< / highlight >}}

由上清單確認後，我有一個舊版本的 `1.8.0_172-zulu-8.30.0.1` 可使用，若想固定本機的 JAVA 版本可以設定在 ~/.bash_profile 裡面，但若只是執行此程式時需要使用此版本，可以在指令前方新增指定 JAVA 版本的設定。


{{< highlight shell "lineNos=false" >}}
$ JAVA_HOME=`/usr/libexec/java_home -v 1.8.0_172-zulu-8.30.0.1` sh ./encrypt.sh input=password123 password=CAT

----ENVIRONMENT-----------------

Runtime: Azul Systems, Inc. OpenJDK 64-Bit Server VM 25.172-b01

----ARGUMENTS-------------------

input: password123
password: CAT

----OUTPUT----------------------

U9zgvbxBMVbmn/JVKpIGbqHlnjIFglRJ
{{< / highlight >}}


