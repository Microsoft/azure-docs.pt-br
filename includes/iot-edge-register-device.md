---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bb0b140dd1f42cae1d5d4bb670af8780d66c1f80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768626"
---
Crie uma identidade de dispositivo para seu dispositivo simulado para que ele possa se comunicar com o hub IoT. Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare esse para ser um dispositivo de IoT Edge desde o início. 

1. No portal do Azure, navegue para o hub IoT.
1. Selecione **IoT Edge** e então selecione **Adicionar Dispositivo do IoT Edge**.

   ![Adicionar dispositivo do IoT Edge](./media/iot-edge-register-device/add-device.png)

1. Dê o seu dispositivo simulado uma ID de dispositivo exclusivo.
1. Selecione **Salvar** para adicionar seu dispositivo.
1. Selecione seu dispositivo na lista de dispositivos.
1. Copie o valor de **Cadeia de conexão — chave primária** e salve-o. Você usará esse valor para configurar o tempo de execução de IoT Edge na próxima seção. 

