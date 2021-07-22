---
title: "[Docker] WSL2 도커 데스크탑 볼륨 줄이는 방법"
description: "로컬에있는 .vhdx파일의 크기 줄이기"
layout: post
toc: false
comments: true
search_exclude: true
categories: [Docker]
image: "/images/docker/docker.png"
---

처음 kaggle/python의 이미지를 다운받고 도커를 사용할려고 하다 보니까 어느 순간 로컬 저장소에 약 60GB정도 쌓인 것을 확인했습니다.


그래서 도커 iamge, container들을 제거하는 것은 쉽게 찾을 수 있었습니다.  
하지만 로컬 docker의 wsl파일에 있는 .vhdx파일의 용량이 줄어들지는 않았습니다.


# 도커 image, container 삭제하기(window)
```
# 도커 all stop
docker kill $(docker ps -q)
# 도커에 있는 모든 container 삭제
docker rm $(docker ps -a -q)
# 도커에 있는 모든 image 삭제
docker rmi $(docker images -q -f dangling=true)# -f 옵션을 통해 컨테이너에 실행중인 것도 삭제 가능
docker rmi $(docker images -q)

# and

$ docker system prune

WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all build cache Are you sure you want to continue? [y/N] y
```

![]({{ site.baseurl }}/images/docker/reduce/d3.PNG "도커 이미지 모두 삭제")


# .vhdx 볼륨 줄이기

WSL2를 사용하는 Windows v2용 Docker Desktop은 모든 이미지와 컨테이너 파일을 별도의 가상 볼륨(vhdx)에 저장합니다.  
이 가상 하드 디스크 파일은 더 많은 공간이 필요할 때(특정 제한까지) 자동으로 커질 수 있습니다.  
불행히도 사용하지 않는 이미지를 제거하여 일부 공간을 확보하면 vhdx가 자동으로 축소되지 않습니다.  
운 좋게도 PowerShell(관리자 권한)에서 이 명령을 호출하여 수동으로 크기를 줄일 수 있습니다.

> Notice: 작업을 실행하기 전 도커를 종료해주세요.  
미리 도커를 종료하지 않으면 `Optimize-VHD : 가상 디스크를 압축하지 못했습니다.
시스템에서 'C:\Users\all7j\AppData\Local\Docker\wsl\data\ext4.vhdx'을(를) 압축하지 못했습니다. 다른 프로세스가 파일을
사용 중이기 때문에 프로세스가 액세스 할 수 없습니다.(0x80070020).` 와 같은 에러 메세지를 만날 수 있습니다.

```
Optimize-VHD -Path c:\path\to\data.vhdx -Mode Full

```
위 -Path 뒤에 사용자의 .vhdx 파일경로를 대신해서 넣어주면 됩니다.

![]({{ site.baseurl }}/images/docker/reduce/d1.PNG)


현재 .vhdx의 파일 용량은 약 45GB입니다.

![]({{ site.baseurl }}/images/docker/reduce/d2.PNG)


윗줄은 제가 타이핑을 잘못쳐서 ..ㅎㅎ  
코드를 실행하시면 위와 같이 가상 디스크를 압축시켜 줍니다.  
(사실 디스크를 압축하는게 내용물을 지우는지 일반적인 압축방법인지는 잘 모르겠습니다.)


![]({{ site.baseurl }}/images/docker/reduce/d4.PNG)
압축 결과로 38.4GB로 줄어든 것을 확인 할 수 있습니다.




오늘 포스트는 여기서 마치겠습니다.

# References
<https://dev.to/marzelin/how-to-reduce-size-of-docker-data-volume-in-docker-desktop-for-windows-v2-5d38>  
<https://brunch.co.kr/@hopeless/10>