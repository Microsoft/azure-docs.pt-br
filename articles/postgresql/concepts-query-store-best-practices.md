---
title: Repositório de Consultas práticas recomendadas no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve as práticas recomendadas para o Repositório de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708825"
---
# <a name="best-practices-for-query-store"></a>Práticas recomendadas para Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

Este artigo descreve as práticas recomendadas para o uso do Repositório de Consultas no Banco de Dados do Azure para PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Definir o modo de captura de consulta ideal
Deixe que o Repositório de Consultas capture os dados que importam para você. 

|**pg_qs.query_capture_mode** | **Cenário**|
|---|---|
|_Todos_  |Analise sua carga de trabalho cuidadosamente em termos de todas as consultas e das respectivas frequências de execução e outras estatísticas. Identifique novas consultas na carga de trabalho. Detectar se consultas ad hoc são usadas para identificar oportunidades para a parametrização automática ou de usuário. _All_ acompanha um custo de consumo de recursos maior. |
|_Top_  |Concentre sua atenção nas principais consultas – aquelas emitidas pelos clientes.
|_Nenhum_ |Você já capturou um conjunto de consultas e a janela de tempo que você deseja investigar, e você deseja eliminar as distrações que outras consultas podem causar. _Nenhum_ é adequado para ambientes de teste e de marca de banca. _None_ deve ser usado com cuidado, pois você pode perder a oportunidade de acompanhar e otimizar consultas novas importantes. Você não pode recuperar dados nessas janelas de tempo do passado. |

O Repositório de Consultas também inclui um repositório de estatísticas de espera. Há uma consulta de modo de captura adicional que controla as estatísticas de espera: **pgms_wait_sampling.query_capture_mode** pode ser definido como _none_ ou _all_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs. query_capture_mode for _None_, a configuração pgms_wait_sampling. query_capture_mode não terá efeito. 


## <a name="keep-the-data-you-need"></a>Manter os dados de que precisa
O parâmetro **pg_qs.retention_period_in_days** especifica, em dias, o período de retenção de dados para o Repositório de Consultas. Dados de consulta e estatísticas mais antigos são excluídos. Por padrão, o Repositório de Consultas é configurado para reter os dados por 7 dias. Evite manter dados históricos que você não planeja usar. Aumente o valor se você precisar manter dados por mais tempo.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Definir a frequência de amostragem de estatísticas de espera 
O parâmetro **pgms_wait_sampling.history_period** especifica a frequência (em milissegundos) em que eventos de espera são usados como amostra. Quanto menor for o período, mais frequente será a amostragem. Mais informações são recuperadas, mas isso acompanha o custo de um maior consumo de recursos. Aumente esse período se o servidor está sob carregamento ou se você não precisa de granularidade


## <a name="get-quick-insights-into-query-store"></a>Obter insights rápidos sobre o Repositório de Consultas
Você pode usar a [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure para obter insights rápidos sobre os dados no Repositório de Consultas. As visualizações revelam as consultas em execução a mais tempo e os eventos de espera de maior duração ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas
- Saiba como obter ou definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou a [CLI do Azure](howto-configure-server-parameters-using-cli.md).