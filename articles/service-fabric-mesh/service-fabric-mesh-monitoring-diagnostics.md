---
title: Monitoramento e diagnóstico em aplicativos de Malha do Service Fabric do Azure | Microsoft Docs
description: Saiba mais sobre como monitorar e diagnosticar aplicativos na Malha do Service Fabric no Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 6166808c85bbee9465a8fa12332afe2163027982
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810627"
---
# <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico
A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Monitoramento e diagnóstico para a malha de Service Fabric é categorizado em três principais tipos de dados de diagnóstico:

- Logs de aplicativos - estes são definidos como os logs de aplicativos em contêineres, com base em como você tiver instrumentado seu aplicativo (por exemplo, logs de docker)
- Eventos de plataforma – eventos da plataforma Malha relevantes à sua operação de contêiner, incluindo atualmente encerramento, desativação e a ativação de contêiner.
- Métricas de contêiner - as métricas de desempenho e a utilização de recursos para seus contêineres (estatísticas do docker)

Este artigo discute as opções de monitoramento e diagnóstico para a versão de visualização mais recente disponível.

## <a name="application-logs"></a>Logs de aplicativo

Você pode exibir os logs de docker de seus contêineres implantados, em uma base por contêiner. No modelo de aplicativo de Malha do Service Fabric, cada contêiner é um pacote de código em seu aplicativo. Para ver os logs associados com um pacote de códigos, use o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Você pode usar o comando "az mesh service-replica" para obter o nome da réplica. Nomes de réplica estão incrementando números de 0.*

Aqui é com o que isso se parece para ver os logs do contêiner do aplicativo de votação VotingWeb.Code:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre Malha do Service Fabric, leia a visão geral:
- [Visão geral da Malha do Service Fabric](service-fabric-mesh-overview.md)
