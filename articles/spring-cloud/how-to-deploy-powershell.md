---
title: Como implantar o Azure Spring Cloud com o Azure PowerShell
description: Como implantar o Azure Spring Cloud com o Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877723"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Implantar o Azure Spring Cloud com o Azure PowerShell

Este artigo descreve como você pode criar uma instância do Azure Spring Cloud usando o módulo do PowerShell [AZ. SpringCloud](/powershell/module/Az.SpringCloud) .

## <a name="requirements"></a>Requisitos

* Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Enquanto o módulo **AZ. SpringCloud** do PowerShell está em versão prévia, você deve instalá-lo separadamente usando o `Install-Module` cmdlet. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Provisionar uma nova instância do Azure Spring Cloud

Para criar uma nova instância do Azure Spring Cloud, use o cmdlet [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) . O exemplo a seguir cria um serviço de nuvem Spring do Azure com o nome especificado no grupo de recursos criado anteriormente.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Criar um novo aplicativo do Azure Spring Cloud

Para criar um novo aplicativo, use o cmdlet [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) . O exemplo a seguir cria um aplicativo de nuvem Spring do Azure chamado `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Criar uma nova implantação do Azure Spring Cloud

Para criar uma nova implantação, use o cmdlet [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) . O exemplo a seguir cria uma implantação do Azure Spring Cloud chamada `default` para o `gateway` aplicativo.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Obter um serviço de nuvem do Azure Spring

Para obter um serviço de nuvem Spring do Azure e suas propriedades, use o cmdlet [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) . O exemplo a seguir recupera informações sobre o serviço de nuvem Spring do Azure especificado.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Obter um aplicativo de nuvem do Azure Spring

Para obter um aplicativo de nuvem Spring do Azure e suas propriedades, use o cmdlet [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) . O exemplo a seguir recupera informações sobre o `gateway` aplicativo Spring Cloud.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Obter uma implantação do Azure Spring Cloud

Para obter uma implantação do Azure Spring Cloud e suas propriedades, use o cmdlet [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) . O exemplo a seguir recupera informações sobre a `default` implantação do Spring Cloud.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, você poderá excluí-los executando o exemplo a seguir.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Excluir uma implantação do Azure Spring Cloud

Para remover uma implantação do Azure Spring Cloud, use o cmdlet [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) . O exemplo a seguir exclui uma implantação de aplicativo do Azure Spring Cloud chamada `default` para o serviço e o aplicativo especificados.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Excluir um aplicativo de nuvem do Azure Spring

Para remover um aplicativo Spring Cloud, use o cmdlet [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) . O exemplo a seguir exclui o `gateway` aplicativo no serviço especificado e no grupo de recursos.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Excluir um serviço de nuvem do Azure Spring

Para remover um serviço de nuvem do Azure Spring, use o cmdlet [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) . O exemplo a seguir exclui o serviço de nuvem Spring do Azure especificado.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Próximas etapas

[Recursos para desenvolvedores de nuvem do Azure Spring](spring-cloud-resources.md).
