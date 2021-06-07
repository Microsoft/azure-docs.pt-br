---
title: Residência de dados do cliente no Azure IoT Central | Microsoft Docs
description: Este artigo descreve a residência de dados do cliente nos aplicativos IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280669"
---
# <a name="azure-iot-central-customer-data-residency"></a>Residência de dados do cliente do Azure IoT Central

IoT Central não armazena dados do cliente fora da geografia especificada pelo cliente, exceto para os seguintes cenários:

- Quando um novo usuário é adicionado a um aplicativo IoT Central existente, a ID de email do usuário pode ser armazenada fora da geografia até que o usuário convidado acesse o aplicativo pela primeira vez.

- IoT Central blocos de mapa do painel usam [mapas do Azure](../../azure-maps/about-azure-maps.md). Quando você adiciona um bloco de mapa a um aplicativo de IoT Central existente, os dados de localização podem ser processados ou armazenados de acordo com as regras de localização geográfica do serviço do Azure Maps.
