# MyRocks_zh_doc
MyRocks 文档翻译

# 内容体系说明
* [一、概览](#概览)
* [二、事务](#事务)
* [三、备份](#备份)
* [四、性能调优](#性能调优)
* [五、监控](#监控)
* [六、迁移](#迁移)
* [七、内部结构](#内部结构)

# 原文出自

* FaceBook MyRocks Wiki : https://github.com/facebook/mysql-5.6/wiki





## 项目说明
* 支持 HTTP 协议 及 TCP 协议
* 支持报警媒介: sms / wechat / email / all(三者合并) (注:微信报警需要在微信公众官方平台配置完成才可以正常使用微信)
* 支持 用户与组: 在 config.py 配置文件中指定usermap,groupmap,方便权限控制
* 支持 群组: boss kanzhun dianzhang ops rcd bigdata search 
* 支持 用户 / 组级别报警
* 支持 中文 / 英文 发送
* 支持调用方式: HTTP TCP(Socket)
          (1)HTTP : GET方式调用
             用户报警:
             http://alarm.bosszhipin.com/useralarm/?user=zhangshuao&media=all&subject=sub&message=info
             http://alarm.bosszhipin.com/useralarm/?user=zhangshuao&media=sms&subject=sub&message=info
             http://alarm.bosszhipin.com/useralarm/?user=zhangshuao&media=wechat&subject=sub&message=info
             http://alarm.bosszhipin.com/useralarm/?user=zhangshuao&media=mail&subject=sub&message=info
             组报警: 
             http://alarm.bosszhipin.com/groupalarm/?group=ops&media=all&subject=sub&message=info 
             http://alarm.bosszhipin.com/groupalarm/?group=ops&media=sms&subject=sub&message=info 
             http://alarm.bosszhipin.com/groupalarm/?group=ops&media=wechat&subject=sub&message=info 
             http://alarm.bosszhipin.com/groupalarm/?group=ops&media=mail&subject=sub&message=info 
             访问:浏览器访问,程序访问,curl访问
          (2)HTTP : POST方式调用
             用户报警:
             curl -d "user=zhangshuao&media=all&subject=sub&message=info" "http://alarm.bosszhipin.com/useralarm/"
             curl -d "user=zhangshuao&media=sms&subject=sub&message=info" "http://alarm.bosszhipin.com/useralarm/"
             curl -d "user=zhangshuao&media=wechat&subject=sub&message=info" "http://alarm.bosszhipin.com/useralarm/"
             curl -d "user=zhangshuao&media=mail&subject=sub&message=info" "http://alarm.bosszhipin.com/useralarm/"
             组报警: 
             curl -d "group=ops&media=all&subject=sub&message=info" "http://alarm.bosszhipin.com/groupalarm/" 
             curl -d "group=ops&media=sms&subject=sub&message=info" "http://alarm.bosszhipin.com/groupalarm/"
             curl -d "group=ops&media=wechat&subject=sub&message=info" "http://alarm.bosszhipin.com/groupalarm/"
             curl -d "group=ops&media=mail&subject=sub&message=info" "http://alarm.bosszhipin.com/groupalarm/"
             访问: 程序访问,curl访问            
          (3)TCP  : Socket方式调用 (数据传递规则,否则报警不生效)
             用户报警:
             数据发送规则:
                 data = "user$$username$$media$$subject$$message"
             组报警:
             数据发送规则:
                 data = "group$$ops$$media$$subject$$message"                 
          (4)邮件附件发送: HTTP POST方式调用
             用户发送附件:
             curl -F "file=@/opt/a.txt" -F "user=zhangshuao" -F "media=mail" -F "subject=sub" -F "message=info"  "http://alarm.bosszhipin.com/mail/user/attach"
             用户组发送附件:
             curl -F "file=@/opt/a.txt" -F "group=ops" -F "media=mail" -F "subject=sub" -F "message=info"  "http://alarm.bosszhipin.com/mail/group/attach"
             必须使用 file=@/path全路径      
             例如: 发送给用户zhangshuao, 附件为:/usr/local/report/20170101.xls
             curl -F "file=@/usr/local/report/20170101.xls" -F "user=zhangshuao" -F "media=mail" -F "subject=sub" -F "message=info"  "http://alarm.bosszhipin.com/mail/user/attach"             
          **注意:
          (1)必须支持config配置中的usermap和groupmap,必须media参数支持sms/wechat/mail/all,否则报警认证不通过.**
          **(2)邮件附件发送:必须支持config配置中的usermap和groupmap,必须media媒介类型为mail,并且只能发送单个附件,附件不要超过20M,否则邮件发送附件不成功.**      
          Socket客户端调用方式:
          代码详见: alarmserver/socket/TCPClient.py
          
* 文件路径说明:
          配置文件: config.py
          日志文件: 
                   alarm_media.log (报警媒介发送状态日志)
                   alarm_http.log  (http请求内容日志)
                   alarm_tcp.log   (tcp请求内容日志)
          
## 安装说明:
* 需要 Python2.7 环境
* 需要 Django 1.7.14环境 
* 使用 uwsgi 启动 Django 项目
* 需要 Django 整合 Nginx配置,使用nginx upstream代理alarm服务(HTTP),使用tcp 代理 alarmtcpserver服务(TCP)
* 使用 Supervisor 来管理进程,防止服务挂掉.
* 安装说明详见: help.txt (生产推荐第二种安装方式)
* http监听端口:9000   
  tcp server监听端口:9001

## 启动说明:
* 详见: start_server.sh 脚本

