---
title: Processar mensagens em lote como um grupo
description: Enviar e receber mensagens em grupos entre seus fluxos de trabalho usando o processamento em lotes em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87458174"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Enviar, receber e processar em lote mensagens nos Aplicativos Lógicos do Azure

Para enviar e processar mensagens de forma específica como grupos, você pode criar uma solução de envio em lote. Essa solução coleta mensagens em um *lote* e aguarda até que os critérios especificados sejam atendidos antes de liberar e processar as mensagens em lote. Os lotes podem reduzir a frequência com que seu aplicativo lógico processa mensagens.

Este artigo mostra como criar uma solução de envio em lote criando dois aplicativos lógicos dentro da mesma assinatura do Azure, região do Azure e, nesta ordem:

1. O aplicativo lógico ["receptor em lote"](#batch-receiver), que aceita e coleta mensagens em um lote até que seus critérios especificados sejam atendidos para liberar e processar essas mensagens. Primeiro, certifique-se de criar esse receptor do lote para que você possa selecionar posteriormente o destino do lote ao criar o remetente do lote.

1. Um ou mais aplicativos lógicos ["remetente em lote"](#batch-sender), que enviam as mensagens para o destinatário do lote criado anteriormente.

   Você também pode especificar uma chave exclusiva, como um número de cliente, que *particione* ou divida o lote de destino em subconjuntos lógicos com base nessa chave. Dessa forma, o aplicativo receptor pode coletar todos os itens com a mesma chave e processá-los juntos.

O receptor do lote e o remetente do lote precisam compartilhar a mesma assinatura do Azure *e* a região do Azure. Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote porque não estarão visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura, poderá [começar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Ou, [inscreva-se para uma assinatura de Pagamento Conforme o Uso](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de email com qualquer [provedor de email com suporte dos Aplicativos Lógicos do Azure](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrição nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar esse conector somente com serviços específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação com o conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para usar o Visual Studio em vez do portal do Azure, certifique-se de [Configurar o Visual Studio para trabalhar com aplicativos lógicos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Criar destinatário do lote

Antes de enviar mensagens para um lote, esse lote deverá existir primeiro como o destino para o qual essas mensagens serão enviadas. Portanto, primeiro, é necessário criar o aplicativo lógico "receptor do lote" que inicia com o gatilho do **Lote**. Dessa forma, ao criar o aplicativo lógico "remetente do lote", será possível selecionar o aplicativo lógico do receptor do lote. O receptor do lote continuará coletando mensagens até que seus critérios especificados sejam atendidos para liberar e processar essas mensagens. Embora os receptores dos lotes não precisem saber nada sobre os remetentes dos lotes, os remetentes dos lotes deverão saber o destino para onde enviarão as mensagens.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico com este nome: `BatchReceiver`

1. No designer de aplicativo lógico, adicione o gatilho de **lote** , que inicia o fluxo de trabalho do aplicativo lógico. Na caixa de pesquisa, insira `batch` e selecione este gatilho: **mensagens em lotes**

   ![Adicionar gatilho de "Mensagens em lote"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Defina estas propriedades para o receptor do lote:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Modo de lote** | - **Inline**: para definir os critérios de liberação dentro do acionador de lote <br>- **Conta de Integração**: para definir várias configurações de critérios de liberação por meio de uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Com uma conta de integração, você pode manter essas configurações em um só lugar e não em aplicativos lógicos separados. |
   | **Nome do lote** | O nome do seu lote, que é "TestBatch" neste exemplo, e se aplica apenas ao modo em lote **Inline** |
   | **Critérios de liberação** | Aplica-se somente ao modo em lote **Embutido** e selecione os critérios a serem atendidos antes do processamento de cada lote: <p>- **Contagem de mensagens com base**: Libere o lote com base no número de mensagens coletadas pelo lote. <br>- **Baseado em tamanho**: Libere o lote com base no tamanho total em bytes para todas as mensagens coletadas por esse lote. <br>- **Agenda**: libera o lote com base em um agendamento de recorrência, que especifica um intervalo e uma frequência. Nas opções avançadas, você também pode selecionar um fuso horário e fornecer uma data e hora de início. <br>- **Selecionar tudo**: usar todos os critérios especificados. |
   | **Contagem de mensagens** | O número de mensagens a coletar no lote, por exemplo, 10 mensagens. O limite de um lote é 8.000 mensagens. |
   | **Tamanho do lote** | O tamanho total em bytes para coletar no lote, por exemplo, 10 MB. O limite de tamanho de um lote é de 80 MB. |
   | **Agenda** | O intervalo e a frequência entre lançamentos de lote, por exemplo, 10 minutos. A recorrência mínima é de 60 segundos ou 1 minuto. Os valores de fração de minutos são arredondados para 1 minuto. Para especificar um fuso horário ou uma data e hora de início, abra a lista **Adicionar novo parâmetro** e selecione as propriedades correspondentes. |
   |||

   > [!NOTE]
   >
   > Se você alterar os critérios de liberação enquanto o gatilho ainda tiver mensagens em lote não enviadas, o gatilho usará os critérios de liberação atualizados para lidar com as mensagens não enviadas.

   Este exemplo mostra todos os critérios, mas para seus próprios testes, experimente apenas um critério:

   ![Fornecer detalhes sobre o gatilho Lote](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Agora adicione uma ou mais ações que processam cada lote.

   Para este exemplo, adicione uma ação que envia um email quando o acionador em lote é disparado. O gatilho é executado e envia um email quando o lote tem 10 mensagens, atinge 10 MB ou após 10 minutos.

   1. No gatilho lote, selecione **nova etapa**.

   1. Na caixa de pesquisa, insira `send email` como o filtro. Com base em seu provedor de email, selecione um conector de email.

      Por exemplo, se você tiver uma conta corporativa ou de estudante, como @fabrikam.com ou @fabrikam.onmicrosoft.com , selecione o conector do **Microsoft 365 Outlook** . Se você tiver uma conta pessoal, como @outlook.com ou @hotmail.com , selecione o conector do **Outlook.com** . Este exemplo usa o conector do Microsoft 365 Outlook.

   1. Selecione a ação "enviar um email" para seu provedor, por exemplo:

      ![Selecione a ação "Enviar um email" para o seu provedor de email](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. Se solicitado, entre em sua conta de email.

1. Defina as propriedades da ação que você adicionou.

   * Na caixa **Para**, insira o endereço de email do destinatário. Para fins de teste, você pode usar seu próprio endereço de email.

   * Na caixa **Assunto**, quando a lista de conteúdo dinâmico aparecer, selecione o campo **Nome da Partição**.

     ![Na lista de conteúdo dinâmico, selecione "Nome da Partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Posteriormente, no remetente do lote, você pode especificar uma chave de partição exclusiva que divide o lote de destino em subconjuntos lógicos para os quais você pode enviar mensagens. Cada conjunto possui um número exclusivo gerado pelo aplicativo lógico do remetente em lote. Esse recurso permite o uso de um único lote com várias subconjuntos e define cada subconjunto com o nome fornecido por você.

     > [!IMPORTANT]
     > Uma partição tem um limite de 5.000 mensagens ou 80 MB. Se uma das condições for atendida, os Aplicativos Lógicos poderão liberar o lote, mesmo quando sua condição de liberação definida não for atendida.

   * Na caixa **Body**, quando a lista de conteúdo dinâmico aparecer, selecione o campo **Message Id**.

     O designer de aplicativo lógico adiciona automaticamente um loop **for each** em volta da ação enviar email, pois essa ação trata a saída da ação anterior como uma coleção, em vez de um lote.

     ![Para "Corpo", selecione "Id de Mensagem"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Salve seu aplicativo lógico. Você criou agora um receptor de lote.

    ![Salve seu aplicativo lógico](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Se você estiver usando o Visual Studio, antes de continuar para a próxima seção, certifique-se de [ *implantar* primeiro o aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Criar remetente em lote

Agora crie um ou mais aplicativos de lógica de remetente em lote que enviem mensagens para o aplicativo lógico de recebimento em lote. Em cada remetente do lote, você especifica o nome do lote e do destinatário do lote, o conteúdo da mensagem e quaisquer outras configurações. Opcionalmente, você pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos lógicos para coletar mensagens com essa chave.

* Certifique-se de que você [criou e implantou anteriormente seu receptor do lote](#batch-receiver) , portanto, ao criar o remetente do lote, você pode selecionar o destinatário do lote existente como o lote de destino. Embora os receptores dos lotes não precisem saber nada sobre os remetentes dos lotes, os remetentes dos lotes deverão saber para onde enviarão as mensagens.

* Verifique se o destinatário do lote e o remetente do lote compartilham a mesma região do Azure *e* a assinatura do Azure. Caso contrário, não será possível selecionar o receptor do lote ao criar o remetente do lote porque não estarão visíveis um para o outro.

1. Crie outro aplicativo lógico com este nome: `BatchSender`

   1. Na caixa de pesquisa, insira `recurrence` como o filtro. Na lista de gatilhos, selecione este gatilho: **Recorrência**

      ![Adicionar gatilho Recorrência](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Defina o intervalo e a frequência para executar o aplicativo lógico remetente a cada minuto.

      ![Definir a frequência e o intervalo para o gatilho de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Adicione uma nova ação para enviar mensagens para um lote.

   1. No gatilho de **recorrência** , selecione **nova etapa**.

   1. Na caixa de pesquisa, insira `batch` como seu filtro e selecione esta ação: **escolher um fluxo de trabalho de aplicativos lógicos com gatilho de lote**

      ![Selecione "Escolher um fluxo de trabalho de Aplicativos Lógicos com gatilho do lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      Uma lista é exibida e mostra apenas os aplicativos lógicos que têm gatilhos em lote e existem na mesma região do Azure *e* na assinatura do Azure que o aplicativo lógico do remetente do lote.

   1. Na lista de aplicativos lógicos, selecione o aplicativo lógico do receptor do lote que você criou anteriormente.

      ![Selecione o aplicativo lógico do receptor do lote](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Se você estiver usando o Visual Studio e não vir nenhum receptor de lote para selecionar, verifique se você criou e implantou o seu destinatário do lote no Azure. Se você ainda não fez isso, saiba [como implantar seu aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure).

   1. Na lista de ações, selecione esta ação: **Batch_messages-<*Your-Logic-app-Name* >**

      ![Selecione esta ação: "Batch_messages - < your--aplicativo lógico >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. Defina propriedades do remetente de lote:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do lote** | O nome do lote definido pelo aplicativo lógico do receptor, que está `TestBatch` neste exemplo <p>**Importante**: o nome do lote é validado no runtime e deve corresponder ao nome especificado pelo aplicativo lógico do receptor. Alterar o nome do lote faz com que o remetente do lote falhe. |
   | **Conteúdo da mensagem** | O conteúdo da mensagem que você deseja enviar |
   |||

   > [!NOTE]
   > O **nome do gatilho** e os valores de Propriedade do **fluxo de trabalho** são preenchidos automaticamente a partir do aplicativo lógico selecionado.

   Para este exemplo, adicione essa expressão, que insere a data e a hora atuais no conteúdo da mensagem que você envia ao lote:

   1. Clique dentro de **o conteúdo da mensagem** caixa.

   1. Quando a lista de conteúdo dinâmico for exibida, selecione **expressão**.

   1. Insira a expressão `utcnow()` e selecione **OK**.

      ![Em "conteúdo da mensagem", selecione "expressão", digite "UtcNow ()" e selecione "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Agora, configure uma partição para o lote. Na `BatchReceiver` ação, abra a lista **Adicionar novo parâmetro** e selecione estas propriedades:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome da Partição** | Uma chave de partição exclusiva opcional a ser usada para dividir o lote de destino em subconjuntos lógicos e coletar mensagens com base nessa chave |
   | **ID da mensagem** | Um identificador de mensagem opcional que é um identificador global exclusivo gerado (GUID) quando estiver vazio |
   |||

   Para este exemplo, na caixa **Partition Name**, adicione uma expressão que gere um número aleatório entre um e cinco. Deixe a caixa **ID da mensagem** vazia.

   1. Clique dentro de **nome da partição** caixa para que apareça na lista de conteúdo dinâmica.

   1. Na lista de conteúdo dinâmico, selecione **Expressão**.

   1. Insira a expressão `rand(1,6)` e, em seguida, selecione **OK**.

      ![Configurar uma partição para seu lote de destino](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Essa função **rand** gera um número entre um e cinco. Portanto, você está dividindo esse lote em cinco partições numeradas, definidas dinamicamente por esta expressão.

1. Salve seu aplicativo lógico. Seu aplicativo lógico remetente agora parece com este exemplo:

   ![Salve seu aplicativo lógico remetente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar sua solução de envio em lote, deixe seus aplicativos lógicos em execução por alguns minutos. Em breve, você começará a receber emails em grupos de cinco, todos com a mesma chave de partição.

Seu aplicativo de lógica de remetente em lote é executado a cada minuto, gera um número aleatório entre um e cinco e usa esse número gerado como a chave de partição para o lote de destino para o qual as mensagens são enviadas. Cada vez que o lote tem cinco itens com a mesma chave de partição, o aplicativo de lógica de recebimento do lote dispara e envia mensagens para cada mensagem.

> [!IMPORTANT]
> Quando terminar de testar, certifique-se de desabilitar o `BatchSender` aplicativo lógico para interromper o envio de mensagens e evitar sobrecarregar sua caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

* [Colocar em lote e enviar mensagens EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Compilar definições de aplicativo lógico usando JSON](../logic-apps/logic-apps-author-definitions.md)
* [Compilar um aplicativo sem servidor no Visual Studio com os Aplicativos Lógicos do Azure e o Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceção e log de erros para aplicativos lógicos](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
