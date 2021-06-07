---
title: Instalação do driver da GPU da série N do Azure para Linux
description: Como configurar drivers NVIDIA GPU para VMs da série N que executam o Linux no Azure
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: c4c6bee6d3f9e423d83458ad48d213fe65223514
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551753"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalar drivers NVIDIA GPU em VMs da série N que executam o Linux

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que contam com GPUs da NVIDIA, é preciso instalar os drivers para GPU NVIDIA. A [Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como a CLI do Azure ou os modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) para saber quais são as distribuições compatíveis e as etapas de implantação.

Se você optar por instalar os drivers de GPU da NVIDIA manualmente, este artigo fornecerá as distribuições compatíveis, os drivers e as etapas de instalação e verificação. Também há informações de As informações de instalação manual de driver também estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md).

Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos de VM Linux para GPU](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

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

lspci lista os dispositivos PCIe na VM, incluindo a NIC InfiniBand e GPUs, se houver. Se lspci não retornar com êxito, talvez seja necessário instalar o LIS no CentOS/RHEL (instruções abaixo).
Em seguida, execute os comandos de instalação específicos para a sua distribuição.

### <a name="ubuntu"></a>Ubuntu 

1. Baixe e instale os drivers CUDA do site da NVIDIA. 
    > [!NOTE]
   >  O exemplo a seguir mostra o caminho do pacote CUDA para o Ubuntu 16, 4. Substitua o caminho específico da versão que você planeja usar. 
   >  
   >  Visite o [Centro de download NVIDIA] ( https://developer.download.nvidia.com/compute/cuda/repos/) para o caminho completo específico de cada versão. 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

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
   ```

2. Instale o [Integration Services Linux mais recente para o Hyper-V e o Azure](https://www.microsoft.com/download/details.aspx?id=55106). Verifique se LIS é necessário verificando os resultados de lspci. Se todos os dispositivos GPU estiverem listados como esperado (e documentados acima), a instalação de LIS não será necessária.

   Observe que o LIS é aplicável a Red Hat Enterprise Linux, CentOS e a Oracle Linux kernel do Red Hat compatível com o 5.11, 6.0-6.10 e 7.0-7.7. Consulte a [documentação de Integration Services do Linux] ( https://www.microsoft.com/en-us/download/details.aspx?id=55106) para obter mais detalhes. 
   Ignore esta etapa se você planeja usar o CentOS/RHEL 7,8 (ou versões posteriores), pois LIS não é mais necessária para essas versões.

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
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A instalação poderá levar vários minutos. 

4. Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Se você vir uma mensagem de erro relacionada a pacotes ausentes, como Vulkan-FileSystem, talvez seja necessário editar/etc/yum.repos.d/RH-Cloud, procurar por optional-RPMs e definir Enabled como 1
   >  

5. Reinicie a VM e prossiga para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação de drivers

Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. Observe que **GPU-Util** mostrará 0%, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser habilitada em VMs da série N compatíveis com RDMA, como o NC24r implantado no mesmo conjunto de disponibilidade ou em um grupo de posicionamento único em um conjunto de dimensionamento de máquina virtual (VM). A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com Intel MPI 5.x ou uma versão posterior. Requisitos adicionais são listados a seguir:

### <a name="distributions"></a>Distribuições

Implante VMs da série N habilitadas para RDMA de uma das imagens no Azure Marketplace que é compatível com a conectividade RDMA nas VMs da série N:
  
* **Ubuntu 16.04 LTS** – Configure os drivers RDMA na VM e registre-se na Intel para baixar a MPI da Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-based 7.4 HPC** - Os drivers RDMA e o Intel MPI 5.1 são instalados na VM.

* **HPC baseado em CentOS** -CentOS-HPC 7,6 e posterior (para SKUs em que a InfiniBand tem suporte em relação a Sr-IOV). Essas imagens têm bibliotecas Mellanox OFED e MPI pré-instaladas.

> [!NOTE]
> CX3-Pro cartões têm suporte apenas por meio de versões LTS do Mellanox OFED. Use LTS Mellanox OFED Version (4.9-0.1.7.0) nas VMs da série N com cartões de ConnectX3-Pro. Para obter mais informações, consulte [drivers do Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Além disso, algumas das mais recentes imagens do HPC do Azure Marketplace têm o Mellanox OFED 5,1 e posterior, que não dá suporte a cartões ConnectX3-Pro. Verifique a versão do Mellanox OFED na imagem do HPC antes de usá-la em VMs com cartões de ConnectX3-Pro.
>
> As imagens a seguir são as mais recentes imagens do CentOS-HPC que dão suporte a cartões de ConnectX3-Pro:
>
> - OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
> - OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
> - OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
> - OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalar drivers de GRID em VMs da série NVv3 ou NV

Para instalar drivers NVIDIA GRID em VMs da série NVv3 ou NV, faça uma conexão SSH para cada VM e siga as etapas para a sua distribuição do Linux. 

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
3. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Apenas use o driver NVIDIA em VMs NVv2 ou NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d` chamado `nouveau.conf` com o conteúdo a seguir:

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
   
9. Remova o seguinte de `/etc/nvidia/gridd.conf` se estiver presente:
 
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

2. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use apenas o driver NVIDIA em VMs NV ou NV3.) Para fazer isso, crie um arquivo em `/etc/modprobe.d` nome `nouveau.conf` com o seguinte conteúdo:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Reinicie a VM, reconecte e instale o último [Integration Services do Linux para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Verifique se LIS é necessário verificando os resultados de lspci. Se todos os dispositivos GPU estiverem listados como esperado (e documentados acima), a instalação de LIS não será necessária. 

   Ignore esta etapa se você planeja usar o CentOS/RHEL 7,8 (ou versões posteriores), pois LIS não é mais necessária para essas versões.

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
9. Remova o seguinte de `/etc/nvidia/gridd.conf` se estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie a VM e prossiga para verificar a instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. Observe que **GPU-Util** mostrará 0%, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Captura de tela que mostra a saída quando o estado do dispositivo GPU é consultado.](./media/n-series-driver-setup/smi-nv.png)
 

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
* Se você atualizou os drivers NVIDIA CUDA para a versão mais recente e descobrir que a conectividade RDMA não está mais funcionando, [reinstale os drivers RDMA](#rdma-network-connectivity) para restabelecer essa conectividade. 
* Durante a instalação do LIS, se uma determinada versão do sistema operacional CentOS/RHEL (ou kernel) não tiver suporte para LIS, um erro "versão do kernel sem suporte" será gerado. Relate esse erro junto com as versões do sistema operacional e do kernel.

## <a name="next-steps"></a>Próximas etapas

* Para capturar uma imagem de VM do Linux na qual você tenha instalado drivers NVIDIA, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md).
