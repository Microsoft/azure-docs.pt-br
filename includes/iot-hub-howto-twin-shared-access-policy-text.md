---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70050386"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, você cria um serviço de back-end que adiciona as propriedades desejadas a um dispositivo gêmeo e, em seguida, consulta o registro de identidade para localizar todos os dispositivos com as propriedades relatadas que foram atualizadas de acordo. O seu serviço precisa da permissão **conexão de serviço** para modificar as propriedades desejadas de um dispositivo gêmeo e precisa da permissão **leitura do registro** para consultar o registro de identidade. Não há nenhuma política de acesso compartilhado padrão que contenha apenas essas duas permissões, portanto, você precisa criar uma.
