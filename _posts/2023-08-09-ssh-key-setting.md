---
layout: post
title: SSH Key 생성하고 Github에 등록하기
categories: ["study"]
---

#### SSH 인증
SSH는 Secure Shell의 약자로 네트워크를 통해 안전하게 원격 서버에 접속하기 위한 프로토콜의 일종이다. 주로 리눅스나 유닉스 기반 시스템에서 원격으로 서버에 접속하거나 파일을 전송하는데 사용된다. 이번에는 비밀번호 인증 대신 공개키/개인키 한 쌍을 이용한 인증방식인 공개키 인증 방식을 이용할 것이다.

#### 공개키 인증이 필요한 이유는 ?
![git clone](/assets/img/ssh-key-setting.png)

Github 리포지토리의 데이터를 추가하고 수정하려면 SSH 인증을 거쳐야 한다. HTTPS방식으로 clone하면 push나 pull을 할때 깃헙 유저네임과 패스워드를 물어보는데 패스워드 부분에는 **패스워드가 아닌 Personal access token을 입력해야 인증이 된다.**(2021년 8월 13일부터) 퍼스널 엑세스 토큰도 깃헙 설정에서 생성이 가능하지만 최초 생성 이후 key값을 보여주는 화면을 나가면 다시는 토큰 키 값을 확인할 수 없다. 그렇기 때문에 토큰 생성 페이지를 나가기 전에 토큰 키를 복사하여 별도의 저장 공간에 저장해야 하는 귀찮음이 있다.

#### 한번만 하면 된다
SSH키를 등록하는 일은 약간 귀찮지만 처음 기기를 장만했을때 한번만 하면 된다. 나는 wsl+우분투 / 맥북 / 윈도우에 한번씩 총 3번 인증키를 등록했다. 이렇게 여러개의 키를 생성하지 않아도 개인키 보안 유지만 잘 된다면 하나의 키 쌍을 여러 기기에서 공유하여 사용할 수 있다.

#### SSH키 생성하기
터미널에 `ssh-keygen` 입력 후 SSH Key를 저장하고 싶은 디렉토리를 지정한 뒤 암호를 두 번 입력한다. 이 암호는 키를 추가로 보호하는 수단이며 선택 사항이다. 암호를 비워두면 키를 사용할 때 암호를 묻지 않는다.

```bash
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
Created directory '/home/user/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_rsa.
Your public key has been saved in /home/user/.ssh/id_rsa.pub.
The key fingerprint is:
d0:82:24:8e:d7:f1:bb:9b:33:53:96:93:49:da:9b:e3 user@mylaptop.local
```

이 과정을 거치면 공개키와 개인키 한 쌍이 만들어진다.

* **개인 키** 확인 방법
  개인 키는 로컬 컴퓨터에 저장된다.

```bash
  cat .ssh/id_rsa
```
* **공개 키** 확인 방법  
  공개키는 Github 설정에 저장되어야 한다. SSH 접속을 시도할때 공개키와 개인키를 비교해서 일치하는지 확인하는 방식으로 인증이 진행된다.

```bash
  cat .ssh/id_rsa.pub
```

### Github에 키 등록하기
Settings -> SSH keys and GPG keys -> New SSH Key를 클릭해서 키를 등록할 수 있다.