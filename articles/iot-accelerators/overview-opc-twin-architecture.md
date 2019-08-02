---
title: Arquitetura de OPC Twin – Azure | Microsoft Docs
description: Arquitetura do OPC Twin
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6ce9394f3d454bda5ead51f2c77a47db137a5136
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606173"
---
# <a name="opc-twin-architecture"></a>Arquitetura do OPC Twin

Os diagramas a seguir ilustram a arquitetura do OPC Twin.

## <a name="discover-and-activate"></a>Descobrir e ativar

1. O operador habilita o exame de rede no módulo ou faz uma descoberta única usando uma URL de descoberta. As informações de aplicativo e os pontos de extremidade descobertos são enviados por meio de telemetria para o agente de integração para processamento.  O agente de integração do dispositivo da UA de OPC processa eventos de descoberta do servidor da UA de OPC enviados pelo módulo do IoT Edge do Gêmeo OPC quando no modo de descoberta ou de exame. Os eventos de descoberta resultam em registro de aplicativo e atualizações no registro do dispositivo da UA de OPC.

   ![Como o OPC Twin funciona](media/overview-opc-twin-architecture/opc-twin1.png)

1. O operador inspeciona o certificado do ponto de extremidade descoberto e ativa o gêmeo do ponto de extremidade registrado para acesso. 

   ![Como o OPC Twin funciona](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procurar e monitor

1. Uma vez ativado, o operador pode usar a API REST de serviço de Gêmeo para procurar ou inspecionar o modelo de informações do servidor, ler/gravar variáveis de objeto e chamar métodos.  O usuário usa uma API simplificada da UA de OPC totalmente expressa em HTTP e JSON.

   ![Como o OPC Twin funciona](media/overview-opc-twin-architecture/opc-twin3.png)

1. A interface REST do serviço de gêmeo também pode ser usada para criar itens monitorados e assinaturas no OPC Publisher. O OPC Publisher permite enviar telemetria de sistemas de servidor da UA de OPC ao Hub IoT. Para obter mais informações sobre o OPC Publisher, veja [O que é o OPC Publisher](overview-opc-publisher.md).

   ![Como o OPC Twin funciona](media/overview-opc-twin-architecture/opc-twin4.png)
