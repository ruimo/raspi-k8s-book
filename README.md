# 「目で見て体験！ Kubernetesのしくみ」補足情報

## 2021/9/21

バージョン情報

    $ kubectl version
    Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.2", GitCommit:"8b5a19147530eaac9476b0ab82980b4088bbc1b2", GitTreeState:"clean", BuildDate:"2021-09-15T21:38:50Z", GoVersion:"go1.16.8", Compiler:"gc", Platform:"linux/arm"}
    Server Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.2", GitCommit:"8b5a19147530eaac9476b0ab82980b4088bbc1b2", GitTreeState:"clean", BuildDate:"2021-09-15T21:32:41Z", GoVersion:"go1.16.8", Compiler:"gc", Platform:"linux/arm"}

    $ cat /etc/os-release 
    PRETTY_NAME="Raspbian GNU/Linux 10 (buster)"
    NAME="Raspbian GNU/Linux"
    VERSION_ID="10"
    VERSION="10 (buster)"
    VERSION_CODENAME=buster
    ID=raspbian
    ID_LIKE=debian
    HOME_URL="http://www.raspbian.org/"
    SUPPORT_URL="http://www.raspbian.org/RaspbianForums"
    BUG_REPORT_URL="http://www.raspbian.org/RaspbianBugs"

    $ gpio -v
    gpio version: 2.52
    Copyright (c) 2012-2018 Gordon Henderson
    This is free software with ABSOLUTELY NO WARRANTY.
    For details type: gpio -warranty

    Raspberry Pi Details:
      Type: Pi 4B, Revision: 02, Memory: 4096MB, Maker: Sony 
      * Device tree is enabled.
      *--> Raspberry Pi 4 Model B Rev 1.2
      * This Raspberry Pi supports user-level GPIO access.

## kubeletのcgroupと、Dockerのcgroupが異なるためkubeletの起動に失敗する。

### 症状

以下のコマンドでkubeletの状況を確認すると正常稼動していない。

    $ sudo systemctl status kubelet

以下のコマンドでログを確認するとcgroupのエラーが記録されている。

    $ journalctl -u kubelet
    error: failed to run Kubelet: failed to create kubelet:
    misconfiguration: kubelet cgroup driver: "systemd" is different from docker cgroup driver: "cgroupfs"

### 対処方法

本書のDockerインストールの手順の直後に以下を実施。

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

