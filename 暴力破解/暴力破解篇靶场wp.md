### 思维导图

![image-20240131203323244](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131203323244.png)

### 基于表单的暴力破解

1.抓包

![image-20240131201114361](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201114361.png)

2.发送到intruder后把账户密码这两个地方标记，payloads中写入密码本

![image-20240131201318979](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201318979.png)

![image-20240131201339309](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201339309.png)

开始爆破

![image-20240131201407577](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201407577.png)

### 验证码绕过（on server）

1.抓包

![image-20240131201521378](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201521378.png)

2.发送后标记账户密码验证码改为网站图标的正确验证码

写入密码本

![image-20240131201718074](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201718074.png)

开始爆破

![image-20240131201742613](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201742613.png)

### 验证码绕过（on client）

1.输入正确验证码才能抓到包后，利用该包发送到模块

![image-20240131201933633](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131201933633.png)

照旧写入密码本，爆破

![image-20240131202104236](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131202104236.png)

这里也可以直接禁用JS

![image-20240131202246154](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131202246154.png)

就和第一个一样简单了

### token防爆破

经过几次抓包发现本次token值就是上次服务器发送回来的token值，所以用递归来重定向token（题目默认知道账户名，所以还是两个变量，使用鱼叉是因为第二个变量token是每次都重定向）

1.先抓包

2.写入密码，并定义线程为1，因为每一次循环后重定向，不是单线程token引用就乱了

3.设置重定向

![image-20240131202851694](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131202851694.png)

爆破

![image-20240131202942532](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240131202942532.png)

