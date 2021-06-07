---
title: Atualizar um aplicativo do Service Fabric no PowerShell
description: 'Amostra de script do Azure PowerShell: Atualizar e monitorar um aplicativo do Azure Service Fabric usando o PowerShell.'
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784690"
---
# <a name="upgrade-a-service-fabric-application"></a>Fazer upgrade de um aplicativo do Service Fabric

Esse exemplo de script faz upgrade de uma instância de aplicativo do Service Fabric em execução para a versão 1.3.0. O script copia o novo pacote de aplicativos para o repositório de imagens do cluster, registra o tipo de aplicativo e remove o pacote de aplicativos desnecessários.  O script inicia uma atualização monitorada e verifica continuamente o status da atualização até sua conclusão ou reversão. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo Service Fabric do PowerShell com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | Obtém todos os aplicativos no cluster do Service Fabric ou um aplicativo específico.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Obtém o status do upgrade de um aplicativo do Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Obtém os tipos de aplicativo do Service Fabric registrados no cluster do Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Cancela o registro de um tipo de aplicativo do Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Copia um pacote de aplicativos do Service Fabric para o repositório de imagens.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Registra um tipo de aplicativo do Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Faz upgrade de um aplicativo do Service Fabric para a versão do tipo de aplicativo especificado. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Remove do repositório de imagens um pacote de aplicativos do Service Fabric.|


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Service Fabric, confira [Documentação do Azure PowerShell](/powershell/azure/service-fabric/overview).

Mais exemplos do PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
