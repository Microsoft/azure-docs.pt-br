---
title: Otimizar a taxa de transferência de rede de VM | Microsoft Docs
description: Otimize a taxa de transferência de rede para VMs Microsoft Azure Windows e Linux, incluindo distribuições principais como Ubuntu, CentOS e Red Hat.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: bb9235f4d1190bf7f71ddc007f09c9666c353234
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216794"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar a taxa de transferência de rede para máquinas virtuais do Azure

Máquinas virtuais do Azure (VM) têm configurações de rede padrão que podem ser mais otimizadas para taxa de transferência de rede. Este artigo descreve como otimizar a taxa de transferência de rede para VMs Windows e Linux do Microsoft Azure, incluindo as distribuições principais como o Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM Windows

Se sua VM Windows for compatível com a [Rede Acelerada](create-vm-accelerated-networking-powershell.md), habilitar esse recurso será a configuração ideal para a taxa de transferência. Para todas as outras VMs do Windows, usar RSS (Receive Side Scaling) pode alcançar uma taxa de transferência máxima maior que uma VM sem RSS. RSS pode ser desabilitado por padrão em uma VM do Windows. Para determinar se o RSS está habilitado, e habilitá-lo se ele estiver desabilitado no momento, conclua as seguintes etapas:

1. Veja se o RSS está habilitado para um adaptador de rede no comando do PowerShell `Get-NetAdapterRss`. Na saída do exemplo seguinte retornada do `Get-NetAdapterRss`, RSS não está habilitado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Para habilitar o RSS, insira o seguinte comando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem uma saída. O comando alterou configurações de NIC, causando uma perda de conectividade temporária por aproximadamente um minuto. Aparece uma caixa de diálogo Reconnecting durante a perda de conectividade. Normalmente, a conectividade for restaurada após a terceira tentativa.
3. Confirme se o RSS está habilitado na VM inserindo o `Get-NetAdapterRss` comando novamente. Se for bem-sucedido, será retornada a seguinte saída de exemplo:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>VM Linux

RSS está sempre habilitado por padrão em uma VM do Linux do Azure. Kernels do Linux liberados desde outubro de 2017 incluem novas opções de otimização de rede que permitem que uma VM Linux obtenha maior taxa de transferência de rede.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implantações

O kernel do Ubuntu Azure é o mais otimizado para o desempenho de rede no Azure. Para obter as otimizações mais recentes, primeiro instale a versão mais recente com suporte do 18, 4-LTS, da seguinte maneira:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

Depois que a criação for concluída, digite os seguintes comandos para obter as atualizações mais recentes. Essas etapas também funcionam para VMs executando o kernel do Azure no Ubuntu no momento.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

O conjunto de comandos opcional a seguir pode ser útil para implantações do Ubuntu existentes que já têm o kernel do Azure, mas que falharam ao obter atualizações com erros.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Atualização de kernel do Azure no Ubuntu para VMs existentes

Um desempenho de taxa de transferência significativo pode ser obtido com o upgrade para o kernel do Linux no Azure. Para verificar se você tem esse kernel, verifique a versão do kernel. Ele deve ser o mesmo ou posterior ao exemplo.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Se sua VM não tiver o kernel do Azure, o número de versão geralmente começará com "4.4". Se a VM não tiver o kernel do Azure, execute os comandos a seguir como raiz:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter as otimizações mais recentes, é melhor criar uma VM com a versão mais recente com suporte especificando os seguintes parâmetros:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

VMs novas e existentes podem se beneficiar da instalação do LIS (Linux Integration Services) mais recente. A otimização de taxa de transferência está no LIS, começando a partir de 4.2.2-2, embora as versões posteriores contenham mais melhorias. Insira os seguintes comandos para instalar o último LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter as otimizações, é melhor criar uma VM com a versão mais recente com suporte especificando os seguintes parâmetros:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

VMs novas e existentes podem se beneficiar da instalação do LIS (Linux Integration Services) mais recente. A otimização de taxa de transferência é feita no LIS, a partir da versão 4.2. Digite os comandos a seguir para baixar e instalá-los:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre o Linux Integration Services versão 4.2 para o Hyper-V exibindo a [página de download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Próximas etapas
* Implantar VMs próximas umas às outras para baixa latência com o [grupo de posicionamento de proximidade](../virtual-machines/co-location.md)
* Veja o resultado otimizado com o [Teste de Largura de Banda/Taxa de Transferência da VM do Azure](virtual-network-bandwidth-testing.md) para seu cenário.
* Leia sobre como [a largura de banda é alocada para máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com as [Perguntas frequentes sobre a rede virtual do Azure](virtual-networks-faq.md)