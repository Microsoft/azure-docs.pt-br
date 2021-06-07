---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75692566"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Host do Processador de Eventos
O Host do Processador de Eventos é um agente inteligente que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos. Para o ponto de verificação, o Host do Processador de Eventos requer uma conta de armazenamento. O exemplo a seguir mostra como criar uma conta de armazenamento e como obter suas chaves de acesso:

1. No menu do portal do Azure, selecione **Criar um recurso**.

    ![Criar um item de menu de recurso, portal do Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Selecione **Armazenamento** > **Conta de armazenamento**.
   
    ![Selecionar conta de armazenamento, portal do Microsoft Azure](./media/event-hubs-create-storage/select-storage-account.png)

3. Na página **Criar conta de armazenamento**, execute as etapas a seguir: 

   1. Insira o **Nome da conta de armazenamento**.
   2. Escolha uma **Assinatura** do Azure que contém o hub de eventos.
   3. Escolha ou crie o **Grupo de recursos** que tem o hub de eventos.
   4. Selecione um **Local** no qual criar o recurso. 
   5. Selecione **Examinar + criar**.
   
        ![Examinar + criar, criar conta de armazenamento, portal do Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Na página **Revisar + criar**, revise os valores e selecione **Criar**. 

    ![Examinar configurações da conta de armazenamento e criar, portal do Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Depois de ver a mensagem **Implantações concluídas com sucesso** em suas notificações, selecione **Ir para recurso** para abrir a página da Conta de armazenamento. Como alternativa, você poderá expandir **Detalhes da implantação** e, em seguida, selecionar seu novo recurso na lista de recursos.  

    ![Vá para recurso, implantação da conta de armazenamento, portal do Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecione **Contêineres**.

    ![Selecione o serviço de contêiner de Blobs, contas de armazenamento, portal do Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecione **+ Contêiner** na parte superior, insira um **Nome** para o contêiner e selecione **OK**. 

    ![Criar um contêiner de blob, contas de armazenamento, portal do Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Escolha **Chaves de acesso** no menu de página da **Conta de armazenamento** e copie o valor de **key1**.

    Salve esses valores no Bloco de notas ou em outro local temporário.
    - Nome da conta de armazenamento
    - Chave de acesso da conta de armazenamento
    - Nome do contêiner
