---
title: Segurança de acesso & dados de recomendação
description: Saiba mais sobre como acessar seus dados de alerta e recomendação de segurança ao usar o defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 495f9d568760421c7f42df3acf74217c15b01a05
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246332"
---
# <a name="access-your-security-data"></a>Acessar seus dados de segurança

O defender para IoT armazena alertas de segurança, recomendações e dados brutos de segurança (se você optar por salvá-lo) em seu espaço de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual espaço de trabalho do Log Analytics é usado:

1. Abrir seu Hub IoT.
1. Clique na folha **configurações** na seção **segurança** .
1. Clique em **coleta de dados** e altere a configuração do log Analytics espaço de trabalho.

Para acessar seus alertas e recomendações em seu espaço de trabalho do Log Analytics após a configuração:

1. Escolha um alerta ou recomendação no defender para IoT.
1. Clique em **investigação adicional** e, em seguida, clique **para ver quais dispositivos têm esse alerta clique aqui e exiba a coluna DeviceID**.

Para obter detalhes sobre como consultar dados de Log Analytics, consulte Introdução [às consultas no log Analytics](../azure-monitor/log-query/get-started-queries.md).

## <a name="security-alerts"></a>Alertas de segurança

Os alertas de segurança são armazenados na tabela _AzureSecurityOfThings. SecurityAlert_ no espaço de trabalho log Analytics configurado para a solução defender para IOT.

Fornecemos várias consultas úteis para ajudá-lo a começar a explorar os alertas de segurança.

### <a name="sample-records"></a>Registros de exemplo

Selecionar alguns registros aleatórios

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Descrição                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Ataque de força bruta bem-sucedido           | Um ataque de força bruta no dispositivo foi bem-sucedido        |    {"Endereço de origem completo": "[ \" 10.165.12.18: \" ]", "nomes de usuário": "[ \" \" ]", "DeviceID": "IOT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Logon local bem-sucedido no dispositivo      | Foi detectado um logon local bem-sucedido no dispositivo     | {"Endereço remoto": "?", "porta remota": "", "porta local": "", "Shell de logon": "/bin/su", "ID do processo de logon": "28207", "nome de usuário": "invasor", "DeviceID": "IoT-dispositivo-Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Falha na tentativa de logon local no dispositivo  | Foi detectada uma tentativa de logon local com falha no dispositivo |    {"Endereço remoto": "?", "porta remota": "", "porta local": "", "Shell de logon": "/bin/su", "ID do processo de logon": "22644", "nome de usuário": "invasor", "DeviceID": "IoT-dispositivo-Linux"} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de alertas de segurança distintos detectados na última semana, agrupados por Hub IoT, dispositivo, severidade do alerta, tipo de alerta.

```
// Get the number of distinct security alerts detected in the last week, grouped by
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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Ataque de força bruta bem-sucedido           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Falha na tentativa de logon local no dispositivo  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | Logon local bem-sucedido no dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | Miner de moeda de criptografia                     | 4   |

### <a name="iot-hub-summary"></a>Resumo do Hub IoT

Selecione um número de dispositivos distintos que tinham alertas na última semana, por Hub IoT, severidade do alerta, tipo de alerta

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Ataque de força bruta bem-sucedido           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Falha na tentativa de logon local no dispositivo  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Logon local bem-sucedido no dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Miner de moeda de criptografia                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

As recomendações de segurança são armazenadas na tabela _AzureSecurityOfThings. SecurityRecommendation_ no espaço de trabalho log Analytics configurado para a solução defender para IOT.

Fornecemos várias consultas úteis para ajudá-lo a começar a explorar as recomendações de segurança.

### <a name="sample-records"></a>Registros de exemplo

Selecionar alguns registros aleatórios

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

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | Recomendaçãostate | RecommendationDisplayName | Descrição | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativo | A regra de firewall permissiva na cadeia de entrada foi encontrada | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio | Ativo | A regra de firewall permissiva na cadeia de entrada foi encontrada | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de recomendações de segurança ativas distintas, agrupadas por Hub IoT, dispositivo, severidade de recomendação e tipo.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alta          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Médio        | 4   |

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do defender for IOT
- Saiba mais sobre o defender para [arquitetura](architecture.md) de IOT
- Entender e explorar o [defender para alertas de IOT](concept-security-alerts.md)
- Entender e explorar as [recomendações do defender para IOT](concept-recommendations.md)