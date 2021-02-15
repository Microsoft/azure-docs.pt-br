---
title: Configuração e otimização de máquinas virtuais do Azure das séries H e N-Series habilitadas para InfiniBand
description: Saiba como configurar e otimizar as VMs da série H e da série N habilitada para InfiniBand para HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/23/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b1686b08608e4f1c49cd918e86e8149f0fe2a21c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963361"
---
# <a name="configure-and-optimize-vms"></a>Configurar e otimizar VMs

Este artigo compartilha técnicas conhecidas para configurar e otimizar as VMs da série [H](../../sizes-hpc.md) e da série [N](../../sizes-gpu.md) habilitadas para InfiniBand para HPC.

## <a name="vm-images"></a>Imagens de VM
Em VMs habilitadas para InfiniBand, os drivers apropriados são necessários para habilitar o RDMA. No Linux, as imagens de VM CentOS-HPC no Marketplace vêm pré-configuradas com os drivers apropriados. As imagens de VM Ubuntu podem ser configuradas com os drivers corretos usando as [instruções aqui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Também é recomendável criar [imagens de VM personalizadas](../../linux/tutorial-custom-images.md) com os drivers e a configuração apropriados e reutilizá-las de forma recorrente.

> [!NOTE]
> Em VMs da [série N](../../sizes-gpu.md) habilitadas para GPU, os drivers de GPU apropriados também são necessários, que podem ser adicionados por meio de [extensões de VM](../../extensions/hpccompute-gpu-linux.md) ou [manualmente](../../linux/n-series-driver-setup.md). Algumas imagens de VM no Marketplace também vêm pré-instaladas com os drivers NVIDIA GPU.

### <a name="centos-hpc-vm-images"></a>Imagens de VM CentOS-HPC

#### <a name="non-sr-iov-enabled-vms"></a>VMs não habilitadas para SR-IOV
Para [VMs compatíveis com RDMA com](../../sizes-hpc.md#rdma-capable-instances)não-Sr-IOV, a versão 6,5 do CentOS-HPC ou uma versão posterior, até 7,5 no Marketplace são adequadas. Como exemplo, para as [VMs da série H16](../../h-series.md), as versões 7,1 a 7,5 são recomendadas. Essas imagens de VM são pré-carregadas com os drivers diretos de rede para RDMA e Intel MPI versão 5,1.

> [!NOTE]
> Nessas imagens HPC baseadas em CentOS para VMs não habilitadas para SR-IOV, as atualizações de kernel são desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux NetworkDirect são distribuídos como um pacote RPM, e as atualizações de driver podem não funcionar se o kernel for atualizado.

#### <a name="sr-iov-enabled-vms"></a>VMs habilitadas para SR-IOV
  Para [VMs compatíveis](../../sizes-hpc.md#rdma-capable-instances)com o RDMA do Sr-IOV, [a versão 7,6 do CentOS-HPC ou uma versão posterior](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) das imagens de VM no Marketplace são adequadas. Essas imagens de VM são otimizadas e pré-carregadas com os drivers OFED para RDMA e várias bibliotecas MPI comumente usadas e pacotes de computação científica e são a maneira mais fácil de começar.

  Exemplo de scripts usados na criação do CentOS-HPC versão 7,6 e imagens de VM posteriores de uma imagem do Marketplace do CentOS base estão no [repositório azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE] 
  > As imagens mais recentes do Azure HPC Marketplace têm Mellanox OFED 5,1 e superior, que não dão suporte a cartões InfiniBand ConnectX3-Pro. Os tamanhos de VM da série N habilitados para SR-IOV com FDR InfiniBand (por exemplo, NCv3) poderão usar as seguintes versões de imagem de VM CentOS-HPC ou mais antigas:
  >- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
  >- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
  >- OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
  >- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
  >- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
  >- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401


### <a name="rhelcentos-vm-images"></a>Imagens de VM RHEL/CentOS
Imagens de VM não HPC baseadas em RHEL ou CentOS no Marketplace podem ser configuradas para uso em [VMs compatíveis com RDMA](../../sizes-hpc.md#rdma-capable-instances)de Sr-IOV habilitadas. Saiba mais sobre como [habilitar o InfiniBand](enable-infiniband.md) e configurar o [MPI](setup-mpi.md) nas VMs.

  Exemplo de scripts usados na criação do CentOS-HPC versão 7,6 e imagens de VM posteriores de uma imagem do Marketplace do CentOS base estão no [repositório azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE]
  > O Mellanox OFED 5,1 e versões superiores não dão suporte a ConnectX3-Pro placas InfiniBand em tamanhos de VM da série N habilitada para a FDR InfiniBand (por exemplo, NCv3). Use o LTS Mellanox OFED versão 4.9-0.1.7.0 ou mais antigo nas VMs da série N com cartões de ConnectX3-Pro. Veja mais detalhes [aqui](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Imagens de VM Ubuntu
As imagens de VM do Ubuntu Server 16, 4 LTS, 18, 4 LTS e 20, 4 LTS no Marketplace têm suporte para [VMs compatíveis com](../../sizes-hpc.md#rdma-capable-instances)Sr-IOV e não-Sr-IOV RDMA. Saiba mais sobre como [habilitar o InfiniBand](enable-infiniband.md) e configurar o [MPI](setup-mpi.md) nas VMs.

  Exemplos de scripts que podem ser usados na criação das imagens de VM do HPC baseadas no Ubuntu 18, 4 LTS estão no [repositório azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server imagens de VM
SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, as imagens de VM SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15 no Marketplace têm suporte. Essas imagens de VM são pré-carregadas com os drivers diretos de rede para RDMA e Intel MPI versão 5,1. Saiba mais sobre como [Configurar o MPI](setup-mpi.md) nas VMs.

## <a name="optimize-vms"></a>Otimizar VMs

A seguir estão algumas configurações de otimização opcionais para melhorar o desempenho na VM.

### <a name="update-lis"></a>Atualizar LIS

Se necessário para funcionalidade ou desempenho, OS [drivers do Integration Services do Linux (LIS)](../../linux/endorsed-distros.md) podem ser instalados ou atualizados em distribuições do sistema operacional com suporte, especialmente implantando usando uma imagem personalizada ou uma versão mais antiga do sistema operacional, como CentOS/RHEL 6. x ou versão anterior do 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Recuperar memória

Melhore o desempenho recuperando automaticamente a memória para evitar o acesso remoto à memória.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Para fazer essa persistência após a reinicialização da VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Desabilitar Firewall e SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Desabilitar cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Configurar o WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Opcionalmente, o WALinuxAgent pode ser desabilitado como uma etapa de pré-trabalho e habilitado novamente após o post-Job para obter a disponibilidade máxima de recursos da VM para o HPC Workload.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [habilitar o InfiniBand](enable-infiniband.md) nas VMs da série [H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) habilitada para InfiniBand.
- Saiba mais sobre como instalar várias [bibliotecas MPI com suporte](setup-mpi.md) e sua configuração ideal nas VMs.
- Examine a [visão geral da série HB](hb-series-overview.md) e a [visão geral da série HC](hc-series-overview.md) para saber mais sobre como configurar de maneira ideal as cargas de trabalho para desempenho e escalabilidade.
- Leia os comunicados mais recentes e alguns exemplos e resultados da HPC nos [Blogs da Tech Community da Computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
