---
layout:     post
title:      服务器小脚本尝试
category: blog
description: 服务器维护脚本 tomcat 应用备份操作  数据库备份操作

---

###备份MySQL数据库

```
#!/bin/bash

mysqldump -uroot -proot cswebsite > /root/cswebsite_`date +%Y%m%d%H%M%S`.sql

```


###备份tomcat 应用

```
#!/bin/bash

echo "shutdown tomcat.."

/root/tomcat8/bin/shutdown.sh

echo "begin backup stillone..."

tar -zcvf /root/backup/stillone_`date +%Y%m%d%H%M%S`.tar.gz /root/tomcat8/webapps/ROOT --exclude=Files --exclude=uploadFiles

echo ""
echo ""
echo ""
echo -e "\033[32;49;1m backup stillone done! have fun!! \033[39;49;0m"
echo ""
echo ""
  
```


### 筛选nginx日志访问量IP排名top1000(http 状态码403  请求路径/v2/sms/send)

```
awk '{if($9=="403" && $7=="/v2/sms/send") print $1}' /usr/local/tomcat/nginx-log/api.credan.com.log | sort | uniq -c | sort -nr -k1|head -n 1000
``` 
  
 