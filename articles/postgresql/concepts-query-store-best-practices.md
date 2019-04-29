---
title: Práticas recomendadas de Repositório de Consultas no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve práticas recomendadas para o Repositório de Consultas no Banco de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: af68d5cdc891a8c924ca493d8e798edaa4670ab5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563164"
---
# <a name="best-practices-for-query-store"></a>Práticas recomendadas para Repositório de Consultas

**Aplica-se a:** Banco de Dados do Azure para PostgreSQL 9.6 e 10

Este artigo descreve as práticas recomendadas para o uso do Repositório de Consultas no Banco de Dados do Azure para PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Definir o modo de captura de consulta ideal
Deixe que o Repositório de Consultas capture os dados que importam para você. 

|**pg_qs.query_capture_mode** | **Cenário**|
|---|---|
|_Todas_  |Analise sua carga de trabalho cuidadosamente em termos de todas as consultas e das respectivas frequências de execução e outras estatísticas. Identifique novas consultas na carga de trabalho. Detecte se consultas ad hoc são usadas para identificar oportunidades de parametrização automática ou usuário. _All_ acompanha um custo de consumo de recursos maior. |
|_Top_  |Concentre sua atenção nas principais consultas – aquelas emitidas pelos clientes.
|_Nenhum_ |Você já capturou um conjunto de consultas e a janela de tempo que você deseja investigar, e você deseja eliminar as distrações que outras consultas podem causar. _None_ é adequado para teste e avaliação de desempenho de ambientes. _None_ deve ser usado com cuidado, pois você pode perder a oportunidade de acompanhar e otimizar consultas novas importantes. Você não pode recuperar dados nessas janelas de tempo do passado. |

O Repositório de Consultas também inclui um repositório de estatísticas de espera. Há uma consulta de modo de captura adicional que controla as estatísticas de espera: **pgms_wait_sampling.query_capture_mode** pode ser definido como _none_ ou _all_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode for _none_, a configuração pgms_wait_sampling.query_capture_mode não terá efeito. 


## <a name="keep-the-data-you-need"></a>Manter os dados de que precisa
O parâmetro **pg_qs.retention_period_in_days** especifica, em dias, o período de retenção de dados para o Repositório de Consultas. Dados de consulta e estatísticas mais antigos são excluídos. Por padrão, o Repositório de Consultas é configurado para reter os dados por 7 dias. Evite manter dados históricos que você não planeja usar. Aumente o valor se você precisar manter dados por mais tempo.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Definir a frequência de amostragem de estatísticas de espera 
O parâmetro **pgms_wait_sampling.history_period** especifica a frequência (em milissegundos) em que eventos de espera são usados como amostra. Quanto menor for o período, mais frequente será a amostragem. Mais informações são recuperadas, mas isso acompanha o custo de um maior consumo de recursos. Aumente esse período se o servidor está sob carregamento ou se você não precisa de granularidade


## <a name="get-quick-insights-into-query-store"></a>Obter insights rápidos sobre o Repositório de Consultas
Você pode usar a [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure para obter insights rápidos sobre os dados no Repositório de Consultas. As visualizações revelam as consultas em execução a mais tempo e os eventos de espera de maior duração ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas
- Saiba como obter ou definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou a [CLI do Azure](howto-configure-server-parameters-using-cli.md).