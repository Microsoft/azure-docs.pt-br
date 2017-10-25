---
title: Criar uma regra do Azure Load Balancer para um cluster
description: Configure um Azure Load Balancer para abrir portas para seu cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: adegeo
ms.openlocfilehash: d152444f38e7a09b97ce7cb9778d8c67a0a5a421
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Abrir portas para um cluster do Service Fabric

O balanceador de carga implantado com o cluster do Azure Service Fabric direciona o tráfego para seu aplicativo em execução em um nó. Se você alterar o aplicativo para usar uma porta diferente, será preciso expor essa porta (ou rotear outra porta) no Azure Load Balancer.

Na implantação do cluster do Service Fabric no Azure, um balanceador de carga foi criado automaticamente para você. Caso não tenha um balanceador de carga, confira [Configurar um balanceador de carga voltado para Internet](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Configurar o Service Fabric

O arquivo de configuração do seu aplicativo do Service Fabric **ServiceManifest.xml** define os pontos de extremidade que o aplicativo espera usar. Depois que o arquivo de configuração tiver sido atualizado para definir um ponto de extremidade, o balanceador de carga deverá ser atualizado para expor essa porta (ou outra). Para saber mais sobre como criar o ponto de extremidade do Service Fabric, confira [Configurar um ponto de extremidade](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga abre uma porta voltada para a Internet e encaminha o tráfego para a porta do nó interna usado pelo seu aplicativo. Caso não tenha um balanceador de carga, confira [Configurar um balanceador de carga voltado para Internet](..\load-balancer\load-balancer-get-started-internet-portal.md).

Para criar uma regra de balanceador de carga, você precisa coletar as seguintes informações:

- Nome do balanceador de carga.
- Grupo de recursos do cluster do Service Fabric e do balanceador de carga.
- Porta externa.
- Porta interna.

## <a name="azure-cli"></a>CLI do Azure
É preciso apenas um único comando para criar uma regra de balanceador de carga com a **CLI do Azure**. Você só precisa saber o nome do balanceador de carga e do grupo de recursos para criar uma nova regra.

>[!NOTE]
>Se precisar determinar o nome do balanceador de carga, use esse comando para obter rapidamente uma lista de todos os balanceadores de carga e os grupos de recursos associados.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

O comando da CLI do Azure tem alguns parâmetros que são descritos na seguinte tabela:

| Parâmetro | Descrição |
| --------- | ----------- |
| `--backend-port`  | A porta em que o aplicativo do Service Fabric está escutando. |
| `--frontend-port` | A porta que o balanceador de carga expõe para conexões externas. |
| `-lb-name` | O nome do balanceador de carga a ser alterado. |
| `-g`       | O grupo de recursos que tem o cluster do Service Fabric e do balanceador de carga. |
| `-n`       | O nome desejado da regra. |


>[!NOTE]
>Para saber mais sobre como criar um balanceador de carga com a CLI do Azure, confira [Criar um balanceador de carga com a CLI do Azure](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

O PowerShell é um pouco mais complicado do que a CLI do Azure. Siga estas etapas conceituais para criar uma regra:

1. Obtenha o balanceador de carga do Azure.
2. Criar uma regra.
3. Adicione a regra ao balanceador de carga.
4. Atualize o balanceador de carga.

>[!NOTE]
>Se precisar determinar o nome do balanceador de carga, use esse comando para obter rapidamente uma lista de todos os balanceadores de carga e os grupos de recursos associados.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Em relação ao comando `New-AzureRmLoadBalancerRuleConfig`, o `-FrontendPort` representa a porta que o balanceador de carga expõe para conexões externas e o `-BackendPort` representa a porta em que o aplicativo do Service Fabric está escutando.

>[!NOTE]
>Para saber mais sobre como criar um balanceador de carga com o PowerShell, confira [Criar um balanceador de carga com o PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [rede no Service Fabric](service-fabric-patterns-networking.md).