---
title: Solucionar problemas de latência usando logs da Análise de Armazenamento
description: Identificar e solucionar problemas de latência usando logs analíticos do armazenamento do Azure e otimizar o aplicativo cliente.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99221101"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Solucionar problemas de latência usando logs da Análise de Armazenamento

Diagnosticar e solucionar problemas é uma importante habilidade para criar e dar suporte a aplicativos cliente com o armazenamento do Azure.

Devido à natureza distribuída de um aplicativo do Azure, o diagnóstico e a solução de problemas de erros e de desempenho podem ser mais complexos do que em ambientes tradicionais.

As etapas a seguir demonstram como identificar e solucionar problemas de latência usando logs analíticos do armazenamento do Azure e otimizar o aplicativo cliente.

## <a name="recommended-steps"></a>Etapas recomendadas

1. Baixe os [logs de análise de armazenamento](./manage-storage-analytics-logs.md#download-storage-logging-log-data).

2. Use o seguinte script do PowerShell para converter os logs de formato bruto em formato tabular:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. O script iniciará uma janela de GUI na qual você pode filtrar as informações por colunas, conforme mostrado abaixo.

   ![Janela analisador do log analítico de armazenamento](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Restrinja as entradas de log com base em "tipo de operação" e procure a entrada de log criada durante o período de tempo do problema.

   ![Entradas de log de tipo de operação](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Durante o tempo em que o problema ocorreu, os seguintes valores são importantes:

   * Operação-tipo = getBlob
   * solicitação-status = SASNetworkError
   * Latência de ponta a ponta em MS = 8453
   * Servidor-latência-in-MS = 391

   A latência de ponta a ponta é calculada usando a seguinte equação:

   * Latência de ponta a ponta = Server-Latency + latência de cliente

   Calcule a latência do cliente usando a entrada de log:

   * Latência de cliente = latência de ponta a ponta – Server-Latency

        Exemplo: 8453 – 391 = 8062ms

   A tabela a seguir fornece informações sobre o OperationType de alta latência e os resultados de RequestStatus:

   | Tipo de blob |RequestStatus =<br>Êxito|RequestStatus =<br>RÍGIDO NetworkError|Recomendação|
   |---|---|---|---|
   |GetBlob|Sim|Não|[**Operação getBlob:** RequestStatus = êxito](#getblob-operation-requeststatus--success)|
   |GetBlob|Não|Sim|[**Operação getBlob:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Sim|Não|[**Operação Put:** RequestStatus = êxito](#put-operation-requeststatus--success)|
   |PutBlob|Não|Sim|[**Operação Put:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Resultados de status

### <a name="getblob-operation-requeststatus--success"></a>Operação getBlob: RequestStatus = êxito

Verifique os valores a seguir, conforme mencionado na etapa 5 da seção "etapas recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Em uma **operação getBlob** com **RequestStatus = Success**, se o **tempo máximo** for gasto na **latência do cliente**, isso indica que o armazenamento do Azure está gastando um grande volume de tempo gravando dados no cliente. Esse atraso indica um problema de Client-Side.

**Recomendação:**

* Investigue o código em seu cliente.
* Use o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operação getBlob: RequestStatus = (SAS) NetworkError

Verifique os valores a seguir, conforme mencionado na etapa 5 da seção "etapas recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Em uma **operação getBlob** com **REQUESTSTATUS = (SAS) NetworkError**, se o **tempo máximo** for gasto na **latência do cliente**, o problema mais comum é que o cliente está desconectando antes que o tempo limite expire no serviço de armazenamento.

**Recomendação:**

* Investigue o código no cliente para entender porque e quando o cliente desconecta do serviço de armazenamento.
* Use o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--success"></a>Operação Put: RequestStatus = êxito

Verifique os valores a seguir, conforme mencionado na etapa 5 da seção "etapas recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Em uma **operação Put** com **RequestStatus = Success**, se o **tempo máximo** for gasto na **latência do cliente**, isso indica que o cliente está demorando mais tempo para enviar dados para o armazenamento do Azure. Esse atraso indica um problema de Client-Side.

**Recomendação:**

* Investigue o código em seu cliente.
* Use o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operação Put: RequestStatus = (SAS) NetworkError

Verifique os valores a seguir, conforme mencionado na etapa 5 da seção "etapas recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Em uma **operação PutBlob** com **REQUESTSTATUS = (SAS) NetworkError**, se o **tempo máximo** for gasto na **latência do cliente**, o problema mais comum é que o cliente está desconectando antes que o tempo limite expire no serviço de armazenamento.

**Recomendação:**

* Investigue o código no cliente para entender porque e quando o cliente desconecta do serviço de armazenamento.
* Use o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente.