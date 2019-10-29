# What
**发布-订阅者模式或机制，定义了一种依赖关系**
# Why
**为了解决主体对象与观察者功能之间的耦合**

>主体对象与观察者就好比卫星，飞机，和中转站。
中转站与天空中的飞机要想知道某架飞机的运行情况，各地中转站都要在卫星上注册这架飞机的信息，以便接收到这架飞机的信息。于是每当飞机到达一个地方时，都会向卫星发出位置信息，然后卫星又将信息广播到已经订阅过这架飞机的中转站。这样每个中转站便可以接收飞机的信息并做相应的处理来避免飞机事故发生。

# How
  **创建一个观察者**
  * 观察者应该有一个消息容器，和三个方法，分别是订阅消息方法、取消订阅的消息方法、发送订阅的消息方法
```
var Observer=(function(){
    // 防止消息队列暴漏而被篡改故将消息容器作为静态私有变量报错
    var _messgaes={};
    return {
        // 注册消息接口
        regist:function(type,fn){
            // 如果此消息不存在则应该创建一个该消息类型
            if(typeof _messages[type]==='undefined'){
                _messages[type]=[fn];
            // 如果此消息存在
            }else{
                // 将动作方法推入该消息对应的动作执行序列中
                _messages[type].push();
            }
        },
        // 发布信息接口
        fire:function(type,args){
            // 如果该消息没有被注册，则返回
            if(!_messages[type])return;
            // 定义消息信息
            var events={
                type:type,
                args:args||{}
            },
            i=0,
            len=_messages[type].length;
            // 遍历消息动作
            for(;len;i++){
                // 依次执行注册的消息对应的动作序列
                _messages[type][i].call(this,events)
            }
        },
        // 移除信息接口
        remove:function(type.fn){
            // 如果消息动作队列存在
            if(_messages[type] instanceof Array){
                // 从最后一个消息动作遍历
                var i=_messages[type].length - 1;
                for(;i>=0;i--){
                    // 如果存在该动作则在消息动作序列中移除相应动作
                    _messages[type][i]===fn&&_messages[type].slice(i,1);
                }
            }
        }
    }
})
```
# Case
* 解决不同闭包模块相互调用
```
function $(id){
    return document.getElementById(id);
}
// 工程师A
(function(){
    // 追加一则消息
    function addMsgItem(e){
        var text=e.args.text,               // 获取消息中用户添加的文本内容
        ul=$('msg'),                        // 留言容器元素
        li=document.createElement('li'),    // 创建内容容器元素
        span=document.createElement('span');// 删除按钮
        li.innerHTML=text;                  // 写入评论
        // 关闭按钮
        span.onclick=function(){
            ul.removeChild(li);             //移除留言
            // 发布删除留言信息
            Observer.fire('removeCommentMessage',{
                num:-1
            })
        }
        // 添加删除按钮
        li.appendChild(span);
        // 添加留言节点
        ul.appendChild(li);
    }
    // 注册添加评论信息
    Observer.regist('addCommentMessage',addMsgItem);
})()

// 工程师B
(function(){
    // 更改用户消息数目
    function changeMsgNum(e){
        // 获取需要增加的用户消息数目
        var num=e.args.num;
        // 增加用户消息数目并写入页面中
        $('msg_num').innerHTML=parentInt($('msg_num').innerHTML)+num;
        // 注册添加评论信息
        Observer.regist('addCommentMessage',changeMsgNum).regist('removecommentMessage',changeMsgNu);

    }
})()
// 工程师C
(function(){
    // 用户点击提交按钮
    $('user_submit').onclick=function(){
        // 获取用户输入框中的信息
        var text=$('user_input');
        // 如果消息为空则提交失败
        if(text.value === ''){
            return;
        }
        // 发布一则评论消息
        Observer.fire('addCommentMessage',{
            text:text.value,    // 消息评论内容
            num:1               // 消息评论输入
        })
        text.value = '';
    }
})()
```
