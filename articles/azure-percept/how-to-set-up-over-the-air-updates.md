---
title: Configurar o Hub IoT do Azure para implantar nas atualizações do ar
description: Saiba como configurar o Hub IoT do Azure para implantar atualizações no ar para o Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661731"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Como configurar o Hub IoT do Azure para implantar as atualizações do ar no Azure Percept DK
Mantenha seu Azure Percept DK seguro e atualizado usando atualizações por satélite. Em algumas etapas simples, você poderá configurar seu ambiente do Azure com a atualização do dispositivo para o Hub IoT e implantar as atualizações mais recentes no Azure Percept DK.

## <a name="create-a-device-update-account"></a>Criar uma conta de atualização de dispositivo

1. Vá para a [portal do Azure](https://portal.azure.com) e entre com a conta do Azure que você está usando com o Azure Percept 

1. Na janela de pesquisa na parte superior da página, comece digitando "atualização do dispositivo para o Hub IoT"

1. Selecione a **atualização do dispositivo para os hubs IOT** , como ele aparece na janela Pesquisar.

1. Clique no botão **+ Adicionar** na parte superior esquerda da página.

1. Selecione a assinatura do Azure e o grupo de recursos associados ao dispositivo Percept do Azure (é aí que o Hub IoT da integração está localizado).

1. Especifique um nome e um local para sua conta de atualização de dispositivo

1. Examine os detalhes e selecione **revisar + criar**.
 
1. Quando a implantação for concluída, clique em **ir para o recurso**.
 
## <a name="create-a-device-update-instance"></a>Criar uma instância de atualização de dispositivo
Agora, crie uma instância dentro de sua atualização de dispositivo para a conta do Hub IoT.

1. Em sua atualização de dispositivo para o recurso do Hub IoT, clique em **instâncias** em **Gerenciamento de instância**.
 
1. Clique em **+ criar**, especifique um nome de instância e selecione o Hub IOT associado ao dispositivo Percept do Azure (ou seja, criado durante a experiência de integração). Isso pode demorar alguns minutos para ser concluído.
 
1. Clique em **Criar**

## <a name="configure-iot-hub"></a>Configurar o Hub IoT

1. Na página **instâncias** de gerenciamento de instância, aguarde até que a instância de atualização do dispositivo passe para um estado **bem-sucedido** . Clique no ícone de **atualização** ao lado de **excluir** para atualizar o estado.
 
1. Selecione a instância que foi criada para você e clique em **Configurar Hub IOT**. No painel esquerdo, selecione **concordo para fazer essas alterações** e clique em **Atualizar**.
 
1. Aguarde até que o processo seja concluído com êxito.
 
## <a name="configure-access-control-roles"></a>Configurar funções de controle de acesso
A etapa final permitirá que você conceda permissões a usuários para publicar e implantar atualizações.

1. Em sua atualização de dispositivo para o recurso de Hub IoT, clique em **controle de acesso (iam)**
 
2. Clique em **+ Adicionar** e selecione **Adicionar atribuição de função**
 
3. Para **função**, selecione **administrador de atualização de dispositivo**. Para **atribuir acesso para** selecionar **usuário, grupo ou princípio de serviço**. Para **selecionar** , selecione sua conta ou a conta da pessoa que irá implantar atualizações. Em seguida, clique em **Salvar**. 

    > [!TIP]
    > Se desejar fornecer mais pessoas em seu acesso à organização, você pode repetir essa etapa e tornar cada um desses usuários um administrador de **atualização de dispositivo**.

## <a name="next-steps"></a>Próximas etapas

Agora você está definido e pode [atualizar seu kit de desenvolvimento do Percept do Azure por meio do ar](./how-to-update-over-the-air.md) usando a atualização do dispositivo para o Hub IOT. Navegue até o Hub IoT do Azure que você está usando para o dispositivo Percept do Azure.