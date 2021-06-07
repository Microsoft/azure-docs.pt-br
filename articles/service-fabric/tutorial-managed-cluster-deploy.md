---
title: Implantar um cluster gerenciado do Service Fabric (versão prévia)
description: Neste tutorial, você implantará um cluster gerenciado do Service Fabric para teste.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791704"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Tutorial: Implantar um cluster gerenciado do Service Fabric (versão prévia)

Nesta série de tutoriais, discutiremos:

> [!div class="checklist"]
> * Como implantar um cluster gerenciado do Service Fabric 
> * [Como escalar horizontalmente um cluster gerenciado do Service Fabric](tutorial-managed-cluster-scale.md)
> * [Como adicionar e remover nós em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Como implantar um aplicativo em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-deploy-app.md)

Esta parte da série aborda como:

> [!div class="checklist"]
> * Conectar-se à sua conta do Azure
> * Criar um grupo de recursos
> * Implantar um cluster gerenciado do Service Fabric
> * Adicionar um tipo de nó primário ao cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) se você ainda não tiver uma assinatura do Azure

* Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md).

* Instale o [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (ou posteriores).

## <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Substitua `<your-subscription>` pela cadeia de assinatura da conta do Azure e se conecte:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Criar um grupo de recursos

Em seguida, crie o grupo de recursos do cluster gerenciado do Service Fabric, substituindo `<your-rg>` e `<location>` pelo nome e localização do grupo desejado.

> [!NOTE]
> As regiões com suporte na versão prévia pública incluem `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus` e `eastus2`.

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Implantar um cluster gerenciado do Service Fabric

### <a name="create-a-service-fabric-managed-cluster"></a>Criar um cluster gerenciado do Service Fabric

Nesta etapa, você criará um cluster gerenciado do Service Fabric usando o comando New-AzServiceFabricManagedCluster do PowerShell. O exemplo a seguir cria um cluster chamado myCluster no grupo de recursos chamado myResourceGroup. Esse grupo de recursos foi criado na etapa anterior na região eastus2.

Nessa etapa, forneça os próprios valores para os seguintes parâmetros:

* **Nome do cluster**: Insira um nome exclusivo para o cluster, como *mysfcluster*.
* **Senha do Administrador**: Insira uma senha para o administrador a ser usada para o RDP nas VMs subjacentes no cluster.
* **Impressão Digital do Certificado do Cliente**: Forneça a impressão digital do certificado do cliente que você deseja usar para acessar o cluster. Se você não tiver um certificado, siga [definir e recuperar um certificado](../key-vault/certificates/quick-create-portal.md) para criar um certificado autoassinado.
* **SKU do cluster**: Especifique o [tipo de cluster gerenciado do Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) a ser implantado. Os clusters de SKU *básicos* destinam-se apenas a implantações de teste e não permitem a adição ou remoção de tipo de nó.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Adicionar um tipo de nó primário ao cluster gerenciado do Service Fabric

Nesta etapa, você adicionará um tipo de nó primário ao cluster que acabou de criar. Todos os clusters do Service Fabric precisam ter pelo menos um tipo de nó primário.

Nessa etapa, forneça os próprios valores para os seguintes parâmetros:

* **Nome do Tipo de Nó**: Insira um nome exclusivo para o tipo de nó a ser adicionado ao cluster, como "NT1".

> [!NOTE]
> Se o tipo de nó que está sendo adicionado for o primeiro ou o único tipo de nó no cluster, a propriedade Primária precisará ser usada.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Esse comando pode demorar um pouco para ser concluído.

## <a name="validate-the-deployment"></a>Validar a implantação

### <a name="review-deployed-resources"></a>Examinar os recursos implantados

Quando a implantação for concluída, localize o valor do Service Fabric Explorer na página Visão geral do recurso de cluster gerenciado do Service Fabric no Portal. Quando um certificado for solicitado, use o certificado para o qual a impressão digital do cliente foi fornecida no comando do PowerShell.

## <a name="next-steps"></a>Próximas etapas

Nesta etapa, criamos e implantamos nosso primeiro cluster gerenciado do Service Fabric. Para saber mais sobre como escalar um cluster, confira:

> [!div class="nextstepaction"]
> [Escalar horizontalmente um cluster gerenciado do Service Fabric](tutorial-managed-cluster-scale.md)
