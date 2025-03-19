## pikachu

2.![image-20240226224347347](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226224347347.png)

3.![image-20240226224631395](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226224631395.png)

4.![image-20240226230121960](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226230121960.png)

5.![image-20240226231112455](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226231112455.png)

6.先转到后台登入就能看到之前盲打消失的内容![image-20240226231337832](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226231337832.png)

## DVWA

1.low

url直接改

![image-20240226231801828](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226231801828.png)

1.medium

![image-20240226233046852](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240226233046852.png)

1.high白名单只允许四个语言用#注释后的部分不发到后端过滤

![image-20240227222850034](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227222850034.png)

2.low

![image-20240227223034158](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227223034158.png)

2.medium只过滤<script>

![image-20240227223214998](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227223214998.png)

2.high过滤了<script>和其大小写用别的语句

![image-20240227223404883](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227223404883.png)

3.low

![image-20240227223511949](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227223511949.png)

3.medium   Name部分只过滤<script>所以前端改一下name长度限制然后用其他语句或者大小写，双写就可以绕过

![image-20240227224149918](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227224149918.png)

3.high和medium差不多只是大小写也不能用了

![image-20240227224320274](C:/Users/Lynn/AppData/Roaming/Typora/typora-user-images/image-20240227224320274.png)