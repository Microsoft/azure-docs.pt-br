---
title: Monitorar seus aplicativos sem alterações de código – instrumentação automática para Application Insights do Azure Monitor | Microsoft Docs
description: Visão geral da instrumentação automática para Application Insights do Azure Monitor – gerenciamento de desempenho de aplicativos sem código
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708485"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>O que é a instrumentação automática ou a anexação sem código do Application Insights do Azure Monitor?

Com a instrumentação automática (ou anexação sem código) você habilita o monitoramento de aplicativos com o Application Insights sem alterar o código.  

O Application Insights é integrado a vários provedores de recursos e funciona em ambientes diferentes. Tudo o que você precisa fazer é habilitar e, em alguns casos, configurar o agente, que coleta a telemetria automaticamente. Logo você passa a ver as métricas, os dados e as dependências do recurso do Application Insights, o que permite identificar a fonte de possíveis problemas antes que eles ocorram e analisar a causa raiz com a exibição de transação de ponta a ponta.

## <a name="supported-environments-languages-and-resource-providers"></a>Ambientes, linguagens e provedores de recursos com suporte

À medida que adicionamos integrações, a matriz de funcionalidade de instrumentação automática se torna complexa. A tabela a seguir mostra a situação atual do suporte para vários provedores de recursos, linguagens e ambientes.

|Ambiente/provedor de recursos          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Serviço de Aplicativo do Azure no Windows           | GA, OnBD*       | GA, aceitação      | Em andamento     | Em andamento     | Sem suporte   |
|Serviço de Aplicativo do Azure no Linux             | N/D             | Sem suporte   | Em Andamento     | Visualização pública  | Sem suporte   |
|Serviço de Aplicativo do Azure no AKS               | N/D             | Em design       | Em design       | Em design       | Sem suporte   |
|Azure Functions – básico                | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       |
|Azure Functions no Windows – dependências | Sem suporte   | Sem suporte   | Visualização pública  | Sem suporte   | Sem suporte   |
|Serviço de Kubernetes do Azure               | N/D             | Em design       | Por meio de agente   | Em design       | Sem suporte   |
|VMs do Azure no Windows                      | Visualização pública  | Sem suporte   | Sem suporte   | Sem suporte   | Sem suporte   |
|VMs locais no Windows                | GA, aceitação      | Sem suporte   | Por meio de agente   | Sem suporte   | Sem suporte   |
|Agente autônomo – qualquer amb.            | Sem suporte   | Sem suporte   | GA              | Sem suporte   | Sem suporte   |

*OnBD significa "ativado por padrão". O Application Insights é habilitado automaticamente depois que você implanta seu aplicativo em ambientes com suporte. 

## <a name="azure-app-service"></a>Serviço de aplicativo do Azure

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
O monitoramento de aplicativos no Serviço de Aplicativo do Azure no Windows está disponível para [aplicativos .NET](./azure-web-apps.md?tabs=net) e é habilitado por padrão.

#### <a name="netcore"></a>.NETCore
O monitoramento de [aplicativos .NETCore](./azure-web-apps.md?tabs=netcore) pode ser habilitado com um clique.

#### <a name="java"></a>Java
A integração do portal para o monitoramento de aplicativos Java no Serviço de Aplicativo do Windows não está disponível no momento. No entanto, você pode adicionar o [agente autônomo de Java 3.0](./java-in-process-agent.md) do Application Insights a aplicativos sem mudar o código, antes de implantá-los no Serviço de Aplicativo. O agente de Java 3.0 do Application Insights está em disponibilidade geral.

#### <a name="nodejs"></a>Node.js
O monitoramento de aplicativos Node.js no Windows não pode ser habilitado no portal no momento. Para monitorar aplicativos Node.js, use o [SDK](./nodejs.md).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>.NETCore
Para monitorar aplicativos .NETCore em execução no Linux, use o [SDK](./asp-net-core.md).

#### <a name="java"></a>Java 
Não é possível habilitar o monitoramento de aplicativos Java do Serviço de Aplicativo no Linux no portal, mas você pode adicionar o [agente de Java 3.0 do Application Insights](./java-in-process-agent.md) ao aplicativo antes de implantá-lo no Serviço de Aplicativo. O agente de Java 3.0 do Application Insights está em disponibilidade geral.

#### <a name="nodejs"></a>Node.js
O [monitoramento de aplicativos Node.js do Serviço de Aplicativo no Linux](./azure-web-apps.md?tabs=nodejs) está na versão prévia pública e pode ser habilitado no portal do Azure, disponível em todas as regiões. 

#### <a name="python"></a>Python
Usar o SDK para [monitorar aplicativos Python](./opencensus-python.md) 

## <a name="azure-functions"></a>Funções do Azure

O monitoramento básico do Azure Functions é habilitado por padrão para coletar logs, desempenho, dados de erro e solicitações HTTP. Para aplicativos Java, você pode habilitar um monitoramento mais avançado, com rastreamento distribuído, e obter os detalhes de transação de ponta a ponta. Essa funcionalidade para Java está na versão prévia pública e você pode [habilitá-la no portal do Azure](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

A instrumentação sem código do Serviço de Kubernetes do Azure está disponível atualmente para aplicativos Java por meio do [agente autônomo](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>VMs e conjunto de dimensionamento de máquinas virtuais do Windows do Azure

A instrumentação automática para VMs do Azure e o conjunto de dimensionamento de máquinas virtuais estão disponíveis para [.NET](./azure-vm-vmss-apps.md) e [Java](./java-in-process-agent.md).  

## <a name="on-premises-servers"></a>Servidores locais
Você pode facilmente habilitar o monitoramento para [servidores locais do Windows para aplicativos .NET](./status-monitor-v2-overview.md) e para [aplicativos Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Outros ambientes
O versátil agente autônomo de Java funciona em qualquer ambiente, sem precisar instrumentar o código. [Siga o guia](./java-in-process-agent.md) para habilitar o Application Insights e leia sobre os recursos incríveis do agente de Java. O agente está na versão prévia pública e disponível em todas as regiões. 

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Application Insights](./app-insights-overview.md)
* [Mapa do aplicativo](./app-map.md)
* [Monitoramento de desempenho de ponta a ponta](../app/tutorial-performance.md)