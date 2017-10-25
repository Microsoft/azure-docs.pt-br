---
title: "Os tipos de nó do Service Fabric e os conjuntos de dimensionamento da máquina virtual | Microsoft Docs"
description: "Saiba como os tipos de nó do Service Fabric se relacionam com os conjuntos de dimensionamento da máquina virtual e como fazer a conexão remotamente com uma instância do conjunto de dimensionamento da VM ou um nó de cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nó do Service Fabric e os conjuntos de dimensionamento da máquina virtual
Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure. Você pode usar os conjuntos de dimensionamento para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Defina um conjunto de dimensionamento separado para cada tipo de nó que você define em um cluster do Azure Service Fabric. Cada tipo de nó pode ser escalada verticalmente ou horizontalmente de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferentes.

A figura abaixo mostra um cluster com dois tipos de nó, denominados FrontEnd e BackEnd. Cada tipo de nó tem cinco nós.

![Um cluster com dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapeando instâncias de conjuntos de dimensionamento de máquinas virtuais para nós
Conforme mostrado na figura anterior, as instâncias do conjunto de dimensionamento começam na instância 0 e, em seguida, aumentam em 1. A numeração é refletida nos nomes do nó. Por exemplo, o nó BackEnd_0 é a instância 0 do conjunto de dimensionamento de BackEnd. Esse conjunto de dimensionamento específico tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você escala um conjunto de dimensionamento verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de dimensionamento geralmente será o nome do conjunto de dimensionamento mais o número de instância seguinte. Em nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nó e conjuntos de dimensionamento
Se você tiver implantado o cluster no portal do Azure ou usado o modelo do exemplo do Azure Resource Manager, todos os recursos em um grupo de recursos serão listados. Você pode ver os balanceadores de carga para cada conjunto de dimensionamento ou tipo de nó. O nome do balanceador de carga usa o seguinte formato: **LB-&lt;nome do tipo de nó&gt;**. Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura a seguir:

![Recursos][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do conjunto de dimensionamento de máquinas virtuais ou a um nó de cluster
Defina um conjunto de dimensionamento separado para cada tipo de nó que você define em um cluster. Você pode escalar os tipos de nó verticalmente ou horizontalmente de forma independente. Você também pode usar diferentes SKUs de VM. Ao contrário das máquinas virtuais de instância única, as instâncias de conjunto de dimensionamento da VM não recebem um endereço IP virtual próprio. Isso pode ser complicado quando você um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Para localizar um endereço IP e porta que você pode usar para se conectar remotamente a uma instância específica, conclua as etapas a seguir.

**Etapa 1**: Localizar o endereço IP virtual para o tipo de nó, obtendo as regras de entrada de NAT para o protocolo RDP.

Primeiro, obtenha os valores de regras de entrada NAT que foram definidos como parte da definição de recurso para `Microsoft.Network/loadBalancers`.

No portal do Azure, na página do balanceador de carga, selecione **Configurações** > **Regras NAT de entrada**. Isso lhe dá o endereço IP e a porta que você pode usar para fazer a conexão remota com a primeira instância de conjunto de dimensionamento. 

![Balanceador de carga][LBBlade]

Na figura a seguir, o endereço IP e porta são **104.42.106.156** e **3389**.

![Regras de NAT][NATRules]

**Etapa 2**: Descobrir a porta que você pode usar para fazer conexão remota com o nó/instância do conjunto de dimensionamento específico.

As instâncias do conjunto de dimensionamento mapeiam para os nós. Use as informações do conjunto de dimensionamento para determinar a porta exata a ser usada.

As portas são alocadas em uma ordem crescente que corresponde à instância do conjunto de dimensionamento. Para o exemplo anterior do tipo de nó FrontEnd, a tabela a seguir lista as portas para cada uma das cinco instâncias de nó. Aplique o mesmo mapeamento para a instância do conjunto de dimensionamento.

| **Instância do conjunto de dimensionamento de máquinas virtuais** | **Porta** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**Etapa 3**: Conectar-se remotamente à instância do conjunto de dimensionamento específico.

A figura a seguir demonstra como usar a Conexão de Área de Trabalho Remota para se conectar à instância do conjunto de dimensionamento FrontEnd_1:

![Conexões de Área de Trabalho Remota][RDP]

## <a name="change-the-rdp-port-range-values"></a>Alterar os valores de intervalo da porta RDP

### <a name="before-cluster-deployment"></a>Antes da implantação de cluster
Quando você configurar o cluster usando um modelo do Gerenciador de Recursos, especifique o intervalo em `inboundNatPools`.

Vá para a definição do recurso para `Microsoft.Network/loadBalancers`. Localize a descrição de `inboundNatPools`.  Substitua os valores `frontendPortRangeStart` e `frontendPortRangeEnd`.

![Valores de inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Depois da implantação de cluster
Alterar os valores de intervalo de porta RDP após a implantação de cluster é mais complexo. Para garantir que você não recicle as VMs, use o Azure PowerShell para definir novos valores. 

> [!NOTE]
> Certifique-se de que você tenha o Azure PowerShell 1.0 ou uma versão posterior instalada em seu computador. Se você não tem o Azure PowerShell 1.0 ou uma versão posterior, recomendamos fortemente que você execute as etapas descritas em [Como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

1. Entre na sua conta do Azure. Se o seguinte comando do PowerShell falhar, verifique se você instalou o PowerShell corretamente.

    ```
    Login-AzureRmAccount
    ```

2. Para obter detalhes sobre o seu balanceador de carga e para ver os valores para a descrição de `inboundNatPools`, execute o seguinte código:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Defina `frontendPortRangeEnd` e `frontendPortRangeStart` para os valores que você deseja.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Como alterar o nome de usuário e senha RDP para nós

Para alterar a senha para todos os nós de um tipo de nó específico, siga as seguintes etapas. Essas alterações serão aplicadas a todos os nós atuais e futuros no conjunto de dimensionamento.

1. Abra o PowerShell como administrador. 
2. Para efetuar logon e selecione sua assinatura para a sessão, execute os comandos a seguir. Altere o `SUBSCRIPTIONID` parâmetro para sua ID da assinatura. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Execute o seguinte script. Use os valores `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` e `PASSWORD` relevantes. Os valores `USERNAME` e `PASSWORD` são as novas credenciais que deverão ser usadas em futuras sessões RDP. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Próximas etapas
* Consulte [visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* Saiba mais em [Introdução e SDK do Service Fabric](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
