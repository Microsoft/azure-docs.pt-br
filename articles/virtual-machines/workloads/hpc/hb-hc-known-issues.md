---
title: Solucionando problemas conhecidos com VMs do HPC e de GPU-máquinas virtuais do Azure | Microsoft Docs
description: Saiba como solucionar problemas conhecidos com tamanhos de VM HPC e de GPU no Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d8c3a2d961cc5b6fd719b77dae07b6e46c3d8b65
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604831"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problemas conhecidos com VMs da série H e da série N

Este artigo tenta listar problemas comuns recentes e suas soluções ao usar as VMs da [série H](../../sizes-hpc.md) e do HPC e da [série N](../../sizes-gpu.md) .

## <a name="mofed-installation-on-ubuntu"></a>Instalação do MOFED no Ubuntu
No Ubuntu-18, 4, o adaptador Mellanox OFED mostrou incompatibilidade com a versão de kernels `5.4.0-1039-azure #42` e mais recente, o que causa um aumento no tempo de inicialização da VM para cerca de 30 minutos. Isso foi relatado para as versões do Mellanox OFED 5.2-1.0.4.0 e 5.2-2.2.0.0.
A solução temporária é usar a imagem **canônica: UbuntuServer: 18_04-LTS-Gen2:18.04.202101290** Marketplace ou mais antiga e não para atualizar o kernel.
Esse problema deve ser resolvido com um MOFED mais recente (TBD).

## <a name="mpi-qp-creation-errors"></a>Erros de criação de MPI QP
Se, no meio da execução de qualquer carga de trabalho MPI, erros de criação de InfiniBand QP, como mostrado abaixo, são apresentados, sugerimos reinicializar a VM e tentar novamente a carga de trabalho. Esse problema será corrigido no futuro.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Você pode verificar os valores do número máximo de pares de fila quando o problema é observado da seguinte maneira.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Rede acelerada em HB, HC, HBv2 e NDv2

A [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) agora está disponível nos tamanhos de VM habilitados para RDMA e de Sr-IOV compatíveis com [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)e [NDv2](../../ndv2-series.md). Esse recurso agora permite a melhoria em todo o período (até 30 Gbps) e latências na rede Ethernet do Azure. Embora isso seja separado dos recursos RDMA na rede InfiniBand, algumas alterações de plataforma para esse recurso podem afetar o comportamento de determinadas implementações de MPI ao executar trabalhos por InfiniBand. Especificamente, a interface InfiniBand em algumas VMs pode ter um nome ligeiramente diferente (mlx5_1 em oposição ao mlx5_0 anterior) e isso pode exigir o ajuste das linhas de comando MPI, especialmente ao usar a interface UCX (normalmente com OpenMP e HPC-X). A solução mais simples no momento pode ser usar o HPC-X mais recente nas imagens de VM CentOS-HPC ou desabilitar a rede acelerada, se não for necessário.
Mais detalhes sobre isso estão disponíveis neste [artigo do TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) com instruções sobre como resolver problemas observados.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Instalação do driver InfiniBand em VMs que não são do SR-IOV

Atualmente, H16r, H16mr e NC24r não são habilitados para SR-IOV. Alguns detalhes sobre o bifurcação da pilha InfiniBand estão [aqui](../../sizes-hpc.md#rdma-capable-instances).
A InfiniBand pode ser configurada em tamanhos de VM habilitados para SR-IOV com os drivers OFED, enquanto os tamanhos de VM que não são de SR-IOV exigem drivers ND. Esse suporte do IB está disponível adequadamente para [CentOS, RHEL e Ubuntu](configure.md).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicar o MAC com Cloud-init com Ubuntu nas VMs da série H e da série N

Há um problema conhecido com Cloud-init em imagens de VM Ubuntu, pois ele tenta abrir a interface IB. Isso pode ocorrer na reinicialização da VM ou ao tentar criar uma imagem de VM após a generalização. Os logs de inicialização da VM podem mostrar um erro como este:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Esse "duplicar MAC com Cloud-init no Ubuntu" é um problema conhecido. Isso será resolvido nos kernels mais recentes. Se o problema for encontrado, a solução alternativa é:
1) Implantar a imagem de VM do Marketplace (Ubuntu 18, 4)
2) Instalar os pacotes de software necessários para habilitar o IB ([instrução aqui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Edite waagent. conf para alterar EnableRDMA = y
4) Desabilitar rede na nuvem-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Editar o arquivo de configuração de rede do netplan gerado por Cloud-init para remover o MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="qp0-access-restriction"></a>Restrição de acesso qp0

Para evitar o acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o par de filas 0 não é acessível para VMs convidadas. Isso só deve afetar as ações normalmente associadas à administração da NIC ConnectX-5 e a execução de alguns diagnósticos InfiniBand como ibdiagnet, mas não os aplicativos do usuário final.

## <a name="dram-on-hb-series-vms"></a>DRAM em VMs da série HB

As VMs da série HB podem expor apenas 228 GB de RAM para VMs convidadas no momento. Da mesma forma, 458 GB em HBv2 e 448 GB em VMs HBv3. Isso ocorre devido a uma limitação conhecida do hipervisor do Azure para impedir que as páginas sejam atribuídas ao DRAM local do AMD CCX (domínios NUMA) reservado para a VM convidada.

## <a name="gss-proxy"></a>Proxy GSS

O proxy GSS tem um bug conhecido no CentOS/RHEL 7,5 que pode ser manifestado como um desempenho significativo e uma penalidade de capacidade de resposta quando usado com NFS. Isso pode ser reduzido com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza de cache

Em sistemas HPC, geralmente é útil limpar a memória após a conclusão de um trabalho antes que o próximo usuário receba o mesmo nó. Depois de executar aplicativos no Linux, você pode descobrir que a memória disponível é reduzida enquanto a memória do buffer aumenta, apesar de não executar nenhum aplicativo.

![Captura de tela do prompt de comando antes da limpeza](./media/known-issues/cache-cleaning-1.png)

`numactl -H`O uso do mostrará a quais NUMAnode a memória é armazenada em buffer (possivelmente todos). No Linux, os usuários podem limpar os caches de três maneiras para retornar memória armazenada em buffer ou em cache para ' Free '. Você precisa ser root ou ter permissões sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Captura de tela do prompt de comando após a limpeza](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos do kernel

Você pode ignorar as seguintes mensagens de aviso do kernel ao inicializar uma VM da série HB no Linux. Isso ocorre devido a uma limitação conhecida do hipervisor do Azure que será abordada ao longo do tempo.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Próximas etapas

- Examine a [visão geral da série HB](hb-series-overview.md) e a [visão geral da série HC](hc-series-overview.md) para saber mais sobre como configurar de maneira ideal as cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma exibição arquitetônica de nível superior da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](/azure/architecture/topics/high-performance-computing/).
