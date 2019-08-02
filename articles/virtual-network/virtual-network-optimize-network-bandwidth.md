---
title: Otimizar a taxa de transferência de rede de VM | Microsoft Docs
description: Aprenda a otimizar a taxa de transferência de rede de máquina virtual do Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202909"
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

O kernel do Azure no Ubuntu fornece o melhor desempenho de rede no Azure e tem sido o kernel padrão desde 21 de setembro de 2017. Para obter esse kernel, primeiro instale a versão mais recente compatível do 16.04-LTS, da seguinte maneira:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
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

Um desempenho de taxa de transferência significativo pode ser obtido com o upgrade para o kernel do Linux no Azure. Para verificar se você tem esse kernel, verifique a versão do kernel.

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
"Sku": "7.4",
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
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre o Linux Integration Services versão 4.2 para o Hyper-V exibindo a [página de download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Próximas etapas
* Veja o resultado otimizado com o [Teste de Largura de Banda/Taxa de Transferência da VM do Azure](virtual-network-bandwidth-testing.md) para seu cenário.
* Leia sobre como a [largura de banda é alocada para máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com as [Perguntas frequentes sobre a rede virtual do Azure](virtual-networks-faq.md)
