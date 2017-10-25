---
title: Excluir um cluster do Azure e seus recursos | Microsoft Docs
description: "Saiba como excluir por completo um cluster do Service Fabric excluindo o grupo de recursos que contém o cluster ou excluindo os recursos seletivamente."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: chackdan
ms.openlocfilehash: 7672aa12421fbe4ad86e7315d6a7a06c2ff5124d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Excluir um cluster do Service Fabric e os recursos que ele utiliza
Um cluster do Service Fabric é composto por vários outros recursos do Azure, além do próprio recurso de cluster. Portanto, para excluir por completo um cluster do Service Fabric, também é necessário excluir todos os recursos que o compõem.
Você tem duas opções: excluir o grupo de recursos do qual o cluster faz parte (o que exclui o recurso de cluster e quaisquer outros recursos no grupo de recursos) ou excluir especificamente o recurso de cluster e seus recursos associados (mas não os outros recursos no grupo de recursos).

> [!NOTE]
> A exclusão do recurso de cluster **não** exclui todos os outros recursos que compõem o cluster do Service Fabric.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Excluir o RG (grupo de recursos) inteiro do qual o cluster do Service Fabric faz parte
Essa é a maneira mais fácil de garantir que todos os recursos associados ao seu cluster serão excluídos, incluindo o grupo de recursos. Você pode excluir o grupo de recursos usando o PowerShell ou por meio do portal do Azure. Caso o grupo de recursos tenha recursos não relacionados ao cluster do Service Fabric, você poderá excluir recursos específicos.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Excluir o grupo de recursos usando o Azure PowerShell
Também é possível excluir o grupo de recursos executando os seguintes cmdlets do Azure PowerShell. Certifique-se de que o Azure PowerShell 1.0 ou superior está instalado em seu computador. Se não tiver feito isso antes, siga as etapas descritas em [Como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

Abra uma janela do PowerShell e execute os seguintes cmdlets do PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Você receberá um aviso para confirmar a exclusão, caso não tenha usado a opção *-Force* . Após a confirmação, o RG e todos os recursos que ele contém são excluídos.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Excluir um grupo de recursos no portal do Azure
1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Navegue até o cluster do Service Fabric que você deseja excluir.
3. Clique no nome do Grupo de Recursos na página de conceitos básicos do cluster.
4. Isso abre a página **Princípios básicos do grupo de recursos** .
5. Clique em **Excluir**.
6. Siga as instruções na página para concluir a exclusão do grupo de recursos.

![Exclusão do Grupo de Recursos][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Excluir o recurso de cluster e os recursos usados por ele, mas não os outros recursos no grupo de recursos
Se o grupo de recursos tiver apenas recursos relacionados ao cluster do Service Fabric que você deseja excluir, será mais fácil excluir o grupo de recursos inteiro. Se quiser excluir seletivamente os recursos do grupo de recursos um por um, siga estas etapas.

Caso você tenha implantado o cluster usando o portal ou um dos modelos do Resource Manager do Service Fabric da galeria de modelos, todos os recursos usados pelo cluster serão marcados com as duas marcações a seguir. Você pode usá-las para decidir quais recursos deseja excluir.

***Marcação nº 1:*** Chave = clusterName, Valor = 'nome do cluster'

***Marcação n º 2:*** Chave = resourceName, Valor = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Excluir recursos específicos no Portal do Azure
1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Navegue até o cluster do Service Fabric que você deseja excluir.
3. Vá para **Todas as configurações** na folha Conceitos básicos.
4. Clique em **Marcações** em **Gerenciamento de Recursos** na folha Configurações.
5. Clique em uma das **Marcações** na folha Marcações para obter uma lista de todos os recursos com essa marcação.
   
    ![Marcações de recursos][ResourceTags]
6. Depois de obter a lista de recursos marcados, clique em cada um dos recursos e os exclua.
   
    ![Recursos marcados][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Excluir os recursos usando o Azure PowerShell
É possível excluir os recursos um por um executando os seguintes cmdlets do Azure PowerShell. Certifique-se de que o Azure PowerShell 1.0 ou superior está instalado em seu computador. Se não tiver feito isso antes, siga as etapas descritas em [Como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

Abra uma janela do PowerShell e execute os seguintes cmdlets do PS:

```powershell
Login-AzureRmAccount
```
Para cada um dos recursos que você deseja excluir, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Para excluir o recurso de cluster, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos para saber também sobre como atualizar um cluster e particionar serviços:

* [Saiba mais sobre atualizações de cluster](service-fabric-cluster-upgrade.md)
* [Saiba mais sobre os serviços com estado de particionamento para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
