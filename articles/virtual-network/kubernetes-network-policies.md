---
title: Políticas de rede do Kubernetes do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de rede do Kubernetes para proteger seu cluster Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: ff6fd45e0a68a3e93e4c62eb31a566a6dffa2344
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494942"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Visão geral das políticas de rede do Kubernetes do Azure

As políticas de rede fornecem microssegmentação para pods da mesma forma que os NSGs (Grupos de Segurança de Rede) fornecem para VMs. A implementação da Política de rede do Azure é compatível com a especificação de Política de rede de Kubernetes padrão. É possível usar rótulos para selecionar um grupo de pods e definir uma lista de regras de entrada e saída que especificam o tipo de tráfego permitido de e para esses pods. Saiba mais sobre as políticas de rede do Kubernetes na [documentação do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Visão geral das políticas de rede do Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

As políticas de rede do Azure trabalham em conjunto com o Azure CNI, que fornece integração de VNet para contêineres. Atualmente, ele é compatível apenas com nós do Linux. As implementações configuram regras da tabela de IP do Linux com base nas políticas definidas para impor a filtragem de tráfego.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planejar a segurança para o cluster Kubernetes
Ao implementar a segurança para o cluster, use NSGs (grupos de segurança de rede) para filtrar o tráfego Norte-Sul, ou seja, o tráfego que entra e sai da sub-rede do cluster e usa políticas de rede kubernetes para o tráfego leste-oeste, ou seja, o tráfego entre pods seu cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Usar as políticas de rede do Kubernetes do Azure
As políticas de rede do Azure podem ser usadas das maneiras a seguir para fornecer microssegmentação aos pods.

### <a name="acs-engine"></a>ACS-engine
O ACS-Engine é uma ferramenta que gera um modelo do Azure Resource Manager para a implantação de um cluster Kubernetes no Azure. A configuração do cluster é especificada em um arquivo JSON que é passado para a ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de configurações de cluster compatíveis e suas descrições, consulte Mecanismo do Serviço de Contêiner do Microsoft Azure – definição de cluster.

Para habilitar as políticas em clusters implantados usando o acs-engine, especifique o valor da configuração networkPolicy no arquivo de definição do cluster como "azure".

#### <a name="example-configuration"></a>Exemplo de configuração

A configuração de exemplo JSON abaixo cria uma rede virtual e uma sub-rede, e implanta um cluster Kubernetes nele com o Azure CNI. É recomendável que você use o "Bloco de notas do Windows" para editar o arquivo JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Criar seu próprio cluster Kubernetes no Azure
A implementação pode ser usada para fornecer políticas de rede para Pods em clusters Kubernetes que você implantou por conta própria, sem contar com ferramentas como o ACS-Engine. Nesse caso, instale primeiro o plug-in do CNI e habilite-o em todas as máquinas virtuais em um cluster. Para ver instruções detalhadas, consulte [Implantar o plug-in para um cluster Kubernetes que você implantou por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Depois que o cluster for implantado, execute o seguinte comando `kubectl` para baixar e aplicar a política de rede do Azure *daemonset* ao cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é um software livre e o código está disponível no [repositório da Rede de Contêiner do Azure](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Serviço de Kubernetes do Azure](../aks/intro-kubernetes.md).
-  Saiba mais sobre [redes de contêiner](container-networking-overview.md).
- [Implante o plug-in](deploy-container-networking.md) para clusters Kubernetes ou contêineres do Docker.
