---
title: Exemplos do Azure PowerShell – Redefinir Serviços de Nuvem do Azure (suporte estendido)
description: Scripts de exemplo para redefinir uma implantação do Serviço de Nuvem do Azure (suporte estendido)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475313"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Redefinir um Serviço de Nuvem do Azure (suporte estendido) 
Esses exemplos abordam várias maneiras de redefinir uma implantação existente do Serviço de Nuvem do Azure (suporte estendido).

## <a name="reimage-role-instances-of-cloud-service"></a>Refazer imagem das instâncias de função do serviço de nuvem
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Este comando refaz a imagem de duas instâncias de função (ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1) do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Refazer a imagem de todas as funções do Serviço de Nuvem
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Este comando refaz a imagem de todas as instâncias de função do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Refazer a imagem de uma instância de função de um Serviço de Nuvem
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Este comando refaz a imagem da instância de função chamada ContosoFrontEnd_IN_0 do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Recompilar instâncias de função do serviço de nuvem
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Este comando recompila a imagem de duas instâncias de função (ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1) do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Recompilar a imagem de todas as funções do serviço de nuvem
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Este comando recompila a imagem de todas as instâncias de função do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Reiniciar as instâncias de função do serviço de nuvem
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Este comando reinicia duas instâncias de função (ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1) do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Reiniciar todas as funções do serviço de nuvem
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Este comando reinicia todas as instâncias de função do serviço de nuvem denominado ContosoCS, que pertence ao grupo de recursos denominado ContosOrg.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os Serviços de Nuvem do Azure (suporte estendido), confira [Visão geral dos Serviços de Nuvem do Azure (suporte estendido)](overview.md).
- Visite o [repositório de exemplos dos Serviços de Nuvem (suporte estendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
