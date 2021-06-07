---
title: Esquema sobre eventos do Log de Atividades do Azure
description: Descreve o esquema de evento para cada categoria no log de atividades do Azure.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 09/30/2020
ms.author: bwren
ms.openlocfilehash: 721563fa140dfddbfb5984db8465187fb90018e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049161"
---
# <a name="azure-activity-log-event-schema"></a>Esquema sobre eventos do Log de Atividades do Azure
O [log de atividades do Azure](./platform-logs-overview.md) fornece informações sobre qualquer evento de nível de assinatura que ocorreu no Azure. Este artigo descreve as categorias de log de atividades e o esquema para cada uma. 

O esquema irá variar dependendo de como você acessa o log:
 
- Os esquemas descritos neste artigo são quando você acessa o log de atividades da [API REST](/rest/api/monitor/activitylogs). Esse também é o esquema usado quando você seleciona a opção **JSON** ao exibir um evento na portal do Azure.
- Consulte o [esquema de seção final da conta de armazenamento e dos hubs de eventos](#schema-from-storage-account-and-event-hubs) para o esquema quando você usa uma configuração de [diagnóstico](./diagnostic-settings.md) para enviar o log de atividades para o armazenamento do Azure ou hubs de eventos do Azure.
- Consulte [Azure monitor referência de dados](/azure/azure-monitor/reference/) para o esquema ao usar uma [configuração de diagnóstico](./diagnostic-settings.md) para enviar o log de atividades para um espaço de trabalho do log Analytics.

## <a name="severity-level"></a>Nível de severidade
Cada entrada no log de atividades tem um nível de severidade. O nível de severidade pode ter um dos seguintes valores:  

| Severidade | Descrição |
|:---|:---|
| Crítico | Eventos que exigem a atenção imediata de um administrador do sistema. Pode indicar que um aplicativo ou sistema falhou ou parou de responder.
| Erro | Eventos que indicam um problema, mas não exigem atenção imediata.
| Aviso | Eventos que fornecem forewarning de possíveis problemas, embora não seja um erro real. Indique que um recurso não está em um estado ideal e possa diminuir mais tarde para mostrar erros ou eventos críticos.  
| Informativo | Eventos que passam informações não críticas para o administrador. Semelhante a uma observação que diz: "para suas informações". 

O devlopers de cada provedor de recursos escolhe os níveis de severidade de suas entradas de recurso. Como resultado, a severidade real para você pode variar dependendo de como seu aplicativo é criado. Por exemplo, itens que são "críticos" para um recurso específico em isolamento podem não ser tão importantes quanto "erros" em um tipo de recurso que é fundamental para seu aplicativo do Azure. Certifique-se de considerar esse fato ao decidir quais eventos alertar.  

## <a name="categories"></a>Categorias
Cada evento no log de atividades tem uma categoria específica que são descritas na tabela a seguir. Consulte as seções abaixo para obter mais detalhes sobre cada categoria e seu esquema ao acessar o log de atividades do portal, do PowerShell, da CLI e da API REST. O esquema é diferente quando você [transmite o log de atividades para o armazenamento ou hubs de eventos](./resource-logs.md#send-to-azure-event-hubs). Um mapeamento das propriedades para o [esquema de logs de recursos](./resource-logs-schema.md) é fornecido na última seção do artigo.

| Categoria | Descrição |
|:---|:---|
| [Administrativo](#administrative-category) | Contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Exemplos de eventos administrativos incluem _criar máquina virtual_ e _excluir grupo de segurança de rede_.<br><br>Cada ação tomada por um usuário ou aplicativo usando o Resource Manager é modelada como uma operação em um determinado tipo de recurso. Se o tipo de operação for _Gravação_, _Exclusão_ ou _Ação_, os registros do início e do êxito ou falha da operação são registrados na categoria Administrativa. Os eventos administrativos também incluem quaisquer alterações no controle de acesso baseado em função do Azure em uma assinatura. |
| [Integridade do Serviço](#service-health-category) | Contém o registro de qualquer incidente de integridade do serviço ocorrido no Azure. Um exemplo de um evento de Integridade do Serviço _SQL Azure no Leste dos EUA está apresentando tempo de inatividade_. <br><br>Eventos de integridade do serviço são fornecidos em seis variedades: _Ação Necessária_, _Recuperação Assistida_, _Incidente_, _Manutenção_, _Informações_ ou _Segurança_. Esses eventos serão criados somente se você tiver um recurso na assinatura que seria impactado pelo evento.
| [Integridade de recursos](#resource-health-category) | Contém o registro de qualquer evento de integridade do recurso ocorrido nos recursos do Azure. Um exemplo de um evento de Integridade de Recursos é _Status de integridade da máquina virtual alterado para indisponível_.<br><br>Eventos de integridade de recursos podem representar um dos quatro status de integridade: _Disponível_, _Não disponível_, _Degradado_ e _Desconhecido_. Além disso, os eventos de integridade de recursos podem ser categorizados como _Iniciados pela plataforma_ ou _Iniciados pelo usuário_. |
| [Alerta](#alert-category) | Contém o registro de ativações para alertas do Azure. Um exemplo de evento de alerta é _A % de CPU em myVM está maior que 80 nos últimos cinco minutos_.|
| [Autoescala](#autoscale-category) | Contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em quaisquer configurações de dimensionamento automático que você definiu na sua assinatura. Um exemplo de um evento de dimensionamento automático é _Ação de dimensionamento automático vertical com falha_. |
| [Recomendação](#recommendation-category) | Contém eventos de recomendação do Assistente do Azure. |
| [Segurança](#security-category) | Contém o registro de todos os alertas gerados pela Central de Segurança do Azure. Um exemplo de um evento de segurança é _Arquivo de extensão dupla suspeito executado_. |
| [Política](#policy-category) | Contém registros de todas as operações de ação de efeito executadas pelo Azure Policy. Exemplos de eventos de política incluem _Auditar_ e _Negar_. Cada ação tomada pelo Policy é modelada como uma operação em um recurso. |

## <a name="administrative-category"></a>Categoria administrativa
Essa categoria contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Os exemplos dos tipos de eventos que você vê nessa categoria incluem "criar máquina virtual" e "excluir grupo de segurança". Cada ação tomada por um usuário ou um aplicativo usando o Resource Manager é modelada como uma operação em um tipo de recurso específico. Se o tipo de operação for Gravação, Exclusão ou Ação, os registros do início e do êxito ou falha da operação são registrados na categoria Administrativa. A categoria administrativa também inclui quaisquer alterações no controle de acesso baseado em função do Azure em uma assinatura.

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | Descrição |
| --- | --- |
| autorização |Blob das propriedades do RBAC do Azure do evento. Geralmente, inclui as propriedades "action", "role" e "scope". |
| chamador |Endereço de email do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. |
| canais |Um dos seguintes valores: "Admin", "Operação" |
| declarações |O token JWT usado pelo Active Directory para autenticar o usuário ou o aplicativo a fim de executar essa operação no Resource Manager. |
| correlationId |Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| descrição |Descrição de texto estático de um evento. |
| eventDataId |Identificador exclusivo de um evento. |
| eventName | Nome amigável do evento administrativo. |
| category | Sempre "administrativo" |
| httpRequest |Blob que descreve a solicitação Http. Geralmente inclui a “clientRequestId”, o “clientIpAddress” e o “método” (método HTTP. Por exemplo, PUT). |
| nível |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso” e “Informativo” |
| resourceGroupName |Nome do grupo de recursos do recurso afetado. |
| resourceProviderName |Nome do provedor de recursos do recurso afetado |
| resourceType | O tipo de recurso que foi afetado por um evento administrativo. |
| resourceId |ID de recurso do recurso afetado. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus |Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus, como estes valores comuns: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro de Servidor Interno (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504). |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de assinatura do Azure. |

## <a name="service-health-category"></a>Categoria de integridade do serviço
Essa categoria contém o registro de qualquer incidente de integridade do serviço ocorrido no Azure. Um exemplo do tipo de evento que você vê nessa categoria é "SQL Azure no Leste dos EUA está passando por tempo de inatividade". Os eventos de serviço de integridade são fornecidos em cinco variedades: Ação Necessária, Recuperação Assistida, Incidente, Manutenção, Informações ou Segurança, e só aparecerão se você tiver um recurso na assinatura que seria afetada pelo evento.

### <a name="sample-event"></a>Evento de exemplo
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Consulte as [notificações de integridade de serviço](../../service-health/service-notifications.md) para obter a documentação sobre os valores nas propriedades.

## <a name="resource-health-category"></a>Categoria Resource Health
Esta categoria contém o registro de qualquer evento de integridade do recurso ocorrido nos recursos do Azure. Um exemplo do tipo de evento que você veria nessa categoria é "Status de integridade da máquina virtual alterado para não disponível". Eventos de integridade de recursos podem representar um dos quatro status de integridade: Disponível, Não disponível, Degradado e Desconhecido. Além disso, os eventos de integridade de recursos podem ser categorizados como Iniciados pela plataforma ou Iniciados pelo usuário.

### <a name="sample-event"></a>Evento de exemplo

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | Descrição |
| --- | --- |
| canais | Sempre "Administrador, Operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de alerta. |
| eventDataId |Identificador exclusivo do alerta de evento. |
| category | Sempre "ResourceHealth" |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativo" e "Detalhado" |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| resourceGroupName |Nome do grupo de recursos que contém o recurso. |
| resourceProviderName |Sempre "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | O tipo de recurso que foi afetado por um evento do Resource Health. |
| resourceId | Nome da ID de recursos do recurso afetado. |
| status |Cadeia de caracteres que descreve o status do evento de integridade. Os valores podem ser: Ativo, Resolvido, Em andamento, Atualizado. |
| subStatus | Geralmente é null para alertas. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de assinatura do Azure. |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento.|
| properties.title | Uma cadeia de caracteres amigável ao usuário que descreve o status da integridade do recurso. |
| properties.details | Uma cadeia de caracteres amigável ao usuário que descreve mais detalhes sobre o evento. |
| properties.currentHealthStatus | O status de integridade atual do recurso. Um dos seguintes valores: "Disponível", "Não disponível", "Degradado" e "Desconhecido". |
| properties.previousHealthStatus | O status de integridade anterior do recurso. Um dos seguintes valores: "Disponível", "Não disponível", "Degradado" e "Desconhecido". |
| properties.type | Uma descrição do tipo de evento de integridade do recurso. |
| properties.cause | Uma descrição da causa do evento de integridade do recurso. "Iniciado pelo usuário" e "Iniciado pela plataforma". |


## <a name="alert-category"></a>Categoria de alerta
Essa categoria contém o registro de todas as ativações de alertas clássicos do Azure. Um exemplo do tipo de evento que você vê nessa categoria é "% de CPU em myVM foi 80 nos últimos 5 minutos." Uma variedade de sistemas do Azure têm um conceito de alerta – você pode definir uma regra de algum tipo e receber uma notificação quando as condições corresponderem a essa regra. Cada vez que um tipo de alerta com suporte do Azure é 'ativado', ou as condições são atendidas para gerar uma notificação, um registro de ativação também é enviado para essa categoria de Log de Atividades.

### <a name="sample-event"></a>Evento de exemplo

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | Descrição |
| --- | --- |
| chamador | Always Microsoft.Insights/alertRules |
| canais | Sempre "Administrador, Operação" |
| declarações | Blob JSON com o SPN (nome da entidade de serviço) ou tipo de recurso do mecanismo de alerta. |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de alerta. |
| eventDataId |Identificador exclusivo do alerta de evento. |
| category | Sempre "alerta" |
| nível |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso” e “Informativo” |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado, se for um alerta de métrica. Para outros tipos de alerta, é o nome do grupo de recursos que contém o alerta em si. |
| resourceProviderName |Nome do provedor de recursos para o recurso afetado, se for um alerta de métrica. Para outros tipos de alerta, é o nome do provedor de recursos do próprio alerta. |
| resourceId | Nome da ID de recurso do recurso afetado, se for um alerta de métrica. Para outros tipos de alerta, é a ID de recurso do próprio recurso de alerta. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente é null para alertas. |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de assinatura do Azure. |

### <a name="properties-field-per-alert-type"></a>Campo de propriedades por tipo de alerta
O campo de propriedades conterá valores diferentes dependendo da fonte do evento de alerta. Dois provedores de alerta de evento comuns são alertas do Log de Atividades e de métrica.

#### <a name="properties-for-activity-log-alerts"></a>Propriedades de alertas do Log de Atividades
| Nome do elemento | Descrição |
| --- | --- |
| properties.subscriptionId | A ID da assinatura do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.eventDataId | A ID dos dados de evento do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.resourceGroup | O grupo de recursos do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.resourceId | A ID de recurso do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.eventTimestamp | O carimbo de data/hora do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.operationName | O nome da operação do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.status | O status do evento do log de atividades que ativou essa regra de alerta do log de atividades.|

#### <a name="properties-for-metric-alerts"></a>Propriedades de alertas de métrica
| Nome do elemento | Descrição |
| --- | --- |
| properties.RuleUri | A ID de recurso da própria regra de alerta de métrica. |
| properties.RuleName | O nome da regra de alerta de métrica. |
| properties.RuleDescription | A descrição da regra de alerta de métrica (conforme definido na regra de alerta). |
| properties.Threshold | O valor limite usado na avaliação da regra de alerta de métrica. |
| properties.WindowSizeInMinutes | O tamanho da janela usada na avaliação da regra de alerta de métrica. |
| properties.Aggregation | O tipo de agregação definido na regra de alerta de métrica. |
| properties.Operator | O operador condicional usado na avaliação da regra de alerta de métrica. |
| properties.MetricName | O nome da métrica usada na avaliação da regra de alerta de métrica. |
| properties.MetricUnit | A unidade da métrica usada na avaliação da regra de alerta de métrica. |

## <a name="autoscale-category"></a>Categoria de dimensionamento automático
Essa categoria contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em quaisquer configurações de dimensionamento automático que você definiu na sua assinatura. Um exemplo do tipo de evento que você veria nessa categoria é "Falha na ação de escalar horizontalmente do Dimensionamento Automático". Usando o dimensionamento automático, você pode dimensionar o número de instâncias em um tipo de recurso com suporte com base na hora do dia e/ou dados de carga (métricas) usando uma configuração de dimensionamento automático. Quando as condições de redimensionamento forem atendidas, os eventos iniciados e bem-sucedidos ou com falha serão registrados nessa categoria.

### <a name="sample-event"></a>Evento de exemplo
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | Descrição |
| --- | --- |
| chamador | Always Microsoft.Insights/autoscaleSettings |
| canais | Sempre "Administrador, Operação" |
| declarações | Blob JSON com o SPN (nome da entidade de serviço) ou tipo de recurso do mecanismo de dimensionamento automático. |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de dimensionamento automático. |
| eventDataId |Identificador exclusivo do evento de dimensionamento automático. |
| nível |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso” e “Informativo” |
| resourceGroupName |Nome do grupo de recursos para a configuração de dimensionamento automático. |
| resourceProviderName |Nome do provedor de recursos para a configuração de dimensionamento automático. |
| resourceId |ID do recurso da configuração de dimensionamento automático. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| properties.Description | Descrição detalhada do que o mecanismo de dimensionamento automático estava fazendo. |
| properties.ResourceName | ID do recurso do recurso afetado (o recurso no qual a ação de escala estava sendo executada) |
| properties.OldInstancesCount | O número de instâncias antes da ação de dimensionamento automático começar. |
| properties.NewInstancesCount | O número de instâncias depois da ação de dimensionamento automático começar. |
| properties.LastScaleActionTime | O carimbo de data/hora de quando ocorreu a ação de dimensionamento automático. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente é null para dimensionamento automático. |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de assinatura do Azure. |

## <a name="security-category"></a>Categoria de segurança
Esta categoria contém o registro de todos os alertas gerados pela Central de Segurança do Azure. Um exemplo do tipo de evento que você veria nessa categoria é "Arquivo de extensão dupla suspeito executado".

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | Descrição |
| --- | --- |
| canais | Sempre "Operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de segurança. |
| eventDataId |Identificador exclusivo do evento de segurança. |
| eventName |Nome amigável do evento de segurança. |
| category | Sempre "segurança" |
| ID |Identificador de recursos exclusivo do evento de segurança. |
| nível |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso” e “Informativo” |
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| resourceProviderName |Nome do provedor de recursos para a Central de Segurança do Azure. Sempre "Microsoft.Security". |
| resourceType |O tipo de recurso que gerou o evento de segurança, como "Microsoft.Security/locations/alerts" |
| resourceId |ID do recurso do alerta de segurança. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. Essas propriedades variam dependendo do tipo de alerta de segurança. Consulte [essa página](../../security-center/security-center-alerts-overview.md) para obter uma descrição dos tipos de alertas originados da Central de Segurança. |
| properties.Severity |O nível de gravidade. Os valores possíveis são "Alto", "Médio" ou "Baixo". |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente null para eventos de segurança. |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de assinatura do Azure. |

## <a name="recommendation-category"></a>Categoria de recomendação
Esta categoria contém o registro das novas recomendações geradas para os serviços. Um exemplo de uma recomendação seria "Use conjuntos de disponibilidade para tolerância a falhas melhorada." Há quatro tipos de eventos de recomendação que podem ser gerados: alta disponibilidade, desempenho, segurança e otimização de custos. 

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | Descrição |
| --- | --- |
| canais | Sempre "Operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de recomendação |
| eventDataId | Identificador exclusivo do evento de recomendação. |
| category | Sempre "Recomendação" |
| ID |Identificador exclusivo do recurso do evento de recomendação. |
| nível |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso” e “Informativo” |
| operationName |Nome da operação.  Sempre "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| resourceProviderName |Nome do provedor de recursos do recurso a que essa recomendação se aplica, como "MICROSOFT.COMPUTE" |
| resourceType |Nome do tipo de recurso do recurso a que essa recomendação se aplica, como "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |ID do recurso ao qual a recomendação se aplica |
| status | Sempre "Active" |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de assinatura do Azure. |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes da recomendação.|
| properties.recommendationSchemaVersion| Versão do esquema das propriedades de recomendação publicada na entrada do log de atividades |
| properties.recommendationCategory | Categoria da recomendação. Os valores possíveis são "Alta Disponibilidade", "Desempenho", "Segurança" e "Custo" |
| properties.recommendationImpact| Impacto da recomendação. Os valores possíveis são "Alto", "Médio" ou "Baixo" |
| properties.recommendationRisk| Risco da recomendação. Os valores possíveis são "Erro", "Aviso" e "Nenhum" |

## <a name="policy-category"></a>Categoria da política

Essa categoria contém registros de todas as operações de ação de efeito executadas pelo [Azure Policy](../../governance/policy/overview.md). Exemplos dos tipos de eventos que você veria nessa categoria incluem _auditoria_ e _negação_. Cada ação tomada pelo Policy é modelada como uma operação em um recurso.

### <a name="sample-policy-event"></a>Evento do Policy de exemplo

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Descrições de propriedade de evento do Policy

| Nome do elemento | Descrição |
| --- | --- |
| autorização | Matriz de propriedades do RBAC do Azure do evento. Para os novos recursos, estes são a ação e o escopo da solicitação que disparou a avaliação. Para recursos existentes, a ação é "Microsoft.Resources/checkPolicyCompliance/read". |
| chamador | Para novos recursos, a identidade que iniciou uma implantação. Para recursos existentes, o GUID do grupo de recursos de Insights do Microsoft Azure Policy. |
| canais | Eventos do Policy usam apenas o canal "Operation". |
| declarações | O token JWT usado pelo Active Directory para autenticar o usuário ou o aplicativo a fim de executar essa operação no Resource Manager. |
| correlationId | Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| descrição | Este campo fica em branco para eventos do Policy. |
| eventDataId | Identificador exclusivo de um evento. |
| eventName | "BeginRequest" ou "EndRequest". "BeginRequest" é usado para avaliações de auditIfNotExists e deployIfNotExists atrasadas e quando um efeito deployIfNotExists inicia uma implantação de modelo. Todas as outras operações retornam "EndRequest". |
| category | Declara um evento de log de atividades como pertencente a "Policy". |
| eventTimestamp | Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| ID | Identificador exclusivo do evento no recurso específico. |
| nível | Nível do evento. Audit usa "Warning" e Deny usa "Error". Um erro auditIfNotExists ou deployIfNotExists pode gerar "Warning" ou "Error", dependendo da gravidade. Todos os outros eventos do Policy usam "Informational". |
| operationId | Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName | Nome da operação e se correlaciona diretamente com o efeito do Policy. |
| resourceGroupName | Nome do grupo de recursos do recurso avaliado. |
| resourceProviderName | Nome do provedor de recursos do recurso avaliado. |
| resourceType | Para os novos recursos, é o tipo que está sendo avaliado. Para recursos existentes, retorna "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | ID de recurso do recurso avaliado. |
| status | Cadeia de caracteres que descreve o status do resultado da avaliação do Policy. A maioria das avaliações do Policy retornam "Succeeded", mas um efeito de negação retorna "Failed". Erros em auditIfNotExists ou deployIfNotExists também retornam "Failed". |
| subStatus | O campo fica em branco para eventos do Policy. |
| submissionTimestamp | Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId | ID de assinatura do Azure. |
| properties.isComplianceCheck | Retorna "False" quando um novo recurso é implantado ou propriedades do Resource Manager de um recurso existente são atualizadas. Todos os outros [gatilhos de avaliação](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) resultam em "True". |
| properties.resourceLocation | A região do Azure do recurso que está sendo avaliado. |
| properties.ancestors | Uma lista separada por vírgulas de grupos de gerenciamento pai ordenados desde o pai direto até o avô mais distante. |
| properties.policies | Inclui detalhes sobre a definição de política, a atribuição, o efeito e os parâmetros dos quais essa avaliação do Policy resulta. |
| relatedEvents | Este campo fica em branco para eventos do Policy. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Esquema da conta de armazenamento e dos hubs de eventos
Ao transmitir o log de atividades do Azure para uma conta de armazenamento ou Hub de eventos, os dados seguem o [esquema do log de recursos](./resource-logs-schema.md). A tabela a seguir fornece um mapeamento das propriedades dos esquemas acima para o esquema de logs de recursos.

> [!IMPORTANT]
> O formato dos dados do log de atividades gravados em uma conta de armazenamento alterado para linhas JSON em 1º de novembro de 2018. Consulte [preparar para o formato de alteração para Azure monitor logs de recursos arquivados em uma conta de armazenamento](./resource-logs-blob-format.md) para obter detalhes sobre essa alteração de formato.


| Propriedade de esquema dos logs de recursos | Propriedade de esquema da API REST do Log de Atividades | Observações |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType e resourceGroupName são todos inferidos da resourceId. |
| operationName | operationName.value |  |
| category | Parte do nome da operação | Detalhamento do tipo de operação – "Gravação"/"Exclusão"/"Ação" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | descrição |  |
| durationMs | N/D | Sempre 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identidade | declarações e propriedades de autorização |  |
| Nível | Nível |  |
| local | N/D | Local do qual o evento foi processado. *Esse não é o local do recurso, mas sim onde o evento foi processado. Esta propriedade será removida em uma atualização futura.* |
| Propriedades | properties.eventProperties |  |
| properties.eventCategory | category | Se properties.eventCategory não estiver presente, a categoria será "Administrativo" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |

Veja a seguir um exemplo de um evento que usa esse esquema.

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o log de atividades](./platform-logs-overview.md)
* [Criar uma configuração de diagnóstico para enviar o log de atividades para Log Analytics espaço de trabalho, armazenamento do Azure ou hubs de eventos](./diagnostic-settings.md)