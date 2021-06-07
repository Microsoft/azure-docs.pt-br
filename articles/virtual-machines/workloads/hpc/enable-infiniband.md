---
title: Habilitar InifinBand em VMs HPC – máquinas virtuais do Azure | Microsoft Docs
description: Saiba como habilitar o InfiniBand em VMs do Azure HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 089976f2009e006f53dd2a77f09f57d5090429b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721246"
---
# <a name="enable-infiniband"></a>Habilitar InfiniBand

As VMs [compatíveis com RDMA](../../sizes-hpc.md#rdma-capable-instances) da [série H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) se comunicam pela rede InfiniBand de baixa latência e alta largura de banda. A funcionalidade RDMA em uma interconexão como essa é crítica para aumentar a escalabilidade e o desempenho de cargas de trabalho de HPC e IA de nó distribuído. As VMs das séries H e N habilitadas para InfiniBand estão conectadas em uma fat tree sem bloqueio com um design de diâmetro baixo para desempenho otimizado e consistente de RDMA.

Há várias maneiras de habilitar o InfiniBand nos tamanhos de VM compatíveis.

## <a name="vm-images-with-infiniband-drivers"></a>Imagens de VM com drivers InfiniBand
Confira [imagens de VM](configure.md#vm-images) para obter uma lista de imagens de VM com suporte no Marketplace, que são pré-carregadas com drivers InfiniBand (para VMs Sr-IOV ou não Sr-IOV) ou podem ser configuradas com os drivers apropriados para [VMs compatíveis com RDMA](../../sizes-hpc.md#rdma-capable-instances).
- As imagens de VM do [CentOS-HPC](configure.md#centos-hpc-vm-images) no Marketplace são a maneira mais fácil de começar.
- As imagens de VM [Ubuntu](configure.md#ubuntu-vm-images) podem ser configuradas com os drivers de IB corretos.

## <a name="infiniband-driver-vm-extensions"></a>Extensões de VM do driver InfiniBand
No Linux, a [extensão de VM InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) pode ser usada para instalar os drivers Mellanox ofed e habilitar o InfiniBand nas VMs das séries H e N habilitadas para Sr-iov.

No Windows, a [extensão de VM InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) instala drivers diretos de rede do Windows (em VMs que não são de Sr-IOV) ou drivers Mellanox ofed (em VMs Sr-IOV) para conectividade RDMA. Em determinadas implantações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Observe que a extensão de VM HpcVmDrivers está sendo preterida; Ele não será atualizado.

Para adicionar a extensão de VM a uma VM, use cmdlets do [Azure PowerShell](/powershell/azure/). Para obter mais informações, consulte [Recursos e extensões da máquina virtual](../../extensions/overview.md). Também é possível trabalhar com extensões de VMs implantadas no [modelo de implantação clássico](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Instalação manual
Os [drivers Mellanox OpenFabrics (ofed)](https://www.mellanox.com/products/InfiniBand-VPI-Software) podem ser instalados manualmente nas VMs das séries [H](../../sizes-hpc.md) e série [N](../../sizes-gpu.md) [habilitadas para Sr-IOV](../../sizes-hpc.md#rdma-capable-instances) .

### <a name="linux"></a>Linux
Os [drivers do ofed para Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) podem ser instalados com o exemplo abaixo. Embora o exemplo aqui seja para RHEL/CentOS, mas as etapas são gerais e podem ser usadas para qualquer sistema operacional Linux compatível, como Ubuntu (16, 4, 18, 4 19, 4, 20, 4) e SLES (12 SP4 e 15). Mais exemplos para outros distribuições estão no [repositório azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). Os drivers de caixa de entrada também funcionam bem, mas os drivers Mellanox OFED fornecem mais recursos.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Para o Windows, baixe e instale os [drivers MELLANOX ofed for Windows](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Habilitar IP sobre InfiniBand (IB)
Se você estiver planejando executar trabalhos MPI, normalmente não precisará de IPoIB. A biblioteca MPI usará a interface de verbos para comunicação de IB (a menos que você use explicitamente o canal TCP/IP da biblioteca MPI). Mas se você tiver um aplicativo que usa TCP/IP para comunicação e quiser executar sobre o IB, você poderá usar o IPoIB sobre a interface IB. Use os comandos a seguir (para RHEL/CentOS) para habilitar o IP sobre InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como instalar várias [bibliotecas MPI com suporte](setup-mpi.md) e sua configuração ideal nas VMs.
- Examine as visão geral da série [HBv3](hbv3-series-overview.md) e [HC-Series](hc-series-overview.md).
- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
