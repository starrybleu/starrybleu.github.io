---
layout: post
title: 삽질
comments: true
---

kt ucloud에 VPN 서버를 구축할 목적으로 CentOS7 머신을 생성하였는데 생성 후 hostname이 길다고 해서 hostname을 바꿔 준 이후 yum 이 repository 관련으로 말썽을 부렸다. (`# hostnamectl set-hostname <hostname>`)

`there are no enabled repos` yum 으로 update, install을 하려고 하면 이 메시지가 떠서 잘 안 되었는데, 그럴 경우 DNS 설정(`/etc/resolv.conf`)을 확인하고, `/etc/yum.repos.d/CentOS-Base.repo` 파일이 정상적인지 확인을 하는 정도로 문제가 해결되었다. 근데 삽질 몇십분 좀 했음 ㅜㅜ

```
# cp -arv /var/lib/rpm /var/lib/rpm.bak
# rm -f /var/lib/rpm/__db*
# /usr/lib/rpm/rpmdb_verify /var/lib/rpm/Packages

# mkdir /etc/yum.repos.d/old
# mv /etc/yum.repos.d/*repo /etc/yum.repos.d/old
# rm -rf /var/cache/yum/*
# yum repolist -v
# mv /etc/yum.repos.d/old/CentOS-Base.repo /etc/yum.repos.d/
# yum update
```

~~원래 OpenVPN 클라이언트 설정 내용을 쓰려고 했는데 하지 않기로 결정하여 여기까지의 삽질만..~~