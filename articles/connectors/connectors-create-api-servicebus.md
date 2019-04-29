---
title: Enviar e receber mensagens com o Barramento de Serviço do Azure – Aplicativos Lógicos do Azure | Microsoft Docs
description: Configurar o sistema de mensagens da nuvem empresarial com o Barramento de Serviço do Azure para Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105573"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Trocar mensagens na nuvem com o Barramento de Serviço do Azure e os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Barramento de Serviço do Azure, é possível criar tarefas e fluxos de trabalho automatizados que transferem dados, como ordens de vendas e compra, diários e movimentações de estoque entre aplicativos para sua organização. Além de monitorar, enviar e gerenciar mensagens, o conector também realiza ações com filas, sessões, tópicos, assinaturas e assim por diante; por exemplo:

* Monitorar quando mensagens chegam (preenchimento automático) ou são recebidas (peek-lock) em filas, tópicos e assinaturas de tópicos. 
* Enviar mensagens.
* Criar e excluir assinaturas de tópicos.
* Gerenciar mensagens em filas e assinaturas de tópicos; por exemplo, get, get deferred, complete, defer, abandon e dead-letter.
* Renovar bloqueios em mensagens e sessões nas filas e assinaturas de tópicos.
* Fechar sessões em filas e tópicos.

Você pode usar gatilhos para obter respostas do Barramento de Serviço e disponibilizar a saída para outras ações em seus aplicativos lógicos. Você também pode fazer com que outras ações usem a saída das ações do Barramento de Serviço. Se você for novo em Barramento de Serviço e Aplicativos Lógicos, veja [O que é o Barramento de Serviço do Azure?](../service-bus-messaging/service-bus-messaging-overview.md) e [O que são Aplicativos Lógicos do Azure?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Um namespace de Barramento de Serviço e a entidade de mensagens, como uma fila. Se você não tiver esses itens, aprenda a [criar seu namespace do Barramento de Serviço e uma fila](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Esses itens devem existir na mesma assinatura do Azure que seus aplicativos lógicos que os utilizam.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja usar o Barramento de Serviço. Seu aplicativo lógico deve existir na mesma assinatura do Azure que seu barramento de serviço. Para iniciar com um gatilho do Barramento de Serviço, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Barramento de Serviço, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificar permissões

Confirme se seu aplicativo lógico tem permissões para acessar o namespace do Barramento de Serviço. 

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. Acesse o *namespace* do seu Barramento de Serviço. Na página do namespace, em **Configurações**, selecione **Políticas de acesso compartilhado**. Em **Declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace

   ![Gerenciar permissões para o namespace do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Obtenha a cadeia de conexão para o namespace do Barramento de Serviço. Você precisa dessa cadeia de caracteres quando insere suas informações de conexão no aplicativo lógico.

   1. Selecione **RootManageSharedAccessKey**. 
   
   1. Ao lado de sua cadeia de conexão primária, escolha o botão de cópia. Salve a cadeia de conexão para uso posterior.

      ![Copiar a cadeia de conexão do namespace do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se sua cadeia de conexão está associada ao namespace do Barramento de Serviços ou a uma entidade de sistema de mensagens, como uma fila, pesquise a cadeia de conexão para o parâmetro `EntityPath` . Se encontrar esse parâmetro, a cadeia de conexão servirá para uma entidade específica e não será a cadeia correta a ser usada com seu aplicativo lógico.

## <a name="add-trigger-or-action"></a>Adicionar gatilho ou ação

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para adicionar um *gatilho* para um aplicativo lógico em branco, na caixa de pesquisa, insira "Barramento de Serviço do Azure" como filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   Por exemplo, para acionar o aplicativo lógico quando um novo item for enviado para uma fila do barramento de serviço, selecione este gatilho: **Quando uma mensagem é recebida em uma fila (conclusão automática)**

   ![Selecionar um gatilho do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Alguns gatilhos podem gerar uma mensagem ou mais; por exemplo, o gatilho, **Quando uma ou mais mensagens chegam em uma fila (preenchimento automático)**. Quando esses gatilhos são disparados, eles retornam entre um e o número de mensagens especificado pela propriedade **Contagem máxima de mensagens**.

   *Todos os gatilhos de Barramento de Serviço são gatilhos de sondagem longa*, o que significa que quando um gatilho dispara, o gatilho processa todas as mensagens e aguarda 30 segundos para que mais mensagens apareçam na fila ou na assinatura do tópico. 
   Se nenhuma mensagem aparecer em 30 segundos, a execução do gatilho será ignorada. 
   Caso contrário, o gatilho continuará lendo as mensagens até que a fila ou a assinatura do tópico esteja vazia. A próxima sondagem de gatilho é baseada no intervalo de recorrência especificado nas propriedades do gatilho.

1. Para adicionar um *ação* a um aplicativo lógico existente, siga estas etapas: 

   1. Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

      Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
      Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.

   1. Na caixa de pesquisa, insira "Barramento de Serviço do Azure" como filtro. 
   Na lista de ações, selecione a ação desejada. 
 
      Por exemplo, selecione esta ação: **Enviar mensagem**

      ![Selecionar ação de Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se estiver conectando seu aplicativo lógico ao namespace do seu Barramento de Serviço pela primeira vez, o Designer de Aplicativo Lógico pedirá suas informações de conexão. 

   1. Forneça um nome para sua conexão e selecione o namespace do seu Barramento de Serviço.

      ![Criar uma conexão do Barramento de Serviço, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Para inserir manualmente a cadeia de conexão, escolha **Inserir manualmente as informações de conexão**. 
      Caso não tenha uma cadeia de conexão, aprenda [como localizar sua cadeia de conexão](#permissions-connection-string).

   1. Agora, selecione a política de Barramento de Serviço e escolha **Criar**.

      ![Criar uma conexão do Barramento de Serviço, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Neste exemplo, selecione a entidade de sistema de mensagens desejada, como uma fila ou tópico. Neste exemplo, selecione a fila do seu Barramento de Serviço. 
   
   ![Selecionar fila do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Forneça os detalhes necessários para seu gatilho ou ação. Neste exemplo, siga as etapas relevantes para seu gatilho ou ação: 

   * **Para o gatilho de exemplo**: Defina o intervalo de sondagem e a frequência para verificar a fila.

     ![Configurar intervalo de sondagem](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Quando terminar, continue compilando o fluxo de trabalho do aplicativo lógico, adicionando as ações que deseja. Por exemplo, pode adicionar uma ação que envia um email quando uma nova mensagem chega.
     Quando o gatilho verifica sua fila e localiza uma nova mensagem, seu aplicativo lógico executa as ações selecionadas para a mensagem encontrada.

   * **Para a ação de exemplo**: Insira o conteúdo da mensagem e outros detalhes. 

     ![Fornecer o conteúdo e os detalhes da mensagem](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Quando terminar, continue compilando o fluxo de trabalho do aplicativo lógico, adicionando as outras ações que deseja. Por exemplo, pode adicionar uma ação que envia um email confirmando que sua mensagem foi enviada.

1. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/servicebus/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)