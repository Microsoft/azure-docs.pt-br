---
title: Criar, exibir e gerenciar tópicos do sistema na grade de eventos do Azure (Portal)
description: Este artigo mostra como exibir o tópico do sistema existente, criar tópicos do sistema de grade de eventos do Azure usando o portal do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115104"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Criar, exibir e gerenciar tópicos do sistema de grade de eventos no portal do Azure
Este artigo mostra como criar e gerenciar tópicos do sistema usando o portal do Azure. Para obter uma visão geral dos tópicos do sistema, consulte [Tópicos do sistema](system-topics.md).

## <a name="create-a-system-topic"></a>Criar um tópico do sistema
Você pode criar um tópico do sistema para um recurso do Azure (conta de armazenamento, namespace de hubs de eventos, etc.) de duas maneiras:

- Usando a página de **eventos** de um recurso, por exemplo, a conta de armazenamento ou o namespace de hubs de eventos. Quando você usa a página **eventos** no portal do Azure para criar uma assinatura de evento para um evento gerado por uma fonte do Azure (por exemplo: conta de armazenamento do Azure), o portal cria um tópico do sistema para o recurso do Azure e, em seguida, cria uma assinatura para o tópico do sistema. Você especifica o nome do tópico do sistema se estiver criando uma assinatura de evento no recurso do Azure pela primeira vez. Da segunda vez em diante, o nome do tópico do sistema é exibido para você no modo somente leitura. Consulte [início rápido: rotear eventos de armazenamento de BLOBs para o ponto de extremidade da Web com o portal do Azure](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) para obter etapas detalhadas.
- Usando a página de **Tópicos do sistema de grade de eventos** . As etapas a seguir são para a criação de um tópico do sistema usando a página **Tópicos do sistema de grade de eventos** . 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na caixa de pesquisa na parte superior, digite **Tópicos do sistema de grade de eventos**e pressione **Enter**. 

    ![Pesquisar tópicos do sistema](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na página **Tópicos do sistema de grade de eventos** , selecione **+ Adicionar** na barra de ferramentas.

    ![Adicionar tópico do sistema-botão da barra de ferramentas](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Na página **Criar tópico do sistema de grade de eventos** , execute as seguintes etapas:
    1. Selecione o **tipo de tópico**. No exemplo a seguir, a opção **contas de armazenamento** está selecionada. 
    2. Selecione a **assinatura do Azure** que tem o recurso de conta de armazenamento. 
    3. Selecione o **grupo de recursos** que tem a conta de armazenamento. 
    4. Selecione a **conta de armazenamento**. 
    5. Insira um **nome** para o tópico do sistema a ser criado. 
    
        > [!NOTE]
        > Você pode usar este nome de tópico do sistema para pesquisar métricas e logs de diagnóstico.
    6. Selecione **Examinar + criar**.

        ![Criar tópico do sistema](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Examine as configurações e selecione **criar**. 
        
        ![Examinar e criar o tópico do sistema](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Depois que a implantação for realizada com sucesso, selecione **ir para o recurso** para ver a página de **tópico do sistema da grade de eventos** do tópico do sistema que você criou. 

        ![Página de tópicos do sistema](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Exibir todos os tópicos do sistema
Siga estas etapas para exibir todos os tópicos existentes do sistema de grade de eventos. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na caixa de pesquisa na parte superior, digite **Tópicos do sistema de grade de eventos**e pressione **Enter**. 

    ![Pesquisar tópicos do sistema](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na página **Tópicos do sistema de grade de eventos** , você verá todos os tópicos do sistema. 

    ![Lista de tópicos do sistema](./media/create-view-manage-system-topics/list-system-topics.png)
4. Selecione um **tópico do sistema** na lista para ver detalhes sobre ele. 

    ![Detalhes do tópico do sistema](./media/create-view-manage-system-topics/system-topic-details.png)

    Esta página mostra detalhes sobre o tópico do sistema, como as seguintes informações: 
    - Fonte. Nome do recurso no qual o tópico do sistema foi criado.
    - Tipo de origem. Tipo do recurso. Por exemplo: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , `Microsoft.Resources.ResourceGroups` e assim por diante.
    - Qualquer assinatura criada para o tópico do sistema.

    Esta página permite operações como as seguintes:
    - Crie uma assinatura de evento selecione **+ assinatura de evento** na barra de ferramentas. 
    - Excluir uma assinatura de evento. Selecione **excluir** na barra de ferramentas. 
    - Adicione marcas ao tópico System. Selecione **marcas** no menu à esquerda e especifique nomes e valores de marca. 


## <a name="delete-a-system-topic"></a>Excluir um tópico do sistema
1. Siga as instruções da seção [Exibir tópicos do sistema](#view-all-system-topics) para exibir todos os tópicos do sistema e selecione o tópico do sistema que você deseja excluir da lista. 
2. Na página do **tópico sistema de grade de eventos** , selecione **excluir** na barra de ferramentas. 

    ![Tópico do sistema – botão excluir](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Na página confirmação, selecione **OK** para confirmar a exclusão. Ele exclui o tópico do sistema e também todas as assinaturas de evento do tópico System.  

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento
1. Siga as instruções da seção [Exibir tópicos do sistema](#view-all-system-topics) para exibir todos os tópicos do sistema e selecione o tópico do sistema que você deseja excluir da lista. 
2. Na página do **tópico sistema de grade de eventos** , selecione **+ assinatura de evento** na barra de ferramentas. 

    ![Tópico do sistema – botão Adicionar assinatura de evento](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Confirme se o **tipo de tópico**, o **recurso de origem**e o **nome do tópico** são preenchidos automaticamente. Insira um nome, selecione um **tipo de ponto de extremidade**e especifique o **ponto de extremidade**. Em seguida, selecione **criar** para criar a assinatura de evento. 

    ![Tópico do sistema – criar assinatura de evento](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Próximas etapas
Consulte a seção [Tópicos do sistema na grade de eventos do Azure](system-topics.md) para saber mais sobre os tópicos do sistema e os tipos de tópico com suporte na grade de eventos do Azure. 
