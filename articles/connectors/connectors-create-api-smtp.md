---
title: "Conector SMTP no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se ao SMTP para enviar email."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 1cf96bbf8bd215d7ddb3c99860a5cb4e668be3c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-smtp-connector"></a>Introdução ao conector do SMTP
Conecte-se ao SMTP para enviar email.

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Conectar-se ao SMTP
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](connectors-overview.md) fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao SMTP, é preciso ter uma *conexão* do SMTP. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual se conecta. Assim, no exemplo de SMTP, insira as credenciais de seu nome de conexão, do endereço do servidor SMTP e das informações de logon do usuário para criar a conexão com o SMTP.  

### <a name="create-a-connection-to-smtp"></a>Criar uma conexão com o SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Usar um gatilho de SMTP
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, como o SMTP não tem seu próprio gatilho, usaremos o gatilho **Salesforce – quando um objeto é criado**. Esse gatilho é ativado quando um novo objeto é criado no Salesforce. Em nosso exemplo, nós o configuraremos de modo que toda vez que um novo cliente potencial for criado no Salesforce, uma ação *enviar email* ocorrerá por meio do conector de SMTP com uma notificação do novo cliente potencial que está sendo criado.

1. Digite *salesforce* na caixa de pesquisa no designer de aplicativos lógicos e escolha o gatilho **Salesforce — Quando um objeto é criado** .  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. O controle **Quando um objeto é criado** é exibido.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Escolha o **Tipo de Objeto** e selecione *Cliente Potencial* na lista de objetos. Nessa etapa, instruímos sobre a criação de um gatilho que notificará seu aplicativo lógico sempre que um novo cliente potencial for criado no Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. O gatilho foi criado.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Usar uma ação de SMTP
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Agora que o gatilho foi adicionado, siga estas etapas para adicionar uma ação de SMTP que ocorrerá quando um novo cliente potencial for criado no Salesforce.

1. Escolha **+ Nova etapa** para adicionar a ação que deseja executar quando um novo cliente potencial é criado.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Escolha **Adicionar uma ação**. Isso abre a caixa de pesquisa, na qual é possível procurar qualquer ação que você deseja realizar.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Digite *smtp* para pesquisar todas as ações relacionadas ao SMTP.  
4. Escolha **SMTP - enviar email** como a ação a ser tomada quando o novo cliente potencial é criado. O bloco de controle de ação é aberto. Você terá que estabelecer a conexão smtp no bloco de designer, caso não tenha feito isso anteriormente.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Insira suas informações de email desejadas no bloco **SMTP – enviar email**.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Salve seu trabalho para ativar o fluxo de trabalho.  

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).