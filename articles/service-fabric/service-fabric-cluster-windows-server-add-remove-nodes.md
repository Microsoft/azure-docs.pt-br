---
title: Adicionar ou remover nós de um cluster autônomo do Service Fabric | Microsoft Docs
description: Saiba como adicionar ou remover nós de um cluster do Azure Service Fabric em um computador físico ou virtual executando o Windows Server, que pode ser local ou em qualquer nuvem.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 585d918026ca40bc1a04c55e2bac454492c55936
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711026"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós de um cluster do Service Fabric autônomo em execução no Windows Server
Depois de ter [criado seu cluster autônomo do Service Fabric em computadores com Windows Server](service-fabric-cluster-creation-for-windows-server.md), suas necessidades (ou dos seus negócios) podem mudar e talvez seja preciso adicionar ou remover nós do cluster. Este artigo fornece as etapas detalhadas para fazer isso. Observe que não há suporte para a funcionalidade de adicionar/remover nó em clusters de desenvolvimento local.

## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao cluster

1. Prepare a VM/computador que você deseja adicionar ao seu cluster seguindo as etapas descritas em [Planejar e preparar a implantação de cluster do Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Identifique a qual domínio de falha e domínio de atualização você vai adicionar essa VM/computador
3. Área de rrabalho remota (RDP) na VM/computador que você deseja adicionar ao cluster
4. Copie ou [baixe o pacote autônomo do Service Fabric para Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) para esta VM/computador e descompacte o pacote
5. Execute o Powershell com privilégios elevados e navegue até o local do pacote descompactado
6. Execute o script *AddNode.ps1* com os parâmetros que descrevem o novo nó a adicionar. O exemplo abaixo adiciona um novo nó chamado VM5, com o tipo NodeType0 e endereço IP 182.17.34.52 em UD1 e fd:/dc1/r0. O *ExistingClusterConnectionEndPoint* é um ponto de extremidade de conexão para um nó em um cluster existente, que pode ser o endereço IP de *qualquer* nó no cluster.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Depois que o script terminar a execução, você pode verificar se o novo nó foi adicionado executando o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps).

7. Para garantir a consistência em diferentes nós do cluster, você deve iniciar uma atualização de configuração. Execute [ServiceFabricClusterConfiguration Get](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o arquivo de configuração mais recente e adicionar o nó recém-adicionado à seção "Nós". Também é recomendável sempre ter a configuração de cluster mais recente disponível no caso em que você precisará reimplantar um cluster com a mesma configuração.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Execute [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Você pode monitorar o andamento da atualização no Service Fabric Explorer. Como alternativa, você pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Adicionar nós aos clusters configurados com a Segurança do Windows usando a gMSA
Para clusters configurados com a Conta de Serviço Gerenciado de Grupo (gMSA)(https://technet.microsoft.com/library/hh831782.aspx), um novo nó pode ser adicionado usando uma atualização de configuração:
1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) em qualquer um dos nós existentes para obter o arquivo de configuração mais recente e adicione os detalhes sobre o novo nó que você deseja adicionar na seção "Nós". Verifique se que o novo nó é parte da mesma conta gerenciada de grupo. Essa conta deve ser um Administrador em todos os computadores.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Execute [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Você pode monitorar o andamento da atualização no Service Fabric Explorer. Como alternativa, você pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Adicionar tipos de nós ao cluster
Para adicionar um novo tipo de nó, modifique a configuração para incluir o novo tipo de na seção "Tipos de Nós" em "Propriedades" e comece uma atualização de configuração usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Quando a atualização for concluída, você pode adicionar novos nós ao cluster com esse tipo de nó.

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do cluster
Um nó pode ser removido de um cluster usando uma atualização de configuração, da seguinte maneira:

1. Execute [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o arquivo de configuração mais recente e *remover* o nó da seção "Nós".
Adicione o parâmetro "NodesToBeRemoved" na seção "Configurar" dentro da seção "Configurações do Fabric". O "valor" deve ser uma lista separada por vírgulas de nomes de nó de nós que devem ser removidos.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Execute [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Você pode monitorar o andamento da atualização no Service Fabric Explorer. Como alternativa, você pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> A remoção de nós pode iniciar várias atualizações. Alguns nós são marcados com a marca `IsSeedNode=”true”` e podem ser identificadas consultando o manifesto do cluster usando `Get-ServiceFabricClusterManifest`. A remoção desses nós pode levar mais tempo do que outros, pois os nós de propagação terão de ser movidos nesses cenários. O cluster deve manter um mínimo de 3 nós do tipo de nó primário.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Remover tipos de nó do cluster
Antes de remover um tipo de nó, verifique novamente se há qualquer nó fazendo referência ao tipo de nó. Remova esses nós antes de remover o tipo de nó correspondente. Depois que todos os nós correspondentes são removidos, você pode remover o Tipo de Nó da configuração do cluster e começar uma configuração de atualização usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Substituir nós primários de seu cluster
A substituição de nós primários deve ser realizada um nó após o outro, em vez de remover e depois adicionar em lotes.


## <a name="next-steps"></a>Próximas etapas
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)
* [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

