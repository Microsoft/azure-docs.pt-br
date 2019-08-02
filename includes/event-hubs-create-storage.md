---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172458"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Host do Processador de Eventos
O Host do Processador de Eventos é um agente inteligente que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos. Para o ponto de verificação, o Host do Processador de Eventos requer uma conta de armazenamento. O exemplo a seguir mostra como criar uma conta de armazenamento e como obter suas chaves de acesso:

1. No portal do Azure selecione **Criar um recurso** na parte superior esquerda da tela.

2. Selecione **Armazenamento** e, em seguida, **Conta de armazenamento – blob, arquivo, tabela, fila**.
   
    ![Selecionar Conta de Armazenamento](./media/event-hubs-create-storage/create-storage1.png)

3. Na página **Criar conta de armazenamento**, execute as etapas a seguir: 

   1. Insira um nome para a conta de armazenamento. 
   2. Escolha uma assinatura do Azure que contém o hub de eventos.
   3. Selecione o grupo de recursos que tem o hub de eventos.
   4. Selecione um local para criar o recurso. 
   5. Em seguida clique em **Revisar + criar**.
   
      ![Criar conta de armazenamento - página](./media/event-hubs-create-storage/create-storage2.png)

4. Na página **Revisar + criar**, revise os valores e selecione **Criar**. 

    ![Analisar configurações e criação de conta de armazenamento](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Depois que você consulte os **implantações bem-sucedidas** mensagem, selecione **ir para o recurso** na parte superior da página. Você também pode iniciar a página da conta de armazenamento selecionando sua conta de armazenamento da lista de recursos.  

    ![Selecione a conta de armazenamento na implantação](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. Na janela **Dados Básicos**, selecione **Blobs**. 

    ![Selecione o serviço de Blobs](./media/event-hubs-create-storage/select-blobs-service.png)
1. Selecione **+ Contêiner** na parte superior, insira um **nome** para o contêiner e selecione **OK**. 

    ![Criar um contêiner de blob](./media/event-hubs-create-storage/create-blob-container.png)
1. Selecione **Chaves de acesso** no menu à esquerda e copie o valor de **key1**. 

    Salve esses valores no Bloco de notas ou em outro local temporário.
    - Nome da conta de armazenamento
    - Chave de acesso da conta de armazenamento
    - Nome do contêiner
