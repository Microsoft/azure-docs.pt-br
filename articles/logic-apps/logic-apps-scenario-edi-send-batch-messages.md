---
title: Processar mensagens de EDI em lote como um grupo ou uma coleção – Aplicativos Lógicos do Azure | Microsoft Docs
description: Enviar mensagens de EDI para processamento em lote em aplicativos lógicos
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: d6d3a7111f3a5e49e32eba8ca4f09d692538cb87
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64715796"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Enviar mensagens de EDI em lotes para parceiros comerciais com os Aplicativos Lógicos do Azure

Em cenários de B2B (entre empresas), os parceiros geralmente trocam mensagens em grupos ou em *lotes*. Ao compilar uma solução de envio em lote com Aplicativos Lógicos, você pode enviar mensagens para parceiros comerciais e processar essas mensagens juntas em lotes. Este artigo mostra como processar mensagens de EDI em lotes, usando X12 como exemplo, criando um aplicativo lógico "remetente do lote" e um aplicativo lógico "receptor do lote". 

O envio de mensagens em lotes do X12 funciona como o envio em lote de outras mensagens, isto é, você usa um gatilho de lote que coleta mensagens em um lote e uma ação em lote que envia mensagens para o lote. Além disso, o envio em lote do X12 inclui uma etapa de codificação do X12 antes que as mensagens sejam enviadas para o parceiro comercial ou outro destino. Para saber mais sobre a ação e o disparador de lote, consulte [Mensagens do processo em lote](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Neste artigo, você compilará uma solução de envio em lote criando dois aplicativos lógicos dentro da mesma assinatura do Azure, região do Azure e seguindo esta ordem específica:

* Um aplicativo lógico ["receptor do lote"](#receiver), que aceita e coleta mensagens em um lote até que seus critérios especificados sejam atendidos para liberar e processar essas mensagens. Nesse cenário, o receptor do lote também codifica as mensagens no lote, usando o contrato do X12 especificado ou as identidades do parceiro.

  Certifique-se primeiro de criar o receptor do lote para, em seguida, selecionar o destino do lote ao criar o remetente do lote.

* Um aplicativo lógico ["remetente do lote"](#sender) que envia as mensagens para o receptor do lote criado anteriormente. 

Certifique-se de que o receptor do lote e o remetente do lote compartilham a mesma assinatura do Azure *e* a região do Azure. Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote porque não estarão visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Ou [inscreva-se para uma assinatura de Pagamento Conforme o Uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure e vinculada a seus aplicativos lógicos

* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) na conta de integração. Cada parceiro deve usar o qualificador do X12 (Código Alfa de Transportadora Padrão) como uma identidade de negócios nas propriedades do parceiro.

* Um [contrato do X12](../logic-apps/logic-apps-enterprise-integration-x12.md) na conta de integração

* Para usar o Visual Studio em vez do portal do Azure, certifique-se de [configurar o Visual Studio para trabalhar com Aplicativos Lógicos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Criar receptor do lote do X12

Antes de enviar mensagens para um lote, esse lote deverá existir primeiro como o destino para o qual essas mensagens serão enviadas. Portanto, primeiro, é necessário criar o aplicativo lógico "receptor do lote" que inicia com o gatilho do **Lote**. Dessa forma, ao criar o aplicativo lógico "remetente do lote", será possível selecionar o aplicativo lógico do receptor do lote. O receptor do lote continuará coletando mensagens até que seus critérios especificados sejam atendidos para liberar e processar essas mensagens. Embora os receptores dos lotes não precisem saber nada sobre os remetentes dos lotes, os remetentes dos lotes deverão saber o destino para onde enviarão as mensagens. 

Para esse receptor do lote, você especifica o modo de lote, nome, critérios de liberação, contrato do X12 e outras configurações. 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico com este nome: "BatchX12Messages"

2. [Vincule o aplicativo lógico à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. No Designer de Aplicativos Lógicos, adicione o gatilho **Lote**, o que inicia seu fluxo de trabalho de aplicativo lógico. Na caixa de pesquisa, digite "lote" como filtro. Selecione este gatilho: **Mensagens em lote**

   ![Adicionar o gatilho Lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Defina propriedades de receptor do lote: 

   | Propriedade | Value | Observações | 
   |----------|-------|-------|
   | **Modo de lote** | Embutido |  |  
   | **Nome do lote** | TestBatch | Disponível apenas com o modo de lote **Embutido** | 
   | **Critérios de liberação** | Baseado em contagem de mensagens, Baseado no agendamento | Disponível apenas com o modo de lote **Embutido** | 
   | **Contagem de mensagens** | 10 | Disponível apenas com critérios de liberação **Baseados em contagem de mensagens** | 
   | **Intervalo** | 10 | Disponível apenas com critérios de liberação **Baseados no agendamento** | 
   | **Frequência** | minuto | Disponível apenas com critérios de liberação **Baseados no agendamento** | 
   ||| 

   ![Fornecer detalhes do gatilho de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Este exemplo não configura uma partição para o lote, portanto, cada lote usa a mesma chave de partição. Para saber mais sobre partições, consulte [Mensagens do processo em lote](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Agora, adicione uma ação que codifique cada lote: 

   1. No gatilho de lote, escolha **Nova etapa**.

   2. Na caixa de pesquisa, digite "Lote X12 X" como filtro e selecione a ação (qualquer versão): **Codificar em lote <*versão*>-X12** 

      ![Selecione a ação Codificação de lote X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Caso não tenha conectado a conta de integração anteriormente, crie a conexão agora. Forneça um nome para a conexão, selecione a conta de integração desejada e, em seguida, escolha **Criar**.

      ![Criar conexão entre o codificador de lote e a conta de integração](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Configure essas propriedades para a ação do codificador de lote:

      | Propriedade | DESCRIÇÃO |
      |----------|-------------|
      | **Nome do contrato do X12** | Abra a lista e selecione o contrato existente. <p>Se a lista estiver vazia, certifique-se de [vincular o aplicativo lógico à conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) que tiver o contrato desejado. | 
      | **BatchName** | Clique dentro dessa caixa e, após a exibição da lista de conteúdo dinâmico, selecione o token do **Nome do Lote**. | 
      | **PartitionName** | Clique dentro dessa caixa e, após a exibição da lista de conteúdo dinâmico, selecione o token do **Nome da Partição**. | 
      | **Itens** | Feche a caixa de detalhes do item e clique dentro dessa caixa. Depois que a lista de conteúdo dinâmico for exibida, selecione o token de **Itens em Lote**. | 
      ||| 

      ![Detalhes da ação Codificar em Lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Para a caixa **Itens**:

      ![Itens da ação Codificar em Lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Salve seu aplicativo lógico. 

7. Se estiver usando o Visual Studio, certifique-se de [implantar o aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para certificar-se de que o receptor do lote funciona conforme o esperado, você pode adicionar uma ação HTTP para fins de teste e enviar uma mensagem em lote para o [serviço de Compartimento de Solicitação](https://requestbin.fullcontact.com/). 

1. Na ação de codificação do X12, escolha **Nova etapa**. 

2. Na caixa de pesquisa, insira "http" como o filtro. Selecione esta ação: **HTTP - HTTP**
    
   ![Selecionar a ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Configure as propriedades para a ação HTTP:

   | Propriedade | DESCRIÇÃO | 
   |----------|-------------|
   | **Método** | Nessa lista, selecione **POST**. | 
   | **Uri** | Gere um URI para o compartimento de solicitação e, em seguida, insira esse URI nessa caixa. | 
   | **Corpo** | Clique dentro dessa caixa e, depois que a lista de conteúdo dinâmico for exibida, selecione o token do **Corpo**, que aparece na seção, **Codificar em lote por nome de contrato**. <p>Se você não visualizar o token do **Corpo**, próximo a **Codificar em lote por nome de contrato** , selecione **Ver mais**. | 
   ||| 

   ![Forneça detalhes da ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Salve seu aplicativo lógico. 

   O aplicativo lógico do receptor do lote é semelhante a este exemplo: 

   ![Salvar o aplicativo lógico do receptor do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Criar remetente do lote do X12

Agora crie um ou mais aplicativos lógicos que enviem mensagens ao aplicativo lógico do receptor do lote. Em cada remetente do lote, você especifica o aplicativo lógico do receptor do lote e o nome do lote, o conteúdo da mensagem e quaisquer outras configurações. Opcionalmente, é possível fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos para coletar mensagens com essa chave. 

* Certifique-se de que você já [criou o receptor do lote](#receiver), assim, ao criar o remetente do lote, será possível selecionar o receptor do lote existente como o lote de destino. Embora os receptores dos lotes não precisem saber nada sobre os remetentes dos lotes, os remetentes dos lotes deverão saber para onde enviarão as mensagens. 

* Certifique-se de que receptor do lote e o remetente do lote compartilham a mesma região do Azure *e* a assinatura do Azure. Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote porque não estarão visíveis um para o outro.

1. Crie outro aplicativo lógico com este nome: "SendX12MessagesToBatch" 

2. Na caixa de pesquisa, insira "quando uma solicitação http" como o filtro. Selecione este gatilho: **Quando uma solicitação HTTP é recebida** 
   
   ![Adicionar o gatilho de Solicitação](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Adicione uma ação para enviar mensagens para um lote.

   1. Na ação de solicitação HTTP, escolha **Nova etapa**.

   2. Na caixa de pesquisa, digite "lote" como filtro. 
   Selecione a lista de **Ações** e depois esta ação: **Escolher um fluxo de trabalho de Aplicativos Lógicos com o gatilho de lote – Enviar mensagens para o lote**

      ![Selecione "Escolher um fluxo de trabalho de Aplicativos Lógicos com gatilho do lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Agora, selecione o aplicativo lógico "BatchX12Messages" que você criou anteriormente.

      ![Selecione o aplicativo lógico "destinatário do lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecione esta ação: Selecione esta ação **Batch_messages – <*seu_remetente_de_lote*>**

      ![Selecionar a ação "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Defina as propriedades do remetente do lote.

   | Propriedade | DESCRIÇÃO | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pelo aplicativo lógico do receptor, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado no tempo de execução e deve corresponder ao nome especificado pelo aplicativo lógico do receptor. Alterar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo da mensagem** | O conteúdo da mensagem que você quer enviar, que é o token do **Corpo** neste exemplo | 
   ||| 
   
   ![Definir propriedades do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Salve seu aplicativo lógico. 

   O aplicativo lógico do remetente do lote é semelhante a este exemplo:

   ![Salvar o aplicativo lógico do remetente do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar a solução de envio em lote, poste mensagens X12 no aplicativo lógico do remetente do lote a partir do [Postman](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. Em breve, você começará a receber mensagens X12 no compartimento de solicitações, a cada 10 minutos ou em lotes de 10, todos com a mesma chave de partição.

## <a name="next-steps"></a>Próximas etapas

* [Processar mensagens como lotes](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
