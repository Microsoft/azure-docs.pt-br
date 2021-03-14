---
title: Agregação de eventos
titleSuffix: Azure Defender for IoT
description: Os agentes de segurança do defender para IoT coletam dados e eventos do sistema de seu dispositivo local e enviam os dados para a nuvem do Azure para processamento e análise.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 53811c20330d96b279eeb24e1aa90248b60fcb32
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120038"
---
# <a name="event-aggregation"></a>Agregação de eventos 

Os agentes de segurança do defender para IoT coletam dados e eventos do sistema de seu dispositivo local e enviam os dados para a nuvem do Azure para processamento e análise. O defender para IoT micro Agent coleta muitos tipos de eventos de dispositivo, incluindo novos processos e todos os novos eventos de conexão. O novo processo e os novos eventos de conexão podem ocorrer com frequência em um dispositivo em um segundo. Essa capacidade é importante para uma segurança abrangente, no entanto, o número de mensagens que os agentes de segurança enviam pode atender rapidamente ou exceder os limites de custo e cota do Hub IoT. No entanto, esses eventos contêm informações de segurança altamente valiosas que são cruciais para proteger seu dispositivo. 

Para reduzir a cota extra e os custos enquanto mantém seus dispositivos protegidos, o defender for IoT Agents agrega esses tipos de eventos: 

- ProcessCreate (somente Linux) 

- ConnectionCreate (somente RTOS do Azure) 

## <a name="how-does-event-aggregation-work"></a>Como funciona a agregação de eventos? 

Os agentes do defender for IoT agregam eventos para o período de intervalo ou a janela de tempo. Depois que o período de intervalo tiver passado, o agente enviará os eventos agregados para a nuvem do Azure para análise posterior. Os eventos agregados são armazenados na memória até serem enviados para a nuvem do Azure. 

Quando o agente coleta um evento idêntico a um que já é mantido na memória, o agente aumenta a contagem de acesso desse evento específico para reduzir a superfície de memória do agente. Quando a janela de tempo de agregação passa, o agente envia a contagem de ocorrências de cada tipo de evento ocorrido. A agregação de eventos é simplesmente a agregação das contagens de acertos de cada tipo de evento coletado. 

## <a name="process-events"></a>Processar eventos 

No momento, os eventos de processo só têm suporte em sistemas operacionais Linux. 

Os eventos de processo são considerados idênticos quando a *linha de comando* e  *userid*   são idênticos. 

O buffer padrão para eventos de processo é de 32 processos, após o qual o buffer será reciclado, e os eventos de processo mais antigos são descartados para liberar espaço para novos eventos de processo.  

## <a name="network-connection-events"></a>Eventos de conexão de rede 

Os eventos de conexão de rede atualmente só têm suporte em RTOS do Azure. 

Os eventos de conexão de rede são considerados idênticos quando a *porta local*, a  *porta remota*, o  *protocolo de transporte*, o  *endereço local* e o  *endereço remoto* são idênticos. 

O buffer padrão para eventos de conexão de rede é 64. Nenhum novo evento de rede será armazenado em cache até o próximo ciclo de coleta. Um aviso para aumentar o tamanho do cache será registrado.

## <a name="next-steps"></a>Próximas etapas

Verifique seus [alertas de segurança do defender para IOT](concept-security-alerts.md).
