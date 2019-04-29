---
title: Monitorar e melhorar o desempenho - Banco de Dados SQL do Azure | Microsoft Docs
description: O Banco de Dados SQL do Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas em que é possível melhorar o desempenho de consulta atual.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: jrasnik, carlrab
manager: digimobile
origin.date: 01/25/2019
ms.date: 02/25/2019
ms.openlocfilehash: adbccd5f9cfd5ddd1912e304f800f3ebe04912c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584757"
---
# <a name="monitor-and-improve-performance"></a>Monitorar e melhorar o desempenho

O Banco de Dados SQL do Azure identifica possíveis problemas em seu banco de dados e recomenda ações que podem melhorar o desempenho de sua carga de trabalho, fornecendo ações de ajuste inteligente e recomendações.

Para analisar o desempenho do seu banco de dados, use o bloco **Desempenho** na página Visão Geral ou navegue até a seção "Suporte + solução de problemas":

   ![Exibir Desempenho](./media/sql-database-performance/entries.png)

Na seção "Suporte + solução de problemas" é possível utilizar as seguintes páginas:


1. [Visão geral de desempenho](#performance-overview) para monitorar o desempenho do banco de dados. 
2. [Recomendações de desempenho](#performance-recommendations) para localizar as recomendações de desempenho que podem melhorar o desempenho da carga de trabalho.
3. [Análise de desempenho de consultas](#query-performance-insight) para localizar as principais consultas de consumo de recursos.
4. [Ajuste automático](#automatic-tuning) para permitir que o Banco de Dados SQL do Azure otimize seu banco de dados automaticamente.

## <a name="performance-overview"></a>Visão geral do desempenho

Essa exibição fornece um resumo do desempenho de seu banco de dados e lhe ajudará com ajustes e solução de problemas de desempenho. 

![Desempenho](./media/sql-database-performance/performance.png)

* O bloco **Recomendações** fornece uma divisão das recomendações de ajuste para seu banco de dados (as três principais recomendações serão exibidas, se existirem mais). Ao clicar nesse bloco, será direcionado para **[Recomendações de desempenho](#performance-recommendations)**. 
* O bloco **Atividade de ajuste** fornece um resumo das atividades de ajuste em andamento e concluídas para o banco de dados, oferecendo uma visão rápida do histórico de atividades de ajuste. Clicar nesse bloco leva você para a exibição do histórico completo de ajustes do banco de dados.
* O bloco **Ajuste automático** mostra a[configuração de ajuste automático](sql-database-automatic-tuning-enable.md) para o banco de dados (opções de ajuste que são aplicadas automaticamente ao banco de dados). Clicar nesse bloco abre a caixa de diálogo de configuração de automação.
* O bloco **Consultas de banco de dados** mostra o resumo do desempenho de consulta do banco de dados (uso geral de DTU e consultas com maior consumo de recursos). Ao clicar nesse bloco, será direcionado para **[Análise de Desempenho de Consultas](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Recomendações de desempenho

Esta página fornece [recomendações de ajuste](sql-database-advisor.md) inteligente que podem melhorar o desempenho do banco de dados. Os seguintes tipos de recomendações são mostrados nesta página:

* Recomendações sobre quais índices criar ou soltar.
* Recomendações quando problemas de esquema são identificados no banco de dados.
* Recomendações quando consultas podem se beneficiar de consultas parametrizadas.

![Desempenho](./media/sql-database-performance/recommendations.png)

Você também pode encontrar o histórico completo de ajuste de ações que foram aplicadas no passado.

Saiba como localizar recomendações de desempenho de aplicativo no artigo [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).

## <a name="automatic-tuning"></a>Ajuste automático

Os bancos de dados SQL do Azure podem ajustar o desempenho do banco de dados automaticamente, aplicando as [recomendações de desempenho](sql-database-advisor.md). Para saber mais, leia o artigo de [Ajuste automático](sql-database-automatic-tuning.md). Para habilitá-lo, leia [como habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Análise de Desempenho de Consultas

[Análise de Desempenho de Consultas](sql-database-query-performance.md) permite que você gaste menos tempo solucionando problemas de desempenho de banco de dados, fornecendo o seguinte:

* Mais informações sobre o consumo de recursos de bancos de dados (DTU). 
* As consultas que mais consomem CPU, que potencialmente podem ser ajustadas para melhorar o desempenho. 
* A capacidade de fazer drill down nos detalhes de uma consulta. 

  ![painel de desempenho](./media/sql-database-query-performance/performance.png)

Localize mais informações sobre essa página no artigo  **[Como usar a Análise de Desempenho de Consultas](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Recursos adicionais

* [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos](sql-database-performance-guidance.md)
* [Quando um pool elástico deve ser usado?](sql-database-elastic-pool-guidance.md)

