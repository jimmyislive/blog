---
title: "GoCVE"
date: 2019-11-30
draft: false
tags: ["tech"]
---
Just released a new tool I have been working on: [GoCVE](https://github.com/jimmyislive/gocve)

GoCVE is a simple golang based command line tool to query [CVE](https://cve.mitre.org/) data. 

All you have to do is download the CVE data and insert it into a local database you run (currently sqlite and postgres are supported). The tool helps you to download CVE data and also populate the DB. You can then use GoCVE to conveniently get, search or list CVE data, from the command line.

## Example Usage
To test this, I set up an ubuntu EC2 instance (Ensure the postgres sql client is installed on it) and a RDS instance on AWS.

Log into the EC2 instance and download the gocve tool:

```
ssh -i ~/.ssh/jimmyislive-key-pair.pem ubuntu@xx.xxx.xx.xx
wget https://github.com/jimmyislive/gocve/releases/download/v1.0.0/gocve-linux-amd64
mv gocve-linux-amd64 gocve
chmod +x gocve
export PATH=$PATH:.
```

Check that it works:
```
ubuntu@xx.xxx.xx.xx:~$ gocve help
Gocve is cli tool and rest api server to view CVE details

Usage:
  gocve [flags]
  gocve [command]

Available Commands:
  config      Set the configs for gocve
  db          DB Commands
  get         Get details about a particular CVE
  help        Help about any command
  list        Lists all cve ids
  search      Searches the CVE DB for a pattern
  version     Prints the version of gocve

Flags:
      --config string   Defaults to /home/ubuntu/.gocve/gocve.yaml (default "/home/ubuntu/.gocve/gocve.yaml")
  -h, --help            help for gocve

Use "gocve [command] --help" for more information about a command.
ubuntu@xx.xxx.xx.xx:~$ 
```

Set up a postgres RDS instance and get the connection string to the DB.

```
psql -h database-1.yyyy0xxxxxxx.us-east-1.rds.amazonaws.com -U postgres
create database cvedb;
```

Configure the gocve tool:

```
export GOCVE_PASSWORD=xxxxxxxx
gocve config set-db --dbType postgres --dbHost database-1.yyyy0xxxxxxx.us-east-1.rds.amazonaws.com --dbPort 5432 --dbUser postgres --tableName cve
```

Use it (see [github](https://github.com/jimmyislive/gocve) for more command details):

```
ubuntu@xx.xxx.xx.xx:~$ gocve config show
Using config file: /home/ubuntu/.gocve/gocve.yaml
dbtype:  postgres
dbhost:  database-1.yyyy0xxxxxxx.us-east-1.rds.amazonaws.com
dbname:  cvedb
dbport:  5432
dbuser:  postgres
tablename:  cve
password:  
ubuntu@xx.xxx.xx.xx:~$

ubuntu@xx.xxx.xx.xx:~$ gocve db download
Downloading cve db from https://cve.mitre.org/data/downloads/allitems.csv.gz
CVE DB successfully downloaded.
ubuntu@xx.xxx.xx.xx9:~$ 

ubuntu@xx.xxx.xx.xx:~$ gunzip allitems.csv.gz 
ubuntu@xx.xxx.xx.xx:~$ iconv -f ISO-8859-14 -t UTF-8 allitems.csv > allitems.utf8.csv
ubuntu@xx.xxx.xx.xx:~$ 

ubuntu@xx.xxx.xx.xx:~$ time gocve db populate --fileName allitems.utf8.csv
Using config file: /home/ubuntu/.gocve/gocve.yaml
Skipping first 10 lines of header...
Skipped header:  "CVE Version 20061101",,,,,
Skipped header:  "Date: 20191130",,,,,
Skipped header:  "Name","Status","Description","References","Phase","Votes","Comments"
Skipped header:  "Candidates must be reviewed and accepted by the CVE Editorial Board",,,,,,
Skipped header:  "before they can be added to the official CVE list.  Therefore, these",,,,,,
Skipped header:  "candidates may be modified or even rejected in the future.  They are",,,,,,
Skipped header:  "provided for use by individuals who have a need for an early",,,,,,
Skipped header:  "numbering scheme for items that have not been fully reviewed by",,,,,,
Skipped header:  "the Editorial Board.",,,,,,
Skipped header:  ,,,,,,
Inserting data into DB...
DB Created

real	5m15.671s
user	0m9.373s
sys	0m2.092s
ubuntu@xx.xxx.xx.xx:~$ 
ubuntu@xx.xxx.xx.xx:~$ gocve list | more
Using config file: /home/ubuntu/.gocve/gocve.yaml
CVE-1999-0001 	 ip_input.c in BSD-derived TCP/IP implementations allows remote attackers to cause a denial of servic
CVE-1999-0002 	 Buffer overflow in NFS mountd gives root access to remote attackers, mostly in Linux systems.
CVE-1999-0003 	 Execute commands as root via buffer overflow in Tooltalk database server (rpc.ttdbserverd).
CVE-1999-0004 	 MIME buffer overflow in email clients, e.g. Solaris mailtool and Outlook.
CVE-1999-0005 	 Arbitrary command execution via IMAP buffer overflow in authenticate command.
CVE-1999-0006 	 Buffer overflow in POP servers based on BSD/Qualcomm's qpopper allows remote attackers to gain root 
CVE-1999-0007 	 Information from SSL-encrypted sessions via PKCS #1.



ubuntu@xx.xxx.xx.xx:~$ gocve get CVE-2005-2266
Using config file: /home/ubuntu/.gocve/gocve.yaml
CVE-2005-2266
=============
Status: Candidate

Description: Firefox before 1.0.5 and Mozilla before 1.7.9 allows a child frame to call top.focus and other methods in a parent frame, even when the parent is in a different domain, which violates the same origin policy and allows remote attackers to steal sensitive information such as cookies and passwords from web sites whose child frames do not verify that they are in the same domain as their parents.

Reference: BID:14242   |   URL:http://www.securityfocus.com/bid/14242   |   CONFIRM:http://www.mozilla.org/security/announce/mfsa2005-52.html   |   DEBIAN:DSA-810   |   URL:http://www.debian.org/security/2005/dsa-810   |   FEDORA:FLSA:160202   |   URL:https://bugzilla.redhat.com/bugzilla/show_bug.cgi?id=160202   |   OVAL:oval:org.mitre.oval:def:100107   |   URL:https://oval.cisecurity.org/repository/search/definition/oval%3Aorg.mitre.oval%3Adef%3A100107   |   OVAL:oval:org.mitre.oval:def:10712   |   URL:https://oval.cisecurity.org/repository/search/definition/oval%3Aorg.mitre.oval%3Adef%3A10712   |   OVAL:oval:org.mitre.oval:def:1415   |   URL:https://oval.cisecurity.org/repository/search/definition/oval%3Aorg.mitre.oval%3Adef%3A1415   |   OVAL:oval:org.mitre.oval:def:773   |   URL:https://oval.cisecurity.org/repository/search/definition/oval%3Aorg.mitre.oval%3Adef%3A773   |   REDHAT:RHSA-2005:586   |   URL:http://www.redhat.com/support/errata/RHSA-2005-586.html   |   REDHAT:RHSA-2005:587   |   URL:http://www.redhat.com/support/errata/RHSA-2005-587.html   |   REDHAT:RHSA-2005:601   |   URL:http://www.redhat.com/support/errata/RHSA-2005-601.html   |   SECUNIA:15549   |   URL:http://secunia.com/advisories/15549   |   SECUNIA:15551   |   URL:http://secunia.com/advisories/15551   |   SECUNIA:15553   |   URL:http://secunia.com/advisories/15553   |   SECUNIA:19823   |   URL:http://secunia.com/advisories/19823   |   SUSE:SUSE-SA:2005:045   |   URL:http://www.novell.com/linux/security/advisories/2005_45_mozilla.html   |   SUSE:SUSE-SA:2006:022   |   URL:http://www.novell.com/linux/security/advisories/2006_04_25.html   |   SUSE:SUSE-SR:2005:018   |   URL:http://www.novell.com/linux/security/advisories/2005_18_sr.html   |   VUPEN:ADV-2005-1075   |   URL:http://www.vupen.com/english/advisories/2005/1075   |   XF:mozilla-frame-topfocus-xss(21332)   |   URL:https://exchange.xforce.ibmcloud.com/vulnerabilities/21332

Phase: Assigned (20050713)

Category: None (candidate not yet proposed)

ubuntu@xx.xxx.xx.xx:~$ gocve search CVE-2005-22
Using config file: /home/ubuntu/.gocve/gocve.yaml
CVE-2005-2282
=============
Multiple cross-site scripting (XSS) vulnerabilities in WebEOC before 6.0.2 allow remote attackers to inject arbitrary web script and HTML via unknown vectors.

CVE-2005-2213
=============
Buffer overflow in the mms_interp_header function in mms.c in MMS Ripper before 0.6.4 might allow remote attackers to execute arbitrary code via a file with more than 20 streams.

...
```

Scale this horizontally by installing gocve on each of the boxes you want to query CVE data from. 

You could also use a nightly cron job or some scheduler like Airflow and populate the DB on some regular interval to ensure you always have the latest CVE data.

For more details of usage, see [github](https://github.com/jimmyislive/gocve).

Enjoy !

