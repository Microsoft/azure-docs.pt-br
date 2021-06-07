---
title: incluir arquivo
description: incluir arquivo
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70049048"
---
<!-- put the ## header in the file that includes this file -->

Nesta seção, você cria uma identidade de dispositivo no registro de identidade no hub IoT. Um dispositivo não pode se conectar ao hub, a menos que ele tenha uma entrada no registro de identidade. Para obter mais informações, consulte o [Guia de desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. No menu de navegação do hub IoT, abra **Dispositivos IoT** e, em seguida, selecione **Novo** para adicionar um dispositivo no hub IoT.

    ![Criar identidade do dispositivo no portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Em **Criar um dispositivo**, forneça um nome para o novo dispositivo, como **myDeviceId** e selecione **Salvar**. Essa ação cria uma identidade do dispositivo para o seu hub IoT.

   ![Adicionar um novo dispositivo](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Após a criação do dispositivo, abra o dispositivo na lista do painel **Dispositivos IoT**. Copie a **Cadeia de Conexão Primária** para usá-la mais tarde.

    ![Cadeia de conexão de dispositivo](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
