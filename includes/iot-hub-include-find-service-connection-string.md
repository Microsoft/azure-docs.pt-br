---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402397"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Para obter a cadeia de conexão do Hub IoT para a política de **serviço** , siga estas etapas:

1. Abra o Hub IoT no [portal do Azure](https://portal.azure.com).  A maneira mais fácil de acessar o Hub IoT é selecionar grupos de **recursos**, selecionar o grupo de recursos onde o Hub IOT está localizado e, em seguida, selecionar o Hub IOT na lista de recursos.

2. No painel do lado esquerdo do Hub IoT, selecione políticas de **acesso compartilhado**.

3. Na lista de políticas, selecione a política de **serviço** .

4. Em **chaves de acesso compartilhado**, selecione o ícone de cópia para a **cadeia de conexão--chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre políticas de acesso compartilhado do Hub IoT e permissões, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
