---
title: Obter API de consultas de relatório
description: Use esta API para obter todas as consultas que estão disponíveis para uso em relatórios de análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583707"
---
# <a name="get-report-queries-api"></a>Obter API de consultas de relatório

A API obter consultas de relatório obtém todas as consultas que estão disponíveis para uso em relatórios. Ele obtém todas as consultas definidas pelo sistema e pelo usuário por padrão.

**Sintaxe da solicitação**

| **Método** | **URI da solicitação** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Cabeçalho da solicitação**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure) no formulário `Bearer <token>` |
| Tipo de conteúdo | string | `Application/JSON` |
||||

**Parâmetro de caminho**

Nenhum

**Parâmetro de consulta**

| **Nome do parâmetro** | **Tipo** | **Necessário** | **Descrição** |
| --- | --- | --- | --- |
| `queryId` | Cadeia de caracteres | No | Filtre para obter detalhes de apenas consultas com a ID fornecida no argumento |
| `queryName` | Cadeia de caracteres | No | Filtre para obter detalhes de apenas consultas com o nome fornecido no argumento |
| `IncludeSystemQueries` | booleano | Não | Incluir consultas de sistema predefinidas na resposta |
| `IncludeOnlySystemQueries` | booleano | Não | Incluir somente consultas do sistema na resposta |
|||||

**Carga de solicitação**

Nenhum

**Glossário**

Não

**Resposta**

A carga de resposta é estruturada da seguinte maneira:

Código de resposta: 200, 400, 401, 403, 404, 500

Carga de resposta:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossário**

Esta tabela descreve as principais definições de elementos na resposta.

| **Parâmetro** | **Descrição** |
| --- | --- |
| `QueryId` | UUID exclusivo da consulta |
| `Name` | Nome fornecido à consulta no momento da criação da consulta |
| `Description` | Descrição fornecida durante a criação da consulta |
| `Query` | Cadeia de caracteres de consulta de relatório |
| `Type` | Definir como `userDefined` para consultas criadas pelo usuário e `system` para consultas de sistema predefinidas |
| `User` | ID de usuário que criou a consulta |
| `CreatedTime` | Hora da criação da consulta |
| `TotalCount` | Número de conjuntos de itens na matriz de valores |
| `StatusCode` | Código de resultado. Os valores possíveis são 200, 400, 401, 403, 500 |
|||
