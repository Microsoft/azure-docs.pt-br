---
title: "Instalação de drivers da série N do Azure para Linux | Microsoft Docs"
description: "Como configurar drivers NVIDIA GPU para VMs da série N que executam o Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f8cd4fc37caca7fa6094a4780078d9ed882ba3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalar drivers NVIDIA GPU em VMs da série N que executam o Linux

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam Linux, é necessário instalar os drivers gráficos NVIDIA com suporte. Este artigo apresenta etapas de instalação do driver depois que você implanta uma VM da série N. Também há informações de instalação de driver disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos de VM Linux para GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>Instalar drivers GRID para VMs NV

Para instalar drivers NVIDIA GRID em VMs NV, faça uma conexão SSH com cada VM e siga as etapas para sua distribuição do Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Execute o comando `lspci`. Verifique se a placa ou placas NVIDIA M60 estão visíveis como dispositivos PCI.

2. Instale as atualizações.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use somente o driver NVIDIA em VMs NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d `chamado `nouveau.conf` com o conteúdo a seguir:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Reinicie a VM e reconecte. Saia do servidor X:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Baixe e instale o driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 

6. Quando você for questionado se deseja executar o utilitário nvidia-xconfig para atualizar seu arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie /etc/nvidia/gridd.conf.template para um novo arquivo gridd.conf no local /etc/hosts nvidia/

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Reinicie a VM e prossiga para verificar a instalação.


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Baseado em CentOS 7.3 ou Red Hat Enterprise Linux 7.3

> [!IMPORTANT]
> Não execute `sudo yum update` para atualizar a versão do kernel no CentOS 7.3 ou Red Hat Enterprise Linux 7.3. Atualmente, a instalação e as atualizações do driver não funcionam se o kernel é atualizado.
>

1. Atualize o kernel e o DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use somente o driver NVIDIA em VMs NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d `chamado `nouveau.conf` com o conteúdo a seguir:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Reinicie a VM, reconecte-se e instale os Integration Services do Linux para Hyper-V mais recentes:
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz

  tar xvzf lis-rpms-4.2.3.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Reconecte-se à VM e execute o comando `lspci`. Verifique se a placa ou placas NVIDIA M60 estão visíveis como dispositivos PCI.
 
5. Baixe e instale o driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 
6. Quando você for questionado se deseja executar o utilitário nvidia-xconfig para atualizar seu arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie /etc/nvidia/gridd.conf.template para um novo arquivo gridd.conf no local /etc/hosts nvidia/
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Reinicie a VM e prossiga para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Saída semelhante à seguinte exibida:

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Se você precisar de um servidor X11 para conexões remotas com uma VM NV, o [x11vnc](http://www.karlrunge.com/x11vnc/) é recomendado porque ele permite a aceleração de hardware do elemento gráfico. A BusID do dispositivo M60 deve ser adicionada manualmente ao arquivo xconfig (`etc/X11/xorg.conf` no Ubuntu 16.04 LTS, `/etc/X11/XF86config` no CentOS 7.3 ou Red Hat Enterprise Server 7.3). Adicione uma seção `"Device"` semelhante à seguinte:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Além disso, atualize sua seção `"Screen"` para usar este dispositivo.
 
A BusID pode ser encontrada executando

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
A BusID pode mudar quando uma VM é realocada ou reinicializada. Portanto, convém usar um script para atualizar a BusID na configuração do X11 quando uma VM é reiniciada. Por exemplo:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Esse arquivo pode ser invocado como raiz na inicialização criando uma entrada para ele em `/etc/rc.d/rc3.d`.


## <a name="install-cuda-drivers-for-nc-vms"></a>Instalar drivers CUDA Tesla para VMs NC

Aqui estão as etapas para instalar drivers NVIDIA em VMs NC Linux do Kit de ferramentas NVIDIA CUDA 8.0. 

Os desenvolvedores de C e C++ podem opcionalmente instalar o Kit de ferramentas completo para criar aplicativos com aceleração de GPU. Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Os links de download do driver CUDA fornecidos aqui são atuais no momento da publicação. Para os drivers CUDA mais recentes, visite o site da [NVIDIA](http://www.nvidia.com/).
>

Para instalar o Kit de ferramentas de CUDA, realize uma conexão SSH para cada VM. Para verificar se o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Você verá uma saída semelhante ao seguinte exemplo (mostrando uma placa NVIDIA Tesla K80):

![Saída do comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, execute os comandos de instalação específicos para a sua distribuição.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Baixe e instale os drivers CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-9-0_9.0.176-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  A instalação poderá levar vários minutos.

2. Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

  ```bash
  sudo apt-get install cuda
  ```

3. Reinicie a VM e prossiga para verificar a instalação.

#### <a name="cuda-driver-updates"></a>Atualizações de driver CUDA

É recomendável que você atualize periodicamente os drivers CUDA após a implantação.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Baseado em CentOS 7.3 ou Red Hat Enterprise Linux 7.3

> [!IMPORTANT]
> Não execute `sudo yum update` para atualizar a versão do kernel no CentOS 7.3 ou Red Hat Enterprise Linux 7.3. Atualmente, a instalação e as atualizações do driver não funcionam se o kernel é atualizado.
>

1. Instale os Integration Services do Linux para Hyper-V mais recentes.

  > [!IMPORTANT]
  > Se você tiver instalado uma imagem HPC com base em CentOS em uma VM NC24r, pule para a Etapa 3. Uma vez que drivers de RDMA do Azure e Integration Services do Linux estão pré-instalados na imagem, o LIS não deve ser atualizado e atualizações de kernel estão desabilitadas por padrão.
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz
 
  tar xvzf lis-rpms-4.2.3.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Reconecte-se à VM e continue a instalação com os seguintes comandos:

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9-0-local-9.0.176-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  A instalação poderá levar vários minutos. 

4. Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

  ```bash
  sudo yum install cuda
  ```

5. Reinicie a VM e prossiga para verificar a instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Saída semelhante à seguinte exibida:

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)



## <a name="rdma-network-for-nc24r-vms"></a>Rede RDMA para VMs NC24r

A conectividade de rede RDMA pode ser habilitada em VMs NC24r implantadas no mesmo conjunto de disponibilidade. A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com Intel MPI 5.x ou uma versão posterior. Requisitos adicionais são listados a seguir:

### <a name="distributions"></a>Distribuições

Implante VMs NC24r de uma das seguintes imagens no Azure Marketplace que dá suporte à conectividade RDMA:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **HPC baseado em CentOS** – HPC baseado em CentOS 7.3. Drivers RDMA e o Intel MPI 5.1 são instalados na VM. 


## <a name="troubleshooting"></a>Solucionar problemas

* Há um problema conhecido com drivers CUDA em VMs do Azure série N executando o kernel do Linux 4.4.0-75 no Ubuntu 16.04 LTS. Se você estiver atualizando de uma versão anterior do kernel, atualize para pelo menos a versão 4.4.0-77 do kernel. 



## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre as GPUs NVIDIA nas VMs da série N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs NC do Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs NV do Azure)

* Para capturar uma imagem de VM do Linux na qual você tenha instalado drivers NVIDIA, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
