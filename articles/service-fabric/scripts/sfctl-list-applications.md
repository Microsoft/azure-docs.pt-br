---
title: Listar aplicativos em um cluster no sfctl
description: Exemplo de script de CLI do Service Fabric – Liste os aplicativos provisionados em um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/13/2018
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 41685e53eb0915f54bdc2d678191e5b767990dde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610260"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Listar aplicativos executando em um cluster do Service Fabric

Esse script de exemplo se conecta a um cluster do Service Fabric e, em seguida, lista todos os aplicativos provisionados.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [Documentação da CLI do Service Fabric](../service-fabric-cli.md).

Mais exemplos da CLI do Service Fabric para o Azure Service Fabric podem ser encontrados em [Exemplos da CLI do Service Fabric](../samples-cli.md).
