---
title: Conectar ao servidor IBM MQ – aplicativos lógicos do Azure
description: Enviar e recuperar mensagens com um servidor do IBM MQ do Azure ou no local e os aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273131"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Conectar a um servidor do IBM MQ de aplicativos lógicos do Azure

O conector do IBM MQ envia e recupera mensagens armazenadas em um servidor IBM MQ local ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este artigo fornece um guia introdutório para usar o conector MQ. Você pode comece procurando uma única mensagem em uma fila e, em seguida, tente outras ações.

O conector do IBM MQ inclui essas ações, mas não fornece nenhum gatilho:

- Procurar uma única mensagem sem excluí-la do servidor IBM MQ
- Procurar um lote de mensagens sem excluir as mensagens do servidor IBM MQ
- Receber uma única mensagem e excluir a mensagem do servidor IBM MQ
- Receber um lote de mensagens e excluir as mensagens do servidor IBM MQ
- Enviar uma única mensagem para o servidor IBM MQ

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver usando um servidor do MQ local, [instalar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor em sua rede. O servidor onde o gateway de dados local está instalado também deve ter o .NET Framework 4.6 instalado para o conector MQ funcione. Você também deve criar um recurso no Azure para o gateway de dados local. Para obter mais informações, consulte [configurar a conexão de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  No entanto, se seu servidor MQ está publicamente disponível ou disponível dentro do Azure, você não precisa usar o gateway de dados.

* Versões do IBM WebSphere MQ oficialmente com suporte:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* O aplicativo lógico no qual você deseja adicionar a ação MQ. Esse aplicativo lógico deve usar o mesmo local como sua conexão de gateway de dados locais e já deve ter um gatilho que inicia o fluxo de trabalho. 

  O conector MQ não tem gatilhos, você deve adicionar um gatilho ao seu aplicativo lógico pela primeira vez. Por exemplo, você pode usar o gatilho de recorrência. Se você estiver familiarizado com aplicativos lógicos, tente o seguinte [guia de início rápido para criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Procurar uma única mensagem

1. Em seu aplicativo lógico, sob o gatilho ou outra ação, escolha **nova etapa**. 

1. Na caixa de pesquisa, digite "mq" e selecione esta ação: **Procurar mensagem**

   ![Procurar mensagem](media/connectors-create-api-mq/Browse_message.png)

1. Se você não tiver uma conexão MQ existente, crie a conexão:  

   1. Em ação, selecione **conectar por meio do gateway de dados locais**.
   
   1. Insira as propriedades do servidor MQ.  

      Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.
    
   1. Abra o **gateway** lista, que mostra qualquer configurada anteriormente conexões de gateway. Selecione seu gateway.
    
   1. Quando terminar, escolha **Criar**. 
   
      Sua conexão é semelhante a este exemplo:

      ![Propriedades da conexão](media/connectors-create-api-mq/Connection_Properties.png)

1. Configure as propriedades da ação:

   * **fila**: Especifique uma fila que é diferente do que a conexão.

   * **MessageId**, **CorrelationId**, **GroupId**e outras propriedades: Procure uma mensagem com base nas propriedades de mensagem do MQ diferentes

   * **IncludeInfo**: Especificar **verdadeira** para incluir informações adicionais da mensagem na saída. Ou, especifique **falsos** para não incluir informações adicionais da mensagem na saída.

   * **Tempo limite**: Insira um valor para determinar por quanto tempo a aguardar uma mensagem chegar em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça.

     ![Procurar propriedades de mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Salve** suas alterações e, em seguida **executar** seu aplicativo lógico.

   ![Salvar e executar](media/connectors-create-api-mq/Save_Run.png)

   Após a execução, as etapas da execução são mostradas e você pode examinar a saída.

1. Para examinar os detalhes de cada etapa, escolha a marca de seleção verde. Para obter mais informações sobre os dados de saída de examinar, escolha **Mostrar saídas brutas**.

   ![Procurar saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

   Eis algumas saídas brutos de exemplo:

   ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se você definir **IncludeInfo** como true, a seguinte saída é exibida:

   ![Procurar mensagem inclua informações](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens devem ser retornadas da fila.  Se **BatchSize** não tem nenhuma entrada, todas as mensagens são retornadas. A saída retornada é uma matriz de mensagens.

1. Quando você adiciona o **procurar mensagens** ação, anteriormente a primeira conexão configurada é selecionada por padrão. Para criar uma nova conexão, escolha **alterar conexão**. Ou, selecione uma conexão diferente.

1. Depois de terminar de executar o aplicativo lógico, aqui está um exemplo de saída das **procurar mensagens** ação:

   ![Saída de Procurar mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Receber mensagem única

A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Ao usar **Receber mensagem**, a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Ao usar **Receber mensagens**, as mensagens são excluídas da fila.

Se não houver nenhuma mensagem na fila ao fazer uma pesquisa ou um recebimento, a etapa falha com esta saída:  

![Erro MQ Nenhuma Mensagem](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Enviar mensagem

Quando você adiciona o **enviar mensagens** ação, anteriormente a primeira conexão configurada é selecionada por padrão. Para criar uma nova conexão, escolha **alterar conexão**. Ou, selecione uma conexão diferente.

1. Selecione um tipo de mensagem válido: **Datagrama**, **resposta**, ou **de solicitação**  

   ![Enviar arquivos de propriedades de mensagem](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Após o aplicativo lógico a execução, aqui está um exemplo de saída das **enviar mensagem** ação:

   ![Send Msg Output](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre as ações e os limites, que são descritos por OpenAPI do conector (anteriormente conhecido como Swagger) descrição, examine o conector [página de referência](/connectors/mq/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
