---
title: "Adicionar o conector Outlook do Office 365 em seus Aplicativos Lógicos | Microsoft Docs"
description: "Crie aplicativos lógicos com o conector do Office 365 para permitir a interação com o Office 365. Por exemplo: criação, edição e atualização de itens de calendário e contatos."
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 5335dae62e61659b68e8befb4ed0d404dffb800c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Introdução ao conector do Outlook do Office 365
O conector do Outlook do Office 365 permite interação com o Outlook no Office 365. Use esse conector para criar, editar e atualizar contatos e itens de calendário, bem como para obter, enviar e responder a emails.

Com o Outlook do Office 365, você pode:

* Compilar o fluxo de trabalho usando os recursos de email e calendário no Office 365. 
* Usar gatilhos para iniciar o fluxo de trabalho quando houver um novo email, quando um item de calendário for atualizado e muito mais.
* Usar ações que enviem um email, criem um novo evento de calendário e muito mais. Por exemplo, quando houver um novo objeto em Salesforce (um gatilho), envie um email ao seu Outlook do Office 365 (uma ação). 

Este tópico mostra como usar o conector do Outlook do Office 365 em um aplicativo lógico, além de listar os gatilhos e as ações.

> [!NOTE]
> Esta versão do artigo se aplica à disponibilidade de Aplicativos Lógicos em geral (GA).
> 
> 

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) e [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Conectar ao Office 365
Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao Outlook do Office 365, primeiramente é necessária uma *conexão* do Office 365. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual deseja conectar-se. Assim, com o Outlook do Office 365, insira as credenciais de sua conta do Office 365 para criar a conexão.

## <a name="create-the-connection"></a>Criar a conexão
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Usar um gatilho
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Gatilhos "sondam" o serviço no intervalo e na frequência desejados. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. No aplicativo lógico, digite "office 365" para obter uma lista de gatilhos:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Escolha **Outlook do Office 365 – quando um evento futuro for iniciado em breve**. Se já existir uma conexão, escolha um calendário na lista suspensa.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Se você for solicitado a entrar, insira os detalhes do logon para criar a conexão. [Criar a conexão](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico lista as etapas. 
   
   > [!NOTE]
   > Neste exemplo, o aplicativo lógico é executado quando um evento de calendário é atualizado. Para ver os resultados desse gatilho, adicione outra ação que envie uma mensagem de texto para você. Por exemplo, adicione a ação *Enviar mensagem* do Twilio que avisa você, por texto, quando o evento de calendário estiver iniciando em 15 minutos. 
   > 
   > 
3. Selecione o botão **Editar** e defina os valores **Frequência** e **Intervalo**. Por exemplo, se você quiser que o gatilho faça uma sondagem a cada 15 minutos, defina a **Frequência** como **Minuto** e **Intervalo** como **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="use-an-action"></a>Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **adicionar uma ação**, **adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "office 365" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Em nosso exemplo, escolha **Outlook do Office 365 – Criar contato**. Se já existir uma conexão, escolha **ID da Pasta**, **Nome Fornecido** e outras propriedades:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, criamos um novo contato no Outlook do Office 365. Você pode usar a saída de outro gatilho para criar o contato. Por exemplo, adicione o gatilho *Quando um objeto é criado* do SalesForce. Em seguida, adicione a ação *Criar contato* do Outlook do Office 365 que usa os campos do SalesForce para criar o novo contato no Office 365. 
   > 
   > 
5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

