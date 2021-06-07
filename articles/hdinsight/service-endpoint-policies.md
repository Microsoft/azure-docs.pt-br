---
title: Configurar políticas de ponto de extremidade de serviço – Azure HDInsight
description: Saiba como configurar políticas de ponto de extremidade de serviço para sua rede virtual com o Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940620"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Configurar políticas de ponto de extremidade de serviço de rede virtual para o Azure HDInsight

Este artigo fornece informações sobre como implementar políticas de ponto de extremidade de serviço em redes virtuais com o Azure HDInsight.

## <a name="background"></a>Tela de fundo

O Azure HDInsight permite que você crie clusters em sua própria rede virtual. Se você precisar permitir o tráfego de saída de sua rede virtual para outros serviços do Azure, como contas de armazenamento, poderá criar [políticas de ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoint-policies-overview.md). As políticas de ponto de extremidade de serviço criadas por meio do portal do Azure, no entanto, só permitem que você crie uma política para uma única conta, todas as contas em uma assinatura ou todas as contas em um grupo de recursos.

Como um serviço gerenciado, no entanto, o Azure HDInsight coleta arquivos de dados e de log de cada cluster em contas de armazenamento específicas em cada região. Para que esses dados cheguem ao HDInsight de sua rede virtual, é necessário criar políticas de ponto de extremidade de serviço que permitam o tráfego de saída para pontos de coleta de dados específicos gerenciados pelo Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Políticas de ponto de extremidade de serviço para o HDInsight

Essas políticas de ponto de extremidade de serviço dão suporte à seguinte funcionalidade:

- Coleção de logs e telemetria na criação de cluster, execução de trabalho e operações de plataforma, como o dimensionamento.
- Conectar discos rígidos virtuais (VHDs) a nós de cluster recém-criados para provisionar software e bibliotecas em seu cluster.

Se as políticas de ponto de extremidade de serviço não forem criadas para habilitar esse fluxo de dados, a criação do cluster poderá falhar e o Azure HDInsight não poderá fornecer suporte para seus clusters.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Criar políticas de ponto de extremidade de serviço para o HDInsight

Verifique se as políticas de ponto de extremidade de serviço corretas estão conectadas à sua rede virtual antes de criar novos clusters. Caso contrário, a criação do cluster poderá falhar ou resultar em erro.

Use o seguinte processo para criar as políticas de ponto de extremidade de serviço necessárias:

1. Decida a região em que você criará seu cluster HDInsight.
1. Pesquise essa região na [lista de recursos de política de ponto de extremidade de serviço](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), que fornece todos os grupos de recursos para contas de armazenamento de gerenciamento do HDInsight.
1. Selecione a lista de grupos de recursos para sua região. Um exemplo dos recursos do `Canada Central` é mostrado abaixo:

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Insira essa lista de grupos de recursos no script de instalação escrito em CLI do Azure ou Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Se você preferir configurar sua política de ponto de extremidade de serviço usando o PowerShell, usando o trecho de código a seguir.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Próximas etapas

* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurar solução de virtualização de rede](./network-virtual-appliance.md)
