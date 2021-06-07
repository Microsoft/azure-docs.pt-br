---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70050467"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para criar uma política de acesso compartilhado que conceda as permissões **conexão de serviço** e **leitura de registro** e obter uma cadeia de conexão para essa política, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**. Selecione o grupo de recursos em que o Hub está localizado e, em seguida, selecione o seu hub na lista de recursos.

1. No painel do lado esquerdo do hub, selecione **Políticas de acesso compartilhado**.

1. No menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em **Adicionar uma política de acesso compartilhado**, insira um nome descritivo para a política, como *serviceAndRegistryRead*. Em **Permissões**, selecione **Leitura de registro** e **Conexão de serviço** e, em seguida, selecione **Criar**.

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecione a sua nova política na lista de políticas.

1. Em **Chaves de acesso compartilhado**, selecione o ícone de cópia da **Cadeia de conexão – Chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre permissões e políticas de acesso compartilhado do Hub IoT, consulte [Controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
