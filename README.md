[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]



<!-- PROJECT LOGO -->
<br />
<p align="center">
  <a href="vuls.io">
    <img src="https://sys-guard.com/contents/wp-content/uploads/2017/09/Vuls_banner__2.png" alt="Logo" width="160" height="80">
  </a>

  <h3 align="center">Vuls docker stack</h3>

  <p align="center">
    An awesome docker stack to scan and report to both on servers and container!
    <br />
    <a href="https://vuls.io"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="https://usiusi360.github.io/vulsrepo">View Demo</a>
  </p>
</p>



<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About Vuls](#about-vuls)
  * [Built With](#built-with)
* [Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
  * [Installation](#installation)
* [Usage](#usage)



## About Vuls

[![Product Name Screen Shot][product-screenshot]][]

For a system administrator, having to perform security vulnerability analysis and software updates on a daily basis can be a burden. To avoid downtime in production environment, it is common for system administrators to choose not to use the automatic update option provided by their package manager and instead perform updates manually. This leads to the following problems.

    System administrators need to constantly monitor NVD (National Vulnerability Database) or similar databases for new vulnerabilities.
    It might be impossible for the system administrator to monitor all software if there is a large number of installed packages on the server.
    It is expensive to perform analysis to determine the servers affected by new vulnerabilities. The possibility of overlooking a server or two during analysis is there.

Vuls is a tool created to solve the problems listed above. It has the following characteristics.

    Informs users of the vulnerabilities that are related to the system.
    Informs users of the servers that are affected.
    Vulnerability detection is done automatically to prevent any oversight.
    Report is generated on regular basis using CRON or other methods to manage vulnerability.

#### Scan for any vulnerabilities in Linux/FreeBSD Server
Supports major Linux/FreeBSD such as:
Alpine, Ubuntu, Debian, CentOS, Amazon Linux, RHEL, Oracle Linux, SUSE Enterprise Linux and Raspbian, FreeBSD, Cloud, on-premise, Docker

### High quality scan

Vuls uses Multiple vulnerability databases

* [NVD]
* [JVN](Japanese)
* [OVAL]
    * [RedHat]
    * [Debian]
    * [Ubuntu]
    * [SUSE]
    * [OracleLinux]
* [Alpine-secdb]
* [RedHat-SecurityAdvisories]
* [Debian-SecurityBugTracker]
* [Commands](yum, zypper, pkg-audit)
    * [RHSA/ALAS/ELSA/FreeBSD-SA]
* [Exploit-Database]
* [US-CERT]
* [JPCERT]
* [WPVulnDB]
* [Node.js-Security-Working-Group]
* [Ruby-AdvisoryDatabase]
* [Safety-DB](Python)
* [PHP-SecurityAdvisoriesDatabase]
* [RustSec-AdvisoryDatabase]
* [Changelog]



<!-- GETTING STARTED -->
## Getting Started



### Prerequisites


## Config.toml

### Slack
```
[slack]
hookURL      = "https://hooks.slack.com/services/XXXXXXXXX/XXXXXXXXXX/XXXXXXXXXXXXXXXXXXX"
channel      = "#vuls_results"
authUser     = "vuls report"

[servers]
[servers.k8s-master1]
host        = "192.168.1.21"
port        = "22"
user        = "root"
keyPath     = "/root/.ssh/id_rsa"
```

### Usage

### Populate the DB

```
# sudo docker-compose exec db bash
# mysql -u root -p password
create database exploitdb;
create database gost;
create database oval;
exit
```

```
# (CVE)
for i in `seq 2002 $(date +"%Y")`; do  sudo docker-compose run --rm cve fetchnvd -dbtype=mysql -dbpath="root:password@tcp(db:3306)/cve?parseTime=true" -years $i;   done

# (JVN)
for i in `seq 1998 $(date +"%Y")`; do sudo docker-compose run --rm cve fetchjvn -dbtype=mysql -dbpath="root:password@tcp(db:3306)/cve?parseTime=true" -years $i; done

# OVAL (RHEL)
sudo docker-compose run --rm oval fetch-redhat -dbtype=mysql -dbpath="root:password@tcp(db:3306)/oval?parseTime=true" 6 7

## gost (RHEL)
sudo docker-compose run --rm gost fetch redhat --dbtype=mysql --dbpath="root:password@tcp(db:3306)/gost?parseTime=true"

## exploitdb
sudo docker-compose run --rm go-exploitdb fetch exploitdb --dbtype=mysql --dbpath="root:password@tcp(db:3306)/exploitdb?parseTime=true"
```

### DB update

```
# (CVE)
docker-compose run --rm cve fetchnvd -dbtype=mysql -dbpath="root:password@tcp(db:3306)/cve?parseTime=true" -latest
# (JVN)
docker-compose run --rm cve fetchjvn -dbtype=mysql -dbpath="root:password@tcp(db:3306)/cve?parseTime=true" -latest
# OVAL (RHEL)
docker-compose run --rm oval fetch-redhat -dbtype=mysql -dbpath="root:password@tcp(db:3306)/oval?parseTime=true" 6 7
## gost (RHEL)
docker-compose run --rm gost fetch redhat --dbtype=mysql --dbpath="root:password@tcp(db:3306)/gost?parseTime=true"
## expliotdb
docker-compose run --rm go-exploitdb fetch exploitdb --dbtype=mysql --dbpath="root:password@tcp(db:3306)/exploitdb?parseTime=true"
```

### Test theconfigf
```
docker-compose run --rm vuls configtest -config=./config.toml
```

### Start a Scan
```
docker-compose run --rm vuls scan -config=./config.toml
```

### Report 
```
docker-compose run --rm vuls report -ignore-unfixed -lang ja -config=./config.toml -cvedb-type=mysql -cvedb-url="root:password@tcp(db:3306)/cve?parseTime=true" -ovaldb-type=mysql -ovaldb-url="root:password@tcp(db:3306)/oval?parseTime=true" -gostdb-type=mysql -gostdb-url="root:password@tcp(db:3306)/gost?parseTime=true" -exploitdb-type=mysql -exploitdb-url="root:password@tcp(db:3306)/exploitdb?parseTime=true"
```

### Use TUI to explore the report
```
docker-compose run --rm vuls tui -ignore-unfixed -config=./config.toml -cvedb-type=mysql -cvedb-url="root:password@tcp(db:3306)/cve?parseTime=true" 
-ovaldb-type=mysql -ovaldb-url="root:password@tcp(db:3306)/oval?parseTime=true" -gostdb-type=mysql -gostdb-url="root:password@tcp(db:3306)/gost?parseTime=true" -exploitdb-type=mysql -exploitdb-url="root:password@tcp(db:3306)/exploitdb?parseTime=true"
```

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/einyx/helm-vuls.svg?style=flat-square
[contributors-url]: https://github.com/einyx/helm-vuls/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/einyx/helm-vuls.svg?style=flat-square
[forks-url]: https://github.com/einyx/helm-vuls/network/members
[stars-shield]: https://img.shields.io/github/stars/einyx/helm-vuls.svg?style=flat-square
[stars-url]: https://github.com/einyx/helm-vuls/stargazers
[issues-shield]: https://img.shields.io/github/issues/einyx/helm-vuls.svg?style=flat-square
[issues-url]: https://github.com/einyx/helm-vuls/issues
[license-shield]: https://img.shields.io/github/license/einyx/helm-vuls.svg?style=flat-square
[license-url]: https://github.com/einyx/helm-vuls/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=flat-square&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/alessiogarofalo
[product-screenshot]: https://raw.githubusercontent.com/usiusi360/vulsrepo/master/gallery/demo.gif

