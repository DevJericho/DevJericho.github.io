---
layout: post
title: "Google VM을 활용한 쿠버네티스 설치"
date: "2023-04-20 00:00:00 +0900"
categories: k8s
last_modified_at: "2023-04-17 00:00:00 +0900"
---

우분투 환경에서 쿠버네티스 설치
--

Google Cloud 환경에서 제공하는 VM을 활용하여 우분투 설치

### 1. 인스턴스 만들기

![Foo](/assets/img/k8s/k8s_1.png)

### 2. 인스턴스 정보

![Foo](/assets/img/k8s/k8s_2.png)

- 총 3개의 인스턴스를 만들예정
- master , worker-1 , worker-2  각각 서로 네트워크로 통신을 해야하므로 리전을 동일하게 설정했음

### 3. 부팅디스크 정보

![Foo](/assets/img/k8s/k8s_3.png)

Ubuntu 20.04 x86/64, amd64 버전을 사용했으며 , 표준영구디스크 외에는 추가요금이 붙을 수 있음

### 4. 전체정보

![Foo](/assets/img/k8s/k8s_4.png)

1. 설치가 완료되면 SSH 연결을 통해 접속
2. 접속 후 서로 통신이 되는지 Ping 으로 테스트
- 각각의 SSH 콘솔을 통해 설정을 진행하는데 초기설정은 모두 동일하고 추후에 마스터 노드만 작업하는 과정이 있다

### 5. SWAP 비활성화

```
sudo swapoff -a 
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab 
```

![Foo](/assets/img/k8s/k8s_5.png)

- 각각의 command 창에서 모두 실행

참고URL : [https://askubuntu.com/questions/214805/how-do-i-disable-swap](https://askubuntu.com/questions/214805/how-do-i-disable-swap)

### 6. Docker repository 설정

```
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

#Docker의 GPG 키를 시스템에 추가
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

#시스템에 Docker 리포지토리를 추가
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

sudo apt update
#containerd 설치
sudo apt install -y containerd.io

#상태확인
sudo systemctl status containerd
```

- 상태확인 명령어 입력 후 아래와 같이 Acitve로 되어있어야 한다

![Foo](/assets/img/k8s/k8s_6.png)

### 7. **kubeadm, kubelet ,kubectl 설치**

```
cat <<EOF > kube_install.sh
# 1. apt 패키지 색인을 업데이트하고, 쿠버네티스 apt 리포지터리를 사용하는 데 필요한 패키지를 설치한다.
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

# 2. 구글 클라우드의 공개 사이닝 키를 다운로드 한다.
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

# 3. 쿠버네티스 apt 리포지터리를 추가한다.
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# 4. apt 패키지 색인을 업데이트하고, kubelet, kubeadm, kubectl을 설치하고 해당 버전을 고정한다.
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
EOF

sudo bash kube_install.sh
```

### 8. 넷필터 브릿지 설정

```
sudo -i
modprobe br_netfilter
echo 1 > /proc/sys/net/ipv4/ip_forward
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
exit
```

- 수동으로 진행함에 따라 설정이 필요함

### 9. 마스터노드 초기화

```
sudo kubeadm init
```

※마스터 노드에서만 실행

- 기다리면 아래와 같이 완료가 되는데 내용에 포함된 명령어를 실행해줘야 한다

![Foo](/assets/img/k8s/k8s_7.png)

```
#유저설정
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

- 현재 nodes 확인해보면 아직 추가가 되어 있지 않다

![Foo](/assets/img/k8s/k8s_8.png)

- 초기화 후 출력되는  Join 명령어를 통해 worker 노드들을 추가해준다

-IP와 Token값은 각각 다르므로 직접 확인해야한다.

```
sudo kubeadm join 10.138.0.9:6443 --token fkdxrc.gpu1pd838mqhuh34 \
        --discovery-token-ca-cert-hash sha256:a33b7192f11c027f2179d6f4771348dc29603d395e445ebcde3095690e0e6a1c
```

- 추가 후 다시 명령어를 실행해본 결과

```
kubectl get nodes
```

![Foo](/assets/img/k8s/k8s_9.png)

### 10. 네트워크 배포

- 앞서 설정이 완료된 후 마스터노드에서 명령어를 실행하면 현재 설정을 통해 클러스터에 cilium을 설치한다

```
curl -LO https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
rm cilium-linux-amd64.tar.gz
cilium install
```

※ cilium 외에도 다양한 Add-on이 있으며 아래 내용을 참고해서 원하는 설정 값을 가진 Add-on 설치가 가능하다.

[https://kubernetes.io/docs/concepts/cluster-administration/addons/](https://kubernetes.io/docs/concepts/cluster-administration/addons/)

- 설치 이후 현재 상태확인

```
cilium status
```

![Foo](/assets/img/k8s/k8s_10.png)

- nods status 확인

![Foo](/assets/img/k8s/k8s_11.png)