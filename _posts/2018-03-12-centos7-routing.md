---
layout: post
category: 리눅스
title: CentOS7 Routing 라우팅
comments: true
---

### eth0 NIC의 라우팅 테이블을 수정. 재부팅하여도 변하지 않는 persistent 방법이다.
`# vim /etc/sysconfig/network-scripts/route-eth0`

```
GATEWAY0=<게이트웨이 IP>
NETMASK0=255.255.0.0
ADDRESS0=10.10.0.0 // 10.10.0.0/24 로 들어오는 패킷을 eth0(route-eth0 파일을 수정하고 있음) 로 경로 설정
```

파일을 저장 후 `# systemctl restart network.service` 명령어로 네트워크 서비스를 재시작해주면 위에서 설정한 게이트웨이를 통하여 통신을 하게 된다.