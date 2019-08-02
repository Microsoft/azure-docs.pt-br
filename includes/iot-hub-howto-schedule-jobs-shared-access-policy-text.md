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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402676"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Neste artigo, você cria um serviço de back-end que agenda um trabalho para invocar um método direto em um dispositivo, agenda um trabalho para atualizar o dispositivo e monitora o progresso de cada trabalho. Para executar essas operações, seu serviço precisa das permissões de **leitura** do registro e de **gravação do registro** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **registryReadWrite** que concede essas permissões.
