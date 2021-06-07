---
title: Conectar-se ao servidor do IBM MQ
description: Enviar e recuperar mensagens com um servidor IBM MQ do Azure ou local e os aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 03/10/2021
tags: connectors
ms.openlocfilehash: a07eb6e592c68794f0e4038a7cf9a42bd396b47a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495225"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Conectar-se a um servidor do IBM MQ de aplicativos lógicos do Azure

O conector do MQ envia e recupera mensagens armazenadas em um servidor do MQ localmente ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este artigo fornece um guia introdutório para usar o conector MQ. Você pode começar procurando uma única mensagem em uma fila e tentar as outras ações.

O conector do MQ inclui essas ações, mas não fornece gatilhos:

- Procure uma única mensagem sem excluir a mensagem do servidor MQ.
- Procure um lote de mensagens sem excluir as mensagens do servidor MQ.
- Receba uma única mensagem e exclua a mensagem do servidor MQ.
- Receba um lote de mensagens e exclua as mensagens do servidor MQ.
- Envie uma única mensagem para o servidor MQ.

Aqui estão as versões do IBM WebSphere MQ oficialmente com suporte:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>Pré-requisitos

* Se você usar um servidor MQ local, precisará [instalar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor dentro de sua rede.

  > [!NOTE]
  > Se o servidor MQ estiver publicamente disponível ou disponível no Azure, você não precisará usar o gateway de dados.

  * Para que o conector do MQ funcione, o servidor no qual você instala o gateway de dados local também precisa ter o .NET Framework 4,6 instalado.
  
  * Depois de instalar o gateway de dados local, você também precisará [criar um recurso de gateway do Azure para o gateway de dados local](../logic-apps/logic-apps-gateway-connection.md) que o conector do MQ usa para acessar seu servidor MQ local.

* O aplicativo lógico no qual você deseja usar o conector do MQ. O conector do MQ não tem nenhum gatilho. Portanto, você primeiro deverá adicionar um gatilho ao seu aplicativo lógico. Por exemplo, você pode usar o [gatilho de recorrência](../connectors/connectors-native-recurrence.md). Se ainda não estiver familiarizado com aplicativos lógicos, experimente este [guia de início rápido para criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limitations"></a>Limitações

O conector do MQ não dá suporte ou usa o campo de **formato** da mensagem e não executa nenhuma conversões de conjunto de caracteres. O conector só coloca os dados exibidos no campo de mensagem em uma mensagem JSON e envia a mensagem junto.

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Criar conexão do MQ

Se você ainda não tiver uma conexão do MQ quando adicionar uma ação do MQ, será solicitado a criar a conexão, por exemplo:

![Fornecer informações de conexão](media/connectors-create-api-mq/connection-properties.png)

1. Se você estiver se conectando a um servidor MQ local, selecione **Conectar por meio do gateway de dados local**.

1. Forneça as informações de conexão para seu servidor MQ.

   * Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.

   * Para usar o protocolo TLS ou protocolo SSL (SSL), selecione **habilitar SSL?**.

     O conector do MQ atualmente dá suporte apenas à autenticação do servidor, não autenticação do cliente. Para saber mais, confira [Problemas de conexão e autenticação](#connection-problems).

1. Na seção **Gateway**, siga estas etapas:

   1. Na lista **assinatura** , selecione a assinatura do Azure que está associada ao recurso de gateway do Azure.

   1. Na lista **Gateway de conexão**, selecione o recurso do gateway do Azure que você deseja usar.

1. Quando terminar, selecione **Criar**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemas de conexão e autenticação

Quando seu aplicativo lógico tentar se conectar ao servidor MQ local, você poderá receber esse erro:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se você estiver usando o conector do MQ diretamente no Azure, o servidor MQ precisará usar um certificado emitido por uma [autoridade de certificação](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável.

* Se você estiver usando o gateway de dados local, tente usar um certificado emitido por uma [autoridade de certificação](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável quando possível. No entanto, se essa opção não for possível, você poderá usar um certificado autoassinado, que não é emitido por uma [autoridade de certificação](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável e é considerado menos seguro.

  Para instalar o certificado autoassinado do servidor, você pode usar a ferramenta **Gerenciador de Certificação do Windows** (certmgr.msc). Para esse cenário, no computador local em que o serviço do gateway de dados local está em execução, você precisa instalar o certificado no repositório de certificados do **computador local** no nível **Autoridades de certificação raiz confiáveis**.

  1. No computador em que o serviço do gateway de dados local está em execução, abra o menu Iniciar, localize e selecione **Gerenciar certificados de usuário**.

  1. Depois que a ferramenta do Gerenciador de Certificação do Windows for aberta, vá para a pasta **Certificados – Computador local** >  **Autoridades de certificação raiz confiáveis** e instale o certificado.

     > [!IMPORTANT]
     > Verifique se você instalou o certificado no repositório **Certificados – Computador local** > **Autoridades de certificação raiz confiáveis**.

* O servidor MQ requer que você defina a especificação de codificação que deseja usar para conexões TLS/SSL. No entanto, o SslStream no .NET não permite que você especifique a ordem das especificações de codificação. Para contornar essa limitação, você pode alterar a configuração do servidor do MQ para corresponder à primeira especificação de codificação no conjunto que o conector envia na negociação TLS/SSL.

  Quando você tentar conectar, o servidor MQ registra uma mensagem de evento que indica que a conexão falhou porque a outra extremidade usou a especificação de criptografia incorreta. A mensagem do evento contém a especificação de criptografia que aparece primeiro na lista. Atualize a especificação de criptografia na configuração do canal para corresponder à especificação de criptografia na mensagem do evento.

## <a name="browse-single-message"></a>Procurar única mensagem

1. No aplicativo lógico, no gatilho ou outra ação, selecione **Nova etapa**.

1. Na caixa de pesquisa, insira `mq` e selecione a ação **Procurar mensagem**.

   ![Selecione a ação "Procurar mensagem"](media/connectors-create-api-mq/browse-message.png)

1. Se você ainda não criou uma conexão do MQ, será solicitado a [criar essa conexão](#create-connection).

1. Depois de criar a conexão, configure as propriedades da ação **Procurar mensagem**:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Fila** | Se for diferente da fila especificada na conexão, especifique a fila. |
   | **MessageId**, **CorrelationId**, **GroupId** e outras propriedades | Procurar uma mensagem com base nas diferentes propriedades de mensagem do MQ |
   | **IncludeInfo** | Para incluir informações da mensagem adicionais na saída, selecione **true**. Para omitir informações da mensagem adicionais na saída, selecione **false**. |
   | **Tempo Limite** | Insira um valor para determinar por quanto tempo aguardar até que uma mensagem chegue em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça. |
   |||

   Por exemplo:

   ![Propriedades da ação "Procurar mensagem"](media/connectors-create-api-mq/browse-message-properties.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer. Para testar o aplicativo, selecione **Executar**.

   Após a conclusão da execução, o designer mostra as etapas do fluxo de trabalho e seu status para que você possa revisar a saída.

1. Para exibir os detalhes sobre cada etapa, clique na barra de título da etapa. Para revisar mais informações sobre a saída da etapa, selecione **Mostrar saídas brutas**.

   ![Procurar saída de mensagem](media/connectors-create-api-mq/browse-message-output.png)

   Aqui está um exemplo de saída bruta:

   ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se você definir **IncludeInfo** como **true**, a saída adicional será mostrada:

   ![Procurar informações de inclusão da mensagem](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens retornar da fila. Se **BatchSize** não tiver valor, todas as mensagens serão retornadas. A saída retornada é uma matriz de mensagens.

1. Siga as etapas anteriores, mas adicione a ação **Procurar mensagens**.

1. Se você ainda não criou uma conexão do MQ, será solicitado a [criar essa conexão](#create-connection). Caso contrário, por padrão, a primeira conexão configurada anteriormente será usada. Para criar uma nova conexão, selecione **Alterar conexão**. Ou selecione uma conexão diferente.

1. Forneça as informações para a ação.

1. Salve e execute o aplicativo lógico.

   Depois que o aplicativo lógico concluir a execução, aqui estão alguns exemplos de saída da ação **Procurar mensagens**:

   ![Amostra da saída "Procurar mensagens"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Receber única mensagem

A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Quando você usa **Receber mensagem**, a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Quando você usa **Receber mensagens**, as mensagens são excluídas da fila.

> [!NOTE]
> Quando você executa uma ação de navegação ou de recebimento em uma fila que não tem nenhuma mensagem, a ação falha com esta saída:
>
> ![Erro "nenhuma mensagem" do MQ](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Enviar mensagem

1. Siga as etapas anteriores, mas adicione a ação **Enviar mensagem**.

1. Se você ainda não criou uma conexão do MQ, será solicitado a [criar essa conexão](#create-connection). Caso contrário, por padrão, a primeira conexão configurada anteriormente será usada. Para criar uma nova conexão, selecione **Alterar conexão**. Ou selecione uma conexão diferente.

1. Forneça as informações para a ação. Para **MessageType**, selecione um tipo de mensagem válida: **Datagram**, **Reply** ou **Request**

   ![Propriedades para a ação "Enviar mensagem"](media/connectors-create-api-mq/send-message-properties.png)

1. Salve e execute o aplicativo lógico.

   Depois que o aplicativo lógico concluir a execução, aqui estão alguns exemplos de saída da ação **Enviar mensagem**:

   ![Exemplo de saída "Enviar mensagem"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como ações e limites, que são descritos no arquivo Swagger do conector, examine a [página de referência do conector](/connectors/mq/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
