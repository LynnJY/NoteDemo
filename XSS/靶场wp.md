## pikachu

2.![image-20240226224347347](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853420.png)

3.![image-20240226224631395](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853421.png)

4.![image-20240226230121960](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853423.png)

5.![image-20240226231112455](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853424.png)

6.先转到后台登入就能看到之前盲打消失的内容![image-20240226231337832](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853425.png)

## DVWA

1.low

url直接改

![image-20240226231801828](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853426.png)

1.medium

![image-20240226233046852](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853427.png)

1.high白名单只允许四个语言用#注释后的部分不发到后端过滤

![image-20240227222850034](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853428.png)

2.low

![image-20240227223034158](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853429.png)

2.medium只过滤<script>

![image-20240227223214998](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853430.png)

2.high过滤了<script>和其大小写用别的语句

![image-20240227223404883](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853431.png)

3.low

![image-20240227223511949](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853432.png)

3.medium   Name部分只过滤<script>所以前端改一下name长度限制然后用其他语句或者大小写，双写就可以绕过

![image-20240227224149918](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853433.png)

3.high和medium差不多只是大小写也不能用了

![image-20240227224320274](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153853434.png)