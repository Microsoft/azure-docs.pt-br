---
title: Pausar API de execuções de relatório
description: Use essa API para pausar a execução agendada de um relatório de análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583683"
---
# <a name="pause-report-executions-api"></a>Pausar API de execuções de relatório

Essa API, na execução, pausa a execução agendada de relatórios.

**Sintaxe da solicitação**

| Método | URI da solicitação |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Cabeçalho da solicitação**

| Cabeçalho | Tipo | Descrição |
| ------------ | ------------- | ------------- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure) no formulário `Bearer <token>` |
| Tipo de conteúdo | string | `Application/JSON` |
||||

**Parâmetro de caminho**

Nenhum

**Parâmetro de consulta**

| Nome do parâmetro | Obrigatório | Type | Descrição |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sim | string | ID do relatório que está sendo modificado |
|||||

**Glossário**

Não

**Resposta**

A carga de resposta é estruturada da seguinte maneira no formato JSON:

Código de resposta: 200, 400, 401, 403, 404, 500 conteúdo de resposta:

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
      "RecurrenceCount": 0,
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

| Parâmetro | Descrição |
| ------------ | ------------- |
| `ReportId` | UUID (identificador universal exclusivo) do relatório excluído |
| `ReportName` | Nome fornecido ao relatório durante a criação |
| `Description` | Descrição fornecida durante a criação do relatório |
| `QueryId` | ID de consulta passada no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID de usuário usada para a criação do relatório |
| `CreatedTime` | Hora em que o relatório foi criado. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Hora em que o relatório foi modificado pela última vez. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `StartTime` | Hora em que a execução do relatório será iniciada. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Status da execução do relatório. Os valores possíveis são pausados, ativos e inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno de chamada fornecida na solicitação |
| `Format` | Formato dos arquivos de relatório |
|||
