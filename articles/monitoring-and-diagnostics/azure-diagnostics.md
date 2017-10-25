---
title: "Visão geral do Diagnóstico do Azure | Microsoft Docs"
description: "Usar o diagnóstico do Azure para depurar, medir o desempenho, monitorar e analisar o tráfego em serviços de nuvem, em máquinas virtuais e no Service Fabric"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: 0c6e4d9d2a3744f607b72364f3944c700acd070c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-diagnostics"></a>O que é o Diagnóstico do Azure
O Diagnóstico do Azure é a capacidade do Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. As que têm suporte no momento são as Funções de Trabalho ou Web do Serviço de Nuvem, as Máquinas Virtuais do Azure que executam o Microsoft Windows e o Service Fabric. Outros serviços do Azure têm seu próprios diagnósticos separados.

## <a name="data-you-can-collect"></a>Dados que você pode coletar
O Diagnóstico do Azure pode coletar os seguintes tipos de dados:

| Fonte de dados | Descrição |
| --- | --- |
| Contadores de desempenho |Contadores de desempenho personalizados e do Sistema Operacional |
| Logs de aplicativo |Rastreio de mensagens gravadas pelo seu aplicativo |
| Logs de Eventos do Windows |Informações enviadas ao sistema de log de eventos do Windows |
| Fonte de evento do .NET |Eventos de gravação de código usando a classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) do .NET |
| Logs IIS |Informações sobre sites do IIS |
| Manifesto com base no ETW |Rastreamento de Eventos para eventos do Windows gerados por qualquer processo |
| Despejos de falhas |Informações sobre o estado do processo no caso de uma falha do aplicativo |
| Logs de erros personalizados |Logs criados por seu aplicativo ou serviço |
| Logs de infraestrutura do Diagnóstico do Azure |Informações sobre o próprio Diagnóstico |

A extensão de diagnóstico do Azure pode transferir dados para uma conta de armazenamento do Azure ou enviá-los para serviços como o [Application Insights](../application-insights/app-insights-cloudservices.md). Você pode usar os dados para a depuração e a solução de problemas, a medição de desempenho, o monitoramento de uso de recursos, a análise de tráfego e o planejamento da capacidade, bem como para a auditoria.

## <a name="versioning"></a>Controle de versão
Veja [Histórico de versão do Diagnóstico do Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Próximas etapas
Escolha de qual serviço você está tentando coletar diagnósticos e use os seguintes artigos para começar. Use os links gerais de diagnóstico do Azure para obter referências de tarefas específicas.

## <a name="web-apps"></a>Aplicativos Web
Observe que os aplicativos Web não usam o Diagnóstico do Azure. Encontre as informações equivalentes em [Aplicativos Web](../app-service/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços de Nuvem que usam o Diagnóstico do Azure
* Se estiver usando o Visual Studio, veja [Usar o Visual Studio para rastrear um aplicativo dos Serviços de Nuvem](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar. Caso contrário, veja
* [Como monitorar os Serviços de Nuvem que usam o Diagnóstico do Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configurar o Diagnóstico do Azure em um Aplicativo dos Serviços de Nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md)

Para tópicos mais avançados, veja

* [Uso dos Diagnósticos do Azure com o Application Insights para Serviços de Nuvem](../application-insights/app-insights-cloudservices.md)
* [Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Usar o PowerShell para configurar o diagnóstico nos Serviços de Nuvem](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Máquinas Virtuais que usam o Diagnóstico do Azure
* Se estiver usando o Visual Studio, veja [Usar o Visual Studio para rastrear Máquinas Virtuais do Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar. Caso contrário, veja
* [Configurar o Diagnóstico do Azure em uma Máquina Virtual do Azure](../virtual-machines-dotnet-diagnostics.md)

Para tópicos mais avançados, veja

* [Usar o PowerShell para configurar o diagnóstico nas Máquinas Virtuais do Azure](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric que usa o Diagnóstico do Azure
Comece em [Monitorar um aplicativo do Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico do Service Fabric estarão disponíveis na árvore de navegação à esquerda depois que você acessar este artigo.

## <a name="general-azure-diagnostics-articles"></a>Artigos gerais sobre o Diagnóstico do Azure
* [Configuração do Esquema de Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) - saiba como alterar o arquivo de esquema para coletar e encaminhar dados de diagnóstico. Observe que você também pode usar o Visual Studio para alterar o arquivo de esquema.
* [Como os dados do Diagnóstico do Azure são armazenados no Armazenamento do Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) - conheça os nomes das tabelas e dos blobs onde os dados de diagnóstico são gravados.
* Saiba como [usar os Contadores de Desempenho no Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
* Saiba como [Rotear informações de diagnóstico do Azure para o Application Insights](azure-diagnostics-configure-application-insights.md)
* Se você tiver problemas com o início do diagnóstico ou com a localização de seus dados nas tabelas do Armazenamento do Azure, veja [Solução de Problemas do Diagnóstico do Azure](azure-diagnostics-troubleshooting.md)
