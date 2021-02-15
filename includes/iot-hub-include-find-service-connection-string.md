---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "69558461"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Para obter a cadeia de conexão do Hub IoT para a política de **serviço**, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**. Selecione o grupo de recursos em que o Hub está localizado e, em seguida, selecione o seu hub na lista de recursos.

1. No painel do lado esquerdo do hub IoT, selecione **Políticas de acesso compartilhado**.

1. Na lista de políticas, selecione a política de **serviço**.

1. Em **Chaves de acesso compartilhado**, selecione o ícone de cópia da **Cadeia de conexão – Chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre permissões e políticas de acesso compartilhado do Hub IoT, consulte [Controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
