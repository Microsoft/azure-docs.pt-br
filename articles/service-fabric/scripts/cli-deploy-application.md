---
title: Exemplo de implantação de script da CLI do Azure Service Fabric (sfctl)
description: Implantar um aplicativo em um cluster do Azure Service Fabric usando a CLI do Azure Service Fabric
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
ms.date: 04/16/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ff40dc62b4dcd622156a78518bbdcb6b9b430644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75526593"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Implantar um aplicativo em um cluster do Service Fabric usando a CLI do Service Fabric

Esse script de exemplo copia um pacote de aplicativos em um repositório de imagens de cluster, registra o tipo de aplicativo no cluster e cria uma instância do aplicativo com base no tipo de aplicativo. Todos os serviços padrão também são criados nesse momento.

Se necessário, instale a [CLI do Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Exemplo de script

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Quando terminar, o script de [remoção](cli-remove-application.md) poderá ser usado para remover o aplicativo. O script de remoção exclui a instância do aplicativo, cancela o registro do tipo de aplicativo e exclui o pacote de aplicativos do repositório de imagens.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [Documentação da CLI do Service Fabric](../service-fabric-cli.md).

Mais exemplos da CLI do Service Fabric para o Azure Service Fabric podem ser encontrados em [Exemplos da CLI do Service Fabric](../samples-cli.md).
