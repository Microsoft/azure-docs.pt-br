---
title: Acessando dados usando a Central de segurança do Azure para a versão prévia do IoT | Microsoft Docs
description: Saiba mais sobre como acessar seus dados de alerta e a recomendação de segurança ao usar a Central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 0788a62de701cb85a65c7053006e28cf4fffe5cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357196"
---
# <a name="access-your-security-data"></a>Acessar seus dados de segurança 

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Central de segurança (ASC) do Azure para IoT armazena dados brutos de segurança, recomendações e alertas de segurança (se você optar por salvá-lo) em seu espaço de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual espaço de trabalho do Log Analytics é usado:

1. Abrir seu Hub IoT.
1. Clique em **segurança**
2. Clique em **configurações**e alterar a configuração do espaço de trabalho do Log Analytics.

Para acessar seu espaço de trabalho do Log Analytics após a configuração:

1. Escolha um alerta ou uma recomendação no ASC para IoT. 
2. Clique em **investigação adicional**, em seguida, clique em **para ver quais dispositivos têm esse alerta, clique aqui e exibir a coluna DeviceId**.

Para obter detalhes sobre como consultar dados do Log Analytics, consulte [Introdução às consultas no Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Alertas de segurança são armazenados no _AzureSecurityOfThings.SecurityAlert_ tabela no espaço de trabalho do Log Analytics configurado para o ASC para solução de IoT.

Fornecemos um número de consultas úteis para ajudar você a começar a explorar os alertas de segurança.

### <a name="sample-records"></a>Exemplos de registros

Selecione alguns registros aleatórios

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | DESCRIÇÃO                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Ataque de força bruta foi bem-sucedida           | Um ataque de força bruta no dispositivo foi bem-sucedida        |    {"Endereço de código-fonte completo": "[\"10.165.12.18:\"]", "Nomes de usuário": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Logon local com êxito no dispositivo      | Um logon bem-sucedido de local para o dispositivo foi detectado     | {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Login Shell": "/ bin/su", "Id do processo de logon": "28207", "User Name": "invasor", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Falha na tentativa de logon local no dispositivo  | Uma tentativa de logon local com falha para o dispositivo foi detectada |  {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Login Shell": "/ bin/su", "Id do processo de logon": "22644", "User Name": "invasor", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Resumo do dispositivo

Selecione o número de alertas de segurança distintas detectado na semana passada pelo IoT Hub, dispositivo, severidade do alerta, o tipo de alerta.

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Ataque de força bruta foi bem-sucedida           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média        | Falha na tentativa de logon local no dispositivo  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Logon local com êxito no dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média        | Minerador de moeda de criptografia                     | 4   |

### <a name="iot-hub-summary"></a>Resumo de hub IoT

Selecione um número de dispositivos distintos que não teve alertas na última semana, pelo IoT Hub, severidade do alerta, o tipo de alerta

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Ataque de força bruta foi bem-sucedida           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Média        | Falha na tentativa de logon local no dispositivo  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Logon local com êxito no dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Média        | Minerador de moeda de criptografia                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

Recomendações de segurança são armazenadas no _AzureSecurityOfThings.SecurityRecommendation_ tabela no espaço de trabalho do Log Analytics configurado para o ASC para solução de IoT.

Nós fornecemos um número de consultas úteis para ajudá-lo a começar Explorando as recomendações de segurança.

### <a name="sample-records"></a>Exemplos de registros

Selecione alguns registros aleatórios

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | DESCRIÇÃO | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média | Ativo | Regra de firewall permissivas na cadeia de entrada foi encontrada | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou Portas | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média | Ativo | Regra de firewall permissivas na cadeia de entrada foi encontrada | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou Portas | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Resumo do dispositivo

Selecione o número de recomendações de segurança ativos distintos por tipo, o IoT Hub, dispositivos e severidade de recomendação.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média        | 4   |


## <a name="next-steps"></a>Próximas etapas

- Leia o ASC para IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Compreender e explorar [ASC para alertas de IoT](concept-security-alerts.md)
- Compreender e explorar [ASC para a recomendação de IoT](concept-recommendations.md)