---
title: Conectar ao servidor MQ – Aplicativos Lógicos do Azure | Microsoft Docs
description: Enviar e recuperar mensagens com um servidor MQ do Azure ou local e os Aplicativos Lógicos do Azure
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688666"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Conectar-se a um servidor do IBM MQ de aplicativos lógicos usando o conector MQ

O Conector Microsoft para MQ envia e recupera mensagens armazenadas em um servidor MQ local ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este documento é um guia introdutório para usar o conector MQ. É recomendável que você comece procurando uma única mensagem em uma fila e, em seguida, tente as outras ações.

O Conector do MQ inclui as ações a seguir. Não há gatilhos.

- Procurar uma única mensagem sem excluí-la do servidor IBM MQ
- Procurar um lote de mensagens sem excluí-las do servidor IBM MQ
- Receber uma única mensagem e excluí-la do servidor IBM MQ
- Receber um lote de mensagens e excluí-las do servidor IBM MQ
- Enviar uma única mensagem ao servidor IBM MQ

## <a name="prerequisites"></a>Pré-requisitos

* Se você usar um servidor MQ local, [instale o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor dentro de sua rede. Se o servidor MQ está publicamente disponível ou disponível no Azure, o gateway de dados não é usado nem necessário.

    > [!NOTE]
    > O servidor onde o Gateway de dados local está instalado também deve ter o .NET Framework 4.6 instalado para o conector MQ funcione.

* Criar o recurso do Azure para o gateway de dados local – [configurar a conexão de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

* Versões do IBM WebSphere MQ oficialmente com suporte:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, tal como MQTestApp, **Assinatura**, **Grupo de recursos** e **Localização** (use a localização em que a conexão de gateway de dados local está configurada). Selecione **Fixar no painel** e selecione **Criar**.  
![Criar Aplicativo Lógico](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Adicionar um gatilho

> [!NOTE]
> O conector MQ não tem gatilhos. Portanto, use outro gatilho para iniciar seu aplicativo lógico, tal como o gatilho **Recorrência**.

1. O **Designer de Aplicativos Lógicos** é aberto, selecione **Recorrência** na lista de gatilhos comuns.
2. Selecione **Editar** dentro do gatilho Recorrência.
3. Defina a **Frequência** para **Dia** e defina o **Intervalo** como **7**.

## <a name="browse-a-single-message"></a>Procurar uma única mensagem
1. Selecione **+ Nova etapa** e selecione **Adicionar uma ação**.
2. Na caixa de pesquisa, digite `mq` e, em seguida, selecione **MQ – procurar mensagem**.  
![Procurar mensagem](media/connectors-create-api-mq/Browse_message.png)

3. Se não houver uma conexão MQ existente, crie a conexão:  

    1. Selecione **Conectar-se por meio do gateway de dados local** e insira as propriedades do servidor MQ.  
    Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.
    2. A lista suspensa **gateway** lista quaisquer conexões de gateway existentes que tenham sido configuradas. Selecione seu gateway.
    3. Selecione **Criar** quando terminar. A conexão é semelhante ao seguinte:  
    ![Propriedades da Conexão](media/connectors-create-api-mq/Connection_Properties.png)

4. Nas propriedades da ação, você pode:  

    * Use a propriedade **Fila** para acessar um nome fila diferente do que está definido na conexão
    * Use **MessageId**, **CorrelationId**, **GroupId**e outras propriedades para procurar uma mensagem com base nas propriedades de mensagem do MQ diferentes
    * Defina **IncludeInfo** como **True** para incluir informações adicionais da mensagem na saída. Ou defina-o como **False** para não incluir informações adicionais da mensagem na saída.
    * Insira um valor de **Tempo limite** para determinar por quanto tempo aguardar até que uma mensagem chegue em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça.  
    ![Procurar propriedades de mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Salve** suas alterações e, em seguida, **execute** seu aplicativo lógico:  
![Salvar e Executar](media/connectors-create-api-mq/Save_Run.png)

6. Depois de alguns segundos, as etapas da execução são mostradas e você pode examinar a saída. Selecione a marca de seleção verde para ver os detalhes de cada etapa. Selecione **Ver saídas brutas** para ver detalhes adicionais sobre os dados de saída.  
![Procurar saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

    Saída bruta:  
    ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Quando a opção **IncludeInfo** é definida como true, a seguinte saída é exibida:  
![Procurar informações de inclusão da mensagem](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens
A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens devem ser retornadas da fila.  Se **BatchSize** não tem nenhuma entrada, todas as mensagens são retornadas. A saída retornada é uma matriz de mensagens.

1. Ao adicionar a ação **Procurar mensagens**, a primeira conexão configurada é selecionada por padrão. Selecione **Alterar conexão** para criar uma nova conexão ou selecione uma conexão diferente.

2. A saída de Procurar mensagens mostra:  
![Saída de Procurar mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Receber uma única mensagem
A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Ao usar **Receber mensagem**, a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens
A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Ao usar **Receber mensagens**, as mensagens são excluídas da fila.

Se não houver nenhuma mensagem na fila ao fazer uma pesquisa ou um recebimento, a etapa falhará com a seguinte saída:  
![Erro MQ Nenhuma Mensagem](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Enviar uma mensagem
1. Ao adicionar a ação **Enviar mensagem**, a primeira conexão configurada é selecionada por padrão. Selecione **Alterar conexão** para criar uma nova conexão ou selecione uma conexão diferente. Os **Tipos de Mensagem** válidos são **Datagrama**, **Resposta** ou **Solicitação**.  
![Send Msg Props](media/connectors-create-api-mq/Send_Msg_Props.png)

2. A saída de Enviar mensagem se parece com o seguinte:  
![Send Msg Output](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/mq/).

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).
