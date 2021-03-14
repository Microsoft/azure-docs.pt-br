---
title: Atualizar a API de relatório
description: Use esta API para relatar o parâmetro para relatórios de análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583663"
---
# <a name="update-report-api"></a>Atualizar a API de relatório

Essa API ajuda a modificar um parâmetro de relatório.

**Sintaxe da solicitação**

| Método | URI da solicitação |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Carga de solicitação**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Glossário**

Esta tabela lista as principais definições de elementos na carga de solicitação.

| Parâmetro | Obrigatório | Descrição | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Nome a ser atribuído ao relatório | Cadeia de caracteres |
| `Description` | No | Descrição do relatório criado | string |
| `StartTime` | Yes | Carimbo de data/hora após o qual a geração de relatório será iniciada | Cadeia de caracteres |
| `RecurrenceInterval` | No | Frequência em que o relatório deve ser gerado em horas. O valor mínimo é 4 | inteiro |
| `RecurrenceCount` | Não | Número de relatórios a serem gerados. O padrão é indefinido | inteiro |
| `Format` | Yes | Formato de arquivo do arquivo exportado. O padrão é CSV. | CSV/TSV |
| `CallbackUrl` | Yes | URL de retorno de chamada https a ser chamada na geração de relatório | string |
|||||

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
| `ReportId` | UUID (identificador universal exclusivo) do relatório que está sendo atualizado |
| `ReportName` | Nome fornecido ao relatório na carga de solicitação |
| `Description` | Descrição fornecida durante a criação do relatório |
| `QueryId` | ID de consulta passada no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID de usuário usada para criar o relatório |
| `CreatedTime` | Hora em que o relatório foi criado. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Hora em que o relatório foi modificado pela última vez. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `StartTime` | Hora em que a execução do relatório será iniciada. O formato de hora é AAAA-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Status da execução do relatório. Os valores possíveis são pausados, ativos e inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno de chamada fornecida na solicitação |
| `Format` | Formato dos arquivos de relatório |
|||
