---
title: Início Rápido do Azure – Criar um hub de eventos usando o portal do Azure | Microsoft Docs
description: Neste início rápido, você aprenderá a criar um hub de eventos do Azure usando o portal do Azure e, em seguida, enviar e receber eventos usando o SDK do .NET Standard.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/23/2019
ms.author: shvija
ms.openlocfilehash: 15b2b5863f5b78bd24ffd8cbd55b9914f52e32a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072804"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início Rápido: Criar um hub de eventos usando o portal do Azure
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Neste início rápido, você cria um hub de eventos usando o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, verifique se você tem:

- Assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- [Visual Studio 2019)](https://www.visualstudio.com/vs) ou posterior.
- [SDK do .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica dos recursos do Azure. Todos os recursos são implantados e gerenciados em um grupo de recursos. Para criar um grupo de recursos:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Grupos de recursos**. Clique em **Adicionar**.

   ![Grupos de recursos - botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Para **Assinatura**, selecione o nome da assinatura do Azure na qual você deseja criar o grupo de recursos.
3. Digite um **nome exclusivo para o grupo de recursos**. O sistema imediatamente verifica para ver se o nome está disponível na assinatura do Azure selecionada no momento.
4. Selecione uma **região** para o grupo de recursos.
5. Selecione **Examinar + criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na página **Examinar + Criar**, selecione **Criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Um namespace de Hubs de Eventos fornece um contêiner de escopo exclusivo, referenciado pelo nome de domínio totalmente qualificado, em que você cria uma ou mais hubs de eventos. Para criar um namespace em seu grupo de recursos usando o portal, faça o seguinte:

1. Faça logon no portal do Azure e clique em **Criar um recurso** na parte superior esquerda da tela.
2. Selecione **Todos os serviços** no menu à esquerda e selecione **estrela (`*`)** ao lado de **Hubs de Eventos** na categoria **Análise**. Confirme que **Hubs de Eventos** foi adicionado à **FAVORITOS** no menu de navegação à esquerda. 
    
   ![Pesquisar Hubs de Eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecione **Hubs de Eventos** em **FAVORITOS** no menu de navegação à esquerda e selecione **Adicionar** na barra de ferramentas.

   ![Botão Adicionar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na página **Criar namespace**, execute as seguintes etapas:
    1. Insira um nome para o namespace. O sistema imediatamente verifica para ver se o nome está disponível.
    2. Escolha o tipo de preço (Básico ou Standard).
    3. Selecione a **assinatura** na qual você deseja criar o namespace.
    4. Selecione uma **localização** para o namespace.
    5. Selecione **Criar**. Talvez você precise aguardar alguns minutos para o sistema provisionar totalmente os recursos.

       ![Criar um namespace do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Atualize a página **Hubs de Eventos** para ver o namespace do hub de eventos. Você pode verificar o status da criação do hub de eventos nos alertas. 

    ![Criar um namespace do hub de eventos](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Selecione o namespace. Você verá a home page do seu **Namespace de Hubs de Eventos** no portal. 

   ![Home page do namespace](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Para criar um hub de eventos dentro do namespace, faça o seguinte:

1. Na página Namespace de Hubs de Eventos, selecione **Hubs de Eventos** no menu à esquerda.
1. Na parte superior da janela, clique em **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Digite um nome para seu hub de eventos e clique em **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Você pode verificar o status da criação do hub de eventos em alertas. Depois que o hub de eventos for criado, você o verá na lista de Hubs de Eventos conforme mostrado na imagem a seguir:

    ![Hub de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)

Parabéns! Você usou o portal para criar um namespace de Hubs de eventos e um hub de eventos dentro desse namespace. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um grupo de recursos, um namespace de Hubs de Eventos e um hub de eventos. Para obter instruções passo a passo sobre como enviar eventos (ou) receber eventos de um hub de eventos, confira os tutoriais para **Enviar e receber eventos**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente receber)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
