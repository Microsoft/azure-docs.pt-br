---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404006"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, você cria um serviço de back-end que adiciona as propriedades desejadas a um dispositivo e, em seguida, consulta o registro de identidade para localizar todos os dispositivos com propriedades relatadas que foram atualizadas de acordo. Seu serviço precisa da permissão de **conexão de serviço** para modificar as propriedades desejadas de um dispositivo e precisa da permissão de **leitura do registro** para consultar o registro de identidade. Não há nenhuma política de acesso compartilhado padrão que contenha apenas essas duas permissões, portanto, você precisa criar uma.
