---
title: Paradigma do acesso programático
description: Entenda o fluxo de alto nível do padrão de chamada à API para análise programática. As APIs para acessar relatórios de análise no Microsoft Commercial Marketplace também são abordadas.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583706"
---
# <a name="programmatic-access-paradigm"></a>Paradigma do acesso programático

Este diagrama mostra o padrão de chamada de API usado para criar um novo modelo de relatório, agendar o relatório personalizado e recuperar dados de falha.

[ ![ Ilustra o padrão de chamada de API usado para criar um novo modelo de relatório, agendar o relatório personalizado e recuperar dados de falha.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Figura 1: fluxo de alto nível do padrão de chamada da API***

Esta lista fornece mais detalhes sobre a Figura 1.

1. O aplicativo cliente pode definir o esquema/modelo de relatório personalizado chamando a [API criar relatório de consulta](#create-report-query-api). Como alternativa, você pode usar um modelo de relatório ( `QueryId` ) na [lista de consultas do sistema](analytics-system-queries.md).
1. Em caso de sucesso, a API criar modelo de relatório retorna o `QueryId` .
1. Em seguida, o aplicativo cliente chama a [API de criação de relatório](#create-report-api) usando o `QueryID` junto com a data de início do relatório, intervalo de repetição, recorrência e um URI de retorno de chamada opcional.
1. Em caso de sucesso, a [API criar relatório](#create-report-api) retorna o `ReportID` .
1. O aplicativo cliente é notificado no URI de retorno de chamada assim que os dados do relatório estiverem prontos para download.
1. Em seguida, o aplicativo cliente usa a [API obter execuções de relatório](#get-report-executions-api) para consultar o status do relatório com o `Report ID` intervalo de datas e.
1. Em caso de sucesso, o link de download do relatório é retornado e o aplicativo pode iniciar o download dos dados.

## <a name="report-query-language-specification"></a>Especificação de linguagem de consulta de relatório

Embora forneçamos [consultas de sistema](analytics-system-queries.md) que podem ser usadas para criar relatórios, você também pode criar suas próprias consultas com base em suas necessidades de negócios. Para saber mais sobre consultas personalizadas, consulte [especificação de consulta personalizada](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Criar API de consulta de relatório

Essa API ajuda a criar consultas personalizadas que definem o conjunto de linhas do qual as colunas e métricas precisam ser exportadas. A API fornece a flexibilidade para criar um novo modelo de relatório com base nas suas necessidades de negócios.

Você também pode usar as [consultas do sistema](analytics-system-queries.md) que fornecemos. Quando modelos de relatório personalizados não são necessários, você pode chamar a [API de criação de relatório](#create-report-api) diretamente usando o [QueryIds](analytics-system-queries.md) das consultas do sistema que fornecemos.

O exemplo a seguir mostra como criar uma consulta personalizada para obter o _uso normalizado e encargos financeiros estimados para SKUs pagas_ do conjunto de [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) do último mês.

*Sintaxe da solicitação*

| Método | URI da solicitação |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Cabeçalho da solicitação*

| parâmetro | Type | Descrição |
| ------------- | ------------- | ------------- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure). O formato é `Bearer <token>`. |
| Tipo de conteúdo | `string` | `application/JSON` |
||||

*Parâmetro de caminho*

Nenhum

*Parâmetro de consulta*

Nenhum

*Exemplo de carga de solicitação*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Glossário*

Esta tabela fornece as definições de chave dos elementos na carga de solicitação.

| Parâmetro | Obrigatório | Descrição | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Sim | Nome amigável da consulta | Cadeia de caracteres |
| `Description` | No | Descrição do que a consulta retorna | string |
| `Query` | Sim | Cadeia de caracteres de consulta de relatório | Tipo de dados: cadeia de caracteres<br>[Consulta personalizada](analytics-sample-queries.md) com base na necessidade de negócios |
|||||

> [!NOTE]
> Para obter exemplos de consulta personalizada, consulte [exemplos de consultas de exemplo](analytics-sample-queries.md).

*Exemplo de resposta*

A carga de resposta é estruturada da seguinte maneira:

Códigos de resposta: 200, 400, 401, 403, 500

Exemplo de carga de resposta:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Glossário*

Esta tabela fornece as definições de chave dos elementos na resposta.

| Parâmetro | Descrição |
| ------------ | ------------- |
| `QueryId` | UUID (identificador universal exclusivo) da consulta que você criou |
| `Name` | Nome amigável fornecido à consulta na carga de solicitação |
| `Description` | Descrição fornecida durante a criação da consulta |
| `Query` | Consulta de relatório passada como entrada durante a criação da consulta |
| `Type` | Definida como `userDefined` |
| `User` | ID de usuário usada para criar a consulta |
| `CreatedTime` | Hora UTC em que a consulta foi criada neste formato: YYYY-MM-ddTHH: mm: ssZ |
| `TotalCount` | Número de conjuntos de itens na matriz de valores |
| `StatusCode` | Código de Resultado<br>Os valores possíveis são 200, 400, 401, 403, 500 |
| `message` | Mensagem de status da execução da API |
|||

## <a name="create-report-api"></a>Criar API de relatório

Ao criar um modelo de relatório personalizado com êxito e receber o `QueryID` como parte da resposta de [consulta criar relatório](#create-report-query-api) , essa API pode ser chamada para agendar uma consulta a ser executada em intervalos regulares. Você pode definir uma frequência e um agendamento para o relatório a ser entregue. Para consultas do sistema que fornecemos, a API criar relatório também pode ser chamada com [QueryId](analytics-sample-queries.md).

*Sintaxe da solicitação*

| Método | URI da solicitação |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Cabeçalho da solicitação*

| parâmetro | Type | Descrição |
| ------ | ---- | ----------- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure). O formato é `Bearer <token>`. |
| Tipo de conteúdo | string | `application/JSON` |
||||

*Parâmetro de caminho*

Nenhum

*Parâmetro de consulta*

Nenhum

*Exemplo de carga de solicitação*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Glossário*

Esta tabela fornece as definições de chave dos elementos na carga de solicitação.

| Parâmetro | Obrigatório | Descrição | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Sim | Nome atribuído ao relatório | Cadeia de caracteres |
| `Description` | No | Descrição do relatório criado | string |
| `QueryId` | Sim | ID de consulta de relatório | string |
| `StartTime` | Sim | Carimbo de data/hora UTC no qual a geração de relatório será iniciada.<br>O formato deve ser: YYYY-MM-ddTHH: mm: ssZ | string |
| `RecurrenceInterval` | Sim | Frequência em horas em que o relatório deve ser gerado.<br>O valor mínimo é 4 e o valor máximo é 90. | inteiro |
| `RecurrenceCount` | Não | Número de relatórios a serem gerados. | inteiro |
| `Format` | Não | Formato de arquivo do arquivo exportado.<br>O formato padrão é. CSV. | CSV/TSV |
| `CallbackUrl` | Não | URL acessível publicamente que pode ser configurada opcionalmente como destino do retorno de chamada. | Cadeia de caracteres (URL http) |
| `ExecuteNow` | Não | Esse parâmetro deve ser usado para criar um relatório que será executado apenas uma vez. `StartTime`, `RecurrenceInterval` e `RecurrenceCount` serão ignorados se for definido como `true` . O relatório é executado imediatamente de maneira assíncrona. | true/false |
| `QueryStartTime` | Não | Opcionalmente, especifica a hora de início para a consulta que extrai os dados. Esse parâmetro é aplicável somente para o relatório de execução de um tempo que tenha `ExecuteNow` definido como `true` . O formato deve ser AAAA-MM-ddTHH: mm: ssZ | Timestamp como cadeia de caracteres |
| `QueryEndTime` | Não | Opcionalmente, especifica a hora de término para a consulta que extrai os dados. Esse parâmetro é aplicável somente para o relatório de execução de um tempo que tenha `ExecuteNow` definido como `true` . O formato deve ser AAAA-MM-ddTHH: mm: ssZ | Timestamp como cadeia de caracteres |
|||||

*Exemplo de resposta*

A carga de resposta é estruturada da seguinte maneira:

Código de resposta: 200, 400, 401, 403, 404, 500

Carga de resposta:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Glossário*

Esta tabela fornece as definições de chave dos elementos na resposta.

| Parâmetro | Descrição |
| ------------ | ------------- |
| `ReportId` | UUID (identificador universal exclusivo) do relatório que você criou |
| `ReportName` | Nome fornecido ao relatório na carga de solicitação |
| `Description` | Descrição fornecida durante a criação do relatório |
| `QueryId` | ID de consulta passada no momento em que você criou o relatório |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID de usuário usada para criar o relatório |
| `CreatedTime` | Hora UTC em que o relatório foi criado neste formato: AAAA-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Hora UTC em que o relatório foi modificado pela última vez neste formato: AAAA-MM-ddTHH: mm: ssZ |
| `StartTime` | Hora UTC em que a execução do relatório será iniciada neste formato: AAAA-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Status da execução do relatório. Os valores possíveis são **pausados**, **ativos** e **inativos**. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno de chamada fornecida na solicitação |
| `Format` | Formato dos arquivos de relatório. Os valores possíveis são CSV ou TSV. |
| `TotalCount` | Número de conjuntos de itens na matriz de valores |
| `StatusCode` | Código de Resultado<br>Os valores possíveis são 200, 400, 401, 403, 500 |
| `message` | Mensagem de status da execução da API |
|||

## <a name="get-report-executions-api"></a>Obter API de execuções de relatório

Você pode usar esse método para consultar o status de uma execução de relatório usando o `ReportId` recebido da [API criar relatório](#create-report-api). O método retornará o link de download do relatório se o relatório estiver pronto para download. Caso contrário, o método retornará o status. Você também pode usar essa API para obter todas as execuções que ocorreram para um determinado relatório.

> [!IMPORTANT]
> Essa API tem parâmetros de consulta padrão definidos para `executionStatus=Completed` e  `getLatestExecution=true` . Portanto, chamar a API antes da primeira execução bem-sucedida do relatório retornará 404. Execuções pendentes podem ser obtidas pela configuração `executionStatus=Pending` .

*Sintaxe da solicitação*

| Método | URI da solicitação |
| ------------ | ------------- |
| Obter | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Cabeçalho da solicitação*

| parâmetro | Type | Descrição |
| ------ | ------ | ------ |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure). O formato é `Bearer <token>`. |
| Tipo de conteúdo | string | `application/json` |
||||

*Parâmetro de caminho*

Nenhum

*Parâmetro de consulta*

| Nome do parâmetro | Obrigatório | Type | Descrição |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sim | string | Filtre para obter detalhes de execução apenas de relatórios com `reportId` o fornecido neste argumento. Vários `reportIds` podem ser especificados separando-os com um ponto-e-vírgula ";". |
| `executionId` | Não | string | Filtre para obter detalhes de apenas relatórios com `executionId` o fornecido neste argumento. Vários `executionIds` podem ser especificados separando-os com um ponto-e-vírgula ";". |
| `executionStatus` | Não | Cadeia de caracteres/enumeração | Filtre para obter detalhes de apenas relatórios com `executionStatus` o fornecido neste argumento.<br>Os valores válidos são: `Pending` , `Running` , `Paused` e `Completed` <br>O valor padrão é `Completed`. Vários status podem ser especificados separando-os com um ponto-e-vírgula ";". |
| `getLatestExecution` | Não | boolean | A API retornará detalhes da última execução do relatório.<br>Por padrão, esse parâmetro é definido como `true`. Se você optar por passar o valor desse parâmetro como `false` , a API retornará as últimas 90 dias de execução de instâncias. |
|||||

*Carga de solicitação*

Nenhum

*Exemplo de resposta*

A carga de resposta é estruturada da seguinte maneira:

Códigos de resposta: 200, 400, 401, 403, 404, 500

Exemplo de carga de resposta:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Depois que a execução do relatório for concluída, o status de execução `Completed` será mostrado. Você pode baixar o relatório selecionando a URL no `reportAccessSecureLink` .

*Glossário*

Definições de chave dos elementos na resposta.

| Parâmetro | Descrição |
| ------------ | ------------- |
| `ExecutionId` | UUID (identificador universal exclusivo) da instância de execução |
| `ReportId` | ID do relatório associada à instância de execução |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno de chamada associada à instância de execução |
| `Format` | Formato do arquivo gerado no final da execução |
| `ExecutionStatus` | Status da instância de execução de relatório.<br>Os valores válidos são: `Pending` , `Running` , `Paused` e `Completed` |
| `ReportAccessSecureLink` | Link pelo qual o relatório pode ser acessado com segurança |
| `ReportExpiryTime` | Hora UTC após a qual o link do relatório expirará neste formato: AAAA-MM-ddTHH: mm: ssZ |
| `ReportGeneratedTime` | Hora UTC em que o relatório foi gerado neste formato: AAAA-MM-ddTHH: mm: ssZ |
| `TotalCount` | Número de conjuntos de itens na matriz de valores |
| `StatusCode` | Código de Resultado <br>Os valores possíveis são 200, 400, 401, 403, 404 e 500 |
| `message` | Mensagem de status da execução da API |
|||

## <a name="next-steps"></a>Próximas etapas
- Você pode experimentar as APIs por meio da [URL da API do Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- Introdução ao acesso programático a dados de análise
