最近のRStudioやRStduio ServerではTerminalにアクセスできる。
しかし、なんらかの要因でアクセスできない場合がある。

そんなときは、

- RStudioのToolsメニュー
    - Global Options
        - Terminal
            - Connection
                - Connect with WebSockets

のチェックマークを外してみるといい


参考までに私の環境

R version 3.4.3 (2017-11-30)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: Linux Mint 18.3

RStudio version 1.1.419 

丁度、Guake Terminalを導入した頃だったのもあり、PATHがうまく通ってないのかと思って

- RStudioのToolsメニュー
    - Global Options
        - Terminal
            - Shell
                - New terminals open with: Bash
       
から、BashをCustomにしてPATHを指定してみるも、WebSocketsを使っている限りはうまくいかなかった。



