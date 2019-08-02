---
title: Início Rápido - Criar um servidor do Azure Analysis Services usando PowerShell | Microsoft Docs
description: Saiba como criar a um servidor do Azure Analysis Services usando PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8159cc8d0e00e830f52e1814ac770c4a16f6b957
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619345"
---
# <a name="quickstart-create-a-server---powershell"></a>Início Rápido: Criar um servidor – PowerShell

Este início rápido descreve como usar o PowerShell em uma linha de comando para criar um servidor do Azure Analysis Services em sua assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**: Acesse [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
- **Azure Active Directory**: Sua assinatura deve estar associada a um locatário do Azure Active Directory e você precisa ter uma conta nesse diretório. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md).
- **Azure PowerShell**. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Para instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Importar o módulo Az.AnalysisServices

Para criar um servidor em sua assinatura, use o módulo [Az.AnalysisServices](/powershell/module/az.analysisservices). Carregar o módulo Az.AnalysisServices em sua sessão do PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na assinatura do Azure usando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. Quando você cria o servidor, precisa especificar um grupo de recursos em sua assinatura. Se você ainda não tem um grupo de recursos, crie um novo usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado `myResourceGroup` na região Oeste dos EUA.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Criar um servidor

Crie um novo servidor usando o comando [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). O exemplo a seguir cria um servidor chamado myServer em myResourceGroup, na região Oeste dos EUA, na camada D1 (grátis) e especifica philipc@adventureworks.com como administrador do servidor.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Limpar recursos

Você pode remover o servidor de sua assinatura usando o comando [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). Se você ainda for seguir outros guias de início rápido e tutoriais desta coleção, não remova o servidor. O exemplo a seguir remove o servidor criado na etapa anterior.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um servidor em sua assinatura do Azure usando o PowerShell. Agora que você tem um servidor, pode ajudar a protegê-lo configurando um firewall do servidor (opcional). Você também pode adicionar um modelo de dados de amostra básico ao servidor diretamente do portal. Ter um modelo de exemplo é útil para saber mais sobre como configurar funções do modelo de banco de dados e testar conexões de cliente. Para saber mais, continue para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Início Rápido: Configurar firewall do servidor – Portal](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao seu servidor](analysis-services-create-sample-model.md)
