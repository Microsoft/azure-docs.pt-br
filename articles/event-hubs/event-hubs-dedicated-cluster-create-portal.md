---
title: Criar um cluster de Hubs de Eventos Dedicados usando o portal do Azure
description: Neste início rápido, você aprenderá a criar um cluster dos Hubs de Eventos do Azure usando o portal do Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 596b07210d35f648af78e45b7472c620a4361e8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601016"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Início Rápido: Criar um cluster dos Hubs de Eventos dedicados usando o portal do Azure 
Os clusters de Hubs de Eventos oferecem implantações de locatário único para clientes com exigências de streaming mais rígidas. Essa oferta tem um SLA de 99,99% garantido e está disponível apenas em nossa camada de preços Dedicada. Um [cluster de Hubs de Eventos](event-hubs-dedicated-overview.md) pode inserir milhões de eventos por segundo com latência de subsegundos e capacidade garantida. Os namespaces e hubs de eventos criados dentro de um cluster incluem todos os recursos da oferta padrão e muito mais, mas sem limites de entrada. A oferta Dedicada também inclui o famoso recurso [Captura de Hubs de Eventos](event-hubs-capture-overview.md) sem custo adicional permitindo que você automaticamente agrupe e registre os fluxos de dados no [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Clusters dedicados são provisionados e cobrados por **Unidades de Capacidade (CUs)**, uma quantidade pré-alocada de recursos de CPU e memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 UCS para cada cluster. Neste início rápido, orientaremos você na criação de um cluster de Hubs de Eventos de uma CU por meio do portal do Azure.

> [!NOTE]
> Atualmente, essa experiência de autoatendimento está disponível em versão prévia no [Portal do Azure](https://aka.ms/eventhubsclusterquickstart). Se você tiver alguma dúvida sobre a oferta Dedicada, entre em contato com o [equipe de Hubs de Eventos](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, verifique se você tem:

- Uma conta do Azure. Se você não tiver, [compre uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar. Esse recurso não tem suporte com uma conta gratuita do Azure. 
- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](https://visualstudio.microsoft.com/vs/) ou posterior.
- [SDK do .NET Standard](https://dotnet.microsoft.com/download), versão 2.0 ou posterior.
- [Criado um grupo de recursos](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Criar um Cluster Dedicado de Hubs de Eventos
Um cluster de Hubs de Eventos fornece um contêiner de escopo exclusivo em que você pode criar um ou mais namespaces. Na fase de Versão prévia da experiência de autoatendimento do portal, você pode criar clusters de uma CU em regiões escolhidas. Se você precisar de um cluster maior do que uma CU, envie uma solicitação de suporte do Azure para dimensionar seu cluster após a criação.

> [!IMPORTANT]
> Você não poderá excluir o cluster por pelo menos 4 horas depois de criá-lo. Portanto, você será cobrado por, no mínimo, 4 horas de uso do cluster. Para obter mais informações sobre preços, confira [Hubs de Eventos – Preços](https://azure.microsoft.com/pricing/details/event-hubs/). 

Para criar um cluster em seu grupo de recursos usando o portal do Azure, conclua as seguintes etapas:

1. Siga [esse link](https://aka.ms/eventhubsclusterquickstart) para criar um cluster usando o portal do Azure. Por outro lado, escolha **Todos os serviços** no painel de navegação esquerdo e digite "Clusters de Hubs de Eventos" na barra de pesquisa e escolha "Clusters de Hubs de Eventos" na lista de resultados.
2. Na página **Criar Cluster**, defina as seguintes configurações:
    1. Insira um **nome para o cluster**. O sistema imediatamente verifica para ver se o nome está disponível.
    2. Selecione a **assinatura** na qual você deseja criar o cluster.
    3. Selecione o **grupo de recursos** no qual você deseja criar o cluster.
    4. Selecione um **local** para o cluster. Se sua região preferida estiver esmaecida, ela ficará temporariamente sem capacidade e você pode enviar uma [solicitação de suporte](#submit-a-support-request) para a equipe de Hubs de Eventos.
    5. Selecione o botão **Avançar: Marcas** na parte inferior da página. Talvez você precise aguardar alguns minutos para o sistema provisionar totalmente os recursos.

        ![Criar o Cluster de Hubs de Eventos - página de Noções básicas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na página **Marcas**, configure as seguintes opções:
    1. Insira um **nome** e um **valor** para a marca que você deseja adicionar. Esta etapa é **opcional**.  
    2. Selecione o botão **Revisar + Criar**.

        ![Criar página de Cluster do Hubs de Eventos - página de Categorias](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na página **Revisar + Criar**, revise os valores e selecione **Criar**. 

    ![Criar página do Cluster do Hubs de Eventos - Analisar + Criar página](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Criar um namespace e um hub de eventos dentro de um cluster

1. Para criar um namespace em um cluster, na página **Cluster de Hubs de Eventos** do seu cluster, selecione **+Namespace** no menu superior.

    ![Página de gerenciamento de cluster - adicionar botão de namespace](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na página **Criar um namespace**, executa as seguintes etapas:
    1. Insira um **nome para o namespace**.  O sistema verifica para ver se o nome está disponível.
    2. O namespace herda as propriedades a seguir:
        1. ID da assinatura
        2. Grupo de recursos
        3. Location
        4. Nome do cluster
    3. Selecione **Criar** para criar o namespace. Agora você pode gerenciar seu cluster.  

        ![Criar Namespace na página do cluster](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Depois de criar seu namespace, você pode [criar um hub de eventos](event-hubs-create.md#create-an-event-hub) como você normalmente criaria um dentro de um namespace. 


## <a name="submit-a-support-request"></a>Enviar uma solicitação de suporte

Se quiser alterar o tamanho do cluster após a criação ou se a região preferida não estiver disponível, envie uma solicitação de suporte seguindo estas etapas:

1. No [portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte** no menu à esquerda.
2. Selecione **+Nova solicitação de suporte** no menu de Suporte.
3. Na página de suporte, siga essas etapas:
    1. Na lista suspensa **Tipo de problema**, selecione **Técnico**.
    2. Em **Assinatura**, selecione sua assinatura.
    3. Para **Serviço**, selecione **Meus serviços** e, em seguida, selecione **Hubs de Eventos**.
    4. Para **Recurso**, selecione seu cluster se ele já existir, caso contrário, selecione **Pergunta Geral/Recurso Não Disponível**.
    5. Para **Tipo de problema**, selecione **Cota**.
    6. Para **Subtipo de problema**, selecione um dos seguintes valores na lista suspensa:
        1. Selecione **Solicitação de SKU Dedicado** para solicitar o recurso de suporte em sua região.
        2. Selecione **Solicitação para Escalar Verticalmente Reduzir Verticalmente** se você quiser escalar ou reduzir verticalmente o cluster dedicado. 
    7. Para **Assunto**, descreva o problema.

        ![Página do tíquete de suporte](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Excluir um cluster dedicado
 
1. Para excluir o cluster, selecione **Excluir** no menu superior. 

    > [!IMPORTANT]
    > Você não poderá excluir o cluster por pelo menos 4 horas depois de criá-lo. Portanto, você será cobrado por, no mínimo, 4 horas de uso do cluster. Para obter mais informações sobre preços, confira [Hubs de Eventos – Preços](https://azure.microsoft.com/pricing/details/event-hubs/).     
1. Será exibida uma mensagem confirmando se você deseja excluir o cluster.
1. Digite o **nome do cluster** e selecione **Excluir** para excluir o cluster.

    ![Excluir a página do cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um cluster de Hubs de Eventos. Para obter instruções passo a passo para enviar e receber eventos de um hub de eventos e capturar os eventos para um Armazenamento do Azure ou Azure Data Lake Storage, veja os tutoriais a seguir:

- Enviar e receber eventos 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
- [Usar o portal do Azure para habilitar a Captura dos Hubs de Eventos](event-hubs-capture-enable-through-portal.md)
- [Hubs de Eventos do Azure para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
