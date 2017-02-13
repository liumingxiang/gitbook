### vscode配置

##### php

###### 配置php的debug

```

1.安装wampserver,我安装的是2.5 版本，安装步骤自行百度。

2.确保apache的80 端口未占用，如果占用了修改httpd.conf 配置文件下的端口号

3.修改php.ini（wamp\bin\apache\apache2.4.9\bin 这个文件夹下的） 文件开启debug，

修改以下两项：

xdebug.remote_enable = on

xdebug.remote_autostart=on
下面附录ini文件修改

4.如果vscode报cannot validate the php file. the php program was not found....
那就去修改用户配置php的默认设置进行替换  "php.validate.executablePath": "C:/wamp64/bin/php/php5.6.25/php.exe"
5.配置debug ctrl+shift+D 开启debug模式,选择php调试，设置完这些之后重启apache服务器
6.launch.json 不需要进行额外配置
7.在需要的地方打上断点 F5 启动，在浏览器里输入地址，vscode 自动会停在断点处
```



```ini
//C:\wamp64\bin\apache\apache2.4.23\bin\php.ini 配置位置
[xdebug]
zend_extension ="c:/wamp64/bin/php/php5.6.25/zend_ext/php_xdebug-2.4.1-5.6-vc11-x86_64.dll"
xdebug.remote_enable = on
xdebug.remote_autostart= on
xdebug.profiler_enable = off
xdebug.profiler_enable_trigger = off
xdebug.profiler_output_name = cachegrind.out.%t.%p
xdebug.profiler_output_dir ="c:/wamp64/tmp"
xdebug.show_local_vars=0
```



```json
//launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9000
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 9000
        }
    ]
}
```

