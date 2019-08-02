---
title: Análise de desempenho de consulta no banco de dados do Azure para MySQL
description: Este artigo descreve o recurso de análise de desempenho de consultas no banco de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589067"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Análise de desempenho de consulta no banco de dados do Azure para MySQL

**Aplica-se a:**  banco de dados do Azure para MySQL 5.7

> [!NOTE]
> Análise de desempenho de consulta está em visualização.

A Análise de Desempenho de Consultas ajuda você a identificar rapidamente quais são suas consultas de execução mais longa, como elas mudam ao longo do tempo e quais esperas as estão afetando.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de longa execução

- Identificar consultas de execução mais longas nas últimas X horas
- Identificar as principais N consultas que estão aguardando recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Natureza de espera de compreensão para uma consulta
- Noções básicas sobre as tendências de esperas de recursos e em que existe contenção de recursos

## <a name="permissions"></a>Permissões

**Proprietário** ou **Colaborador** permissões necessárias para exibir o texto das consultas na análise de desempenho de consulta. ** Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para análise de desempenho de consulta para a função, os dados devem existir na [Store consulta](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibição de análises de desempenho

A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas.

Na página do portal do banco de dados do Azure para servidor MySQL, selecione **análise de desempenho de consultas** sob o **desempenho inteligente** seção da barra de menus.

### <a name="long-running-queries"></a>Consultas de longa execução

O **consultas longas** guia mostra as consultas top 5 por duração média por execução, agregados em intervalos de 15 minutos. Você pode exibir mais consultas, selecionando a partir de **número de consultas** lista suspensa. As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico. Como alternativa, use o zoom in e out ícones para exibir um período de tempo menor ou maior, respectivamente.

![Consultas de execução longa da Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Estatísticas de espera

> [!NOTE]
> As estatísticas de espera destinam-se para solução de problemas de desempenho de consulta. É recomendável a serem ativados apenas para fins de solução de problemas.

As estatísticas de espera fornecem uma exibição dos eventos espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de evento de espera na [documentação do mecanismo de MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecione o **estatísticas de espera** guia para exibir as visualizações correspondentes em espera no servidor.

Consultas exibidas na exibição de estatísticas de espera são agrupadas pelas consultas que apresentam o maior espera durante o intervalo de tempo especificado.

![Análise de desempenho de consulta aguarda estatísticas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no banco de dados do Azure para MySQL.