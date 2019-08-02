---
title: Erros de dados de log de diagnóstico do Stream Analytics do Azure
description: Este artigo explica os erros de saída de dados que podem ocorrer ao usar o Azure Stream Analytics e entrada diferentes.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ecc7077bf208adf1ac89adcce2f2e480ce34888e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329593"
---
# <a name="azure-stream-analytics-data-errors"></a>Erros de dados do Azure Stream Analytics

Erros de dados são erros que ocorrem durante o processamento de dados.  Esses erros geralmente ocorrem durante a serialização de eliminação de dados, serialização e operações de gravação.  Quando ocorrem erros de dados, o Stream Analytics grava os eventos de exemplo e obter informações detalhadas nos logs de diagnóstico.  Em alguns casos, um resumo dessas informações também é fornecido por meio de notificações do portal.

Este artigo descreve os diferentes tipos de erros, causas e detalhes do log de diagnóstico de erros de dados de entrada e saída.

## <a name="diagnostic-log-schema"></a>Esquema de log de diagnóstico

Ver [solucionar problemas do Azure Stream Analytics usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) para ver o esquema para os logs de diagnóstico. O JSON a seguir é um valor de exemplo para o **propriedades** campo de um log de diagnóstico para um erro de dados.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Erros de dados de entrada

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Causa: O tipo de compactação de entrada selecionado não corresponde os dados.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Mensagens com quaisquer erros de desserialização, incluindo o tipo de compactação inválidas são removidas da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. Para o Hub de eventos, o identificador é o PartitionId, deslocamento e número de sequência.

**Mensagem de erro**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: O cabeçalho de dados de entrada é inválido. Por exemplo, um CSV tem colunas com nomes duplicados.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Mensagens com quaisquer erros de desserialização incluindo cabeçalho inválidos são removidas da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: As colunas de entrada definidas com CREATE TABLE ou por meio de TIMESTAMP BY não existe.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Eventos com colunas ausentes são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Nomes das colunas que estão faltando. 
   * Carga real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: Não é possível converter a entrada para o tipo especificado na instrução CREATE TABLE.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Eventos com erro de conversão de tipo são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Nome da coluna e tipo esperado.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Causa: Dados de entrada não estão no formato correto. Por exemplo, a entrada não é um JSON válido.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Todos os eventos na mensagem depois que foi encontrado um erro de dados inválidos são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Carga real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Causa: O valor da expressão TIMESTAMP BY não pode ser convertido em datetime.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Eventos com carimbo de hora de entrada inválido são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Mensagem de erro. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Causa: O valor de TIMESTAMP BY OVER timestampColumn é NULL.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto: Eventos com carimbo de hora de entrada inválida de chave são removidos da entrada.
* Detalhes do log
   * A carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Causa: A diferença entre a hora do aplicativo e a hora de chegada é maior que a janela de tolerância da chegada tardia.
* Notificação do portal fornecida: Não
* Nível de log de diagnóstico: Informações
* Impacto:  Eventos de entrada tardia são tratados de acordo com o "manipular outros eventos" definir a ordenação de eventos de seção da configuração do trabalho. Para obter mais informações, consulte [políticas de tratamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Hora do aplicativo e a hora de chegada. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Causa: A diferença entre a hora do aplicativo e a hora de chegada é maior que 5 minutos.
* Notificação do portal fornecida: Não
* Nível de log de diagnóstico: Informações
* Impacto:  Eventos de entrada antecipados são tratados de acordo com o "manipular outros eventos" definir a ordenação de eventos de seção da configuração do trabalho. Para obter mais informações, consulte [políticas de tratamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Hora do aplicativo e a hora de chegada. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Causa: Evento é considerado fora de ordem de acordo com a janela de tolerância fora de ordem definida.
* Notificação do portal fornecida: Não
* Nível de log de diagnóstico: Informações
* Impacto:  Fora de ordem de eventos são tratados de acordo com o "manipular outros eventos" no evento ordenação seção Configuração da configuração do trabalho. Para obter mais informações, consulte [políticas de tratamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erros de saída de dados

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Causa: A coluna necessária para a saída não existe. Por exemplo, existe uma coluna definida como does't PartitionKey de tabela do Azure.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto:  Todos os erros de conversão de dados de saída incluindo coluna necessária ausente são tratados de acordo com o [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) configuração.
* Detalhes do log
   * Nome da coluna e o identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Causa: O valor da coluna não esteja de acordo com a saída. Por exemplo, o nome da coluna não é uma coluna de tabela do Azure válida.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto:  Todos os erros de conversão de dados de saída incluindo o nome de coluna inválido são tratados de acordo com o [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) configuração.
* Detalhes do log
   * Nome da coluna e o identificador do registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: Uma coluna não pode ser convertida em um tipo válido na saída. Por exemplo, o valor da coluna é incompatível com restrições ou tipo definido na tabela SQL.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto:  Todos os erros de conversão de dados de saída incluindo erros de conversão de tipo são tratados de acordo com o [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) configuração.
* Detalhes do log
   * Nome da coluna.
   * Identificador do registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: O valor da mensagem é maior que o tamanho de saída com suporte. Por exemplo, um registro é maior que 1 MB para uma saída do Hub de eventos.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto:  Todos os erros de conversão de dados de saída incluindo o limite de tamanho excedido de registro são tratados de acordo com o [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) configuração.
* Detalhes do log
   * Identificador do registro ou parte do registro.

**Mensagem de erro**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: Um registro já contém uma coluna com o mesmo nome de uma coluna do sistema. Por exemplo, a saída do CosmosDB com uma coluna chamada ID quando a coluna de ID é uma coluna diferente.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: Aviso
* Impacto:  Todos os erros de conversão de dados de saída incluindo chave duplicado são tratados de acordo com o [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) configuração.
* Detalhes do log
   * Nome da coluna.
   * Identificador do registro ou parte do registro.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas do Azure Stream Analytics usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md)

* [Entender o monitoramento de trabalho do Stream Analytics e como monitorar consultas](stream-analytics-monitoring.md)
