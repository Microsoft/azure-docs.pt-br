---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403997"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para criar uma política de acesso compartilhado que conceda permissões de **leitura do registro** e **conexão de serviço** e obtenha uma cadeia de conexão para essa política, siga estas etapas:

1. Abra o Hub IoT no [portal do Azure](https://portal.azure.com). A maneira mais fácil de acessar o Hub IoT é selecionar grupos de **recursos**, selecionar o grupo de recursos onde o Hub IOT está localizado e, em seguida, selecionar o Hub IOT na lista de recursos.

2. No painel do lado esquerdo do Hub IoT, selecione políticas de **acesso compartilhado**.

3. No menu superior acima da lista de políticas, selecione **Adicionar**.

4. No painel **Adicionar uma política de acesso compartilhado** , insira um nome descritivo para a política; por exemplo: *serviceAndRegistryRead*. Em **permissões**, selecione **registro ler** e **serviço conectar**e, em seguida, selecione **criar**.

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. De volta ao painel **políticas de acesso compartilhado** , selecione sua nova política na lista de políticas.

6. Em **chaves de acesso compartilhado**, selecione o ícone de cópia para a **cadeia de conexão--chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre políticas de acesso compartilhado do Hub IoT e permissões, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
