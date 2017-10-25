---
title: "Configuração de DHCPv6 para VMs Linux | Microsoft Docs"
description: Como configurar DHCPv6 para VMs Linux.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "ipv6, azure load balancer, pilha dual, ip público, ipv6 nativo, móvel, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 84558cb6e3a5524969f590eb0272a64ad8839ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-dhcpv6-for-linux-vms"></a>Configuração de DHCPv6 para VMs Linux

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Algumas das imagens de máquina virtual do Linux no Azure Marketplace não tem o DHCPv6 configurado por padrão. Para oferecer suporte a IPv6, o DHCPv6 deve ser configurado dentro da distribuição do SO Linux que você está usando. Distribuições diferentes do Linux têm maneiras diferentes de configurar o DHCPv6, pois usam pacotes diferentes.

> [!NOTE]
> Imagens recentes do SUSE Linux e CoreOS no Azure Marketplace foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens.

Este documento descreve como habilitar o DHCPv6 para que a sua máquina virtual Linux obtenha um endereço IPv6.

> [!WARNING]
> A edição inadequada de arquivos de configuração de rede podem causar a perda de acesso à rede para sua VM. Recomendamos que você teste as alterações de configuração em sistemas de não produção. As instruções neste artigo foram testadas em versões mais recentes das imagens do Linux no Azure Marketplace. Consulte a documentação para a versão específica do Linux para obter instruções mais detalhadas.

## <a name="ubuntu"></a>Ubuntu

1. Edite o arquivo `/etc/dhcp/dhclient6.conf` e adicione a linha a seguir:

        timeout 10;

2. Edite a configuração de rede para a interface eth0 com a seguinte configuração:

   * No **Ubuntu 12.04 e 14.04**, edite o arquivo `/etc/network/interfaces.d/eth0.cfg`
   * No **Ubuntu 16.04**, edite o arquivo `/etc/network/interfaces.d/50-cloud-init.cfg`

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Edite o arquivo `/etc/dhcp/dhclient6.conf` e adicione a linha a seguir:

        timeout 10;

2. Edite o arquivo `/etc/network/interfaces` e adicione a configuração a seguir:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Edite o arquivo `/etc/sysconfig/network` e adicione o parâmetro a seguir:

        NETWORKING_IPV6=yes

2. Edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione os dois parâmetros a seguir:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Imagens recentes de SLES e openSUSE no Azure foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens. Se você tiver uma VM baseada em uma imagem SUSE mais antiga ou personalizada, use as seguintes etapas:

1. Instale o pacote `dhcp-client` , se for necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edite o arquivo `/etc/sysconfig/network/ifcfg-eth0` e adicione o parâmetro a seguir:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE Leap

Imagens recentes de SLES e openSUSE no Azure foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens. Se você tiver uma VM baseada em uma imagem SUSE mais antiga ou personalizada, use as seguintes etapas:

1. Edite o arquivo `/etc/sysconfig/network/ifcfg-eth0` e substitua este parâmetro

        #BOOTPROTO='dhcp4'

    pelo seguinte valor:

        BOOTPROTO='dhcp'

2. Adicione o seguinte parâmetro a `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens recentes de CoreOS no Azure foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens. Se você tiver uma VM baseada em uma imagem CoreOS mais antiga ou personalizada, use as seguintes etapas:

1. Edite o arquivo `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
