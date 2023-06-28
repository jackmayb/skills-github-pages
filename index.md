---
title: Welcome to my blog
---

Rapid Discovery Methodology for Internal Pentest

nmap -sn -T4 --max-retries=1 --max-rtt-timeout=200ms 10.0-255.0-255.1,254 172.16-31.0-255.1,254 192.168.0-255.1,254 -oA segment-disco

cat segment-disco.gnmap | grep Up | sort -uV | cut -d “ “ -f 2 | cut -d “.” -f 1-3 | sed ‘s/$/.0\/24/’ > livesegments

nmap -T4 --max-rtt-timeout=200ms --host-timeout=5m --open -p 22,80,139,443,445,1099,1433,2001,2010,2049,2901,3306,3389,4447,4848,5432,5433,5555,5900,5901,6101,7001,7002,8002,8004,8008,8080,8089,8300,8443,8500,8501,8980,9001,9043,9060,9200,9990,9999,10000 --stats-every=10s

for i in $(cat scanports); do cat profile.gnmap | grep $i/open | cut -d “ “ -f 2 > $i-hosts; done

msfconsole -q -n -x “use auxiliary/scanner/smb/smb_ms17_010; set ConnectTimeout 1; set DCERPC::ReadTimeout 1; set threads 24; set RHOSTS file:/root/client/445-hosts ; run; exit;” -o /root/client/ms17-010

for i in {80,443,8080,8443}; do msfconsole -q -n -x “use auxiliary/scanner/http/title; set ConnectTimeout 1; set rhosts file:/root/q4-2018/$i-hosts ; set rport $i; set store_notes false; set threads 64; run; exit;” -o /root/q4-2018/http-title-$i; done

grep -i tomcat http-title-8080 | cut -d : -f 1 | cut -d “[” -f 3 | sort -Vu > /root/client/tomcat-8080 grep -i tomcat http-title-8443 | cut -d : -f 1 | cut -d “[” -f 3 | sort -u > /root/client/tomcat-8443

grep -i jenkins http-title-8080 | cut -d : -f 1 | cut -d “[” -f 3 | sort -u > /root/client/jenkins-8080 grep -i jenkins http-title-8443 | cut -d : -f 1 | cut -d “[” -f 3 | sort -Vu > /root/client/jenkins-8443