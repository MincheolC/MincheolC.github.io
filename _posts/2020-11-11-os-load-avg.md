---
title: 'O.S Load Average'
excerpt: '유닉스 기반 OS가 부하를 나타내기 위해 사용하는 수치(평균치)이다. '
categories:
  - os
---

유닉스 기반 OS가 부하를 나타내기 위해 사용하는 수치(평균치)이다.

`uptime` 또는 `top` 명령어로 확인 가능하다. 왼쪽부터 `1분 평균 CPU 부하`, `10분 평균 CPU 부하`, `15분 평균 CPU 부하`를 나타낸다.
![Load Average](/assets/images/LoadAvg.png)

#### 수치 의미

CPU가 n개 인 경우.

```
- 1 * n 이상: 시스템 부하 어느정도 있음.
- 2 * n 이상 : 사용량 많음
- 3 * n 이상: 사용량 아주 많음
- 4 * n 이상: 시스템이 현저히 느려짐.
```

> CPU 1개당 2까지의 부하는 정상으로 본다고 한다. [아이군 블로그]

#### CPU 개수 확인 방법

Mac용 명령어

```sh
sysctl -a | grep cpu
```

Linux용 명령어

```sh
grep -c processor /proc/cpuinfo
```

### References

- [Load Average Wiki](<https://en.wikipedia.org/wiki/Load_(computing)>)
- [아이군 블로그](http://theeye.pe.kr/archives/382)
