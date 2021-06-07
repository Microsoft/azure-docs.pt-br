---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80756954"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Para criar uma política de acesso compartilhado que conceda permissões de gravação de **serviço** e de **registro** e para obter uma cadeia de conexão para essa política, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**. Selecione o grupo de recursos em que o Hub está localizado e, em seguida, selecione o seu hub na lista de recursos.

1. No painel do lado esquerdo do hub, selecione **Políticas de acesso compartilhado**.

1. No menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em **Adicionar uma política de acesso compartilhado**, insira um nome descritivo para sua política, como *serviceAndRegistryReadWrite*. Em **permissões**, selecione **gravação do registro** e **conexão do serviço** e, em seguida, selecione **criar**. (A permissão de **leitura do registro** é incluída automaticamente quando você seleciona **gravar registro**.)

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selecione a sua nova política na lista de políticas.

1. Em **Chaves de acesso compartilhado**, selecione o ícone de cópia da **Cadeia de conexão – Chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre permissões e políticas de acesso compartilhado do Hub IoT, consulte [Controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
