---
title: Conectar-se a sistemas SAP-aplicativos lógicos do Azure
description: Acesse e gerencie recursos do SAP automatizando fluxos de trabalho com aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: 9e46c51ae06920bd57f272248f06020dfad380e7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326721"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure

Este artigo mostra como você pode acessar seus recursos SAP locais de dentro de um aplicativo lógico usando o conector SAP. O conector funciona com as versões clássicas do SAP, como sistemas de R/3 e ECC locais. O conector também habilita a integração com os sistemas SAP mais recentes baseados em HANA da SAP, como S/4 HANA, se eles estão hospedados localmente ou na nuvem. O conector SAP dá suporte à integração de mensagens ou de dados de e para sistemas baseados no SAP NetWeaver por meio de IDoc (documento intermediário), BAPI (interface de programação de aplicativo de negócios) ou RFC (chamada de função remota).

O conector SAP usa a [biblioteca do NCo (conector do SAP .net)](https://support.sap.com/en/product/connectors/msnet.html) e fornece estas operações ou ações:

* **Enviar para o SAP**: Envie IDoc sobre tRFC, chame funções BAPI sobre a RFC ou chame RFC/tRFC em sistemas SAP.
* **Receber do SAP**: Receba IDoc sobre tRFC, chame funções BAPI em tRFC ou chame RFC/tRFC em sistemas SAP.
* **Gerar esquemas**: Gere esquemas para os artefatos do SAP para IDoc, BAPI ou RFC.

Para essas operações, o conector SAP dá suporte à autenticação básica por meio de nomes de acessações e senhas. O conector também dá suporte a [SNC (comunicações de rede segura)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). O SNC pode ser usado para SSO (logon único) do SAP NetWeaver ou para recursos de segurança adicionais fornecidos por um produto de segurança externo.

O conector SAP integra-se a sistemas SAP locais por meio do [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Em cenários de envio, por exemplo, quando uma mensagem é enviada de um aplicativo lógico para um sistema SAP, o gateway de dados atua como um cliente RFC e encaminha as solicitações recebidas do aplicativo lógico para o SAP. Da mesma forma, em cenários de recebimento, o gateway de dados atua como um servidor RFC que recebe solicitações do SAP e as encaminha para o aplicativo lógico.

Este artigo mostra como criar aplicativos lógicos de exemplo que se integram ao SAP ao abordar os cenários de integração descritos anteriormente.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir com este artigo, são necessário esses itens:

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico de onde você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Se você for novo em aplicativos lógicos, consulte [o que é o aplicativo lógico do Azure?](../logic-apps/logic-apps-overview.md) e [o início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Servidor de aplicativos SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [servidor de mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Baixe e instale a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador local. Antes de continuar, verifique se você configurou seu gateway no portal do Azure. O gateway ajuda você a acessar com segurança os dados e recursos locais. Para obter mais informações, consulte [instalar um gateway de dados local para aplicativos lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

* Se você usar o SNC com SSO, verifique se o gateway está sendo executado como um usuário mapeado no usuário SAP. Para alterar a conta padrão, selecione **alterar conta**e insira as credenciais do usuário.

  ![Alterar conta de gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se você habilitar o SNC com um produto de segurança externo, copie a biblioteca de SNC ou os arquivos no mesmo computador em que o gateway está instalado. Alguns exemplos de produtos do SNC incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

* Baixe e instale a biblioteca de cliente SAP mais recente, que é atualmente o [conector SAP (NCo 3,0) para Microsoft .net 3.0.22.0 compilado com .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), no mesmo computador que o gateway de dados local. Instale esta versão ou uma posterior por estes motivos:

  * Versões anteriores do SAP NCo podem ser bloqueadas quando mais de uma mensagem IDoc é enviada ao mesmo tempo. Essa condição bloqueia todas as mensagens posteriores enviadas ao destino SAP, o que faz com que as mensagens expirem o tempo limite.
  
  * O gateway de dados local é executado apenas em sistemas de 64 bits. Caso contrário, você obtém um erro de “imagem incorreta” porque o serviço de host do gateway de dados não oferece suporte a assemblies de 32 bits.
  
  * O serviço de host do gateway de dados e o adaptador SAP da Microsoft usam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que usam o tempo de execução do .NET 4.0 para 4.7.1. O SAP NCo para .NET Framework 2,0 funciona com processos que usam o tempo de execução do .NET 2,0 para 3,5, mas não funciona mais com o gateway de dados local mais recente.

* O conteúdo da mensagem que você pode enviar para o servidor SAP, como um arquivo IDoc de exemplo, deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Enviar para o SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. O aplicativo lógico envia um IDoc para um servidor SAP e retorna uma resposta para o solicitante que chamou o aplicativo lógico. 

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho de solicitação HTTP

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Neste exemplo, você criará um aplicativo lógico com um ponto de extremidade no Azure para seja possível enviar *solicitações HTTP POST* para o aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco, que abre o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, digite “solicitação http” como filtro. Na lista  de gatilhos, selecione **quando uma solicitação HTTP é recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Agora, salve seu aplicativo lógico para que você possa gerar uma URL de ponto de extremidade para seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicionar uma ação SAP

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Caso ainda não tenha adicionado um gatilho ao seu aplicativo lógico e deseje acompanhar esse exemplo [adicione o gatilho descrito nesta seção](#add-trigger).

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

   ![Selecione "nova etapa"](./media/logic-apps-using-sap-connector/add-action.png)

1. Na caixa de pesquisa, insira "sap" como seu filtro. Na lista **ações** , selecione **Enviar mensagem para SAP**.
  
   ![Selecionar ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou, em vez de Pesquisar, escolha a guia **Enterprise** e selecione a ação SAP.

   ![Selecionar ação de envio do SAP na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Caso contrário, se a conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP.

   **Criar uma conexão SAP local**

    1. Forneça as informações de conexão para seu servidor SAP. Para a propriedade **Gateway de Dados**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway.

         - Se a propriedade **Tipo de Logon** estiver definida como **Servidor de Aplicativos**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

            ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

         - Se a propriedade **Tipo de Logon** estiver definida como **Grupo**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

            ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

           Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

    1. Quando tiver terminado, selecione **criar**.

       Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Agora localize e selecione uma ação do servidor SAP.

    1. Na caixa **ação SAP** , selecione o ícone de pasta. Na lista de arquivos, localize e selecione a mensagem do SAP que você deseja usar. Para navegar pela lista, use as setas.

       Este exemplo seleciona um IDoc com o  tipo Orders.

       ![Localizar e selecionar a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

       Se não encontrar a ação desejada, é possível inserir um caminho manualmente, por exemplo:

       ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

       > [!TIP]
       > Forneça o valor para a **ação do SAP** por meio do editor de expressão. Dessa forma, você pode usar a mesma ação para tipos de mensagens diferentes.

       Para obter mais informações sobre operações IDoc, consulte [Message schemas for IDOC operations](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) (Esquemas de mensagens para operações IDOC).

    1. Clique dentro da caixa **Mensagem de entrada** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Quando uma solicitação HTTP é recebida**, selecione o campo **Corpo**.

       Esta etapa inclui o conteúdo do corpo do gatilho de solicitação HTTP e envia essa saída para o servidor SAP.

       ![Selecionar o campo “Corpo”](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

       Quando terminar, sua ação SAP será parecida com este exemplo:

       ![Concluir ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Salve seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicionar uma ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída de uma ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original.

1. No designer de aplicativo lógico, na ação SAP, selecione **nova etapa**.

1. Na caixa de pesquisa, insira “resposta” como filtro. Na lista **ações** , selecione **resposta**.

1. Clique dentro da caixa **Corpo** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Enviar mensagem para SAP**, selecione o campo **corpo** .

   ![Concluir ação SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Salve seu aplicativo lógico.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se seu aplicativo lógico ainda não estiver habilitado, no menu do aplicativo lógico, selecione **visão geral**. Na barra de ferramentas, selecione **habilitar**.

1. Na barra de ferramentas do designer, selecione **executar**. Essa etapa inicia manualmente o aplicativo lógico.

1. Dispare seu aplicativo lógico enviando uma solicitação HTTP POST para a URL em seu gatilho de solicitação HTTP.
Inclua o conteúdo da mensagem com sua solicitação. Para enviar a solicitação, você pode usar uma ferramenta como [Postman](https://www.getpostman.com/apps).

   Neste artigo, a solicitação envia um arquivo IDoc, que deve estar no formato XML e incluir o namespace para a ação SAP que você está usando, por exemplo:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Depois de enviar a solicitação HTTP, aguarde a resposta do seu aplicativo lógico.

   > [!NOTE]
   > O aplicativo lógico pode atingir o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do [tempo limite da solicitação](./logic-apps-limits-and-config.md). Se essa condição ocorrer, as solicitações podem ser bloqueadas. Para auxílio para diagnosticar problemas, saiba como você pode [verificar e monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Agora você criou um aplicativo lógico que pode se comunicar com o servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, é possível explorar outras ações SAP disponíveis, como BAPI e RFC.

## <a name="receive-from-sap"></a>Receber do SAP

Este exemplo usa um aplicativo lógico que é disparado quando o aplicativo recebe uma mensagem de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicionar um gatilho SAP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, insira "sap" como seu filtro. Na lista  de gatilhos, selecione **quando uma mensagem é recebida do SAP**.

   ![Adicionar gatilho do SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Ou, você pode ir para a guia **Enterprise** e selecionar o gatilho:

   ![Adicionar gatilho SAP da guia Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP.

   **Criar uma conexão SAP local**

   - Forneça as informações de conexão para seu servidor SAP. Para a propriedade **Gateway de Dados**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway.

      - Se a propriedade **Tipo de Logon** estiver definida como **Servidor de Aplicativos**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

         ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Se a propriedade **Tipo de Logon** estiver definida como **Grupo**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

          ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

1. Forneça os parâmetros necessários com base na configuração do sistema SAP.

   Também é possível oferecer uma ou mais ações do SAP. Esta lista de ações especifica as mensagens que o gatilho recebe do servidor SAP por meio do gateway de dados. Uma lista vazia especifica que o gatilho recebe todas as mensagens. Se a lista tiver mais de uma mensagem, o gatilho receberá apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas do seu servidor SAP são rejeitadas pelo gateway.

   É possível selecionar uma ação do SAP no seletor de arquivos:

   ![Adicionar ação do SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou é possível especificar manualmente uma ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Veja um exemplo que mostra como a ação é exibida quando você configura o gatilho para receber mais de uma mensagem.

   ![Exemplo de gatilho](media/logic-apps-using-sap-connector/example-trigger.png)  

   Para obter mais informações sobre a ação do SAP, consulte [Message schemas for IDOC operations](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) (Esquemas de mensagem para operações IDOC)

1. Agora salve seu aplicativo lógico para poder começar a receber mensagens do seu sistema SAP.
Na barra de ferramentas do designer, selecione **salvar**.

Agora seu aplicativo lógico está pronto para receber mensagens do seu sistema SAP.

> [!NOTE]
> O gatilho SAP não é um gatilho de sondagem, mas é um gatilho baseado em webhook. O gatilho é chamado do gateway somente quando há uma mensagem; portanto, não é necessária nenhuma sondagem.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Para disparar seu aplicativo lógico, envie uma mensagem do sistema SAP.

1. No menu do aplicativo lógico, selecione **visão geral**. Examine o **histórico** de execuções para qualquer nova execução para seu aplicativo lógico.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na seção de saídas de gatilho.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefatos no SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. A ação do SAP envia uma solicitação para um sistema SAP para gerar os esquemas para IDoc e BAPI especificados. Os esquemas que retornam na resposta são carregados em uma conta de integração usando o conector de Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho de solicitação HTTP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, digite “solicitação http” como filtro. Na lista  de gatilhos, selecione **quando uma solicitação HTTP é recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Agora salve seu aplicativo lógico para poder gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Na barra de ferramentas do designer, selecione **salvar**.

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicionar uma ação SAP para gerar esquemas

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

   ![Selecione "nova etapa"](./media/logic-apps-using-sap-connector/add-action.png)

1. Na caixa de pesquisa, insira "sap" como seu filtro. Na lista **ações** , selecione **gerar esquemas**.
  
   ![Selecionar ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ou, você também pode escolher a guia **Enterprise** e selecionar a ação SAP.

   ![Selecionar ação de envio do SAP na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se forem solicitados os detalhes da conexão, crie sua conexão SAP agora. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP.

   **Criar uma conexão SAP local**

   1. Forneça as informações de conexão para seu servidor SAP. Para a propriedade **Gateway de Dados**, selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway.

      - Se a propriedade **Tipo de Logon** estiver definida como **Servidor de Aplicativos**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Se a propriedade **Tipo de Logon** estiver definida como **Grupo**, estas propriedades, que geralmente aparecem como opcionais, serão obrigatórias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**. 
   
      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Forneça o caminho para o artefato para o qual você deseja gerar o esquema.

   É possível selecionar a ação do SAP no seletor de arquivos:

   ![Adicionar ação do SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou é possível inserir manualmente a ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemas para mais de um artefato, forneça os detalhes da ação do SAP para cada artefato, por exemplo:

   ![Selecionar Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagem para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Salve seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, selecione **executar** para disparar uma execução para seu aplicativo lógico.

1. Abra a execução e verifique as saídas para a ação **gerar esquemas** .

   As saídas mostram os esquemas gerados para a lista de mensagens especificada.

### <a name="upload-schemas-to-an-integration-account"></a>Carregar esquemas para uma conta de integração

Opcionalmente, é possível baixar ou armazenar os esquemas gerados em repositórios, como um blob, armazenamento ou conta de integração. As contas de integração oferecem uma experiência de primeira classe com outras ações de XML; portanto, este exemplo mostra como carregar esquemas para uma conta de integração para o mesmo aplicativo lógico usando o conector do Azure Resource Manager.

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

1. Na caixa de pesquisa, digite "Resource Manager" como seu filtro. Selecione **criar ou atualizar um recurso**.

   ![Selecionar ação do Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Insira os detalhes da ação, incluindo sua assinatura do Azure, grupo de recursos do Azure e conta de integração. Para adicionar os tokens do SAP aos campos, clique dentro das caixas para esses campos e selecione na lista de conteúdo dinâmico que aparece.

   1. Abra a lista **Adicionar novo parâmetro** e selecione os campos **local** e **Propriedades** .

   1. Forneça detalhes para esses novos campos, conforme mostrado neste exemplo.

      ![Insira os detalhes da ação do Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   A ação **Gerar esquemas** do SAP gera esquemas como uma coleção, portanto o designer adiciona automaticamente um loop **For each** à ação. Veja um exemplo que mostra como essa ação é exibida:

   ![Ação do Azure Resource Manager com o loop "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  
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

1. Salve seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, selecione **executar** para disparar manualmente seu aplicativo lógico.

1. Após uma execução bem-sucedida, vá para a conta de integração e verifique se os esquemas gerados existem.

## <a name="enable-secure-network-communications"></a>Habilitar comunicações de rede seguras

Antes de começar, certifique-se de que você atende aos [pré-requisitos](#pre-reqs)listados anteriormente:

* O gateway de dados local é instalado em um computador que está na mesma rede que o seu sistema SAP.
* Para o SSO, o gateway é executado como um usuário que é mapeado para um usuário SAP.
* A biblioteca SNC que fornece as funções de segurança adicionais é instalada no mesmo computador que o gateway de dados. Alguns exemplos incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

   Para habilitar o SNC para suas solicitações de ou para o sistema SAP, marque a caixa de seleção **usar SNC** na conexão SAP e forneça estas propriedades:

   ![Configurar o SAP SNC na conexão](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | DESCRIÇÃO |
   |----------| ------------|
   | **Caminho da biblioteca SNC** | O nome ou caminho da biblioteca SNC relativo ao local de instalação do NCo ou ao caminho absoluto. Os exemplos `sapsnc.dll` são `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll`ou. |
   | **SSO DO SNC** | Quando você se conecta por meio do SNC, a identidade da SNC normalmente é usada para autenticar o chamador. Outra opção é substituir para que as informações de usuário e senha possam ser usadas para autenticar o chamador, mas a linha ainda está criptografada. |
   | **Meu nome do SNC** | Na maioria dos casos, essa propriedade pode ser omitida. A solução SNC instalada geralmente sabe seu próprio nome de SNC. Somente para soluções que dão suporte a várias identidades, talvez seja necessário especificar a identidade a ser usada para esse destino ou servidor específico. |
   | **Nome do parceiro SNC** | O nome da SNC de back-end. |
   | **Qualidade de proteção da SNC** | A qualidade do serviço a ser usada para comunicação de SNC desse servidor ou destino específico. O valor padrão é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança usado para a SNC. |
   |||

   > [!NOTE]
   > Não defina as variáveis de ambiente SNC_LIB e SNC_LIB_64 no computador em que você tem o gateway de dados e a biblioteca SNC. Se definido, eles terão precedência sobre o valor da biblioteca SNC passado pelo conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Digitação segura

Por padrão, quando você cria sua conexão SAP, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Se você escolher **a digitação segura**, o tipo de DATs e o tipo TIMS no SAP serão tratados como cadeias de caracteres em `xs:date` vez de como `xmlns:xs="http://www.w3.org/2001/XMLSchema"`seus equivalentes XML e `xs:time`, em que. A digitação segura afeta o comportamento de toda a geração de esquema, a mensagem de envio para a carga "enviada" e a resposta "foi recebida" e o gatilho. 

Quando a digitação forte é usada (a**digitação segura** não está habilitada), o esquema MAPEIA os DATs e os tipos TIMS para tipos XML mais diretos:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando você envia mensagens usando tipagem forte, os DATS e a resposta TIMS estão em conformidade com o formato de tipo XML correspondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando a **digitação segura** está habilitada, o esquema MAPEIA os DATs e os tipos TIMS para campos de cadeia de caracteres XML com restrições de comprimento apenas, por exemplo:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Quando as mensagens são enviadas com a **digitação segura** habilitada, a resposta de DATs e TIMS é semelhante a este exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

Veja os problemas e limitações atualmente conhecidos para o conector do SAP:

* Apenas uma única chamada de envio para SAP ou mensagem funciona com tRFC. O padrão de confirmação BAPI, como fazer várias chamadas tRFC na mesma sessão, não é suportado.

* O gatilho SAP não dá suporte ao recebimento de IDocs do lote do SAP. Essa ação poderia resultar em falha na conexão RFC entre seu sistema SAP e o gateway de dados.

* O gatilho do SAP não dá suporte a clusters de gateway de dados. Em alguns casos de failover, o nó do gateway de dados que se comunica com o sistema SAP pode ser diferente do nó ativo, o que resulta em um comportamento inesperado. Para cenários de envio, há suporte para clusters de gateway de dados.

* No momento, o conector do SAP não é compatível com cadeia de caracteres do roteador do SAP. O gateway de dados local deve existir na mesma LAN que o sistema SAP que você deseja conectar.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/sap/).

## <a name="next-steps"></a>Próximas etapas

* [Conecte-se a sistemas locais](../logic-apps/logic-apps-gateway-connection.md) de aplicativos lógicos do Azure.
* Saiba como validar, transformar e usar outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md).
