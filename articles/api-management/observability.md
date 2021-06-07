---
title: Observação no gerenciamento de API do Azure | Microsoft Docs
description: Visão geral de todas as opções de observação no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095653"
---
# <a name="observability-in-azure-api-management"></a>Observação no gerenciamento de API do Azure

A observação é a capacidade de entender o estado interno de um sistema a partir dos dados que ele produz e a capacidade de explorar esses dados para responder a perguntas sobre o que aconteceu e por quê. 

O gerenciamento de API do Azure ajuda as organizações a centralizar o gerenciamento de todas as APIs. Como ele serve como um ponto único de entrada de todo o tráfego de API, é um lugar ideal para observar as APIs. 

## <a name="observability-tools"></a>Ferramentas de observação

A tabela a seguir resume todas as ferramentas com suporte do gerenciamento de API para observar as APIs, cada uma é útil para um ou mais cenários:

| Ferramenta        | Útil para    | Atraso de dados | Retenção | amostragem | Tipo de dados | habilitado|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[Inspetor de API](api-management-howto-api-inspector.md)** | Testando e depurando | Instantâneo | Últimos rastreamentos 100 | Ativado por solicitação | Rastreamentos de solicitação | Sempre
| **Análise interna** | Relatórios e monitoramento | minutos | Tempo de vida | 100% | Relatórios e logs | Sempre |
| **[Métricas do Azure Monitor](api-management-howto-use-azure-monitor.md)** | Relatórios e monitoramento | minutos | 93 dias (atualização para estender) | 100% | Métricas | Sempre |
| **[Logs de Azure Monitor](api-management-howto-use-azure-monitor.md)** | Relatórios, monitoramento e depuração | minutos | 31 dias/5 GB (atualização para estender) | 100% (ajustável) | Logs | Opcional |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | Relatórios, monitoramento e depuração | Segundos | 90 dias/5 GB (atualização para estender) | Personalizado | Logs, métricas | Opcional |
| **[Registro em log por meio do hub de eventos do Azure](api-management-howto-log-event-hubs.md)** | Cenários personalizados | Segundos | Usuário gerenciado | Personalizado | Personalizado | Opcional |

### <a name="self-hosted-gateway"></a>Gateway auto-hospedado

Todas as ferramentas mencionadas acima têm suporte pelo gateway gerenciado na nuvem. Atualmente [, o gateway auto-hospedado](self-hosted-gateway-overview.md) não envia logs de diagnóstico para Azure monitor. No entanto, é possível configurar e persistir os logs localmente onde o gateway auto-hospedado é implantado. Para obter mais informações, consulte [Configurando métricas e logs de nuvem para gateway auto-hospedado](how-to-configure-cloud-metrics-logs.md) e [Configurando métricas locais e logs para o gateway de hospedagem interna](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Próximas etapas

* [Siga os tutoriais para saber mais sobre o gerenciamento de API](import-and-publish.md)
