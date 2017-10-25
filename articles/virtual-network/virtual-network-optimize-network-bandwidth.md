---
title: "Otimizar a taxa de transferência de rede de VM | Microsoft Docs"
description: "Aprenda a otimizar a taxa de transferência de rede de máquina virtual do Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: 914747983d4d974810836be66d6c6af343f58b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar a taxa de transferência de rede para máquinas virtuais do Azure

Máquinas virtuais do Azure (VM) têm configurações de rede padrão que podem ser mais otimizadas para taxa de transferência de rede. Este artigo descreve como otimizar a taxa de transferência de rede para VMs em Windows e Linux do Microsoft Azure, incluindo distribuições principais como o Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM Windows

Se sua VM do Windows tiver suporte para [Rede Acelerada](virtual-network-create-vm-accelerated-networking.md), habilitar esse recurso será a configuração ideal para a taxa de transferência. Para todas as outras VMs do Windows, usar RSS (Receive Side Scaling) pode alcançar uma taxa de transferência máxima maior que uma VM sem RSS. RSS pode ser desabilitado por padrão em uma VM do Windows. Conclua as seguintes etapas para determinar se o RSS está habilitado e habilitá-lo se ele estiver desabilitado.

1. Insira o `Get-NetAdapterRss` comando do PowerShell para ver se o RSS está habilitado para um adaptador de rede. Na saída do exemplo seguinte retornada do `Get-NetAdapterRss`, RSS não está habilitado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Digite o comando a seguir para habilitar o RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem uma saída. O comando alterou configurações de NIC, causando uma perda de conectividade temporária por aproximadamente um minuto. Aparece uma caixa de diálogo Reconnecting durante a perda de conectividade. Normalmente, a conectividade for restaurada após a terceira tentativa.
3. Confirme se o RSS está habilitado na VM inserindo o `Get-NetAdapterRss` comando novamente. Se for bem-sucedido, será retornada a seguinte saída de exemplo:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>VM Linux

RSS está sempre habilitado por padrão em uma VM do Linux do Azure. Kernels do Linux lançados desde janeiro de 2017 incluem novas opções de otimização de rede que permitem que uma VM do Linux obter maior taxa de transferência de rede.

### <a name="ubuntu"></a>Ubuntu

Para obter a otimização, primeiro atualize para a versão mais recente com suporte, a partir de junho de 2017, que é:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Depois que a atualização for concluída, digite os seguintes comandos para obter o kernel mais recente:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Comando opcional:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-preview-kernel"></a>Kernel de visualização do Azure no Ubuntu
> [!WARNING]
> Esse núcleo de Visualização do Linux do Azure pode não ter o mesmo nível de disponibilidade e confiabilidade que imagens e kernels do Marketplace em versão de disponibilidade, em geral. O recurso não tem suporte, pode ter recursos restritos e pode não estar tão confiável quanto o kernel padrão. Não use este kernel para cargas de trabalho de produção.

O desempenho de taxa de transferência significativo pode ser obtido pela instalação do kernel do Linux do Azure proposto. Para testar este kernel, adicione esta linha a /etc/apt/sources.list

```bash
#add this to the end of /etc/apt/sources.list (requires elevation)
deb http://archive.ubuntu.com/ubuntu/ xenial-proposed restricted main multiverse universe
```

Em seguida, execute estes comandos como raiz.
```bash
apt-get update
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter a otimização, primeiro atualize para a versão mais recente com suporte, a partir de julho de 2017, que é:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
Depois que a atualização for concluída, instale o LIS (Linux Integration Services) mais recente.
A otimização de taxa de transferência é feita no LIS, a partir da versão 4.2.2-2. Digite os seguintes comandos para instalar o LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter a otimização, primeiro atualize para a versão mais recente com suporte, a partir de julho de 2017, que é:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017071923"
```
Depois que a atualização for concluída, instale o LIS (Linux Integration Services) mais recente.
A otimização de taxa de transferência é feita no LIS, a partir da versão 4.2. Digite os comandos a seguir para baixar e instalá-los:

```bash
mkdir lis4.2.2-2
cd lis4.2.2-2
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
tar xvzf lis-rpms-4.2.2-2.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre o Linux Integration Services versão 4.2 para o Hyper-V exibindo a [página de download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Próximas etapas
* Agora que a VM está otimizada, veja o resultado com [Teste de Largura de Banda/Taxa de Transferência da VM do Azure](virtual-network-bandwidth-testing.md) para seu cenário.
* Saiba mais com as [Perguntas frequentes sobre a rede virtual do Azure](virtual-networks-faq.md)
