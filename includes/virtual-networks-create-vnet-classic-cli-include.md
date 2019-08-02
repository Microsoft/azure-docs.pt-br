---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cd686e1bf62bbd7f37f61ced767e92918edf919c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171840"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Como criar uma rede virtual clássica usando a CLI do Azure
Você pode usar a CLI do Azure para gerenciar os recursos do Azure no prompt de comando de qualquer computador com Windows, Linux ou OSX.

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../articles/cli-install-nodejs.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Para criar uma Rede Virtual e uma sub-rede, execute o comando **azure network vnet create**:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Saída esperada:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Nome da rede virtual a ser criada. Para o cenário, *TestVNet*
   * **-e (ou --address-space)** . Espaço de endereço da rede virtual. Para o cenário, *192.168.0.0*
   * **-i (ou -cidr)** . Máscara de rede no formato CIDR. Para o cenário, *16*.
   * **-n (ou --subnet-name**). Nome da primeira sub-rede. Para o cenário, *FrontEnd*.
   * **-p (ou --subnet-start-ip)** . Endereço IP inicial da sub-rede ou espaço de endereço da sub-rede. Para o cenário, *192.168.1.0*.
   * **-r (ou --subnet-cidr)** . Máscara de rede no formato CIDR para a sub-rede. Para o cenário, *24*.
   * **-l (ou --location)** . Região do Azure em que a VNet é criada. Para o cenário, *Central US*.
3. Para criar uma sub-rede, execute o comando **azure network vnet subnet create**:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Resultado esperado para o comando anterior:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (ou --vnet-name**. Nome da rede virtual onde a sub-rede será criada. Para o cenário, *TestVNet*.
   * **-n (or --name)** . Nome da nova sub-rede. Para o cenário, *BackEnd*.
   * **-a (ou --address-prefix)** . Bloco CIDR da sub-rede. Para o cenário, *192.168.2.0/24*.
4. Para visualizar as propriedades da nova Rede Virtual, execute o comando **azure network vnet show**:
   
            azure network vnet show
   
    Resultado esperado para o comando anterior:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

