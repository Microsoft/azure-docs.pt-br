---
title: Conectar aos sistemas SAP – Aplicativos Lógicos do Azure | Microsoft Docs
description: Como acessar e gerenciar recursos SAP automatizando os fluxos de trabalho com os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 468e73c64037a76da612cba8d6c2e9507dd3ac87
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120139"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure

Este artigo mostra como é possível acessar os recursos do SAP locais de dentro de um aplicativo lógico usando o conector ECC (Componente Central do ERP) do SAP. O conector funciona com sistemas de ECC e S/4 HANA no local. O conector ECC do SAP é compatível com a integração de dados ou de mensagens entre os sistemas baseados no SAP Netweaver por meio do IDoc (Documento Intermediário) ou BAPI (Interface de programação de aplicativo de negócios) ou RFC (Chamada de função remota).

O conector do SAP ECC usa o <a href="https://support.sap.com/en/product/connectors/msnet.html">biblioteca do SAP .NET Connector (NCo)</a> e fornece essas operações ou ações:

- **Enviar para o SAP**: Envie o IDoc ou chame funções BAPI pelo tRFC em sistemas SAP.
- **Receber do SAP**: Receba o IDoc ou chamadas de função BAPI pelo tRFC em sistemas SAP.
- **Gerar esquemas**: Gere esquemas para os artefatos do SAP para IDoc, BAPI ou RFC.

O conector SAP integra-se a sistemas SAP locais por meio do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127). Em cenários de Envio, por exemplo, ao enviar uma mensagem de Aplicativos Lógicos para um sistema SAP, o gateway de dados funciona como um cliente RFC e encaminha as solicitações recebidas de Aplicativos Lógicos para o SAP.
Da mesma forma, em cenários de Recebimento, o gateway de dados funciona como um servidor RFC que recebe solicitações do SAP e encaminha para o Aplicativo lógico. 

Este artigo mostra como criar aplicativos lógicos de exemplo que se integram ao SAP ao abordar os cenários de integração descritos anteriormente.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir com este artigo, são necessário esses itens:

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* O aplicativo lógico de onde você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Seu <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">Servidor de Aplicativos SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">Servidor de Mensagens SAP</a>

* Baixe e instale a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador local. Antes de continuar, verifique se você configurou seu gateway no portal do Azure. O gateway ajuda você a acessar dados e recursos com segurança no local. Para obter mais informações, consulte [Instalar o gateway de dados local para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

* Baixe e instale a biblioteca de cliente mais recente da SAP, que atualmente é <a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">Conector SAP (NCo) 3.0.21.0 para Microsoft .NET Framework 4.0 e Windows de 64 bits (x64)</a>, no mesmo computador do gateway de dados local. Instale esta versão ou uma posterior por estes motivos:

  * Versões anteriores do NCo SAP podem se tornar deadlock quando mais de uma mensagem do IDoc é enviada ao mesmo tempo. 
  Essa condição bloqueia todas as mensagens posteriores enviadas para o destino da SAP, fazendo com que as mensagens atinjam o tempo limite.

  * O gateway de dados local é executado apenas em sistemas de 64 bits. 
  Caso contrário, você obtém um erro de “imagem incorreta” porque o serviço de host do gateway de dados não oferece suporte a assemblies de 32 bits.

  * O serviço de host do gateway de dados e o adaptador SAP da Microsoft usam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que usam o tempo de execução do .NET 4.0 para 4.7.1. 
  O NCo SAP para o .NET Framework 2.0 funciona com processos que usam o tempo de execução do .NET 2.0 para 3.5 e não funciona com o gateway de dados no local mais recente.

* Conteúdo de mensagem que você pode enviar para o servidor SAP, como um arquivo de exemplo IDoc. Este conteúdo deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Enviar para o SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. O aplicativo lógico envia um documento intermediário (IDoc) para um servidor SAP e retorna uma resposta ao solicitante que chamou o aplicativo lógico. 

### <a name="add-http-request-trigger"></a>Adicionar gatilho de solicitação HTTP

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Neste exemplo, você criará um aplicativo lógico com um ponto de extremidade no Azure para seja possível enviar *solicitações HTTP POST* para o aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco, que abre o Designer do Aplicativo Lógico. 

2. Na caixa de pesquisa, digite “solicitação http” como filtro. Na lista de gatilhos, selecione este gatilho: **Solicitação – quando uma solicitação HTTP é recebida**

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Agora salve seu aplicativo lógico para poder gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Clique em **Salvar** na barra de ferramentas do designer. 

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Adicionar ação SAP

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Caso ainda não tenha adicionado um gatilho ao seu aplicativo lógico e deseje acompanhar esse exemplo [adicione o gatilho descrito nesta seção](#add-trigger).

1. No Designer de Aplicativo Lógico, no gatilho, escolha **Nova etapa** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector/add-action.png) 

2. Na caixa de pesquisa, insira "sap" como seu filtro. Na lista de ações, selecione esta ação: **Enviar mensagem ao SAP**
  
   ![Selecionar ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Como alternativa, em vez de pesquisar, escolha a guia **Enterprise** e selecione a ação do SAP.

   ![Selecionar ação de envio do SAP na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Caso contrário, se a conexão já existir, continue a próxima etapa para que possa configurar a ação SAP. 

   **Criar conexão SAP local**

   1. Forneça as informações de conexão para seu servidor SAP. 
   Para a propriedade **Gateway de Dados**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway.

      Se a propriedade **Tipo de Logon** estiver definida como **Servidor de Aplicativos**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

      ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se a propriedade **Tipo de Logon** estiver definida como **Grupo**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias: 

      ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Quando terminar, escolha **Criar**. 
   
      Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto.

4. Agora localize e selecione uma ação do servidor SAP. 

   1. Na caixa **Ação SAP**, escolha o ícone de pasta. 
   Na lista de arquivos, localize e selecione a mensagem do SAP que você deseja usar. 
   Para navegar pela lista, use as setas.

      Este exemplo seleciona um IDoc com o tipo **Ordem**. 

      ![Localizar e selecionar a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se não encontrar a ação desejada, é possível inserir um caminho manualmente, por exemplo:

      ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Forneça o valor da Ação do SAP por meio do editor de expressão. Dessa forma, é possível usar a mesma ação para diferentes tipos de mensagem.

      Para obter mais informações sobre operações IDoc, consulte [Message schemas for IDOC operations](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) (Esquemas de mensagens para operações IDOC).

   2. Clique dentro da caixa **Mensagem de entrada** para que seja exibida a lista de conteúdo dinâmica. 
   Nessa lista, em **Quando uma solicitação HTTP é recebida**, selecione o campo **Corpo**. 

      Esta etapa inclui o conteúdo do corpo do seu gatilho de solicitação HTTP e envia essa saída para o servidor SAP.

      ![Selecionar o campo “Corpo”](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Ao concluir, sua ação SAP será semelhante a este exemplo:

      ![Concluir ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Adicionar ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída de uma ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original. 

1. No Designer do Aplicativo Lógico, na ação SAP, escolha **Nova etapa** > **Adicionar uma ação**.

2. Na caixa de pesquisa, insira “resposta” como filtro. Na lista de ações, selecione esta ação: **Solicitação – Resposta**

3. Clique dentro da caixa **Corpo** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Enviar para a SAP**, selecione o campo **Corpo**. 

   ![Concluir ação SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Salve seu aplicativo lógico. 

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se seu aplicativo lógico já não estiver habilitado, no menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Habilitar**. 

2. Na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Executar**. Essa etapa inicia manualmente o aplicativo lógico.

3. Dispare o aplicativo lógico, enviando uma solicitação HTTP POST para a URL em seu gatilho de solicitação HTTP e inclua o conteúdo da mensagem na solicitação. Para enviar a solicitação, você pode usar uma ferramenta como [Postman](https://www.getpostman.com/apps). 

   Neste artigo, a solicitação envia um arquivo IDoc, que deve estar no formato XML e incluir o namespace para a ação SAP que você está usando, por exemplo: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Depois de enviar a solicitação HTTP, aguarde a resposta do seu aplicativo lógico.

   > [!NOTE]
   > O aplicativo lógico pode atingir o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do [tempo limite da solicitação](./logic-apps-limits-and-config.md). Se essa condição ocorrer, as solicitações podem ser bloqueadas. Para auxílio para diagnosticar problemas, saiba como você pode [verificar e monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns, você criou um aplicativo lógico que pode se comunicar com o servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, é possível explorar outras ações SAP disponíveis, como BAPI e RFC.

## <a name="receive-from-sap"></a>Receber do SAP

Este exemplo usa um aplicativo lógico que dispara ao receber uma mensagem de um sistema SAP. 

### <a name="add-sap-trigger"></a>Adicionar gatilho do SAP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico. 

2. Na caixa de pesquisa, insira "sap" como seu filtro. Na lista de gatilhos, selecione este gatilho: **Quando uma mensagem é recebida do SAP**

   ![Adicionar gatilho do SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Como alternativa, é possível ir até a guia Enterprise e selecionar o gatilho

   ![Adicionar gatilho do SAP da guia ent](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Caso contrário, se a conexão já existir, continue a próxima etapa para que possa configurar a ação SAP. 

   **Criar conexão SAP local**

   1. Forneça as informações de conexão para seu servidor SAP. 
   Para a propriedade **Gateway de Dados**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway.

      Se a propriedade **Tipo de Logon** estiver definida como **Servidor de Aplicativos**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

      ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se a propriedade **Tipo de Logon** estiver definida como **Grupo**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

      ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. Forneça os parâmetros necessários com base na configuração do sistema SAP. 

   Também é possível oferecer uma ou mais ações do SAP. 
   Esta lista de ações especifica as mensagens que o gatilho recebe do servidor SAP por meio do gateway de dados. 
   Uma lista vazia especifica que o gatilho recebe todas as mensagens. 
   Se a lista tiver mais de uma mensagem, o gatilho receberá apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas do seu servidor SAP são rejeitadas pelo gateway.

   É possível selecionar uma ação do SAP no seletor de arquivos:

   ![Adicionar ação do SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou é possível especificar manualmente uma ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Veja um exemplo que mostra como a ação é exibida quando você configura o gatilho para receber mais de uma mensagem.

   ![Exemplo de gatilho](media/logic-apps-using-sap-connector/example-trigger.png)  

   Para obter mais informações sobre a ação do SAP, consulte [Message schemas for IDOC operations](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) (Esquemas de mensagem para operações IDOC)

5. Agora salve seu aplicativo lógico para poder começar a receber mensagens do seu sistema SAP.
Clique em **Salvar** na barra de ferramentas do designer. 

Agora seu aplicativo lógico está pronto para receber mensagens do seu sistema SAP. 

> [!NOTE]
> O gatilho do SAP não é um gatilho de sondagem, mas um gatilho baseado em webhook. O gatilho é chamado do gateway somente quando há uma mensagem; portanto, não é necessária nenhuma sondagem. 

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Para disparar seu aplicativo lógico, envie uma mensagem do sistema SAP.

2. No menu do aplicativo lógico, escolha **Visão geral** e analise o **Histórico de execuções** para ver se há novas execuções em seu aplicativo lógico. 

3. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na seção de saídas de gatilho.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefatos no SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. A ação do SAP envia uma solicitação a um sistema SAP para gerar os esquemas para o IDoc (Documento Intermediário) e o BAPI especificados. Os esquemas retornados na resposta são carregados para uma Conta de integração usando o conector do Azure Resource Manager.

### <a name="add-http-request-trigger"></a>Adicionar gatilho de solicitação HTTP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico. 

2. Na caixa de pesquisa, digite “solicitação http” como filtro. Na lista de gatilhos, selecione este gatilho: **Solicitação – quando uma solicitação HTTP é recebida**

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Agora salve seu aplicativo lógico para poder gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Clique em **Salvar** na barra de ferramentas do designer. 

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Adicionar ação do SAP para gerar esquemas

1. No Designer de Aplicativo Lógico, no gatilho, escolha **Nova etapa** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector/add-action.png) 

2. Na caixa de pesquisa, insira "sap" como seu filtro. Na lista de ações, selecione esta ação: **Gerar esquemas**
  
   ![Selecionar ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Como alternativa, também é possível escolher a guia **Enterprise** e selecionar a ação do SAP.

   ![Selecionar ação de envio do SAP na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Caso contrário, se a conexão já existir, continue a próxima etapa para que possa configurar a ação SAP. 

   **Criar conexão SAP local**

   1. Forneça as informações de conexão para seu servidor SAP. 
   Para a propriedade **Gateway de Dados**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway.

      Se a propriedade **Tipo de Logon** estiver definida como **Servidor de Aplicativos**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

      ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se a propriedade **Tipo de Logon** estiver definida como **Grupo**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:
   
      ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Quando terminar, escolha **Criar**. Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto.

4. Forneça o caminho para o artefato para o qual você deseja gerar o esquema.

   É possível selecionar a ação do SAP no seletor de arquivos:

   ![Adicionar ação do SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou é possível inserir manualmente a ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   Para gerar esquemas para mais de um artefato, forneça os detalhes da ação do SAP para cada artefato, por exemplo:

   ![Selecionar Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   Para obter mais informações sobre a ação do SAP, consulte [Message schemas for IDOC operations](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) (Esquemas de mensagem para operações IDOC).

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, escolha **Executar** para disparar uma execução do seu aplicativo lógico.

2. Abra a execução e verifique as saídas para a ação **Gerar esquema**. 

   As saídas mostram os esquemas gerados para a lista de mensagens especificada.

### <a name="upload-schemas-to-integration-account"></a>Carregar esquemas para a conta de integração

Opcionalmente, é possível baixar ou armazenar os esquemas gerados em repositórios, como um blob, armazenamento ou conta de integração. As contas de integração oferecem uma experiência de primeira classe com outras ações de XML; portanto, este exemplo mostra como carregar esquemas para uma conta de integração para o mesmo aplicativo lógico usando o conector do Azure Resource Manager.

1. No Designer de Aplicativo Lógico, no gatilho, escolha **Nova etapa** > **Adicionar uma ação**. Na caixa de pesquisa, insira "resource manager" como seu filtro. Selecione esta ação: **Criar ou atualizar um recurso**

   ![Selecionar ação do Azure Resource Manager](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Insira os detalhes, incluindo sua assinatura do Azure, grupo de recursos do Azure e conta de integração. Para outros campos, siga o exemplo a seguir.

   ![Insira os detalhes da ação do Azure Resource Manager](media/logic-apps-using-sap-connector/arm-action.png)

   A ação **Gerar esquemas** do SAP gera esquemas como uma coleção, portanto o designer adiciona automaticamente um loop **For each** à ação. 
   Veja um exemplo que mostra como essa ação é exibida:

   ![Ação do Azure Resource Manager com o loop "for each"](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > Os esquemas usam o formato codificado para base64. Para carregar os esquemas para uma conta de integração, eles devem ser decodificados usando a função `base64ToString()`. Veja um exemplo que mostra o código do elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, escolha **Executar** para disparar manualmente seu aplicativo lógico.

2. Após uma execução bem-sucedida, vá para a conta de integração e verifique se os esquemas gerados existem.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

Veja os problemas e limitações atualmente conhecidos para o conector do SAP:

* O gatilho do SAP não dá suporte ao recebimento de IDocs em lote do SAP. Essa ação poderia resultar em falha na conexão RFC entre seu sistema SAP e o gateway de dados.

* O gatilho do SAP não dá suporte a clusters de gateway de dados. Em alguns casos de failover, o nó do gateway de dados que se comunica com o sistema SAP pode ser diferente do nó ativo, resultando em um comportamento inesperado. Para cenários de Envio, há suporte para clusters de gateway de dados.

* Em cenários de Recebimento, não há suporte para retornar uma resposta não nula. Um aplicativo lógico com um gatilho e uma ação de resposta resulta em um comportamento inesperado. 

* Apenas uma única chamada ou mensagem Enviar para SAP funciona com o tRFC. Não há suporte para o padrão de confirmação BAPI (Interface de programação de aplicativo de negócios), como realizar várias chamadas de tRFC na mesma sessão.

* Não há suporte para RFCs com anexos nas ações Enviar para SAP e Gerar esquemas.

* No momento, o conector do SAP não é compatível com cadeia de caracteres do roteador do SAP. O gateway de dados local deve existir na mesma LAN que o sistema SAP que você deseja conectar.

* A conversão para valores ausentes (nulos), vazios, mínimos e máximos para campos DATS e TIMS do SAP está sujeita a alteração em atualizações posteriores para o gateway de dados local.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a sistemas locais](../logic-apps/logic-apps-gateway-connection.md) por meio de aplicativos lógicos
* Saiba como validar, transformar e outras operação de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
