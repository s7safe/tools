2025年开始了，我要重构此项目，在之前的项目中，该方案给我带来了几千块的收益，虽然是自动化，但是想法还是过于幼稚，希望今年重构一个更高效的自动化。


# 总结赏金技巧和工作技巧

```
本列表，收集一些在服务器上运行的一些工具，组件自动化，服务器长期挂跑项目 欢迎提issues，来丰富工作流程，比如自己挖洞时候的一些简易流程，工具+调用命令，
在我的日常渗透中，我发现我重复调用几个工具，但是不同的调用组合渗透的工作流程，有时候调用命令会忘记，所以有了这个列表，来达到帮助我记忆一些流程命令的文档，未来还会细化过程，脚本小子福音了

其中有些连接和笔记从17年我的笔记所摘取，有错误请提醒，我修改一下，有些技巧也很老了，现在信息收集分为主动化和被动化，主动化更偏向于发现新资产，被动化稍微弱点。
```

**//子域名获取**

```
subdomain3 https://github.com/yanxiu0614/subdomain3/blob/master/README_ZH.md 

subfinder  https://github.com/projectdiscovery/subfinder 

ksubdomain  https://github.com/knownsec/ksubdomain

```

通过RapidDNS查询子域名

```
curl -s "https://rapiddns.io/subdomain/$1?full=1" | grep -oP '_blank">\K[^<]*' | grep \* |sort -u
```

证书透明收集子域名

```
证书透明度：
echo 'https://bl669.com' | ./httpx -tls-probe -json -silent | jq -r '.["tls-grab"]'.dns_names  >> a.txt
```

使用工具提取证书的域名；https://github.com/cheetz/sslScrape



# 一键调用subfinder+ksubdomain+httpx 强强联合从域名发现到域名验证到获取域名标题、状态码以及响应大小

```
./subfinder -d mrxn.net -silent|sudo ./ksubdomain -verify -skip-wild -silent|./httpx -title -content-length -status-code -o mrxn.net.txt
```

https://github.com/Mr-xn/subdomain_shell/blob/master/ubuntu/run.sh

这样接入二次扫描好一点 

```
subfinder -d shopifykloud.com -silent|ksubdomain -verify -skip-wild -silent|httpx -o shopifykloud.com.txt

```

**DNS枚举**

```
https://github.com/projectdiscovery/dnsx
```


**端口扫描**

```
https://github.com/projectdiscovery/naabu
```


**//存活验证**

```
httpx  https://github.com/projectdiscovery/httpx  
```


**批量目录扫描**

```
https://github.com/H4ckForJob/dirmap
```

命令行调用命令

```
subfinder -silent -dL domain.txt | dnsx -silent | naabu -top-ports 1000 -silent| httpx -title -tech-detect -status-code

subfinder -d domain.net -silent | ksubdomain e --stdin --silent | naabu -top-ports 1000 -silent

subfinder -silent -dL domain.txt | dnsx -silent | httpx -o output.txt

subfinder -d domain.com -silent

subfinder -silent -dL domain.txt  |  dnsx -silent | httpx -mc 200 -timeout 30 -o output.txt 
```



**//去重对比**

```
anew   https://github.com/tomnomnom/anew  

```

gf  https://github.com/tomnomnom/gf      GF分步安装教程；https://doepichack.com/gf-tool-step-by-step-guide/

```
go install github.com/tomnomnom/gf@latest
```



**//获取子域名，对比文件，验证存活，达到监听新资产的目的**

```
subfinder -silent -dL domain.txt | anew domians.txt | httpx -title -tech-detect -status-code 
```

**视觉侦查**
https://github.com/sensepost/gowitness

```
gowitness file <urlslist.txt>
```

**网站历史URL获取**

```
**gau**   https://github.com/lc/gau  

**hakrawler** https://github.com/hakluke/hakrawler  

**waybackurls** https://github.com/tomnomnom/waybackurls

**gospider** https://github.com/jaeles-project/gospider
```

```
爬虫获取链接，运行20个站点每个站点10个爬虫
gospider -S domain.txt -o output.txt -c 10 -d 1 -t 20

通过爬虫获取链接包含子域名
gospider -s "https://google.com/" -o output -c 10 -d 1 --other-source --include-subs

对文件进行处理过滤出js文件
gospider -S urls.txt | grep js | tee -a js-urls.txt
```



**针对单一网站与文件，调用命令进行打点**

```
echo domain.com  | gau  --blacklist  png,jpg,gif,html,eot,svg,woff,woff2  | httpx -title -tech-detect -status-code

cat domian.txt  | gau  --blacklist  png,jpg,gif,html,eot,svg,woff,woff2  | httpx -title -tech-detect -status-code 

此命令也获取了标题，直接操作httpx即可衔接下一个命令运行工具
```

**针对JS进行提取敏感字段**
https://github.com/machinexa2/Nemesis

**或许你需要一些命令**

```
cat livejs.txt | grep dev
cat livejs.txt | grep app
cat livejs.txt | grep static
```

提取器

提取JS命令  https://github.com/tomnomnom/fff

```
cat jslist.txt | fff | grep 200 | cut -d “ “ -f1 | tee livejs.txt
```

**目录FUZZ扫描**
https://github.com/ffuf/ffuf

对UEL进行提取
https://github.com/tomnomnom/gf

```
cat subdomains.txt | waybackurls | gf xss | qsreplace | tee xss.txt
```




**XRAY 命令行批量扫描**

针对单个域名进行子域名爆破，使用GAU提取链接，httpx验证存活，推送到XRAY进行爬虫扫描。

```
subfinder -d domain.com -silent | gau  --blacklist  png,jpg,gif,html,eot,svg,woff,woff2  | httpx -o 200.txt

for i in $(cat 200.txt);do echo "xray scanning $i" ; ./xray webscan --browser-crawler  $i --html-output vuln.html; done
```

**XSS扫描**

https://github.com/hahwul/dalfox

```
 cat url.txt | hakrawler -subs |grep -v key | grep key |grep -v google | grep = | dalfox pipe --silence --skip-bav 
```

```
echo "http://m.client.10010.com" | gau | egrep -v '(.css|.png|.jpeg|.jpg|.svg|.gif|.wolf)' | while read url; do vars=$(curl -s $url | grep -Eo "var [a-zA-Z0-9]+" | sed -e 's,'var','"$url"?',g' -e 's/ //g' | grep -v '.js' | sed 's/.*/&=xss/g'); echo -e "\e[1;33m$url\n\e[1;32m$vars";done | tee 10010.out
```

来源；https://www.t00ls.com/viewthread.php?tid=63173&highlight=XSS



一键查找任意文件读取Credit: @Alra3ees , i just changed it a bit

```
 subfinder -silent -dL domain.txt |  dnsx -silent | naabu -top-ports 1000 -silent| gau |  gf lfi |  httpx  -path lfi_wordlist.txt -threads 100 -random-agent -x GET,POST  -tech-detect -status-code  -follow-redirects -mc 200 -mr "root:[x*]:0:0:"
```


隐藏参数枚举
https://github.com/s0md3v/Arjun

```
单目标扫描
arjun -u https://api.example.com/endpoint
单目标扫描输出
arjun -u https://api.example.com/endpoint -oJ result.json
多目标扫描
arjun -i targets.txt

指定方法
arjun -u https://api.example.com/endpoint -m POST

```

一行代码组成的XSS扫描器（适合无WAF场景）【单论反射XSS，XRAY可封神】

```
echo url | subfinder -silent | waybackurls | grep "=" | grep -Ev ".(jpeg|jpg|png|svg|gif|ico|js|css|txt|pdf|woff|woff2|eot|ttf|tif|tiff)" | sed -e 's/=[^?\|&amp;]*/=/g' -e 's/=/=xssspayload/g' | sort -u | httpx -silent -probe -ms "xsspayload" 
```


XRAY自动化查找单漏洞

```
xargs -a urls.txt -I@ sh -c './xray_linux_amd64 webscan --plugins cmd-injection,sqldet,xss --url "@" --html-output vuln.html' 

.\xray.exe webscan --listen 127.0.0.1:7777  --plugins sqldet,brute-force --html-output xray-testphhjp.html

```

使用扫描器nuclei扫描安卓漏洞

```
find . -iname "*.apk" -exec apktool d -o {}_out {} \;
echo  vph3.apk_out | nuclei -t mobile-nuclei-templates/

nuclei -target "android testing"/allapks/ -t /mobile-nuclei-templates/


echo /output_apktool/ | nuclei -t Keys/

使用POC
https://github.com/optiv/mobile-nuclei-templates
```

文件夹下文件整理

```
dir/b>E:\鱼1.xls"
dir/b>C:\Users\1均\Desktop\HW\HW漏洞列表.xls"
```

BURP不抓火狐的包

```
火狐地址搜索

about:config

network.captive-portal-service

把true改成fale
```

index of 打包

```
wget -r --no-pare target.com/dir
```

渗透测试中的资产获取

```
wget https://raw.githubusercontent.com/modood/Administrative-divisions-of-China/master/dist/pcas.json
cat pcas.json |jq '."北京市"'>out.txt        #获取北京市所有辖区、街道信息cat pcas.json |jq '."北京市"."市辖区"'>out.txt

cat pcas.json |jq '."北京市"."市辖区"."东城区"'>out.txt      #获取北京市东城区所有街道信息



```



有时候文件太大,想先确认一下文件结构和部分内容,这时可以使用 remotezip,直接列出远程 zip 文件的内容，而无需完全下载,甚至可以远程解压,仅下载部分内容

```
pip3 install remotezip
 remotezip -l "http://site/bigfile.zip"#列出远程zip文件的内容
 remotezip "http://site/bigfile.zip""file.txt"#从远程zip⽂件解压出file.txt
```



整理字典时，推荐用linux下的工具快速合并和去重

```
cat file1.txt file2.txt fileN.txt >out.txt 
sort out.txt |uniq >out2.txt
```



docker 快速安装AWVS扫描器，配合批量添加脚本，在市级HVV上抢占一些高危漏洞点

```
#  pull 拉取下载镜像
docker pull secfa/docker-awvs

#  将Docker的3443端口映射到物理机的 13443端口
docker run -it -d -p 13443:3443 secfa/docker-awvs

# 容器的相关信息
awvs13 username: admin@admin.com
awvs13 password: Admin123
```



