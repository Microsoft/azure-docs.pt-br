---
title: Recomendações de desempenho no banco de dados do Azure para MySQL
description: Este artigo descreve o recurso de recomendação de desempenho do banco de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: af3c4482b1ce9e521d14a0e0c63de40625c25c73
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461790"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Recomendações de desempenho no banco de dados do Azure para MySQL

**Aplica-se a:** Banco de dados do Azure para MySQL 5.7

> [!NOTE]
> Recomendações de desempenho está em visualização.

O recurso de recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizados para melhorar o desempenho. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilitar [Store consulta](concepts-query-store.md) no seu servidor para utilizar totalmente o recurso de recomendações de desempenho. Se o esquema de desempenho for OFF, ativar a consulta Store permite performance_schema e um subconjunto de instrumentos de esquema de desempenho necessária para o recurso. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho

O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** da **desempenho inteligente** seção da barra de menus, na página do portal do Azure para o servidor MySQL.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **analisar** e escolha um banco de dados, que iniciará a análise. Dependendo de sua carga de trabalho, a análise pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. Análise realiza um exame detalhado de seu banco de dados. Recomendamos que você execute análise durante períodos de pico.

O **recomendações** janela mostrará uma lista de recomendações, caso seja encontrado e a ID da consulta relacionados que gerou essa recomendação. Com a ID da consulta, você pode usar o [mysql.query_store](concepts-query-store.md#mysqlquery_store) exibição para saber mais sobre a consulta.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não serão aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e executá-lo do seu cliente de escolha. Lembre-se de testar e monitorar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, apenas *Create Index* recomendações têm suporte.

### <a name="create-index-recommendations"></a>Criar recomendações de índice

*Criar índice* recomendações sugerem índices novos para agilizar as consultas na carga de trabalho com mais frequência executadas ou demoradas. Esse tipo de recomendação requer [Store consulta](concepts-query-store.md) esteja habilitado. Consulta Store coleta informações de consulta e fornece as estatísticas de tempo de execução e a frequência de consulta detalhados que a análise usa para fazer a recomendação.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no banco de dados do Azure para MySQL.