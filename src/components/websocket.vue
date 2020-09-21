<template>
    <div>
        <div id="username-page">
            <div class="username-page-container">
                <h1 class="title">Type your username</h1>
                <form id="usernameForm" name="usernameForm">
                    <div class="form-group">
                        <input type="text" id="name" placeholder="Username" autocomplete="off" class="form-control"/>
                    </div>
                    <div class="form-group">
                        <button type="submit" class="accent username-submit">Start Chatting</button>
                    </div>
                </form>
            </div>
        </div>

        <div id="chat-page" class="hidden">
            <div class="chat-container">
                <div class="chat-header">
                    <h2>Spring WebSocket Chat Demo</h2>
                </div>
                <div class="connecting">
                    Connecting...
                </div>
                <ul id="messageArea">

                </ul>
                <form id="messageForm" name="messageForm">
                    <div class="form-group">
                        <div class="input-group clearfix">
                            <input type="text" id="message" placeholder="Type a message..." autocomplete="off"
                                   class="form-control"/>
                            <button type="submit" class="primary">Send</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>


<script>
    import SockJS from 'sockjs-client';
    import Stomp from 'stompjs';

    export default {
        data() {
            return {
                dataList: []
            };
        },
        mounted: function () {
            this.initWebSocket();
        },
        beforeDestroy: function () {
            // 页面离开时断开连接,清除定时器
            this.disconnect();
            clearInterval(this.timer);
        },
        methods: {
            initWebSocket() {
                this.connection();
                /*let self = this;
                // 断开重连机制,尝试发送消息,捕获异常发生时重连
                this.timer = setInterval(() => {
                    try {
                        self.stompClient.send("test");
                    } catch (err) {
                        console.log("断线了: " + err);
                        self.connection();
                    }
                }, 5000);*/
            },
            removeTab(targetName) {
                console.log(targetName)
            },
            connection() {
                // 建立连接对象
                this.socket = new SockJS('http://192.168.38.65:8000/websocket');//连接服务端提供的通信接口，连接以后才可以订阅广播消息和个人消息
                // 获取STOMP子协议的客户端对
                this.stompClient = Stomp.over(this.socket);
                // 定义客户端的认证信息,按需求配置
                /*var headers = {
                    'Upgrade': 'websocket',
                    'Connection': 'Upgrade'
                    // additional header
                    //'client-id': 'my-client192.168.38.65-id'
                };*/
                // 向服务器发起websocket连接
                this.stompClient.connect({}, (frame) => {
                        console.log('websocket连接成功:' + frame);
                        this.$message('websocket服务器连接成功');
                    /*this.stompClient.subscribe('/topic/chat_msg', (msg) => { // 订阅服务端提供的某个topic
                        console.log(msg.body);  // msg.body存放的是服务端发送给我们的信息
                    });*/
                }, (err) => {
                    // 连接发生错误时的处理函数
                    console.log(err);
                });

            },
            // 断开连接
            disconnect() {
                if (this.stompClient != null) {
                    this.stompClient.disconnect();
                    console.log("Disconnected");
                }
            }
        }
    };

</script>

