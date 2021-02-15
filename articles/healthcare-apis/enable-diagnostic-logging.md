---
title: Habilitar o log de diagnóstico na API do Azure para FHIR
description: Este artigo explica como habilitar o log de diagnóstico na API do Azure para FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 02/03/2021
ms.openlocfilehash: 8dc87ae5b296f322d9d5a4d59c0a8c9b1c50d5da
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575425"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Habilitar o log de diagnóstico na API do Azure para FHIR

Neste artigo, você aprenderá a habilitar o log de diagnóstico na API do Azure para FHIR e poderá examinar algumas consultas de exemplo para esses logs. O acesso aos logs de diagnóstico é essencial para qualquer serviço de saúde em que a conformidade com os requisitos regulatórios (como a HIPAA) é uma necessidade. O recurso na API do Azure para FHIR que habilita logs de diagnóstico são as [**configurações de diagnóstico**](../azure-monitor/platform/diagnostic-settings.md) no portal do Azure. 

## <a name="enable-audit-logs"></a>Habilitar logs de auditoria
1. Para habilitar o log de diagnóstico na API do Azure para FHIR, selecione sua API do Azure para o serviço FHIR no portal do Azure 
2. Navegue até **configurações de diagnóstico**  
 ![ configurações de diagnóstico](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Selecione **+ Adicionar configuração de diagnóstico**

4. Insira um nome para a configuração

5. Selecione o método que você deseja usar para acessar seus logs de diagnóstico:

    1. **Arquivar em uma conta de armazenamento** para auditoria ou inspeção manual. A conta de armazenamento que você deseja usar precisa já estar criada.
    2. **Transmita para o Hub de eventos** para ingestão por um serviço de terceiros ou uma solução analítica personalizada. Você precisará criar um namespace do hub de eventos e uma política do hub de eventos antes de configurar esta etapa.
    3. **Transmitir para o** espaço de trabalho Log Analytics no Azure monitor. Você precisará criar seu espaço de trabalho do log Analytics antes de poder selecionar essa opção.

6. Selecione **AuditLogs** e/ou todas as **métricas**. As métricas incluem nome do serviço, disponibilidade, tamanho dos dados, latência total, total de solicitações, total de erros e carimbo de data/hora.

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Configurações de diagnóstico do Azure FHIR. Selecione AuditLogs e/ou todas as métricas." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Selecione **Salvar**


> [!Note] 
> Pode levar até 15 minutos para que os primeiros logs sejam mostrados em Log Analytics.  
 
Para obter mais informações sobre como trabalhar com logs de diagnóstico, consulte a [documentação do log de recursos do Azure](../azure-monitor/platform/platform-logs-overview.md)

## <a name="audit-log-details"></a>Detalhes do log de auditoria
Neste momento, a API do Azure para o serviço FHIR retorna os seguintes campos no log de auditoria: 

|Nome do campo  |Type  |Observações  |
|---------|---------|---------|
|CallerIdentity|Dinâmico|Um recipiente de propriedades genérico contendo informações de identidade
|CallerIdentityIssuer|String|Emissor 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|O endereço IP do chamador 
|CorrelationId|String| ID de Correlação
|FhirResourceType|String|O tipo de recurso para o qual a operação foi executada
|LogCategory|String|A categoria de log (atualmente, estamos retornando ' AuditLogs ' LogCategory)
|Location|String|O local do servidor que processou a solicitação (por exemplo, EUA Central do Sul)
|OperationDuration|int|O tempo necessário para concluir esta solicitação em segundos
|OperationName|String| Descreve o tipo de operação (por exemplo, atualização, tipo de pesquisa)
|RequestUri|String|O URI de solicitação 
|ResultType|String|Os valores disponíveis atualmente foram **iniciados**, **tiveram êxito** ou **falharam**
|StatusCode|int|O código de status HTTP. (por exemplo, 200) 
|TimeGenerated|Datetime|Data e hora do evento|
|Propriedades|String| Descreve as propriedades do fhirResourceType
|SourceSystem|String| Sistema de origem (sempre o Azure, neste caso)
|TenantId|String|ID do locatário
|Type|String|Tipo de log (sempre MicrosoftHealthcareApisAuditLog neste caso)
|_ResourceId|String|Detalhes sobre o recurso

## <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas Application Insights básicas que você pode usar para explorar os dados de log.

Execute esta consulta para ver os logs **mais recentes do 100** :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Execute esta consulta para agrupar operações por **tipo de recurso FHIR**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Executar esta consulta para obter todos os **resultados com falha**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusão 
Ter acesso aos logs de diagnóstico é essencial para monitorar um serviço e fornecer relatórios de conformidade. A API do Azure para FHIR permite que você execute essas ações por meio de logs de diagnóstico. 
 
FHIR é uma marca registrada da HL7, usada com permissão da HL7.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a habilitar os logs de auditoria para a API do Azure para FHIR. Em seguida, saiba mais sobre outras configurações adicionais que você pode configurar na API do Azure para FHIR
 
>[!div class="nextstepaction"]
>[Configurações adicionais](azure-api-for-fhir-additional-settings.md)
