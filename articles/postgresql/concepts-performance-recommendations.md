---
title: Recomendações de desempenho no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564420"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recomendações de desempenho no Banco de Dados do Azure para PostgreSQL

**Aplica-se a:** Banco de Dados do Azure para PostgreSQL 9.6 e 10

O recurso de recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizados para melhorar o desempenho. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilitar [Store consulta](concepts-query-store.md) no seu servidor para utilizar totalmente o recurso de recomendações de desempenho. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho
O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** da **desempenho inteligente** seção da barra de menus, na página do portal do Azure para seu servidor PostgreSQL.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **analisar** e escolha um banco de dados, que iniciará a análise. Dependendo de sua carga de trabalho, análise th pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. Análise realiza um exame detalhado de seu banco de dados. Recomendamos que você execute análise durante períodos de pico. 

O **recomendações** janela mostrará uma lista de recomendações, caso seja encontrado.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não serão aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e executá-lo do seu cliente de escolha. Lembre-se de testar e monitorar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, há suporte para dois tipos de recomendações: *Criar índice* e *descartar índice*.

### <a name="create-index-recommendations"></a>Criar recomendações de índice
*Criar índice* recomendações sugerem índices novos para agilizar as consultas na carga de trabalho com mais frequência executadas ou demoradas. Esse tipo de recomendação requer [Store consulta](concepts-query-store.md) esteja habilitado. Consulta Store coleta informações de consulta e fornece as estatísticas de tempo de execução e a frequência de consulta detalhados que a análise usa para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
Além de detectar índices ausentes, o banco de dados do Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice é raramente usados ou redundantes, o analisador recomendará cancelá-lo.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.

