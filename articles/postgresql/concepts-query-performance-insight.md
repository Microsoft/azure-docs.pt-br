---
title: Análise de Desempenho de Consultas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso Análise de Desempenho de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: babf2c8208732a194184549dfa5ed3228b376d0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710253"
---
# <a name="query-performance-insight"></a>Análise de Desempenho de Consultas 

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

A Análise de Desempenho de Consultas ajuda você a identificar rapidamente quais são suas consultas de execução mais longa, como elas mudam ao longo do tempo e quais esperas as estão afetando.

## <a name="permissions"></a>Permissões
**Permissões do Proprietário** ou **Colaborador** necessárias para exibir o texto das consultas na Análise de Desempenho de Consultas **Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para a Análise de Desempenho de Consultas funcionar, os dados precisam existir no [Repositório de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibição de análises de desempenho
A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas. 

Na página do portal do servidor do banco de dados do Azure para PostgreSQL, selecione análise de **desempenho de consultas** na seção **desempenho inteligente** da barra de menus.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Consultas de execução longa da Análise de Desempenho de Consultas":::

A guia **consultas de longa execução** mostra as cinco principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Você pode exibir mais consultas selecionando na lista suspensa **número de consultas** . As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico. Como alternativa, use os ícones de ampliar e afastar para exibir um período maior ou menor, respectivamente.

A tabela abaixo do gráfico contém mais detalhes sobre as consultas de execução longa na janela de tempo.

Selecione a guia das **Estatísticas de Espera** guia para exibir as visualizações correspondentes em espera no servidor.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Consultas de execução longa da Análise de Desempenho de Consultas":::

## <a name="considerations"></a>Considerações
* Análise de Desempenho de Consultas não está disponível para [réplicas de leitura](concepts-read-replicas.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.


