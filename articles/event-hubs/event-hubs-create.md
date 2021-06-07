---
title: Início Rápido do Azure – Criar um hub de eventos usando o portal do Azure
description: Neste início rápido, você aprenderá a criar um hub de eventos do Azure usando o portal do Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bf83c53792c4934f43e0039d092bb3e36954bf7b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303110"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início Rápido: Criar um hub de eventos usando o portal do Azure
O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Neste início rápido, você cria um hub de eventos usando o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, verifique se você tem:

- Assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica dos recursos do Azure. Todos os recursos são implantados e gerenciados em um grupo de recursos. Para criar um grupo de recursos:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No painel de navegação esquerdo, selecione **Grupos de recursos**. Em seguida, selecione **Adicionar**.

   ![Grupos de recursos - botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. Para **Assinatura**, selecione o nome da assinatura do Azure na qual você deseja criar o grupo de recursos.
1. Digite um **nome exclusivo para o grupo de recursos**. O sistema imediatamente verifica para ver se o nome está disponível na assinatura do Azure selecionada no momento.
1. Selecione uma **região** para o grupo de recursos.
1. Selecione **Examinar + criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. Na página **Examinar + Criar**, selecione **Criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Um namespace dos Hubs de Eventos fornece um contêiner de escopo exclusivo, em que você cria um ou mais Hubs de Eventos. Para criar um namespace em seu grupo de recursos usando o portal, faça o seguinte:

1. No portal do Azure selecione **Criar um recurso** na parte superior esquerda da tela.
1. Selecione **Todos os serviços** no menu à esquerda e selecione **estrela (`*`)** ao lado de **Hubs de Eventos** na categoria **Análise**. Confirme que **Hubs de Eventos** foi adicionado à **FAVORITOS** no menu de navegação à esquerda. 
    
   ![Pesquisar Hubs de Eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. Selecione **Hubs de Eventos** em **FAVORITOS** no menu de navegação à esquerda e selecione **Adicionar** na barra de ferramentas.

   ![Botão Adicionar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. Na página **Criar namespace**, execute as seguintes etapas:  
   1. Selecione a **assinatura** na qual você deseja criar o namespace.  
   1. Selecione o **grupo de recursos** criado na etapa anterior.   
   1. Insira um **nome** para o namespace. O sistema imediatamente verifica para ver se o nome está disponível.  
   1. Selecione uma **localização** para o namespace.
   1. Escolha o **tipo de preço** (Básico ou Standard). Para saber mais sobre algumas diferenças entre as camadas básica e padrão, confira [Preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/), [Diferenças entre camadas](event-hubs-faq.yml#what-is-the-difference-between-event-hubs-basic-and-standard-tiers-) e [Cotas e limites](event-hubs-quotas.md). 
   1. Não altere as configurações das **unidades de produtividade**. As unidades de taxa de transferência são unidades de capacidade pré-adquiridas. Para saber mais sobre as unidades de produtividade, confira [Escalabilidade de Hubs de Eventos](event-hubs-scalability.md#throughput-units).  
   1. Selecione **Examinar + Criar** na parte inferior da página.
      
      ![Criar um namespace do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. Na página **Examinar + Criar**, examine as configurações e selecione **Criar**. Aguarde até que a implantação seja concluída. 
      
      ![Página Examinar + criar](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. Na página **Implantação**, selecione **Ir para o recurso** para navegar até a página de seu namespace. 
      
      ![Implantação concluída – Ir para o recurso](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Confirme que você vê a página **Namespace dos Hubs de Eventos** semelhante ao seguinte exemplo:   
      
      ![Home page do namespace](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Hubs de Eventos do Azure fornecem um ponto de extremidade do Kafka. Esse ponto de extremidade permite que seu namespace de Hubs de eventos entenda nativamente a mensagem de protocolo [Kafka Apache](https://kafka.apache.org/intro) e as APIs. Com essa funcionalidade, você pode se comunicar com seus Hubs de eventos, como você faria com tópicos Kafka sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Hubs de Eventos dão suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html) e posterior. Para obter mais informações, confira [Usar os Hubs de Eventos de aplicativos Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Para criar um hub de eventos dentro do namespace, faça o seguinte:

1. Na página Namespace de Hubs de Eventos, selecione **Hubs de Eventos** no menu à esquerda.
1. Na parte superior da janela, selecione **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Digite um nome para seu hub de eventos e selecione **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    A configuração **contagem de partição** permite que você paralelize o consumo entre muitos consumidores. Para saber mais, confira [Partições](event-hubs-scalability.md#partitions).

    A configuração de **retenção de mensagem** especifica por quanto tempo o serviço dos Hubs de Eventos mantém os dados. Para obter mais informações, confira [Retenção de eventos](event-hubs-features.md#event-retention).
1. Você pode verificar o status da criação do hub de eventos em alertas. Depois que o hub de eventos for criado, você o verá na lista de Hubs de Eventos.

    ![Hub de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um grupo de recursos, um namespace de Hubs de Eventos e um hub de eventos. Para obter instruções passo a passo sobre como enviar eventos (ou) receber eventos de um hub de eventos, confira estes tutoriais: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente receber)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
