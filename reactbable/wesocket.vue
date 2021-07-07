<template>
  <div>

  </div>
</template>

<script>
export default {
    data(){
        return {
            webstock: null,
            send_stationId: "0637",
            socket_ip: '192.168.25.130',
            socket_port: '8080'
        }
    },
    methods:{

        initLoading(){
            if (typeof WebSocket !== "undefined") {
                 if (this.webstock !== null) {
                    this.webstock.close(); // 关闭websocket
                    // console.log("关闭socket！");
                }
              // console.log("您的浏览器支持Websocket通信协议");
              this.initWebSocket(); // 开启WebSocket长连接
            } else {
              this.$alert("您的浏览器不支持Websocket通信协议，请使用Chrome或者Firefox浏览器！","温馨提示：");
            }
        },
        /**
         * webstocket 开启长连接
         */
        initWebSocket() {
            // 页面刚进入时开启长连接
            const http_ip = "ws://" + this.socket_ip + ":" + this.socket_port;
            this.webstock = new WebSocket(http_ip);
            // console.log("ip:",http_ip);
            this.webstock.onopen = this.webstockonopen;
            this.webstock.onmessage = this.webstockonmessage;
            this.webstock.onclose = this.webstockclose;
            this.webstock.onerror = this.webstockonerror;
        },

        /**
         * webstock连接成功
         */
        webstockonopen(onopen) {
            // console.log("websocket open 连接成功", onopen);
            // 定义全局变量, 发送协议参数
            this.send_variable = "0x01000101" + "/" + this.send_stationId;
            this.webstock.send(JSON.stringify(this.send_variable));
        },

        /**
         * webstock接收消息
         */
        webstockonmessage(message) {
            // console.log( '返回的message', message);
            let list_arry = JSON.parse(message.data);
            // console.log( 'list_arry', list_arry);
            let arr = list_arry;
            console.log("arr",arr);
            //处理返回的数据 
        },

        /**
         * webstock关闭
         */
        webstockclose() {
            // console.log("websocket close"); // 监听关闭
        },

        /**
         * webstock出错
         */
        webstockonerror() {
            // console.log("websocket error");
            this.webstockclose(); // 先关闭连接
            this.initWebSocket(); // 开启WebSocket长连接
        }
    },
    created(){
        this.initLoading()
    },
    mounted(){},
    destroyed() {
        // 页面销毁时关闭长连接
        this.$destroy();
        if (this.webstock !== null) {
        this.webstock.close(); // 关闭websocket
        }
    }
}
</script>