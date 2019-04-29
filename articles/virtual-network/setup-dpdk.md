---
title: DPDK em uma VM do Azure Linux | Microsoft Docs
description: Aprenda como configurar o DPDK em uma máquina virtual Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c5cb840035c5d0d5694982324c7237c58001e689
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731593"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Configurar o DPDK em uma máquina virtual Linux

O Data Plane Development Kit (DPDK) no Azure oferece uma estrutura de processamento de pacotes de espaço do usuário mais rápida para aplicativos que exigem muito desempenho. Essa estrutura ignora a pilha de rede da máquina virtual do kernel.

No processamento de pacote típico que usa a pilha de rede do kernel, o processo é controlado por interrupção. Quando o adaptador de rede recebe pacotes de entrada, há uma interrupção de kernel para processar o pacote e uma mudança de contexto do espaço de kernel para o espaço do usuário. O DPDK elimina a alternância de contexto e o método acionado por interrupção em favor de uma implementação de espaço do usuário que usa drivers de modo de pesquisa para processamento rápido de pacotes.

O DPDK consiste em um conjunto de bibliotecas de espaço de usuário que fornece acesso a recursos de nível inferior. Esses recursos podem incluir hardware, núcleos lógicos, gerenciamento de memória e drivers de modo de pesquisa para placas de interface de rede.

O DPDK pode ser executado em máquinas virtuais do Azure, com suporte a várias distribuições do sistema operacional. O DPDK fornece uma diferenciação de desempenho chave na condução de implementações de virtualização de funções de rede. Essas implementações podem assumir a forma de NVA (dispositivos virtuais de rede), como roteadores virtuais, firewalls, VPNs, balanceadores de carga, núcleos de pacotes evoluídos e aplicativos de DDoS (negação de serviço).

## <a name="benefit"></a>Benefício

**Maior pacotes por segundo (PPS)**: Ignorar o kernel e ter o controle de pacotes no espaço do usuário reduz a contagem de ciclo, eliminando alternâncias de contexto. Ele também melhora a taxa de pacotes que são processados por segundo nas máquinas virtuais do Azure Linux.


## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

As seguintes distribuições da Galeria do Azure são suportadas:

| Sistema operacional Linux     | Versão do kernel        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Suporte a kernel personalizado**

Para qualquer versão do kernel Linux que não esteja listada, confira [Patches para a criação de um kernel do Linux ajustado para o Azure](https://github.com/microsoft/azure-linux-kernel). Para mais informações, você também pode entrar em contato com [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Suporte de regiões

Todas as regiões do Azure dão suporte a DPDK.

## <a name="prerequisites"></a>Pré-requisitos

A rede acelerada deve ser habilitada em uma máquina virtual Linux. A máquina virtual deve ter pelo menos dois adaptadores de rede, com uma interface de gerenciamento. Saiba como [criar uma máquina virtual Linux com rede acelerada habilitada](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalar dependências DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Kernel do Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Kernel padrão**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Configurar o ambiente de máquina virtual (uma vez)

1. [Baixar a mais recente DPDK](https://core.dpdk.org/download). Version 18.02 or higher is required for Azure.
2. Crie a configuração padrão com `make config T=x86_64-native-linuxapp-gcc`.
3. Habilitar Mellanox PMDs a configuração gerada com `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Compilar com `make`.
5. Instalar com `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>Configurar o ambiente de tempo de execução

Depois de reiniciar, execute os comandos a seguir, uma vez:

1. Hugepages

   * Configure a página de abra executando o seguinte comando, uma vez para todos os numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Crie um diretório para montagem com `mkdir /mnt/huge`.
   * Monte as hugepages com `mount -t hugetlbfs nodev /mnt/huge`.
   * Verifique se hugepages estão reservados com `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Existe uma maneira de modificar o arquivo grub para que as páginas grandes sejam reservadas na inicialização seguindo as [instruções](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) para o DPDK. As instruções estão na parte inferior da página. Ao usar uma máquina virtual do Azure Linux, modifique os arquivos em **/etc/config/grub.d** para reservar páginas amplas nas reinicializações.

2. Endereços IP e MAC: Use `ifconfig –a` para exibir o endereço MAC e IP das interfaces de rede. A interface de rede *VF* e a interface de rede *NETVSC* têm o mesmo endereço MAC, mas apenas a interface de rede *NETVSC* tem um endereço IP. Interfaces de FV estão em execução como interfaces subordinadas de interfaces NETVSC.

3. Endereços PCI

   * Use `ethtool -i <vf interface name>` para descobrir qual endereço PCI usar para *VF*.
   * Se *eth0* tem rede acelerada habilitada, garanta que testpmd não assuma acidentalmente o controle do dispositivo VF de pci para *eth0*. Se o aplicativo DPDK acidentalmente assumir a interface de rede de gerenciamento e fizer com que você perca a conexão SSH, use o console serial para interromper o aplicativo DPDK. Você também pode usar o console serial para interromper ou iniciar a máquina virtual.

4. Carga *ibuverbs* em cada reinicialização com `modprobe -a ib_uverbs`. Para apenas 15 SLES, também carrega *mlx4_ib* com `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>PMD à prova de falhas

Os aplicativos DPDK devem ser executados sobre o PMD à prova de falhas exposto no Azure. Se o aplicativo for executado diretamente sobre o PMD de VF, ele não receberá **todos** os pacotes destinados à VM, pois alguns pacotes aparecem na interface sintética. 

Se você executar um aplicativo DPDK sobre o PMD à prova de falhas, ele garantirá que o aplicativo receba todos os pacotes destinados a ele. Ele também garante que o aplicativo continue em execução no modo DPDK, mesmo que o FV seja revogado quando o host estiver em manutenção. Confira mais informações sobre PMD à prova de falhas em [Biblioteca de drivers do modo de sondagem à prova de falhas](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Executar testpmd

Para executar testpmd no modo de raiz, use `sudo` antes do comando *testpmd*.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Básico: Verificação de integridade de inicialização do adaptador à prova de falhas

1. Execute os seguintes comandos para iniciar um aplicativo testpmd de porta única:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Execute os seguintes comandos para iniciar um aplicativo testpmd de porta dupla:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Se você estiver executando testpmd com mais de duas NICs, o argumento `--vdev` seguirá este padrão: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Depois de iniciado, execute `show port info all` para verificar as informações de porta. Você deve ver uma ou duas portas DPDK que são net_failsafe (não *net_mlx4*).
4.  Use `start <port> /stop <port>` para iniciar o tráfego.

Os comandos anteriores iniciam *testpmd* no modo interativo, o que é recomendado, para testar alguns comandos testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Básico: Receptor único remetente/único

Os seguintes comandos periodicamente imprimem as estatísticas de pacotes por segundo:

1. No lado do TX, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. No lado do RX, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Ao executar os comandos anteriores em uma máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* em `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são descartados antes de chegar ao receptor.

### <a name="advanced-single-sendersingle-forwarder"></a>Avançado: Encaminhador de único remetente/único
Os seguintes comandos periodicamente imprimem as estatísticas de pacotes por segundo:

1. No lado do TX, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. No lado do FWD, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Ao executar os comandos anteriores em uma máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* em `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são descartados antes de alcançar o encaminhador. Você não poderá fazer com que um terceiro computador receba o tráfego encaminhado, porque o encaminhador *testpmd* não modifica os endereços da camada 3, a menos que você faça algumas alterações no código.

## <a name="references"></a>Referências

* [Opções EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandos de Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
