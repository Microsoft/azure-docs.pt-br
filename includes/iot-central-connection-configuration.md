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
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491013"
---
Quando você executar o aplicativo de dispositivo de exemplo mais adiante neste tutorial, precisará dos seguintes valores de configuração:

* Escopo da ID: no aplicativo do IoT Central, acesse **Administração > Conexão de Dispositivo**. Anote o valor de **Escopo da ID**.
* Chave primária do grupo: no aplicativo do IoT Central, acesse **Administração > Conexão de Dispositivo > Dispositivos IoT de SAS**. Anote o valor da **Chave primária** da Assinatura de Acesso Compartilhado.

Use o Cloud Shell para gerar uma chave de dispositivo com base na chave primária de grupo que você recuperou:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Anote a chave de dispositivo gerada, pois você a usará mais adiante neste tutorial.
