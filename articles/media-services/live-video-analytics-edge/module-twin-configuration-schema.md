---
title: Esquema JSON do módulo/do Azure
description: Este tópico descreve o esquema JSON do módulo para análise de vídeo ao vivo em IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87053067"
---
# <a name="module-twin-json-schema"></a>Esquema JSON de módulo de entrelaçamento

Dispositivos gêmeos são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um dispositivo gêmeo para cada dispositivo que você conecta ao Hub IoT. Para obter uma explicação detalhada, consulte [entender e usar o módulo gêmeos no Hub IOT](../../iot-hub/iot-hub-devguide-module-twins.md)

Este tópico descreve o esquema JSON do módulo para análise de vídeo ao vivo em IoT Edge.

> [!NOTE]
> Para estar autorizado a acessar os recursos e a API dos Serviços de Mídia, primeiro você deve ser autenticado. Para obter mais informações, consulte [acessando a API dos serviços de mídia do Azure](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Propriedades de módulo de propriedade

A análise de vídeo ao vivo em IoT Edge expõe as propriedades de mymódulo a seguir. 

|Propriedade |Obrigatório |Dinâmico |Descrição |
|---|---|---|---|
|applicationDataDirectory |Sim |Não |Caminho para um volume montado para a configuração persistente. |
|azureMediaServicesArmId |Sim |Não |Identificador exclusivo de gerenciamento de recursos do Azure para a conta dos serviços de mídia.|
|aadTenantId |Sim |Não |ID do locatário do Azure AD do cliente.|
|aadServicePrincipalAppId |Sim |Sim |O cliente criou a AppId do Azure AD.|
|aadServicePrincipalCertificate |Sim<sup>*</sup>  |Sim |O cliente criou o certificado AppId do Azure AD.|
|aadServicePrincipalPassword |Sim<sup>*</sup>  |Sim |O cliente criou a senha do Azure AD AppId.|
|aadEndpoint |Não |Não |Ponto de extremidade do Azure AD específico da nuvem. <br/>Padrão: `https://login.microsoftonline.com` |
|aadResourceId |Não |Não |ID de recurso/audiência do Azure AD específico da nuvem <br/>Padrão: `https://management.core.windows.net/` |
|armEndpoint |Não |Não |Ponto de extremidade de gerenciamento de recursos do Azure específico da nuvem. <br/>Padrão: `https://management.azure.com/` |
|diagnosticsLevel |Não |Sim |Detalhes do evento: <br/>&#x02758 de informações; Aviso &#x02758; Erro &#x02758; &#x02758 crítico; None |
|diagnosticsEventsOutputName |Não |Sim |Saída de Hub para eventos de diagnóstico. <br/>(Vazio significa que o diagnóstico não está publicado)|
|operationalEventsOutputName|Não|Sim|Saída de Hub para eventos operacionais.<br/>(Vazio significa que os eventos operacionais não são publicados)
|logLevel|Não|Sim|Um dos seguintes: <br/>&#x000B7; Extensa<br/>&#x000B7; Informações (padrão)<br/>&#x000B7; Alerta<br/>&#x000B7; Ao<br/>&#x000B7; None|
|logCategories|Não|Sim|Uma lista separada por vírgulas dos seguintes: Application, MediaPipeline, Events <br/>Padrão: aplicativo, eventos|
|debugLogsDirectory|Não|Sim|Diretório para logs de depuração. Se houver logs presentes, os logs de depuração serão desabilitados se não estiverem presentes.

<sup>*</sup>Você deve fornecer o certificado ou a senha da entidade de serviço. 

As propriedades dinâmicas podem ser atualizadas sem a reinicialização do módulo. Você pode obter os valores para várias dessas propriedades seguindo a instrução no artigo [obtendo acesso ao API dos serviços de mídia](../latest/access-api-howto.md) . 

Consulte o artigo sobre [monitoramento e registro em log](monitoring-logging.md) para obter mais informações sobre a função das configurações de diagnóstico opcionais.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Próximas etapas

[Métodos diretos](direct-methods.md)
