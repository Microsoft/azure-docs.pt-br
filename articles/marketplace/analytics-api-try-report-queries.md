---
title: Experimentar a API de consultas de relatório
description: Use esta API para executar uma consulta de relatório para relatórios de análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583664"
---
# <a name="try-report-queries-api"></a>Experimentar a API de consultas de relatório

Essa API executa uma instrução de consulta de relatório. A API retorna apenas 10 registros que você como um parceiro pode usar para verificar se os dados são os esperados.

> [!IMPORTANT]
> Essa API tem um tempo limite de execução de consulta de 100 segundos. Se você observar que a API está demorando mais de 100 segundos, é muito provável que a consulta esteja sintaticamente correta ou, caso contrário, você receberia um código de erro diferente de 200. A geração de relatório real será aprovada se a sintaxe da consulta estiver correta.

**Sintaxe da solicitação**

| **Método** | **URI da solicitação** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Cabeçalho da solicitação**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure) no formulário `Bearer <token>` |
| Tipo de conteúdo | string | `Application/JSON` |
|||

**QueryParameter**

| **Nome do parâmetro** | **Tipo** | **Descrição** |
| --- | --- | --- |
| `exportQuery` | string | Cadeia de caracteres de consulta de relatório que precisa ser executada |
| `queryId` | string | Uma ID de consulta existente válida |
|||

**Parâmetro** de caminho  

Nenhum

**Carga de solicitação**

Nenhum

**Glossário**

Não

**Resposta**

A carga de resposta é estruturada da seguinte maneira:

Código de resposta: 200, 400, 401, 403, 404, 500

Carga de resposta: 10 linhas principais da execução da consulta
