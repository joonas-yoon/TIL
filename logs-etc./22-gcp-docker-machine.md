# 2020/08/22

## GCP: docker machine

**GCP\(Google Cloud Platform\)의 Compute Engine 인스턴스에서는 가상화 컨테이너 기능을 지원하지 않는다.**

그래서 VirtualBox 같은 하이퍼바이저를 설치해도, 도커 머신의 드라이버로 만들 수 없다. \(Hosted 형태의 컨테이너가 안된다는 뜻\)

아무리 이미지를 바꿔도 계속 만났던 오류 메시지:

```text
joonas_yoon@gce-instance:~$ docker-machine create --driver virtualbox vm00
Running pre-create checks...
Error with pre-create check: "This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory"
```

진짜 계속 찾아봤는데, 전부 안됐다.

[https://github.com/docker/machine/issues/4590](https://github.com/docker/machine/issues/4590)

위 이슈 쓰레드에서 해결됐다는 코멘트들 다 안됐다. 인스턴스가 E2 유형이건 아니건 그냥 다 안됐다. N1 타입도 해봤고, Linux 16.04 LTS도 해보고 마켓에 있는 이미지도 해봤다.

![](https://www.macadamian.com/wp-content/uploads/2017/01/1-KO-vKqNqGk_8feDcpePvQQ.png)

* Create machines on Google Compute Engine - [https://docs.docker.com/machine/drivers/gce/](https://docs.docker.com/machine/drivers/gce/)

드라이버를 `google`에 맞게 설정해줘서 해결했다. `key.json`은 프로젝트에서 사용하던 credentials.json 이다.

```text
joonas_yoon@gce-instance:~$ export GOOGLE_APPLICATION_CREDENTIALS=$HOME/key.json
joonas_yoon@gce-instance:~$ gcloud auth login
joonas_yoon@gce-instance:~$ docker-machine create --driver google --google-project <GOOGLE_PROJECT_ID> vm01
Running pre-create checks...
(vm01) Check that the project exists
(vm01) Check if the instance already exists
Creating machine...
(vm01) Generating SSH Key
(vm01) Creating host...
(vm01) Opening firewall ports
(vm01) Creating instance
(vm01) Waiting for Instance
(vm01) Uploading SSH Key
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env vm01
```

