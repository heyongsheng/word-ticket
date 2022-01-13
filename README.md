<!--
 * @Author: 贺永胜
 * @Date: 2022-01-12 15:29:02
 * @email: 1378431028@qq.com
 * @LastEditors: 贺永胜
 * @LastEditTime: 2022-01-13 13:21:52
 * @Descripttion: 
-->

> 游戏地址： http://game.pkec.net/word-ticket/</br>
> 开发语言：vue</br>
> 运行平台：Chrome</br>
> gitee地址：https://gitee.com/ihope_top/word-ticket</br>
> github地址：https://github.com/heyongsheng/word-ticket</br>
> 游戏已开源，欢迎大家体验
## 游戏规则

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/64e232e5941b4f6c837d8b994dd31235~tplv-k3u1fbpfcp-watermark.image?)

上面是游戏的开始界面，在下方横线处输入abandon可以开始游戏，点击右上角问号可以跳转至本文章，点击音乐可以打开声音

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/917df4667e684159871e1f50bc770789~tplv-k3u1fbpfcp-watermark.image?)

开始游戏后，上方会显示余票和剩余的单词量，余票每秒减一，单词量输对一个就少一个，哪一个先清零都会触发游戏结束，中间部分是我们需要输入的单词，下面是我们输入单词的输入框，输入正确会加载下一个单词。

具体实现思路请查看文章：[vue新春游戏-拼手速抢车票，学习玩乐两不误](https://juejin.cn/post/7052556327389921294/)