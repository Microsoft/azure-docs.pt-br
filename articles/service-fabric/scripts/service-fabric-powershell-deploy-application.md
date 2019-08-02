---
title: Exemplo de script do Azure PowerShell – implantar aplicativo em um cluster | Microsoft Docs
description: Exemplo de script do Azure PowerShell – implantar um aplicativo em um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 53c4d3c18072f2644c472e5b78f144faba4dce50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597725"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implantar um aplicativo em um cluster do Service Fabric

Esse script de exemplo copia um pacote de aplicativos para um repositório de imagens de cluster, registra o tipo de aplicativo no cluster, remove o pacote de aplicativos desnecessário e cria uma instância do aplicativo com base no tipo de aplicativo.  Se algum serviço padrão tiver sido definido no manifesto do aplicativo do tipo de aplicativo de destino, ele também será criado nesse momento. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo Service Fabric do PowerShell com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Depois que o exemplo de script foi executado, o script em [Remover um aplicativo](service-fabric-powershell-remove-application.md) pode ser usado para remover a instância do aplicativo, cancelar o registro do tipo de aplicativo e excluir o pacote de aplicativos do repositório de imagens.

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Cria uma conexão com um cluster do Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia um pacote de aplicativos para o repositório de imagens do cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registra o tipo e a versão do aplicativo no cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Cria um aplicativo com base em um tipo de aplicativo registrado. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove do repositório de imagens um pacote de aplicativos do Service Fabric.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Service Fabric, confira [Documentação do Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Mais exemplos do PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
