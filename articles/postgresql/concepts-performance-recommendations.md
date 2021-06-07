---
title: Recomendações de desempenho-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d547844671e6485e71be7dd8c355de08f3dec5e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710559"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Recomendações de desempenho no banco de dados do Azure para PostgreSQL-servidor único

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

O recurso de recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizadas para melhorar o desempenho. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilite o [Repositório de Consultas](concepts-query-store.md) no servidor para utilizar totalmente o recurso Recomendações de Desempenho. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho
O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** na seção de **desempenho inteligente** da barra de menus na página portal do Azure para o servidor PostgreSQL.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Página das Recomendações de Desempenho":::

Selecione **Analisar** e escolha um banco de dados que iniciará a análise. Dependendo de sua carga de trabalho, a análise de th pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. A análise executa um exame profundo do banco de dados. Recomendamos que você execute a análise fora dos períodos de pico. 

A janela **recomendações** mostrará uma lista de recomendações, se alguma for encontrada.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nova página de recomendações de desempenho":::

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o no cliente de sua escolha. Lembre-se de testar e monitorar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, há suporte para dois tipos de recomendações: *CREATE INDEX* e *drop index*.

### <a name="create-index-recommendations"></a>Criar recomendações de índice
As recomendações *Criar Índice* sugerem novos índices para acelerar as consultas demoradas ou executadas com mais frequência na carga de trabalho. Esse tipo de recomendação requer a habilitação do [Repositório de Consultas](concepts-query-store.md). O Repositório de Consultas coleta informações de consulta e fornece estatísticas detalhadas de runtime e frequência de consulta usadas pela análise para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
Além de detectar índices ausentes, o banco de dados do Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice for raramente usado ou redundante, o analisador recomendará soltá-lo.

## <a name="considerations"></a>Considerações
* As recomendações de desempenho não estão disponíveis para [réplicas de leitura](concepts-read-replicas.md).
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.

