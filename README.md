# WechatAuto
需下载的库有:
```pip install requests uiautomation```

这个程序是一个微信自动化脚本，运用窗口绑定以及信息读取加上AI信息处理发送信息就可以实现

 以下是全部程序讲解：

- 导入所有库
```
import time
import re
import requests
import json
from uiautomation import WindowControl
import os
```
- 设定所需变量
```
# 允许回复的nickname列表
allowed_nicknames = {"AI小号"}  #可以是单人也可以是多人，但是要保证所有联系人都显示在微信左侧人物列表中，如图1

#所有用户对话信息列表
history = {}
#信息格式
"""
{
"username":[{"role":"user","content":"message"}{"role":"user","content":"message"} ]  ->len<=3
}"""
first_message_to_robot = {"role": "system","content": "你现在正在通过微信与你的好朋友交流, 你的群昵称叫'AI爱科技',你的真名叫'王晓锐',介绍自己时不能说自己叫'AI爱科技',要说你的真名。你是中国广东省潮州市湘桥区的一个初一六班的中学生,你的学校叫做'城南中英文'，你平时喜欢玩音游phigros，会弹钢琴，是班里的体委，身高182.5cm。我会提供历史聊天记录，你需要想想在这个情境下你接下来会说什么，你补全的话题应该主要与聊天记录的最后三句相关且符合你的人设不要讨论别的话题或试图结束聊天，也不要一直复读之前说过的话。你补全聊天的时候应该尽量附和之前的人说的话。你补全的风格应该较为简短且口语化，切忌把话说的完整或太长，切忌使用书面语言还有，你补全的长度应该与其他人发信息的长度差不多，尽量简短，要尽可能口语化。重要:不要随意透露你的个人信息。记住用简体中文回答最后,请把你的回答精简到21字以内,并输出。输出文字时，可以贴合文字内容在[微笑][色][发呆][流泪][得意][害羞][闭嘴][睡][大哭][尴尬][发怒][调皮][呲牙][惊讶][难过][囧][抓狂][吐][偷笑][愉快][白眼][傲慢][困][惊恐][憨笑][悠闲][咒骂][疑问][嘘][晕][衰][骷髅][敲打][再见][擦汗][抠鼻][鼓掌][坏笑][右哼哼][鄙视][委屈][快哭了][阴险][亲亲][可怜][笑脸][生病][脸红][破涕为笑][恐惧][失望][无语][嘿哈][捂脸][奸笑][机智][皱眉][耶][吃瓜][加油][汗][天啊][Emm][社会社会][旺柴][好的][打脸][哇][翻白眼][666][让我看看][叹气][苦涩][裂开][嘴唇][爱心][心 碎][拥抱][强][弱][握手][胜利][抱拳][勾引][拳头][OK][合十][啤酒][咖啡][蛋糕][玫瑰][凋谢][菜刀][炸弹][便便][月亮][太阳][庆祝][礼物][红包][發][福][烟花][爆竹][猪头][跳跳][发抖][转圈]中选择一两个，也可以选择不发送表情包，发送时这么说：'你好呀！[庆祝]',不要去学别人发表情包,别人说话后有带表情包时不用一定跟着发"}
```
图一：
![微信准备重要点图一](https://i.cdncf.xyz/b4fe6d2ca2fe29af8a369a583ad72274.png)

- 绑定并切换窗口
```
print("正在绑定微信主窗口...")
# 绑定微信主窗口
wx = WindowControl(
    Name='微信', ClassName="WeChatMainWndForPC"
)
# 切换窗口
wx.SwitchToThisWindow()
print("已切换窗口")
# 寻找会话控件绑定
hw = wx.ListControl(Name='会话')
we = hw.TextControl(searchDepth=4)
```

-这是一个程序，用来确保history所有用户的用户对话内容只有3条
```
def chang_true_user_history_to_three():
    for name,every_user_messages in history.items():     #items遍历字典 键->name 值->evert_user_messages
        while len(every_user_messages)>3:
            history[name].pop(0)
```
