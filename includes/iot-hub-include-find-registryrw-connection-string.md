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
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993224"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Para obter a cadeia de conexão do Hub IoT para a política **registryReadWrite**, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**. Selecione o grupo de recursos em que o Hub está localizado e, em seguida, selecione o seu hub na lista de recursos.

2. No painel do lado esquerdo do hub, selecione **Políticas de acesso compartilhado**.

3. Na lista de políticas, selecione a política **registryReadWrite**.

4. Em **Chaves de acesso compartilhado**, selecione o ícone de cópia da **Cadeia de conexão – Chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Para obter mais informações sobre permissões e políticas de acesso compartilhado do Hub IoT, consulte [Controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
