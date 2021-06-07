---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400932"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Tipo de dados|Descrição|
|----|----|----|
|**AlertDisplayName**|String|O nome de exibição do alerta.|
|**AlertType**|String|O tipo de alerta. Os alertas do mesmo tipo devem ter o mesmo valor. Esse campo é uma cadeia de caracteres com chave que representa o tipo de alerta e não de uma instância de alerta. Todas as instâncias de alerta da mesma lógica/análise de detecção devem ter o mesmo valor para o tipo de alerta.|
|**CompromisedEntity**|String|O nome de exibição do recurso mais relacionado a este alerta.|
|**Descrição**|String|Descrição do alerta.|
|**EndTimeUtc**|DateTime|A hora do último evento ou atividade incluído no alerta.  O campo deve ser uma cadeia de caracteres que esteja de acordo com o formato ISO8601, incluindo informações de fuso horário UTC.|
|**Entidades**|IEnumerable (IEntity)|Uma lista de entidades relacionadas ao alerta. Essa lista pode conter uma mistura de entidades de tipos diferentes. O tipo de entidades pode ser qualquer um dos tipos definidos na seção entidades. As entidades que não estão na lista abaixo também podem ser enviadas, no entanto, não há garantia de que elas serão processadas (o alerta não falhará na validação com novos tipos de entidades).|
|**ExtendedProperties**|Dictionary (cadeia de caracteres, Cadeia de caracteres)|Os provedores podem (opcionalmente) incluir campos personalizados aqui.|
|**Intenção**|Enumeração|A intenção relacionada de Kill Chain por trás do alerta. Para obter a lista de valores com suporte e as explicações das tentativas de cadeia de eliminação com suporte da central de segurança do Azure, consulte as [intenções](../articles/security-center/alerts-reference.md#intentions).<br/>Esse campo pode ter vários valores (separados por vírgula).|
|**Isincident**|Bool|Este campo determina se o alerta é um incidente (um agrupamento composto de vários alertas) ou um único alerta. O valor padrão para o campo é ' false ' (ou seja, um único alerta).|
|**ProcessingEndTime**|DateTime|A hora em que o alerta foi acessível para o usuário final no produto original que contém o alerta.|
|**ProductName**|String|O nome do produto que publicou este alerta (central de segurança do Azure, Azure ATP, Microsoft defender ATP, MCAS e assim por diante).|
|**RemediationSteps**|Lista<String>|Itens de ação manual a serem necessários para corrigir o alerta.|
|**ResourceIdentifiers**|Lista (identificadores de recurso)|Os identificadores de recurso para esse alerta que podem ser usados para direcionar o alerta para o grupo de exposição de produto correto (locatário, espaço de trabalho, assinatura, etc.). Pode haver vários identificadores de tipo diferente por alerta.|
|**Gravidade**|Enumeração|A severidade do alerta conforme relatado pelo provedor. Valores possíveis: informativo, baixo, médio e alto.|
|**StartTimeUtc**|DateTime|A hora do primeiro evento ou atividade incluído no alerta. O campo deve ser uma cadeia de caracteres que esteja de acordo com o formato ISO8601, incluindo informações de fuso horário UTC.|
|**Status**|Enumeração|O status do ciclo de vida do alerta.<br/>Os status com suporte são: novo, resolvido, ignorado, desconhecido.<br/>Um alerta que especifica um valor diferente das opções com suporte é atribuído ao status ' desconhecido '.<br/>Um alerta que não especifica um valor recebe o status ' novo '.|
|**SystemAlertId**|String|O identificador do alerta.|
|**TimeGenerated**|DateTime|A hora em que o alerta foi gerado pelo provedor de alertas. Se não for relatado por provedores de alerta internos, um produto poderá optar por atribuir a hora em que ele foi recebido para processamento pelo produto.  O campo deve ser uma cadeia de caracteres que esteja de acordo com o formato ISO8601, incluindo informações de fuso horário UTC.|
|**Nome_do_Fornecedor**|String|O nome do fornecedor que gera o alerta.|
|||
