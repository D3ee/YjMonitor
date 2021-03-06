# monitor 自定义 server 作为中转模式
## 监控弹幕房间
1. conf/ctrl.toml 中的 `post_office` 定义了 server 的服务器地址（方法是直接 POST，这是 server 自定义中转服务器模式）；START 与 END 控制监控房间的范围。
1. 使用方法：使用 server 作为中转，设置 ctrl.toml 里面的 `post_office` 为有效值, key 文件夹内只需要 admin_privkey.pem 即可，为了防止恶意推送，这个 key 用于验证推送者身份的正确性。
1. 由于python性能问题，推荐 4000 左右个房间/机器，需要几台机器协同一起监控，之后考虑golang（flag)。
1. 运行 `run.py`。
## 补足部分（暴力轮询）
1. conf/ctrl.toml 中的 `post_office` 定义了 server 的服务器地址（方法是直接 POST，这是 server 自定义中转服务器模式）；`yjmonitor_tcp_addr` 和 `yjmonitor_tcp_key` 需要填写好，用来去除重复推送。
1. 使用方法：如果使用 server 作为中转，设置 ctrl.toml 里面的 `post_office` 为有效值, key 文件夹内只需要 admin_privkey.pem 即可，为了防止恶意推送，这个 key 用于验证推送者身份的正确性。
1. 运行 `run_polling.py`。
## 实时获取更新（几乎实时）
1. conf/ctrl.toml 中的 `post_office` 定义了 server 的服务器地址（方法是直接 POST，这是 server 自定义中转服务器模式）。
1. 使用方法：使用 server 作为中转，设置 ctrl.toml 里面的 `post_office` 为有效值, key 文件夹内只需要 admin_privkey.pem 即可，为了防止恶意推送，这个 key 用于验证推送者身份的正确性。
1. 运行 `run_realtime.py`。