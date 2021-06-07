---
title: Arquitetura de OPC Twin – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da arquitetura do OPC Twin. Ele descreve a descoberta, a ativação, a navegação e o monitoramento do servidor.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646514"
---
# <a name="opc-twin-architecture"></a>Arquitetura do OPC Twin

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Os diagramas a seguir ilustram a arquitetura do OPC Twin.

## <a name="discover-and-activate"></a>Descobrir e ativar

1. O operador habilita o exame de rede no módulo ou faz uma descoberta única usando uma URL de descoberta. As informações de aplicativo e os pontos de extremidade descobertos são enviados por meio de telemetria para o agente de integração para processamento.  O agente de integração do dispositivo da UA de OPC processa eventos de descoberta do servidor da UA de OPC enviados pelo módulo do IoT Edge do Gêmeo OPC quando no modo de descoberta ou de exame. Os eventos de descoberta resultam em registro de aplicativo e atualizações no registro do dispositivo da UA de OPC.

   ![Diagrama que mostra a arquitetura do OPC Twin com o módulo do IoT Edge do OPC Twin no modo de descoberta ou verificação.](media/overview-opc-twin-architecture/opc-twin1.png)

1. O operador inspeciona o certificado do ponto de extremidade descoberto e ativa o gêmeo do ponto de extremidade registrado para acesso. 

   ![Diagrama que mostra a arquitetura do OPC Twin com a "Identidade de Gêmeo" do IoT Edge.](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procurar e monitor

1. Uma vez ativado, o operador pode usar a API REST de serviço de Gêmeo para procurar ou inspecionar o modelo de informações do servidor, ler/gravar variáveis de objeto e chamar métodos.  O usuário usa uma API simplificada da UA de OPC totalmente expressa em HTTP e JSON.

   ![Diagrama que mostra a configuração da arquitetura do OPC Twin para navegar e inspecionar o modelo de informação do servidor.](media/overview-opc-twin-architecture/opc-twin3.png)

1. A interface REST do serviço de gêmeo também pode ser usada para criar itens monitorados e assinaturas no OPC Publisher. O OPC Publisher permite enviar telemetria de sistemas de servidor da UA de OPC ao Hub IoT. Para obter mais informações sobre o OPC Publisher, veja [O que é o OPC Publisher](overview-opc-publisher.md).

   ![Como o OPC Twin funciona](media/overview-opc-twin-architecture/opc-twin4.png)
