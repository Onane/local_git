### **记录**
1. 查找可用的服务器地址：
   1. ping google.cn 
   2. 或者选择下列ip
      - 172.217.215.90
      - 172.253.115.90
      - 142.250.126.90
      - 142.250.10.90
      - 142.250.0.90
   3. https://tool.chinaz.com/speedtest/translate.google.cn
   4. https://github.com/Ponderfly/GoogleTranslateIpCheck
2. 修改本地C:\Windows\System32\drivers\etc 目录下的hosts文件，新增一行
   - {ip地址} translate.googleapis.com
3. 刷新dns，重启浏览器
   - ipconfig /flushdns