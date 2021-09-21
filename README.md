# 「目で見て体験！ Kubernetesのしくみ」補足情報

# Kubernetes 1.22

## kubeletのcgroupと、Dockerのcgroupが異なるためkubeletの起動に失敗する。

### 症状

以下のコマンドでkubeletの状況を確認すると正常稼動していない。

    $ sudo systemctl status kubelet

以下のコマンドでログを確認するとcgroupのエラーが記録されている。

    $ journalctl -u kubelet
    error: failed to run Kubelet: failed to create kubelet:
    misconfiguration: kubelet cgroup driver: "systemd" is different from docker cgroup driver: "cgroupfs"

### 対処方法

本書のDockerインストールの直後に以下を実施。

/etc/docker/daemon.json に以下の内容のファイルを作成する。

    {
      "exec-opts": ["native.cgroupdriver=systemd"]
    }

作成したら以下のコマンドでDockerを再起動。

    $ sudo systemctl restart docker

Dockerのcgroupがsystemdに変更されたことを確認する。

    docker info | grep -i cgroup
     Cgroup Driver: systemd
     Cgroup Version: 1

## WiringpiがRaspberry Pi4だと正しく稼動しない

### 症状

gpio readallなどのコマンドがエラーになる。

    $ gpio readall
    "Oops - unable to determine board type... model: 17"

#### 対処方法

ライブラリを更新する。

    $ cd /tmp
    $ wget https://project-downloads.drogon.net/wiringpi-latest.deb
    $ sudo dpkg -i wiringpi-latest.deb

