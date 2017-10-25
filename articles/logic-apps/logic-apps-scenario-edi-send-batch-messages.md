---
title: "Processar mensagens de EDI em lote como um grupo ou uma coleção – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Enviar mensagens de EDI para processamento em lote em aplicativos lógicos"
keywords: "lote, processamento de lote, codificação em lote"
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 8c794b8c919fe286c3917927a44d64aa84771f28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Envie mensagens X12 em lote para parceiros comerciais

Em cenários de B2B (entre empresas), parceiros geralmente trocam mensagens em grupos ou lotes. Para enviar mensagens em grupos ou lotes para parceiros comerciais, você pode criar um lote com vários itens e, em seguida, usar a ação de lote X12 para processar esses itens como um lote.


Enviar mensagens X12 em lote, como outras mensagens, usa uma ação e um gatilho de lote. Também para X12, o lote passa por uma etapa de Codificação de X12 antes de passar para o parceiro ou qualquer outro destino. Para obter mais informações sobre a ação e o gatilho de lote, consulte [Mensagens do processo em lote](logic-apps-batch-process-send-receive-messages.md).

Este tópico mostra como você pode processar mensagens X12 como um lote executando estas tarefas:
* [Crie um aplicativo lógico que receba itens e crie um lote](#receiver). Este aplicativo lógico "receptor" executa estas ações:
 
   * Especifica os critérios de nome e versão do lote a serem atendidos antes de liberar os itens como um lote.

   * Processa ou codifica os itens no lote usando o contrato de X12 especificado ou identidades de parceiros.

* [Criar um aplicativo lógico que envia os itens a um lote](#sender). Esse aplicativo lógico "remetente" especifica para onde enviar os itens para agrupar em lote, que deve ser um aplicativo lógico receptor existente.


## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure

* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração. Certifique-se de que cada parceiro usa o qualificador X12 (Código alfa de carrier padrão) nas propriedades do parceiro como uma identidade de negócios.

* Pelo menos dois [contratos X12](logic-apps-enterprise-integration-x12.md) que já estão definidos em sua conta de integração

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Criar um aplicativo lógico que receba mensagens X12 e crie um lote

Antes de enviar mensagens a um lote, primeiro, você deve criar um aplicativo lógico "receptor" com o gatilho **Lote**. Dessa forma, você pode selecionar esse aplicativo lógico destinatário do lote ao criar o aplicativo lógico remetente. Para o receptor, especifique o nome do lote, critérios de liberação, contrato X12 e outras configurações. 


1. No [Portal do Azure](https://portal.azure.com), crie um aplicativo lógico com este nome: "BatchX12Messages".

2. No Designer de Aplicativos Lógicos, adicione o gatilho **Lote**, o que inicia seu fluxo de trabalho de aplicativo lógico. Na caixa de pesquisa, digite "lote" como filtro. Selecionar este gatilho: **Lote – Mensagens em lote**

   ![Adicionar o gatilho Lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Forneça um nome para o lote e especifique critérios para liberação do lote, por exemplo:

   * **Nome do Lote**: o nome usado para identificar o lote, que é "LoteTeste" neste exemplo.

   * **Critérios de liberação**: os critérios de liberação de lote, que podem ser baseados na contagem de mensagens, na agenda ou em ambas.
   
     ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Contagem de Mensagens**: o número de mensagens para agrupar como um lote antes de liberar para processamento, neste exemplo é "5".

     ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Agenda**: a agenda de liberação de lote para processamento, que é "a cada 10 minutos" neste exemplo.

     ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Adicione outra ação que codifica as mensagens agrupadas ou em lotes e cria uma mensagem agrupada em lote X12. 

   a. Escolha **+ Nova Etapa** > **Adicionar uma ação**.

   b. Na caixa de pesquisa, digite "lote X12" como seu filtro e selecione uma ação para **X12 – Codificação de lote**. Como o conector X12 Encode, há diversas variações para ação de codificação de lote. Você pode selecionar qualquer uma delas.

   ![Selecione a ação Codificação de lote X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Defina as propriedades para a ação que você acabou de adicionar.

   * Na caixa **Nome do contrato X12**, selecione o contrato na lista suspensa. Se sua lista estiver vazia, certifique-se de que você criou uma conexão com sua conta de integração.

   * Na caixa **BatchName**, selecione o campo **Nome do Lote** na lista de conteúdo dinâmica.
   
   * Na caixa **PartitionName**, selecione o campo **Nome da Partição** na lista de conteúdo dinâmica.

   * Na caixa **Itens**, selecione os **Itens em Lote** na lista de conteúdo dinâmica.

   ![Detalhes da ação de Codificação em lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Para fins de teste, adicione uma ação HTTP para enviar a mensagem em lote para o [serviço Compartimento de solicitação](https://requestbin.fullcontact.com/). 

   1. Na caixa de pesquisa, insira "HTTP" como seu filtro. Selecione esta ação: **HTTP – HTTP**
    
      ![Selecionar a ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Na lista **Método**, selecione **POST**. Para a caixa **Uri**, gere um URI para seu compartimento de solicitação e digite esse URI. Na caixa **Corpo**, quando a lista dinâmica se abrir, selecione o campo **Corpo** sob a seção **Codificação em lote por nome do contrato**. Se você não vir **Corpo**, escolha **Ver mais** ao lado de **Codificação em lote por nome do contrato**.

      ![Forneça detalhes da ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Agora que você criou um aplicativo lógico receptor, salve-o.

    ![Salve seu aplicativo lógico](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Criar um aplicativo lógico que envia mensagens X12 a um lote

Agora, crie um ou mais aplicativos lógicos que enviam itens para o lote definido pelo aplicativo lógico destinatário. Para o remetente, especifique o aplicativo lógico destinatário e o nome do lote, o conteúdo da mensagem e outras configurações. Como opção, você pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos a fim de coletar itens com essa chave.

Os aplicativos lógicos remetentes precisam saber para onde enviar os itens, enquanto os aplicativos lógicos receptores não precisam saber nada sobre os remetentes.


1. Crie outro aplicativo lógico com este nome: "X12MessageSender". Adicione esse gatilho ao seu aplicativo lógico: **Solicitação / Resposta – Solicitação** 
   
   ![Adicionar o gatilho de Solicitação](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Adicione uma nova etapa para enviar mensagens a um lote.

   1. Escolha **+ Nova Etapa** > **Adicionar uma ação**.

   2. Na caixa de pesquisa, digite "lote" como filtro. 

3. Selecione esta ação: **Enviar mensagens para o lote – Escolha um fluxo de trabalho de Aplicativos Lógicos com o gatilho lote**

   ![Selecione "Enviar mensagens para o lote"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Agora, selecione seu aplicativo lógico "BatchX12Messages" que você criou anteriormente e que agora aparece como uma ação.

   ![Selecione o aplicativo lógico "destinatário do lote"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > A lista também mostra todos os outros aplicativos lógicos que tenham um gatilho lote.

5. Defina as propriedades do lote.

   * **Nome do Lote**: o nome de lote definido pelo aplicativo lógico destinatário, que é "LoteTeste" neste exemplo, e é validado no tempo de execução.

     > [!IMPORTANT]
     > Não altere o nome do lote, que deve corresponder ao nome de lote especificado pelo aplicativo lógico destinatário.
     > A alteração do nome do lote faz com que o aplicativo lógico remetente falhe.

   * **Conteúdo da Mensagem**: o conteúdo da mensagem que você quer enviar para o lote
   
   ![Definir propriedades do lote](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Salve seu aplicativo lógico. Seu aplicativo lógico remetente agora parece com este exemplo:

   ![Salve seu aplicativo lógico remetente](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar sua solução de envio em lote, poste mensagens X12 em seu aplicativo lógico remetente do [Postman](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. Logo, você deve começar a receber mensagens X12, tanto como um lote de cinco itens quanto a cada 10 minutos, em seu compartimento de solicitação – todos com a mesma chave de partição.

## <a name="next-steps"></a>Próximas etapas

* [Processar mensagens como lotes](logic-apps-batch-process-send-receive-messages.md) 
* [Compilar um aplicativo sem servidor no Visual Studio com o Aplicativo Lógico do Azure e o Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceção e registro em log de erros para aplicativos lógicos](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
