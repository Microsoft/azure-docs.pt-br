---
title: Criar um servidor do Azure Analysis Services usando PowerShell | Microsoft Docs
description: Saiba como criar a um servidor do Azure Analysis Services usando PowerShell
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.openlocfilehash: cb42fd3ed51364cf478848cc51ebbb2f175e96d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Criar a um servidor do Azure Analysis Services usando PowerShell

Este guia de início rápido descreve como usar o PowerShell em uma linha de comando para criar um servidor do Azure Analysis Services em um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), em sua assinatura do Azure.

Esta tarefa exige o módulo do Azure PowerShell, versão 4.0 ou posterior. Para saber qual é a versão, execute ` Get-Module -ListAvailable AzureRM`. Para instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!NOTE]
> Criar um servidor pode resultar em um novo serviço faturável. Para saber mais, veja [Preços do Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, você precisa de:

* **Assinatura do Azure**: visite a [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: sua assinatura deve estar associada a um locatário do Azure Active Directory, e você precisa ter uma conta nesse diretório. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Importar o módulo AzureRm.AnalysisServices
Para criar um servidor em sua assinatura, use o módulo de componente [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Carregar o módulo AzureRm.AnalysisServices em sua sessão do PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre em sua assinatura do Azure usando o comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount). Siga as instruções na tela.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
 
Um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. Quando você cria o servidor, precisa especificar um grupo de recursos em sua assinatura. Se você ainda não tiver um grupo de recursos, crie um novo usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos chamado `myResourceGroup` na região Oeste dos EUA.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>Criar um servidor

Crie um novo servidor usando o comando [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). O exemplo a seguir cria um servidor chamado myServer em myResourceGroup, na região Oeste dos EUA, na camada D1, e especifica philipc@adventureworks.com como administrador do servidor.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Limpar recursos

Você pode remover o servidor de sua assinatura usando o comando [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Se você ainda for seguir outros guias de início rápido e tutoriais desta coleção, não remova o servidor. O exemplo a seguir remove o servidor criado na etapa anterior.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas
[Gerenciar o Azure Analysis Services com PowerShell](analysis-services-powershell.md)   
[Implantar um modelo a partir do SSDT](analysis-services-deploy.md)   
[Criar um modelo no portal do Azure](analysis-services-create-model-portal.md)