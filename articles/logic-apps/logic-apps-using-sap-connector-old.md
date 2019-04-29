---
title: Conectar aos sistemas SAP – Aplicativos Lógicos do Azure | Microsoft Docs
description: Como acessar e gerenciar recursos SAP automatizando os fluxos de trabalho com os Aplicativos Lógicos do Azure
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60847292"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure

> [!NOTE]
> Este conector SAP está agendada para substituição. Por favor, usar ou migrar para o [conector do SAP mais recente e mais avançado](./logic-apps-using-sap-connector.md). 

Este artigo mostra como é possível acessar os recursos SAP de dentro de um aplicativo lógico usando os conectores do Servidor de Aplicativos SAP e do Servidor de Mensagens SAP. Dessa forma, você pode automatizar tarefas, processos e fluxos de trabalho que gerenciam dados e recursos SAP com a criação de aplicativos lógicos.

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. O aplicativo lógico envia um documento intermediário (IDoc) para um servidor SAP e retorna uma resposta ao solicitante que chamou o aplicativo lógico.
Os conectores SAP atuais têm ações, mas não gatilhos, portanto, este exemplo usa o [gatilho da solicitação HTTP](../connectors/connectors-native-reqres.md) como a primeira etapa no fluxo de trabalho da lógica do aplicativo. Para obter informações técnicas específicas sobre o conector SAP, consulte estes artigos de referência: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Conector do servidor de aplicativos SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Conector do servidor de mensagens SAP</a>

Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir com este artigo, são necessário esses itens:

* O aplicativo lógico de onde você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Atualmente, os conectores SAP fornecem somente as ações. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Seu <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">Servidor de Aplicativos SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">Servidor de Mensagens SAP</a>

* Baixe e instale a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador local. Antes de continuar, verifique se você configurou seu gateway no portal do Azure. O gateway ajuda você a acessar dados e recursos com segurança no local. Para obter mais informações, consulte [Instalar o gateway de dados local para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

* Baixe e instale a biblioteca de cliente mais recente da SAP, que atualmente é <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">Conector SAP (NCo) 3.0.20.0 para Microsoft .NET Framework 4.0 e Windows de 64 bits (x64)</a>, no mesmo computador do gateway de dados local. Instale esta versão ou uma posterior por estes motivos:

  * Versões anteriores do NCo SAP podem se tornar deadlock quando mais de uma mensagem do IDoc é enviada ao mesmo tempo. 
  Essa condição bloqueia todas as mensagens posteriores enviadas para o destino da SAP, fazendo com que as mensagens atinjam o tempo limite.

  * O gateway de dados local é executado apenas em sistemas de 64 bits. 
  Caso contrário, você obtém um erro de “imagem incorreta” porque o serviço de host do gateway de dados não oferece suporte a assemblies de 32 bits.

  * O serviço de host do gateway de dados e o adaptador SAP da Microsoft usam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que usam o tempo de execução do .NET 4.0 para 4.7.1. 
  O NCo SAP para o .NET Framework 2.0 funciona com processos que usam o tempo de execução do .NET 2.0 para 3.5 e não funciona com o gateway de dados no local mais recente.

* Conteúdo de mensagem que você pode enviar para o servidor SAP, como um arquivo de exemplo IDoc. Este conteúdo deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Adicionar gatilho de solicitação HTTP

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Neste exemplo, você criará um aplicativo lógico com um ponto de extremidade no Azure para seja possível enviar *solicitações HTTP POST* para o aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico. 

2. Na caixa de pesquisa, digite “solicitação http” como filtro. Na lista de gatilhos, selecione este gatilho: **Solicitação – quando uma solicitação HTTP é recebida**

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Agora salve seu aplicativo lógico para poder gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Clique em **Salvar** na barra de ferramentas do designer. 

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Adicionar ação SAP

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Caso ainda não tenha adicionado um gatilho ao seu aplicativo lógico e deseje acompanhar esse exemplo [adicione o gatilho descrito nesta seção](#add-trigger).

1. No Designer de Aplicativo Lógico, no gatilho, escolha **Nova etapa** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Na caixa de pesquisa, insira “servidor sap” como filtro. Na lista de ações, selecione a ação do seu servidor SAP: 

   * **Servidor de aplicativos SAP – Enviar para a SAP**
   * **Servidor de mensagens SAP – Enviar para a SAP**

   Este exemplo usa essa ação: **Servidor de aplicativos SAP – Enviar para a SAP**

   ![Selecionar “Servidor de Aplicativos SAP” ou “Servidor de Mensagens SAP”](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Caso contrário, se a conexão já existir, continue a próxima etapa para que possa configurar a ação SAP. 

   **Criar conexão SAP local**

   1. Para **Gateways**, selecione **Conectar por meio do gateway de dados local** para que sejam exibidas as propriedades de conexão local.

   2. Forneça as informações de conexão para seu servidor SAP. 
   Para a propriedade **gateway**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway, por exemplo:

      **Servidor de Aplicativos SAP**

      ![Criar conexão do servidor de aplicativos SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Servidor de Mensagens SAP**

      ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Quando terminar, escolha **Criar**.

      Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto.

4. Agora localize e selecione uma ação do servidor SAP. 

   1. Na caixa **Ação SAP**, escolha o ícone de pasta. 
   Na lista de pastas, localize e selecione a ação que você deseja usar. 

      Este exemplo seleciona a categoria **IDOC** para a ação de IDoc. 

      ![Localizar e selecionar a ação de IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Se não encontrar a ação desejada, é possível inserir um caminho manualmente, por exemplo:

      ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Para obter mais informações sobre operações IDoc, consulte [Esquemas de mensagens para operações IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Clique dentro da caixa **Mensagem de entrada** para que seja exibida a lista de conteúdo dinâmica. 
   Nessa lista, em **Quando uma solicitação HTTP é recebida**, selecione o campo **Corpo**. 

      Esta etapa inclui o conteúdo do corpo do seu gatilho de solicitação HTTP e envia essa saída para o servidor SAP.

      ![Selecionar o campo “Corpo”](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Ao concluir, sua ação SAP será semelhante a este exemplo:

      ![Concluir ação SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Adicionar ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída de uma ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original. 

1. No Designer do Aplicativo Lógico, na ação SAP, escolha **Nova etapa** > **Adicionar uma ação**.

2. Na caixa de pesquisa, insira “resposta” como filtro. Na lista de ações, selecione esta ação: **Solicitação – Resposta**

3. Clique dentro da caixa **Corpo** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Enviar para a SAP**, selecione o campo **Corpo**. 

   ![Concluir ação SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Salve seu aplicativo lógico. 

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se seu aplicativo lógico já não estiver habilitado, no menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Habilitar**. 

2. Na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Executar**. Essa etapa inicia manualmente o aplicativo lógico.

3. Dispare o aplicativo lógico, enviando uma solicitação HTTP POST para a URL em seu gatilho de solicitação HTTP e inclua o conteúdo da mensagem na solicitação. Para enviar a solicitação, você pode usar uma ferramenta como [Postman](https://www.getpostman.com/apps). 

   Neste artigo, a solicitação envia um arquivo IDoc, que deve estar no formato XML e incluir o namespace para a ação SAP que você está usando, por exemplo: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Depois de enviar a solicitação HTTP, aguarde a resposta do seu aplicativo lógico.

> [!NOTE]
> O aplicativo lógico pode atingir o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do [tempo limite da solicitação](./logic-apps-limits-and-config.md). Se essa condição ocorrer, as solicitações podem ser bloqueadas. Para auxílio para diagnosticar problemas, saiba como você pode [verificar e monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns, você criou um aplicativo lógico que pode se comunicar com o servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, é possível explorar outras ações SAP disponíveis, como BAPI e RFC.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre o conector conforme descrito pelos arquivos do Swagger dos conectores, consulte estes artigos de referência: 

* [Servidor de Aplicativos SAP](/connectors/sapapplicationserver/)
* [Servidor de Mensagens SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a sistemas locais](../logic-apps/logic-apps-gateway-connection.md) por meio de aplicativos lógicos
* Saiba como validar, transformar e outras operação de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
