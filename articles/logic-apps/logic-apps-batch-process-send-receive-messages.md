---
title: "Processar mensagens em lote como um grupo ou uma coleção – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Enviar e receber mensagens para processamento em lotes em aplicativos lógicos"
keywords: lote, processo em lote
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: c58cf6485a8a7248e5b8ffce0d279485a80f36ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Enviar, receber e processar mensagens em lote nos aplicativos lógicos

Para processar mensagens em grupos, você pode enviar itens de dados, ou mensagens, para um *lote* e depois processar esses itens como um lote. Essa abordagem é útil quando você deseja certificar-se de que os itens de dados estão agrupados de forma específica e são processados em conjunto. 

Você pode criar aplicativos lógicos que recebem itens como um lote usando o gatilho **Lote**. Depois, você pode criar aplicativos lógicos que enviam itens a um lote usando a ação **Lote**.

Este tópico mostra como você pode compilar uma solução de envio em lote executando estas tarefas: 

* [Criar um aplicativo lógico que receba e colete itens como um lote](#batch-receiver). Este aplicativo lógico "destinatário do lote" especifica os critérios de nome e de liberação do lote que devem ser atendidos antes de o aplicativo lógico receptor liberar e processar os itens. 

* [Criar um aplicativo lógico que envia os itens a um lote](#batch-sender). Esse aplicativo lógico "remetente em lote" especifica para onde enviar os itens, que deve ser um aplicativo lógico destinatário do lote existente. Também é possível especificar uma chave exclusiva, como um número de cliente, para "particionar", ou dividir, o lote de destino em subconjuntos baseados nessa chave. Dessa forma, todos os itens com essa chave serão coletados e processados em conjunto. 

## <a name="requirements"></a>Requisitos

Para seguir este exemplo, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/logic-apps-create-a-logic-app.md) 

* Uma conta de email com qualquer [provedor de email com suporte do Aplicativo Lógico do Azure](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Criar aplicativos lógicos que recebem mensagens como um lote

Antes de enviar mensagens a um lote, primeiro você deve criar um aplicativo de lógica "destinatário do lote" com o gatilho **Lote**. Dessa forma, você pode selecionar esse aplicativo lógico destinatário do lote ao criar o aplicativo lógico remetente. Para o destinatário, você pode especificar o nome do lote, critérios de liberação e outras configurações. 

Os aplicativos lógicos remetentes precisam saber para onde enviar os itens, enquanto os aplicativos lógicos destinatários não precisam saber nada sobre os remetentes.

1. No [Portal do Azure](https://portal.azure.com), crie um aplicativo lógico com este nome: "DestinatárioLote" 

2. No Designer de Aplicativos Lógicos, adicione o gatilho **Lote**, o que inicia seu fluxo de trabalho de aplicativo lógico. Na caixa de pesquisa, digite "lote" como filtro. Selecionar este gatilho: **Lote – Mensagens em lote**

   ![Adicionar o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Forneça um nome para o lote e especifique critérios para liberação do lote, por exemplo:

   * **Nome do Lote**: o nome usado para identificar o lote, que é "LoteTeste" neste exemplo.
   * **Critérios de liberação**: os critérios de liberação de lote, que podem ser baseados na contagem de mensagens, na agenda ou em ambas.
   
     ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Contagem de Mensagens**: o número de mensagens para agrupar como um lote antes de liberar para processamento, neste exemplo é "5".

     ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Agenda**: a agenda de liberação de lote para processamento, que é "a cada 5 minutos" neste exemplo.

     ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Adicione outra ação que envia um email quando o gatilho lote é acionado. Cada vez que o lote acumular cinco itens ou passar de 5 minutos, o aplicativo lógico enviará um email.

   1. No gatilho de lote, escolha **+ Nova etapa** > **Adicionar uma ação**.

   2. Na caixa de pesquisa, insira "email" como filtro.
   Com base em seu provedor de email, selecione um conector de email.
   
      Por exemplo, se você tiver uma conta corporativa ou de estudante, selecione o conector Office 365 Outlook. 
      Se você tiver uma conta do Gmail, selecione o conector Gmail.

   3. Selecione essa ação para o conector:  **{*provedor de email*} – Enviar um email**

      Por exemplo:

      ![Selecione a ação "Enviar um email" para o seu provedor de email](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Se você não tiver criado uma conexão para o seu provedor de email, forneça suas credenciais de email para autenticação quando receber a solicitação. Saiba mais sobre [como autenticar suas credenciais de email](../logic-apps/logic-apps-create-a-logic-app.md).

6. Defina as propriedades para a ação que você acabou de adicionar.

   * Na caixa **Para**, insira o endereço de email do destinatário. 
   Para fins de teste, você pode usar seu próprio endereço de email.

   * Na caixa **Assunto**, quando a lista **Conteúdo dinâmico** for exibida, selecione o campo **Nome da Partição**.

     ![Na lista "Conteúdo dinâmico", selecione "Nome da Partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Em uma seção posterior, você pode especificar uma chave de partição exclusiva que divide o lote de destino em conjuntos lógicos para onde você pode enviar mensagens. 
     Cada conjunto tem um número exclusivo gerado pelo aplicativo lógico remetente. 
     Esse recurso permite o uso de um único lote com várias subconjuntos e define cada subconjunto com o nome fornecido por você.

   * Na caixa **Corpo**, quando a lista **Conteúdo dinâmico** for exibida, selecione o campo **ID da Mensagem**.

     ![Para "Corpo", selecione "Id de Mensagem"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Como a entrada para a ação de envio de email é uma matriz, o designer adiciona automaticamente um loop **Para cada** na ação **Enviar um email**. 
     Esse loop executa a ação interna em cada item do lote. 
     Portanto, com o gatilho lote definido como cinco itens, você recebe cinco emails sempre que o gatilho é acionado.

7.  Agora que você criou um aplicativo lógico destinatário do lote, salve-o.

    ![Salve seu aplicativo lógico](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Criar aplicativos lógicos que enviam mensagens a um lote

Agora, crie um ou mais aplicativos lógicos que enviam itens para o lote definido pelo aplicativo lógico destinatário. Para o remetente, especifique o aplicativo lógico destinatário e o nome do lote, o conteúdo da mensagem e outras configurações. Como opção, você pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos a fim de coletar itens com essa chave.

Os aplicativos lógicos remetentes precisam saber para onde enviar os itens, enquanto os aplicativos lógicos destinatários não precisam saber nada sobre os remetentes.

1. Crie outro aplicativo lógico com este nome: "RemetenteLote"

   1. Na caixa de pesquisa, insira "recorrência" como filtro. 
   Selecione este gatilho: **Agenda – Recorrência**

      ![Adicione o gatilho "Agenda-Recorrência"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Defina a frequência e o intervalo de execução do aplicativo lógico remetente como a cada minuto.

      ![Defina a frequência e o intervalo do gatilho de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Adicione uma nova etapa para enviar mensagens a um lote.

   1. No gatilho de recorrência, escolha **+ Nova Etapa** > **Adicionar uma ação**.

   2. Na caixa de pesquisa, digite "lote" como filtro. 

   3. Selecione esta ação: **Enviar mensagens para o lote – Escolha um fluxo de trabalho de Aplicativos Lógicos com o gatilho lote**

      ![Selecione "Enviar mensagens para o lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Agora, selecione seu aplicativo lógico "DestinatárioLote" que você criou anteriormente, e que agora aparece como uma ação.

      ![Selecione o aplicativo lógico "destinatário do lote"](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > A lista também mostra todos os outros aplicativos lógicos que tenham um gatilho lote.

3. Defina as propriedades do lote.

   * **Nome do Lote**: o nome de lote definido pelo aplicativo lógico destinatário, que é "LoteTeste" neste exemplo, e é validado no tempo de execução.

     > [!IMPORTANT]
     > Não altere o nome do lote, que deve corresponder ao nome de lote especificado pelo aplicativo lógico destinatário.
     > A alteração do nome do lote faz com que o aplicativo lógico remetente falhe.

   * **Conteúdo da Mensagem**: o conteúdo da mensagem que você quer enviar. 
   Para este exemplo, adicione esta expressão que insere a data e hora atuais no conteúdo da mensagem que você envia para o lote:

     1. Quando a lista **Conteúdo dinâmico** for exibida, escolha **Expressão**. 
     2. Insira a expressão **utcnow()** e escolha **OK**. 

        ![Em "Conteúdo da Mensagem", escolha "Expressão". Insira "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Agora, configure uma partição para o lote. Na ação "DestinatárioLote", escolha **Mostrar opções avançadas**.

   * **Nome da Partição**: uma chave de partição exclusiva e opcional a ser usada para dividir o lote de destino. Para este exemplo, adicione uma expressão que gera um número aleatório entre um e cinco.
   
     1. Quando a lista **Conteúdo dinâmico** for exibida, escolha **Expressão**.
     2. Insira esta expressão: **rand(1,6)**

        ![Configurar uma partição para seu lote de destino](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Essa função **rand** gera um número entre um e cinco. 
        Portanto, você está dividindo esse lote em cinco partições numeradas, definidas dinamicamente por esta expressão.

   * **Id da Mensagem**: um identificador de mensagem opcional, e um GUID gerado quando estiver vazio. 
   Neste exemplo, deixe essa caixa em branco.

5. Salve seu aplicativo lógico. Seu aplicativo lógico remetente agora parece com este exemplo:

   ![Salve seu aplicativo lógico remetente](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar sua solução de envio em lote, deixe seus aplicativos lógicos em execução por alguns minutos. Em breve, você começará a receber emails em grupos de cinco, todos com a mesma chave de partição.

Seu aplicativo lógico RemetenteLote é executado a cada minuto, gera um número aleatório entre um e cinco e usa esse número gerado como a chave de partição para o lote de destino no qual as mensagens são enviadas. Cada vez que o lote tiver cinco itens com a mesma chave de partição, seu aplicativo lógico DestinatárioLote será acionado e enviará um email para cada mensagem.

> [!IMPORTANT]
> Quando você terminar o teste, desabilite o aplicativo lógico RemetenteLote para interromper o envio de mensagens e evitar a sobrecarga de sua caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

* [Compilar definições de aplicativo lógico usando JSON](../logic-apps/logic-apps-author-definitions.md)
* [Compilar um aplicativo sem servidor no Visual Studio com o Aplicativo Lógico do Azure e o Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceção e registro em log de erros para aplicativos lógicos](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
