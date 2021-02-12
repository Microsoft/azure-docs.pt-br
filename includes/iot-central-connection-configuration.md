---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017453"
---
Quando você executar o aplicativo de dispositivo de exemplo mais adiante neste tutorial, precisará dos seguintes valores de configuração:

* Escopo da ID: no aplicativo do IoT Central, acesse **Administração > Conexão de Dispositivo**. Anote o valor de **Escopo da ID**.
* Chave primária do grupo: no aplicativo do IoT Central, acesse **Administração > Conexão de Dispositivo > Dispositivos IoT de SAS**. Anote o valor da **Chave primária** da Assinatura de Acesso Compartilhado.

Use o Cloud Shell para gerar uma chave de dispositivo com base na chave SAS de grupo recém-recuperada:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Anote a chave de dispositivo gerada, pois você a usará mais adiante neste tutorial.
