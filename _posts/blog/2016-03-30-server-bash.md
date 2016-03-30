---
layout:     post
title:      服务器小脚本尝试
category: blog
description: 服务器维护脚本 tomcat 应用备份操作  数据库备份操作

---

###备份MySQL数据库

  #!/bin/bash

  mysqldump -uroot -proot cswebsite > /root/cswebsite_`date +%Y%m%d%H%M%S`.sql



###备份tomcat 应用

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
  
  
  
 