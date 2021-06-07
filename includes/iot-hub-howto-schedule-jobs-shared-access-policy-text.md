---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70049003"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Neste artigo, você cria um serviço de back-end que agenda um trabalho para invocar um método direto em um dispositivo, agenda um trabalho para atualizar o dispositivo gêmeo e monitora o progresso de cada trabalho. Para executar essas operações, o seu serviço precisa das permissões **leitura de registro** e **gravação de registro**. Por padrão, todo hub IoT é criado com uma política de acesso compartilhado chamada **registryReadWrite** que concede essas permissões.
