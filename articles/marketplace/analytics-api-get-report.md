---
title: Obter API de relatório
description: Use esta API para obter relatórios de análise que foram agendados no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583686"
---
# <a name="get-report-api"></a>Obter API de relatório

Esta API Obtém todos os relatórios que foram agendados.

**Sintaxe da solicitação**

| **Método** | **URI da solicitação** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Cabeçalho da solicitação**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure) no formulário `Bearer <token>` |
| Tipo de conteúdo | string | `Application/JSON` |

**Parâmetro de caminho**

Nenhum

**Parâmetro de consulta**

| **Nome do parâmetro** | **Obrigatório** | **Tipo** | **Descrição** |
| --- | --- | --- | --- |
| `reportId` | Não | string | Filtre para obter detalhes de apenas relatórios com o `reportId` fornecido neste argumento |
| `reportName` | Não | string | Filtre para obter detalhes de apenas relatórios com o `reportName` fornecido neste argumento |
| `queryId` | Não | boolean | Incluir consultas de sistema predefinidas na resposta |

**Glossário**

Não

**Resposta**

A carga de resposta é estruturada no formato JSON da seguinte maneira:

Código de resposta: 200, 400, 401, 403, 404, 500

Carga de resposta:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
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
| `ReportId` | UUID exclusivo do relatório que foi criado |
| `ReportName` | Nome fornecido ao relatório na carga de solicitação |
| `Description` | Descrição fornecida quando o relatório foi criado |
| `QueryId` | ID de consulta passada no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID de usuário usada para criar o relatório |
| `CreatedTime` | Hora em que o relatório foi criado. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Hora em que o relatório foi modificado pela última vez. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `StartTime` | A execução de tempo será iniciada. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Status da execução do relatório. Os valores possíveis são pausados, ativos e inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno de chamada fornecida na solicitação |
| `Format` | Formato dos arquivos de relatório |
