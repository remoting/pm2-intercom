# pm2-intercom
 原版插件有Bug，这个是修复版本


# 测试用例
~~~

function onAppCache(packet) {
    console.log(packet, process.env.INSTANCE_ID)
}
function startMaster() {
    setInterval(() => {
        process.send({
            topic: 'process:msg:cache',
            data: JSON.stringify({ "a": "a" })
        });
    }, 1000)
}
function startWorker() {

}
function start() {
    process.on('message', function (packet) {
        onAppCache(packet);
    });
    if (process.env.INSTANCE_ID !== undefined) {
        // pm2 cluster mode 
        if (process.env.INSTANCE_ID === "0") {
            console.log("-----------master--begin", process.pid, process.env.INSTANCE_ID)
            startMaster()
            console.log("-----------master--end")
        } else {
            console.log("-----------worker--begin", process.pid, process.env.INSTANCE_ID)
            startWorker()
            console.log("-----------worker--end")
        }
    } else {
        console.log(process)
        // 单进程启动模式
        startMaster()
        startWorker()
    }
}
// 程序入口在这里
start()



module.exports = {
    apps: [ {
        name: "server",
        exec_mode: "cluster",
        instances: 3,
        instance_var: "INSTANCE_ID",
        //error_file: logPath + "err.log",//错误输出日志
        //out_file: logPath + "out.log", //日志
        script: "./index.js",
        //log_date_format: "YYYY-MM-DD HH:mm:ss.SSS "
    }]
}


pm2-runtime start ecosystem.config.js
~~~