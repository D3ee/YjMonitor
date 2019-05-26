YjMonitor
===========
b站 上船风暴监控  
作者自用，更新不频繁，bug 基本没有，但是更新慢，衍生自 https://github.com/yjqiang/bili2.0  

monitor 部分（监控）
------------
1. ctrl.toml 最后那里定义了发送目标房间，结果都会发到指定房间里面；start end 控制监控范围。
1. 由于python性能问题，推荐一台机器监控 4000 左右房间，需要几台机器协同一起监控，之后考虑 golang（flag)。


ctrl 部分
-------------
1. tcp_client.py 是 tcp 接收端，自定义心跳，同时把原数据前面加一个 header 作为数据发送（就像 bilibili 弹幕那样）。
1. req_check.py 负责监控 websocket 服务器的状态，需要管理员权限。 `observers_count` 是在线总计用户数量； `observers_count` 分别统计每个 key 的用户数量（ key 是取了 md5 开头 5 位， 不是原 key ）； `posters_count` 统计所有推送者的状态，数字是该推送者最后一次推送的 unix 时间，若 unix 时间距离当前时间过久，那么说明该推送者可能掉线了； `curr_db` 展示了数据库内 key 的存储，日期为`创建 unix 时间-到期 unix 时间`，这里的 key 同样是 md5 ，如果到期日期为 0 ，那么这个 key 是永久有效。***每运行一次该脚本，都会自动清空过期用户，之后才返回数据。***
1. req_create_key.py 负责让 server 服务器产生新的 key ，需要超管权限。`max_users` 表示该 key 的最大同时使用人数, `available_days` 表示有效的天数，如果为0，那么表示永久有效。
1. req_post_raffle.py 负责向 server 服务器推送 raffle (仅作为示例)，需要管理员权限。
1. global_var.py 里面的 `URL` 是 server 服务器的地址以及端口，控制以上的请求目标。
1. key 文件夹里面的 create_key.py 是单独运行的产生公钥私钥，密钥用于验证身份等。其中 super_admin_privkey.pem 是超管， admin_admin_privkey.pem 是普通管理员。不同 websocket 控制内容有不同的身份控制，状态检查和推送抽奖需要普通管理员，而产生 key 需要超管身份。

server部分（转发 monitor 发送的抽奖信息）
-------------
1. run.py 负责运行。
1. db 负责存储与验证链接 key 。key 在服务器端产生，保存特殊 hash 用于验证客户端的 tcp 连接请求，同时真正的 key 加密之后返回到发出该请求(req_post_raffle.py)的客户端。
1. key 文件夹里面***只存贮公钥***以验证身份等。其中 super_admin_pubkey.pem 是超管， admin_pubkey.pem 是普通管理员。

key
-----------
1. client、server、monitor部分(就是👆那几个部分)都需要 key (这里的 key 指 rsa 的 key)，key 存在***各自***的key文件里面，运行需要两对 key，分别是 super_admin_privkey.pem 与 super_admin_pubkey.pem 、 admin_privkey.pem 与 admin_pubkey.pem 。monitor 部分需要 admin_privkey.pem 用来推送，client 部分需要 super_admin_privkey.pem 和 admin_privkey.pem 用来控制等，server 需要 super_admin_pubkey.pem 和 admin_pubkey.pem 来验证等。