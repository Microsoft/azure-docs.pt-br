---
title: Melhores práticas de rede do Azure Service Fabric | Microsoft Docs
description: Melhores práticas de gerenciamento de rede do Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 86ad6fce34f323d94f7b9c318ba81f547360d4df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038477"
---
# <a name="networking"></a>Rede

Conforme você cria e gerencia clusters do Azure Service Fabric, você fornece conectividade de rede para seus nós e seus aplicativos. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, balanceadores de carga e grupos de segurança de rede. Neste artigo, você aprenderá as melhores práticas para esses recursos.

Examine [Padrões de rede do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) para saber como criar clusters que usam os seguintes recursos: Rede virtual ou sub-rede existente, endereço IP público estático, balanceador de carga somente interno ou balanceador de carga interno e externo.

## <a name="infrastructure-networking"></a>Rede de infraestrutura
Maximize o desempenho de sua Máquina Virtual com a Rede Acelerada declarando a propriedade enableAcceleratedNetworking no modelo do Resource Manager. O seguinte snippet refere-se a NetworkInterfaceConfigurations de um conjunto de dimensionamento de máquinas virtuais que habilita a Rede Acelerada:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
O cluster do Service Fabric pode ser provisionado no [Linux com a Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) e no [Windows com a Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

A Rede Acelerada é compatível com SKUs das Séries de Máquinas Virtuais do Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. A Rede Acelerada foi testada com êxito usando o SKU Standard_DS8_v3 em 23/1/2019 em um Cluster do Windows no Service Fabric e usando o Standard_DS12_v2 em 29/01/2019 em um Cluster do Linux no Service Fabric.

Para habilitar a Rede Acelerada em um cluster existente do Service Fabric, primeiro você precisará [Expandir um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) para executar o seguinte:
1. Provisionar um NodeType com a Rede Acelerada habilitada
2. Migrar serviços e seus estados para o NodeType provisionado com a Rede Acelerada habilitada

A expansão da infraestrutura é necessária para habilitar a Rede Acelerada em um cluster existente, pois a habilitação da Rede Acelerada in-loco causará tempo de inatividade, já que ela exige que todas as máquinas virtuais em um conjunto de disponibilidade sejam [paradas e desalocadas antes de habilitar a Rede Acelerada em uma NIC existente](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Rede de cluster

* Os clusters do Service Fabric podem ser implantados em uma rede virtual existente seguindo as etapas descritas em [Padrões de rede do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Os NSGs (grupos de segurança de rede) são recomendados para tipos de nós que restringem o tráfego de entrada e saída para o cluster. Verifique se as portas necessárias estão abertas no NSG. Por exemplo:  ![Regras NSG do Service Fabric][NSGSetup]

* O tipo de nó primário, que contém os serviços do sistema do Service Fabric, não precisa ser exposto por meio do balanceador de carga externo e pode ser exposto por um [balanceador de carga interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Use um [endereço IP público estático](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) para seu cluster.

## <a name="application-networking"></a>Rede de aplicativo

* Para executar cargas de trabalho de contêiner do Windows, use o [modo de rede aberto](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) para facilitar a comunicação de serviço a serviço.

* Use um proxy reverso, como o [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) ou o [proxy reverso do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy), para expor portas de aplicativo comuns, como 80 ou 443.

## <a name="next-steps"></a>Próximas etapas

* Criar um cluster em VMs ou em computadores executando o Windows Server: [Criação de cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster nas VMS ou computadores executando Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
