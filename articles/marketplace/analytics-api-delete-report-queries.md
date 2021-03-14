---
title: Excluir API de consultas de relatório
description: Use esta API para excluir consultas definidas pelo usuário para análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583721"
---
# <a name="delete-report-queries-api"></a>Excluir API de consultas de relatório

Essa API exclui consultas definidas pelo usuário.

**Sintaxe da solicitação**

| **Método** | **URI da solicitação** |
| --- | --- |
| Delete (excluir) | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Cabeçalho da solicitação**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure) no formulário `Bearer <token>` |
| Tipo de conteúdo | string | `Application/JSON` |

**Parâmetro de caminho**

| **Nome do parâmetro** | **Tipo** | **Descrição** |
| --- | --- | --- |
| `queryId` | string | Filtre para obter detalhes de somente consultas com a ID fornecida neste argumento |

**Parâmetro de consulta**

Nenhum

**Carga de solicitação**

Nenhum

**Glossário**

Não

**Resposta**

A carga de resposta é estruturada da seguinte maneira no formato JSON.

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

Esta tabela lista as principais definições de elementos na resposta.

| **Parâmetro** | **Descrição** |
| --- | --- |
| `QueryId` | UUID exclusivo da consulta que foi excluída. |
| `Name` | Nome da consulta que foi excluída |
| `Description` | Descrição da consulta excluída |
| `Query` | Cadeia de caracteres de consulta de relatório da consulta excluída |
| `Type` | UserDefined |
| `User` | ID de usuário que criou a consulta |
| `CreatedTime` | Hora em que a consulta foi criada |
| `ModifiedTime` | Nulo |
| `TotalCount` | Número de conjuntos de itens na matriz de valores |
| `StatusCode` | Código de resultado. Os valores possíveis são 200, 400, 401, 403, 500 |
