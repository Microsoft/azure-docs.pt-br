---
title: Práticas recomendadas de aplicativo e cluster do Azure Service Fabric | Microsoft Docs
description: Práticas recomendadas de gerenciamento de clusters e aplicativos do Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a0174a5442de7a10e45e9dc2a2f43f5f401d1a69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803175"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Práticas recomendadas de aplicativo e cluster do Azure Service Fabric

Para gerenciar aplicativos e clusters do Azure Service Fabric com êxito, há operações cuja execução é altamente recomendável para otimizar a confiabilidade do seu ambiente de produção. Execute as operações definidas neste documento e selecione um dos nossos [exemplos de modelo de cluster do Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) para começar a projetar sua solução de produção ou modificar o seu modelo existente para incorporar essas práticas.

## <a name="security"></a>Segurança 

* [Práticas recomendadas para segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Rede

* [Práticas recomendadas para rede](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planejamento e escala de computação

* [Práticas recomendadas para dimensionamento de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planejamento de capacidade da computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Práticas recomendadas para implementar infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

* [Práticas recomendadas para monitoramento de cluster e diagnóstico](service-fabric-best-practices-monitoring.md)

## <a name="checklist"></a>Lista de verificação

Após a conclusão de todas as seções acima, certifique-se de que você tenha integrado todas as práticas recomendadas da lista de verificação de preparação da produção:
* [Lista de verificação de preparação da produção do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Próximas etapas

* Criar um cluster em VMs ou em computadores executando o Windows Server: [Criação de cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster nas VMS ou computadores executando Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Solucionar problemas: [Guia de solução de problemas do Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)