---
title: Processar mensagens em lote como um grupo ou uma coleção – Aplicativos Lógicos do Azure | Microsoft Docs
description: Enviar e receber mensagens como lotes em Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: c33b1d46ecf710f050fc998ce27f6448337c6b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683540"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Enviar, receber e processar em lote mensagens nos Aplicativos Lógicos do Azure

Para enviar e processar mensagens juntas de uma maneira específica como grupos, você pode criar uma solução de lotes que coleta mensagens em um *lote* até que os critérios especificados sejam atendidos para liberar e processar as mensagens em lote. Os lotes podem reduzir a frequência com que seu aplicativo lógico processa mensagens. Este artigo mostra como criar uma solução em lotes criando dois aplicativos lógicos dentro da mesma assinatura do Azure, região do Azure e seguindo essa ordem específica: 

* O aplicativo lógico ["receptor em lote"](#batch-receiver), que aceita e coleta mensagens em um lote até que seus critérios especificados sejam atendidos para liberar e processar essas mensagens.

  Certifique-se de criar primeiro o receptor do lote, para depois selecionar o destino do lote ao criar o remetente do lote.

* Um ou mais aplicativos lógicos ["remetente em lote"](#batch-sender), que enviam as mensagens para o destinatário do lote criado anteriormente. 

   Você também pode especificar uma chave exclusiva, como um número de cliente, que *particione* ou divida o lote de destino em subconjuntos lógicos com base nessa chave. Dessa forma, o aplicativo receptor pode coletar todos os itens com a mesma chave e processá-los juntos.

Verifique se o destinatário do lote e o remetente em lote compartilham a mesma assinatura do Azure *e* região do Azure. Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote porque não estarão visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Ou, [inscreva-se para uma assinatura de Pagamento Conforme o Uso](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de email com qualquer [provedor de email com suporte dos Aplicativos Lógicos do Azure](../connectors/apis-list.md)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Para usar o Visual Studio em vez do portal do Azure, certifique-se de [configurar o Visual Studio para trabalhar com Aplicativos Lógicos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Criar destinatário do lote

Antes de enviar mensagens para um lote, esse lote deve existir primeiro como o destino para o qual você envia essas mensagens. Portanto, primeiro, é necessário criar o aplicativo lógico "receptor do lote" que inicia com o gatilho do **Lote**. Dessa forma, ao criar o aplicativo lógico "remetente do lote", será possível selecionar o aplicativo lógico do receptor do lote. O receptor do lote continuará coletando mensagens até que seus critérios especificados sejam atendidos para liberar e processar essas mensagens. Embora os receptores dos lotes não precisem saber nada sobre os remetentes dos lotes, os remetentes dos lotes deverão saber o destino para onde enviarão as mensagens. 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico com este nome: "BatchReceiver" 

2. No Designer de Aplicativos Lógicos, adicione o gatilho **Lote**, o que inicia seu fluxo de trabalho de aplicativo lógico. Na caixa de pesquisa, digite "lote" como filtro. Selecione este gatilho: **Mensagens em lote**

   ![Adicionar gatilho de "Mensagens em lote"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Defina estas propriedades para o receptor do lote: 

   | Propriedade | DESCRIÇÃO | 
   |----------|-------------|
   | **Modo de lote** | - **Embutido**: Para definir os critérios de liberação dentro do gatilho do lote <br>- **Conta de integração**: Para definir várias configurações de critérios de liberação por meio de uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Com uma conta de integração, você pode manter essas configurações em um só lugar e não em aplicativos lógicos separados. | 
   | **Nome do lote** | O nome do seu lote, que é "TestBatch" neste exemplo, e se aplica apenas ao modo em lote **Inline** |  
   | **Critérios de liberação** | Aplica-se somente ao modo em lote **Embutido** e selecione os critérios a serem atendidos antes do processamento de cada lote: <p>- **Baseado na contagem de mensagens**: Libere o lote com base no número de mensagens coletadas pelo lote. <br>- **Com base no tamanho**: Libere o lote com base no tamanho total em bytes de todas as mensagens coletadas por esse lote. <br>- **Agenda**: Libere o lote com base em uma agenda de recorrência, que especifica um intervalo e a frequência. Nas opções avançadas, você também pode selecionar um fuso horário e fornecer uma data e hora de início. <br>- **Selecionar tudo**: Usar todos os critérios especificados. | 
   | **Contagem de mensagens** | O número de mensagens a coletar no lote, por exemplo, 10 mensagens. O limite de um lote é 8.000 mensagens. | 
   | **Tamanho do lote** | O tamanho total em bytes para coletar no lote, por exemplo, 10 MB. O limite de tamanho de um lote é de 80 MB. | 
   | **Agenda** | O intervalo e a frequência entre lançamentos de lote, por exemplo, 10 minutos. A recorrência mínima é de 60 segundos ou 1 minuto. Os valores de fração de minutos são arredondados para 1 minuto. Para especificar um fuso horário ou uma data e hora de início, escolha **Mostrar opções avançadas**. | 
   ||| 

   > [!NOTE]
   > 
   > Se você alterar os critérios de liberação enquanto o gatilho ainda tiver mensagens em lote não enviadas, o gatilho usará os critérios de liberação atualizados para lidar com as mensagens não enviadas. 

   Este exemplo mostra todos os critérios, mas para seus próprios testes, experimente apenas um critério:

   ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Agora adicione uma ou mais ações que processam cada lote. 

   Para este exemplo, adicione uma ação que envia um email quando o acionador em lote é disparado. 
   O gatilho é executado e envia um email quando o lote tem 10 mensagens, atinge 10 MB ou após 10 minutos.

   1. No gatilho de lote, escolha **Nova etapa**.

   2. Na caixa de pesquisa, insira "enviar email" como filtro.
   Com base em seu provedor de email, selecione um conector de email.

      Por exemplo, se você tiver uma conta pessoal, como @outlook.com ou @hotmail.com, selecione o conector Outlook.com. 
      Se você tiver uma conta do Gmail, selecione o conector Gmail. 
      Este exemplo usa o Outlook do Office 365. 

   3. Selecione esta ação: **Enviar um email – <*provedor de email*>**

      Por exemplo: 

      ![Selecione a ação "Enviar um email" para o seu provedor de email](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Se solicitado, entre em sua conta de email. 

6. Defina as propriedades da ação que você adicionou.

   * Na caixa **Para**, insira o endereço de email do destinatário. 
   Para fins de teste, você pode usar seu próprio endereço de email.

   * Na caixa **Assunto**, quando a lista de conteúdo dinâmico aparecer, selecione o campo **Nome da Partição**.

     ![Na lista de conteúdo dinâmico, selecione "Nome da Partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Posteriormente, no remetente do lote, você pode especificar uma chave de partição exclusiva que divide o lote de destino em subconjuntos lógicos para os quais você pode enviar mensagens. 
     Cada conjunto possui um número exclusivo gerado pelo aplicativo lógico do remetente em lote. 
     Esse recurso permite o uso de um único lote com várias subconjuntos e define cada subconjunto com o nome fornecido por você.

     > [!IMPORTANT]
     > Uma partição tem um limite de 5.000 mensagens ou 80 MB. Se uma das condições for atendida, os Aplicativos Lógicos poderão liberar o lote, mesmo quando sua condição de liberação definida não for atendida.

   * Na caixa **Body**, quando a lista de conteúdo dinâmico aparecer, selecione o campo **Message Id**. 

     O Designer de Aplicativos Lógicos adiciona automaticamente um loop "For each" à ação de enviar email porque essa ação trata a saída da ação anterior como uma coleção e não como um lote. 

     ![Para "Corpo", selecione "Id de Mensagem"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Salve seu aplicativo lógico. Você criou agora um receptor de lote.

    ![Salve seu aplicativo lógico](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Se estiver usando o Visual Studio, certifique-se de [implantar o aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, você não poderá selecionar o destinatário do lote quando criar o remetente em lote.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Criar remetente em lote

Agora crie um ou mais aplicativos de lógica de remetente em lote que enviem mensagens para o aplicativo lógico de recebimento em lote. Em cada remetente do lote, você especifica o nome do lote e do destinatário do lote, o conteúdo da mensagem e quaisquer outras configurações. Opcionalmente, você pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos lógicos para coletar mensagens com essa chave. 

* Certifique-se de que você já [criou o seu receptor de lote](#batch-receiver), assim, ao criar o remetente do seu lote, você pode selecionar o receptor de lote existente como o lote de destino. Embora os receptores dos lotes não precisem saber nada sobre os remetentes dos lotes, os remetentes dos lotes deverão saber para onde enviarão as mensagens. 

* Certifique-se de que receptor do lote e o remetente do lote compartilham a mesma região do Azure *e* a assinatura do Azure. Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote porque não estarão visíveis um para o outro.

1. Crie outro aplicativo lógico com este nome: "RemetenteDoLote"

   1. Na caixa de pesquisa, insira "recorrência" como filtro. 
   Selecione este gatilho: **Recorrência – Agenda**

      ![Adicionar o gatilho "Agenda de recorrência –"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Defina a frequência e o intervalo de execução do aplicativo lógico remetente como a cada minuto.

      ![Defina a frequência e o intervalo do gatilho de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Adicione uma nova ação para enviar mensagens para um lote.

   1. Sob o gatilho de recorrência, escolha **Novo passo**.

   2. Na caixa de pesquisa, digite "lote" como filtro. 
   Selecione a lista de **Ações** e depois esta ação: **Escolher um fluxo de trabalho de Aplicativos Lógicos com o gatilho de lote – Enviar mensagens para o lote**

      ![Selecione "Escolher um fluxo de trabalho de Aplicativos Lógicos com gatilho do lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecione seu aplicativo lógico destinatário do lote que você criou anteriormente.

      ![Selecione o aplicativo lógico "destinatário do lote"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista também mostra todos os outros aplicativos lógicos que tenham um gatilho lote. 
      > 
      > Se você estiver usando o Visual Studio e não vir nenhum receptor em lote para selecionar, verifique se você implantou o receptor do lote no Azure. Se ainda não o fez, saiba como [implantar seu aplicativo de lógica do receptor de lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Selecione esta ação: Selecione esta ação **Batch_messages – <*seu_remetente_de_lote*>**

      ![Selecione esta ação: "Batch_messages – <seu-aplicativo-lógico>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Defina propriedades do remetente de lote:

   | Propriedade | DESCRIÇÃO | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pelo aplicativo lógico do receptor, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado no tempo de execução e deve corresponder ao nome especificado pelo aplicativo lógico do receptor. Alterar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo da mensagem** | O conteúdo da mensagem que você deseja enviar | 
   ||| 

   Para este exemplo, adicione essa expressão, que insere a data e a hora atuais no conteúdo da mensagem que você envia ao lote:

   1. Clique dentro de **o conteúdo da mensagem** caixa. 

   2. Quando for exibida a lista de conteúdo dinâmico, escolha **expressão**. 

   3. Insira a expressão `utcnow()`e, em seguida, escolha **Ok**. 

      ![Em "Conteúdo da Mensagem", escolha "Expressão", digite "utcnow ()" e escolha "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Agora, configure uma partição para o lote. Na ação "Destinatáriolote", escolha **Mostrar opções avançadas** e definir essas propriedades:

   | Propriedade | DESCRIÇÃO | 
   |----------|-------------| 
   | **Nome da partição** | Uma chave de partição exclusiva opcional a ser usada para dividir o lote de destino em subconjuntos lógicos e coletar mensagens com base nessa chave | 
   | **Id da mensagem** | Um identificador de mensagem opcional que é um identificador global exclusivo gerado (GUID) quando estiver vazio | 
   ||| 

   Para este exemplo, na caixa **Partition Name**, adicione uma expressão que gere um número aleatório entre um e cinco. Deixe a caixa **ID da mensagem** vazia.
   
   1. Clique dentro de **nome da partição** caixa para que apareça na lista de conteúdo dinâmica. 

   2. Na lista Conteúdo dinâmico, escolha **Expressão**.
   
   3. Insira a expressão `rand(1,6)`e, em seguida, escolha **Ok**.

      ![Configurar uma partição para seu lote de destino](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Essa função **rand** gera um número entre um e cinco. 
      Portanto, você está dividindo esse lote em cinco partições numeradas, definidas dinamicamente por esta expressão.

5. Salve seu aplicativo lógico. Seu aplicativo lógico remetente agora parece com este exemplo:

   ![Salve seu aplicativo lógico remetente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar sua solução de envio em lote, deixe seus aplicativos lógicos em execução por alguns minutos. Em breve, você começará a receber emails em grupos de cinco, todos com a mesma chave de partição.

Seu aplicativo de lógica de remetente em lote é executado a cada minuto, gera um número aleatório entre um e cinco e usa esse número gerado como a chave de partição para o lote de destino para o qual as mensagens são enviadas. Cada vez que o lote tem cinco itens com a mesma chave de partição, o aplicativo de lógica de recebimento do lote dispara e envia mensagens para cada mensagem.

> [!IMPORTANT]
> Quando você terminar o teste, desabilite o aplicativo lógico RemetenteLote para interromper o envio de mensagens e evitar a sobrecarga de sua caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

* [Colocar em lotes e enviar mensagens de EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Compilar definições de aplicativo lógico usando JSON](../logic-apps/logic-apps-author-definitions.md)
* [Compilar um aplicativo sem servidor no Visual Studio com os Aplicativos Lógicos do Azure e o Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceção e registro em log de erros para aplicativos lógicos](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
