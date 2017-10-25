---
title: "Registrar conectores personalizados – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Configurar conectores personalizados para uso em Aplicativos Lógicos do Azure"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 2c384e27798bc4920ef73af1084600fdc4047f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Registrar conectores personalizados em Aplicativos Lógicos do Azure

Depois de criar sua API REST, configurar a autenticação e obter o arquivo de definição de OpenAPI ou uma coleção do Postman, você está pronto para registrar seu conector. 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar seu conector personalizado, estes itens são necessários:

* Detalhes para registrar seu conector no Azure, como o nome, assinatura do Azure, grupo de recursos do Azure e o local que você deseja usar

* Um arquivo de OpenAPI (Swagger) ou uma coleção do Postman que descreve sua API

  Para este tutorial, você pode usar o [arquivo OpenAPI do Azure Resource Manager](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Um ícone que representa seu conector

* Uma descrição curta para seu conector

* O local do host para a sua API

## <a name="1-create-your-connector"></a>1. Criar seu conector

1. No portal do Azure, no menu principal do Azure, escolha **Novo**. Na caixa de pesquisa, digite "conector de aplicativos lógicos" como seu filtro e pressione Enter.

   ![Novo, pesquisar por "conector de aplicativos lógicos"](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. Na lista de resultados, escolha **Conector de Aplicativos Lógicos** > **Criar**.

   ![Criar conector de Aplicativos Lógicos](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Forneça detalhes para registrar seu conector conforme descrito na tabela. Quando tiver concluído, escolha **Fixar no painel** > **Criar**.

   ![Detalhes do conector personalizado de Aplicativo Lógico](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Propriedade | Valor sugerido | Descrição | 
   | -------- | --------------- | ----------- | 
   | **Nome** | *custom-connector-name* | Forneça um nome para seu conector. | 
   | **Assinatura** | *Azure-subscription-name* | Selecione sua assinatura do Azure. | 
   | **Grupo de recursos** | *Azure-resource-group-name* | Crie ou selecione um grupo do Azure para organizar seus recursos do Azure. | 
   | **Localidade** | *deployment-region* | Selecione uma região de implantação para o conector. | 
   |||| 

   Depois que o Azure implanta o conector, o menu de conector personalizado é aberto automaticamente. 
   Caso contrário, escolha seu conector personalizado no painel do Azure.

## <a name="2-define-your-connector"></a>2. Definir seu conector

Agora, especifique o arquivo OpenAPI ou a coleção do Postman para criar o conector, a autenticação que seu conector usa, as ações e gatilhos que seu conector personalizado fornece e parâmetros que ações e gatilhos podem utilizar.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Especifique o arquivo OpenAPI ou a coleção do Postman para seu conector

1. No menu do conector, se ainda não estiver selecionado, escolha **Conector de Aplicativos Lógicos**. Na barra de ferramentas, escolha **Editar**.

   ![Editar conector personalizado](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Escolha **Geral** para que você possa fornecer os detalhes nessas tabelas para criar, proteger e definir as ações e gatilhos para o conector personalizado.

   1. Para **Conectores personalizados**, selecione uma opção para que possa fornecer o arquivo OpenAPI (Swagger) que descreve sua API.

      ![Fornecer o arquivo OpenAPI para a sua API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Opção | Formatar |Descrição | 
      | ------ | ------ | ----------- | 
      | **Carregar um arquivo OpenAPI** | *OpenAPI (Swagger)-json-file* | Navegue até o local para o arquivo OpenAPI e selecione o arquivo. | 
      | **Usar uma URL de OpenAPI** | http://*path-to-swagger-json-file* | Forneça a URL para o arquivo OpenAPI para a sua API. | 
      | **Carregar coleção de Postman V1** | *exported-Postman-collection-V1-file* | Navegue até o local para uma coleção do Postman exportada no formato V1. | 
      |||| 

   2. Para **Informações gerais**, carregue um ícone para o conector. 
   Normalmente, os campos de **Descrição**, **Host** e **URL Base** são preenchidos automaticamente de seu arquivo OpenAPI. 
   Mas, se não forem, adicione essas informações conforme descrito na tabela e escolha **Continuar**. 

      ![Detalhes do conector](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Opção ou configuração | Formatar | Descrição | 
      | ----------------- | ------ | ----------- | 
      | **Carregar ícone** | *png-or-jpg-file-under-1-MB* | Um ícone que representa seu conector <p>Cor: de preferência um logotipo branco em uma tela de fundo colorida. <p>Dimensões: Um logotipo de ~ 160 pixels dentro de um quadrado 230 pixels | 
      | **Cor da tela de fundo do ícone** | *icon-brand-color-hexadecimal-code* | <p>A cor atrás de seu ícone que corresponde à cor da tela de fundo em seu arquivo de ícone. <p>Formato: hexadecimal. Por exemplo, #007ee5 representa a cor azul. | 
      | **Descrição** | *connector-description* | Forneça uma descrição curta para seu conector. | 
      | **Host** | *connector-host* | Forneça o domínio do host para a API Web. | 
      | **URL base** | *connector-base-URL* | Forneça a URL base para a API Web. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descrever a autenticação que o conector usa

1. Agora, escolha **Segurança** para que você possa examinar ou descrever a autenticação que o conector usa. A autenticação assegura que as identidades de seus usuários fluam adequadamente entre seu serviço e todos os clientes.

   ![Tipo de autenticação.](./media/logic-apps-custom-connector-register/security.png)

   * Se você carregar um arquivo OpenAPI, o assistente de registro detectará automaticamente o tipo de autenticação que a API Web usa e preenche automaticamente a seção **Segurança** no assistente, com base no objeto `securityDefinitions` nesse arquivo. Por exemplo, aqui está uma seção que especifica o uso de OAuth2.0:

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Se o arquivo OpenAPI não preencher as propriedades e o tipo de autenticação, escolha **Editar** para que você possa adicionar essas informações. 
   
     Por exemplo, se anteriormente você [configurou a autenticação do Azure AD neste tutorial](../logic-apps/custom-connector-azure-active-directory-authentication.md), os aplicativos Azure AD foram criados para proteger sua API Web e o conector. 
     Portanto, agora você pode fornecer a ID do aplicativo e a chave do cliente que salvou anteriormente:
    
     | Configuração | Valor sugerido | Descrição | 
     | ------- | --------------- | ----------- | 
     | **Tipo de autenticação** | OAuth 2.0 | | 
     | **Provedor de identidade** | Azure Active Directory | | 
     | **ID do cliente** | *application-ID-for-connector-Azure-AD-app* | A ID do aplicativo que você salvou anteriormente para seu aplicativo Azure AD do conector | 
     | **Segredo do cliente** | *client-key-for-connector-Azure-AD-app* | A chave do cliente do aplicativo Azure AD do seu conector | 
     | **URL de logon** | `https://login.windows.net` | | 
     | **URL de recursos** | `https://management.core.windows.net/` | Certifique-se de que você adicionou a URL exatamente conforme especificado, incluindo a barra à direita. | 
     |||| 

   * Uma coleção do Postman, que gera automaticamente um arquivo OpenAPI, preenche automaticamente o tipo de autenticação *somente* quando você usa os tipos de autenticação com suporte, como OAuth 2.0 ou Basic.

2. Para salvar seu conector depois de inserir as informações de segurança, na parte superior da página, escolha **Atualizar conector**, em seguida, escolha **Continuar**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Analisar, atualizar ou definir ações e gatilhos para o conector

1. Agora, escolha **Definição** para que você possa examinar, editar ou definir novas ações e gatilhos que os usuários podem adicionar a seus fluxos de trabalho.

   Ações e gatilhos são baseados nas operações definidas em seu arquivo OpenAPI ou coleção do Postman, que preenchem automaticamente a página **Definição** página e inclui os valores de solicitação e resposta. Portanto, se as operações necessárias já aparecerem aqui, vá para a próxima etapa no processo de registro sem fazer alterações nesta página.

   ![Definição de conector](./media/logic-apps-custom-connector-register/definition.png)

2. Opcionalmente, se você quiser editar gatilhos e ações existentes ou adicionar novos, continue com estas etapas.

#### <a name="edit-or-add-actions-for-your-connector"></a>Editar ou adicionar ações para o conector

1. Para editar uma ação existente, escolha o número para essa ação. Para adicionar uma ação que não existia no arquivo OpenAPI ou na coleção do Postman, em **Ações**, escolha **Nova ação**.

2. Em **Geral**, forneça ou edite essas informações para a ação:
   
   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Resumo** | *operation-name* | O título para esta ação | 
   | **Descrição** | *operation-description* | A descrição para esta ação. <p>**Dica**: Certifique-se de que sua descrição termina com um ponto. |
   | **ID da operação** | *operation-identifier* | Um nome exclusivo para identificar essa ação. Use minúsculas concatenadas, por exemplo: "GetPullRequest" | 
   |**Visibilidade**| **nenhum**, **avançado**, **interno** ou **importante** | A visibilidade voltada para o usuário para essa ação. Para saber mais, consulte [Extensões de OpenAPI](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   |||| 

3. Agora, defina a solicitação para a ação.
  
   1. Na seção **Solicitação**, escolha **Importar do exemplo**. 

   2. Na página **Importar do exemplo**, cole uma solicitação de exemplo. 

      Normalmente, solicitações de exemplo estão disponíveis na documentação da API em que você pode obter informações para os campos **Verbo**, **URL**, **Cabeçalhos** e **Corpo**. Por exemplo, consulte a [documentação da API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importar solicitação de exemplo](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Se você criar um conector de uma coleção do Postman, certifique-se de que removeu o cabeçalho `Content-type` de ações e gatilhos. Os Aplicativos Lógicos adicionam automaticamente esse cabeçalho. Além disso, remova os cabeçalhos de autenticação que você definiu na seção `Security` de ações e gatilhos.

      Para recursos avançados de OpenAPI, consulte [Extensões de OpenAPI para conectores personalizados](../logic-apps/custom-connector-openapi-extensions.md).

   3. Para concluir a definição de solicitação, escolha **Importar**.

4. Agora, defina a resposta para a ação.

   1. Em **Resposta**, especifique uma resposta padrão. 
   Escolha **Adicionar resposta padrão**.

   2. Na página **Importar do exemplo**, cole uma resposta de exemplo, em seguida, selecione **Importar**.

5. Por fim, em **Validação**, revise e corrija quaisquer possíveis problemas identificados para a ação.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Editar ou adicionar gatilhos para o conector

1. Para editar um gatilho existente, escolha o número para esse gatilho. Para adicionar um gatilho que não existia no arquivo OpenAPI ou na coleção do Postman, em **Gatilhos**, escolha **Novo gatilho**.

2. Em **Geral**, forneça ou edite estas informações para o gatilho:

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Resumo** | *operation-name* | O título para este gatilho | 
   | **Descrição** | *operation-description* | A descrição para este gatilho. <p>**Dica**: Certifique-se de que sua descrição termina com um ponto. | 
   | **ID da operação** | *operation-identifier* | Um nome exclusivo para identificar esse gatilho. Use minúsculas concatenadas, por exemplo: "TriggerOnGitHubPushEvent" | 
   |**Visibilidade**| **nenhum**, **avançado**, **interno** ou **importante** | A visibilidade voltada para o usuário para esse gatilho. Para saber mais, consulte [Extensões de OpenAPI](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Tipo de gatilho** | **Webhook** ou **Sondagem** | O tipo para este gatilho. Por exemplo, um gatilho de webhook aguarda um evento específico ocorrer antes de acionar. Um gatilho de sondagem verifica regularmente um ponto de extremidade de serviço com base em um intervalo e frequência especificados. <p>Para obter mais informações sobre padrões de gatilhos de webhook e de sondagem, consulte [Criar APIs personalizadas](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Agora, defina a solicitação que cria o gatilho. 

   1. Na seção **Solicitação**, escolha **Importar do exemplo**.

   2. Na página **Importar do exemplo**, cole uma solicitação de exemplo. 

      Normalmente, solicitações de exemplo estão disponíveis na documentação da API em que você pode obter informações para os campos **Verbo**, **URL**, **Cabeçalhos** e **Corpo**. Por exemplo, consulte a [documentação da API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importar solicitação de exemplo](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Se você criar um conector de uma coleção do Postman, certifique-se de que removeu o cabeçalho `Content-type` de ações e gatilhos. Os Aplicativos Lógicos adicionam automaticamente esse cabeçalho. Além disso, remova os cabeçalhos de autenticação que você definiu na seção `Security` de ações e gatilhos.

      Para recursos avançados de OpenAPI, consulte [Extensões de OpenAPI para conectores personalizados](../logic-apps/custom-connector-openapi-extensions.md).

   3. Para concluir a definição de solicitação, escolha **Importar**. 

4. Agora, defina a resposta do gatilho. Na seção **Resposta**, com base no tipo do gatilho, siga estas etapas:

   **Gatilho de webhook**
   1. Em **Resposta de webhook**, escolha **Importar do exemplo**. 

   2. Na página **Importar do exemplo**, cole uma resposta de exemplo, em seguida, selecione **Importar**. Para exibir um exemplo de resposta, consulte o [Referência da API do GitHub para criar um webhook](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. Em **Configuração do gatilho**, selecione um parâmetro a ser usado para a solicitação de criação do webhook. Os Aplicativos Lógicos usam esse valor de parâmetro para preencher a URL de retorno de chamada usada pelo seu serviço para se comunicar com o gatilho.

   **Gatilho de sondagem**
   1. Em **Resposta**, especifique uma resposta padrão. 
   Escolha **Adicionar resposta padrão**.

   2. Na página **Importar do exemplo**, cole uma resposta de exemplo, em seguida, selecione **Importar**.

   3. Em **Configuração do gatilho**, especifique o parâmetro de consulta, o valor passado para o parâmetro e uma *dica de gatilho* que especifica um intervalo de sondagem adequado para a próxima solicitação.

5. Por fim, em **Validação**, revise e corrija quaisquer possíveis problemas identificados para o gatilho.

#### <a name="review-reference-definitions-for-your-connector"></a>Examinar definições de referência para o conector

A seção **Referências** preenche automaticamente de sua descrição da API e descreve os campos de parâmetro a que ações e gatilhos podem fazer referência. 

1. Em **Referências**, escolha o número para a definição de referência que deseja examinar.

2. Em **Nome**, examine ou atualize o nome da definição de referência.

3. Em **Validação**, examine e corrija quaisquer possíveis problemas identificados para a definição de referência.

## <a name="3-finish-creating-your-connector"></a>3. Concluir a criação do seu conector

Quando você estiver pronto, escolha **Criar** para que você possa implantar seu conector. Se estiver atualizando um conector existente, selecione **Atualizar conector**.

Parabéns! Agora, quando você cria um aplicativo lógico, é possível localizar o conector no Designer de Aplicativos Lógico e adicionar esse conector ao seu aplicativo lógico.

![No Designer de Aplicativos Lógicos, localize o conector](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Compartilhar seu conector com outros usuários de Aplicativos Lógicos

Conectores personalizados registrados, mas não certificados funcionam como conectores gerenciados pela Microsoft, mas são visíveis e estão disponíveis *somente* para o autor do conector e usuários que têm o mesmo locatário do Azure Active Directory e a assinatura do Azure para os aplicativos lógicos na região em que esses aplicativos são implantados. Embora o compartilhamento seja opcional, pode haver situações em que você deseja compartilhar seus conectores com outros usuários. 

> [!IMPORTANT]
> Se você compartilhar um conector, outras pessoas poderão começar a depender esse conector. 
> ***Excluir seu conector exclui todas as conexões com ele.***
 
Para compartilhar seu conector com usuários externos fora desses limites, por exemplo, com os usuários de Aplicativos Lógicos, do Flow e do PowerApps, [envie seu conector para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>Perguntas frequentes

**P:** Há algum limite para conectores personalizados? </br>
**R:** Sim, consulte os [limites de conectores personalizados aqui](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obtenha suporte

* Para obter suporte com o desenvolvimento e a integração ou para solicitar recursos que ainda não estão disponíveis no assistente de registro, entre em contato com [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). A Microsoft monitora essa conta quanto a dúvidas e problemas de desenvolvedor e os direciona para a equipe apropriada.

* Para fazer ou responder perguntas ou saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar os Aplicativos Lógicos, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Próximas etapas

* [Opcional: certificar seu conector](../logic-apps/custom-connector-submit-certification.md)
* [Perguntas frequentes do conector personalizado](../logic-apps/custom-connector-faq.md)