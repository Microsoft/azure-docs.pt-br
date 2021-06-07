---
title: APIs para acessar dados de análise do Marketplace comercial
description: Use essas APIs para acessar dados de análise programaticamente no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583719"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>APIs para acessar dados de análise do Marketplace comercial

A seguir, a lista de APIs para acessar dados de análise do Marketplace comercial e suas funcionalidades associadas.

- [APIs de pull de conjunto de](#dataset-pull-apis)
- [APIs de gerenciamento de consultas](#query-management-apis)
- [APIs de gerenciamento de relatórios](#report-management-apis)
- [APIs de pull de execução de relatório](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>APIs de pull de conjunto de

***Tabela 1: APIs pull do conjunto de***

| **API** | **Funcionalidade** |
| --- | --- |
| [Obter todos os conjuntos de dados](analytics-api-get-all-datasets.md) | Obtém todos os conjuntos de os disponíveis. Conjuntos de valores listam as tabelas, colunas, métricas e intervalos de tempo. |
|||

## <a name="query-management-apis"></a>APIs de gerenciamento de consultas

***Tabela 2: APIs de gerenciamento de consultas***

| **API** | **Funcionalidade** |
| --- | --- |
| [Criar consulta de relatório](analytics-programmatic-access.md#create-report-query-api) | Cria consultas personalizadas que definem o conjunto de linhas do qual as colunas e métricas precisam ser exportadas. |
| [OBTER consultas de relatório](analytics-api-get-report-queries.md) | Obtém todas as consultas disponíveis para uso em relatórios. Obtém todas as consultas definidas pelo sistema e pelo usuário por padrão. |
| [EXCLUIR consultas de relatório](analytics-api-delete-report-queries.md) | Exclui consultas definidas pelo usuário. |
|||

## <a name="report-management-apis"></a>APIs de gerenciamento de relatórios

***Tabela 3: APIs de gerenciamento de relatórios***

| **API** | **Funcionalidade** |
| --- | --- |
| [Criar Relatório](analytics-programmatic-access.md#create-report-api) | Agenda uma consulta a ser executada em intervalos regulares. |
| [TENTAR consultas de relatório](analytics-api-try-report-queries.md) | Executa uma instrução de consulta de relatório. Retorna apenas 10 registros que um parceiro pode usar para verificar se os dados estão conforme o esperado. |
| [Obter relatório](analytics-api-get-report.md) | Obtenha todos os relatórios que foram agendados. |
| [Atualizar relatório](analytics-api-update-report.md) | Modifique um parâmetro de relatório. |
| [Excluir relatório](analytics-api-delete-report.md) | Exclui todos os registros de execução de relatório e relatório. |
| [Pausar execuções de relatório](analytics-api-pause-report-executions.md) | Pausa a execução agendada de relatórios. |
| [Retomar execuções de relatório](analytics-api-resume-report-executions.md) | Retoma a execução agendada de um relatório em pausa. |
|||

## <a name="report-execution-pull-apis"></a>APIs de pull de execução de relatório

***Tabela 4: APIs de pull de execução de relatório***

| **API** | **Funcionalidade** |
| --- | --- |
| [Obter execuções de relatório](analytics-programmatic-access.md#get-report-executions-api) | Obter todas as execuções que ocorreram para um determinado relatório. |
|||

## <a name="next-steps"></a>Próximas etapas

- Você pode experimentar as APIs por meio da [URL da API do Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
