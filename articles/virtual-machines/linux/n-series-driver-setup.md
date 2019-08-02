---
title: Instalação do driver da GPU da série N do Azure para Linux | Microsoft Docs
description: Como configurar drivers NVIDIA GPU para VMs da série N que executam o Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7e798b4316b8ccdc2f76512d4651365f5bb151ce
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278311"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalar drivers NVIDIA GPU em VMs da série N que executam o Linux

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Linux, instale os drivers de GPU NVIDIA. A [Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como a CLI do Azure ou os modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) para saber quais são as distribuições compatíveis e as etapas de implantação.

Se você optar por instalar os drivers de GPU manualmente, este artigo fornecerá as distribuições compatíveis, os drivers e as etapas de instalação e verificação. Também há informações de As informações de instalação manual de driver também estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos de VM Linux para GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalar drivers de CUDA em VMs série N

Aqui estão as etapas para instalar os drivers CUDA a partir do NVIDIA CUDA Toolkit em VMs série N. 


Os desenvolvedores de C e C++ podem opcionalmente instalar o Kit de ferramentas completo para criar aplicativos com aceleração de GPU. Para obter mais informações, consulte o [Guia de instalação do CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Para instalar os drivers de CUDA, realize uma conexão SSH para cada VM. Para verificar se o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Você verá uma saída semelhante ao seguinte exemplo (mostrando uma placa NVIDIA Tesla K80):

![Saída do comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, execute os comandos de instalação específicos para a sua distribuição.

### <a name="ubuntu"></a>Ubuntu 

1. Baixe e instale os drivers CUDA do site da NVIDIA. Por exemplo, para Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

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

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux

1. Atualize o kernel (recomendado). Se você optar por não atualizar o kernel, verifique se as versões `kernel-devel` e `dkms` são apropriadas para seu kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Reconecte-se à VM e continue a instalação com os seguintes comandos:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

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

Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. Observe que **GPU-Util** mostrará 0%, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser habilitada em VMs da série N compatível com RDMA, como NC24r implantado no mesmo conjunto de disponibilidade ou em um único grupo de posicionamento em um conjunto de dimensionamento de VM. A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com Intel MPI 5.x ou uma versão posterior. Requisitos adicionais são listados a seguir:

### <a name="distributions"></a>Distribuições

Implante VMs da série N habilitadas para RDMA de uma das imagens no Azure Marketplace que é compatível com a conectividade RDMA nas VMs da série N:
  
* **Ubuntu 16.04 LTS** – Configure os drivers RDMA na VM e registre-se na Intel para baixar a MPI da Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-based 7.4 HPC** - Os drivers RDMA e o Intel MPI 5.1 são instalados na VM.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalar drivers de grade em VMs do NV ou da série NVv3

Para instalar os drivers NVIDIA GRID nas VMs do NV ou da série NVv3, faça uma conexão SSH com cada VM e siga as etapas para sua distribuição do Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Execute o comando `lspci`. Verifique se a placa ou placas NVIDIA M60 estão visíveis como dispositivos PCI.

2. Instale as atualizações.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Apenas use o driver NVIDIA em VMs NVv2 ou NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d` nome `nouveau.conf` com o seguinte conteúdo:

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
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Quando você for questionado se deseja executar o utilitário nvidia-xconfig para atualizar seu arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie /etc/nvidia/gridd.conf.template para um novo arquivo gridd.conf no local /etc/hosts nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Remova o seguinte de `/etc/nvidia/gridd.conf` se ele estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie a VM e prossiga para verificar a instalação.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Atualize o kernel e o DKMS (recomendado). Se você optar por não atualizar o kernel, verifique se as versões `kernel-devel` e `dkms` são apropriadas para seu kernel.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Apenas use o driver NVIDIA em VMs NV2 ou NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d` nome `nouveau.conf` com o seguinte conteúdo:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Reinicie a VM, reconecte e instale o último [Integration Services do Linux para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Reconecte-se à VM e execute o comando `lspci`. Verifique se a placa ou placas NVIDIA M60 estão visíveis como dispositivos PCI.
 
5. Baixe e instale o driver GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Quando você for questionado se deseja executar o utilitário nvidia-xconfig para atualizar seu arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie /etc/nvidia/gridd.conf.template para um novo arquivo gridd.conf no local /etc/hosts nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Remova o seguinte de `/etc/nvidia/gridd.conf` se ele estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie a VM e prossiga para verificar a instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. Observe que **GPU-Util** mostrará 0%, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Se você precisa de um servidor X11 para conexões remotas com uma VM NVv2 ou NV, o [x11vnc](http://www.karlrunge.com/x11vnc/) é recomendado porque ele permite a aceleração de hardware para gráfico. A BusID do dispositivo M60 precisa ser adicionada manualmente ao arquivo de configuração X11 (normalmente, `etc/X11/xorg.conf`). Adicione uma seção `"Device"` semelhante à seguinte:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Além disso, atualize sua seção `"Screen"` para usar este dispositivo.
 
A BusID decimal pode ser encontrada executando

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
A BusID pode mudar quando uma VM é realocada ou reinicializada. Portanto, convém criar um script para atualizar a BusID na configuração do X11 quando uma VM é reiniciada. Por exemplo, crie um script chamado `busidupdate.sh` (ou outro nome que desejar) com um conteúdo semelhante ao seguinte:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Em seguida, crie uma entrada para o seu script de atualização em `/etc/rc.d/rc3.d` para que o script seja invocado como raiz na inicialização.

## <a name="troubleshooting"></a>Solução de problemas

* Você pode definir o modo de persistência usando `nvidia-smi`, de modo que o resultado do comando seja mais rápido quando você precisar consultar cartões. Para definir o modo de persistência, execute `nvidia-smi -pm 1`. Observe que, se a VM for reiniciada, a configuração do modo desaparecerá. Você sempre pode gerar um script da configuração de modo para ser executada na inicialização.

## <a name="next-steps"></a>Próximas etapas

* Para capturar uma imagem de VM do Linux na qual você tenha instalado drivers NVIDIA, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
