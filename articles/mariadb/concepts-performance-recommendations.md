---
title: Recomendações de desempenho-banco de dados do Azure para MariaDB
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para MariaDB
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 5949c6ba1ed176a609d3ceed4150ac06855440e9
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664309"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Recomendações de desempenho no Banco de Dados do Azure para MariaDB

**Aplica-se a:** Banco de dados do Azure para MariaDB 10,2

O recurso Recomendações de Desempenho analisa os bancos de dados para criar sugestões personalizadas para desempenho aprimorado. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilite o [Repositório de Consultas](concepts-query-store.md) no servidor para utilizar totalmente o recurso Recomendações de Desempenho. Se o esquema de desempenho estiver DESATIVADO, a ativação do Repositório de Consultas habilitará o performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para o recurso. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho

O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** na seção de **desempenho inteligente** da barra de menus na página de portal do Azure para seu servidor do MariaDB.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Página das Recomendações de Desempenho":::

Selecione **Analisar** e escolha um banco de dados que iniciará a análise. Dependendo da carga de trabalho, a análise poderá levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. A análise executa um exame profundo do banco de dados. Recomendamos que você execute a análise fora dos períodos de pico.

A janela **Recomendações** mostrará uma lista de recomendações (e encontradas) e a ID de consulta relacionada que gerou essa recomendação. Com a ID de consulta, você poderá usar a exibição [mysql.query_store](concepts-query-store.md#mysqlquery_store) para saber mais sobre a consulta.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nova página de recomendações de desempenho":::

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o no cliente de sua escolha. Lembre-se de testar e monitorar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

### <a name="index-recommendations"></a>Recomendações de índice

As recomendações *Criar Índice* sugerem novos índices para acelerar as consultas demoradas ou executadas com mais frequência na carga de trabalho. Esse tipo de recomendação requer a habilitação do [Repositório de Consultas](concepts-query-store.md). O Repositório de Consultas coleta informações de consulta e fornece estatísticas detalhadas de runtime e frequência de consulta usadas pela análise para fazer a recomendação.

### <a name="query-recommendations"></a>Recomendações de consulta

As recomendações de consulta sugerem otimizações e regravações para consultas na carga de trabalho. Ao identificar antipadrões de consulta MariaDB e corrigi-los de forma sintaticamente, o desempenho de consultas demoradas pode ser melhorado. Esse tipo de recomendação requer a habilitação do Repositório de Consultas. O Repositório de Consultas coleta informações de consulta e fornece estatísticas detalhadas de runtime e frequência de consulta usadas pela análise para fazer a recomendação.
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no banco de dados do Azure para MariaDB.