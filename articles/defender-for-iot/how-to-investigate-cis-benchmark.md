---
title: Investigar a recomendação do parâmetro de comparação do CIS
titleSuffix: Azure Defender for IoT
description: Realize investigações básicas e avançadas com base nas recomendações de linha de base do sistema operacional.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809689"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Recomendação investigar a linha de base do so (com base na referência de CIS) 

Realize investigações básicas e avançadas com base nas recomendações de linha de base do sistema operacional.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Investigação de recomendação de segurança de linha de base do so básico  

Você pode investigar as recomendações de linha de base do so navegando até o portal do Azure defender para IoT no **Hub IOT**. Para obter mais informações, consulte como [investigar as recomendações de segurança](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Investigação de recomendação de segurança de linha de base do so avançado  

Esta seção descreve como entender melhor os resultados do teste de linha de base do sistema operacional e consultar eventos no Azure Log Analytics.  

A investigação de recomendação de segurança de linha de base do sistema operacional avançado só tem suporte usando o log Analytics. Conecte o defender para IoT a um espaço de trabalho Log Analytics antes de continuar. Para obter mais informações sobre recomendações avançadas de segurança de linha de base do sistema operacional, consulte como [Configurar a solução baseada no agente do Azure defender para IOT](how-to-configure-agent-based-solution.md).

Para consultar os eventos de segurança de IoT em Log Analytics para alertas:

1. Navegue até a página **alertas** .

1. Selecione **investigar recomendações no espaço de trabalho log Analytics**.

Para consultar os eventos de segurança de IoT em Log Analytics para obter recomendações:

1. Navegue até a página **recomendações** .

1. Selecione **investigar recomendações no espaço de trabalho log Analytics**.

1. Selecione **Mostrar so (sistema operacional) regras de linha de base detalhes** da página exibição rápida **detalhes da recomendação** para ver os detalhes de um dispositivo específico.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Consulte os detalhes de um dispositivo específico."::: 

Para consultar seus eventos de segurança de IoT no espaço de trabalho Log Analytics diretamente:

1. Navegue até a página **logs** .

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Selecione logs no painel do lado esquerdo.":::

1. Selecione **investigar os alertas** ou, marque a opção **investigar os alertas no log Analytics** de qualquer recomendação de segurança ou alerta.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Consultas úteis para investigar os recursos de linha de base do sistema operacional: 

> [!Note]
> Certifique-se de substituir `<device-id>` pelos nomes que você forneceu ao dispositivo em cada uma das consultas a seguir. 


### <a name="retrieve-the-latest-information"></a>Recuperar as informações mais recentes

- **Falha de frota de dispositivo**: execute a consulta a seguir para recuperar as informações mais recentes sobre verificações que falharam na frota do dispositivo: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Falha de dispositivo específico** -execute a consulta a seguir para recuperar as informações mais recentes sobre verificações que falharam em um dispositivo específico:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Erro de dispositivo específico** – execute esta consulta para recuperar as informações mais recentes sobre verificações que têm um erro em um dispositivo específico: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Atualizar a lista de dispositivos para a frota do dispositivo que falhou em uma verificação específica** – execute esta consulta para recuperar a lista atualizada de dispositivos (em toda a frota do dispositivo) que falhou em uma verificação específica:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Próximas etapas

[Investigue as recomendações de segurança](quickstart-investigate-security-recommendations.md).
 