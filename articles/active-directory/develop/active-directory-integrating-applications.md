---
title: Integrar aplicativos com Azure Active Directory
description: Como adicionar, atualizar ou remover um aplicativo no Azure AD (Azure Active Directory).
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: d9816f53e1d0e09f938452a022c75a3d20a43da4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrando aplicativos com o Active Directory do Azure
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) podem desenvolver serviços de nuvem comerciais ou aplicativos de linha de negócios que podem ser integrados ao Azure AD (Azure Active Directory) para fornecer conexão segura e autorização para seus serviços. Para integrar um aplicativo ou serviço ao Azure AD, um desenvolvedor deve primeiro registrar o aplicativo no Azure AD.

Este artigo mostra como adicionar, atualizar ou remover um registro de aplicativo no Azure AD. Você aprenderá sobre os diferentes tipos de aplicativo que podem ser integrados ao Azure AD, como configurar seus aplicativos para acessar outros recursos, como APIs Web e muito mais.

Para saber mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de entidade de serviço e aplicativo](active-directory-application-objects.md); para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory, confira [Diretrizes de identidade visual para aplicativos](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Adicionando um aplicativo
Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, primeiramente é preciso registrá-lo em um locatário do AD do Azure. Esse processo de registro envolve fornecer ao AD do Azure detalhes sobre seu aplicativo, como a URL em que ele está localizado, a URL à qual enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo, etc.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Para registrar um novo aplicativo usando o portal do Azure
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** e clique em **Novo registro de aplicativo**.

   ![Registrar um novo aplicativo](./media/active-directory-integrating-applications/add-app-registration.png)

4. Quando a página **Criar** for exibida, insira as informações de registro do aplicativo: 

  - **Nome:** insira um nome significativo de aplicativo
  - **Tipo de Aplicativo:** 
    - Selecione "Nativo" para [aplicativos cliente](active-directory-dev-glossary.md#client-application) que são instalados localmente em um dispositivo. Essa configuração é usada para [clientes nativos](active-directory-dev-glossary.md#native-client) públicos OAuth.
    - Selecione "aplicativo Web/API" para [aplicativos cliente](active-directory-dev-glossary.md#client-application) e [aplicativos de API/recursos](active-directory-dev-glossary.md#resource-server) que estão instalados em um servidor seguro. Essa configuração é usada para [clientes Web](active-directory-dev-glossary.md#web-client) confidenciais OAuth e [clientes baseados em agente de usuário](active-directory-dev-glossary.md#user-agent-based-client) públicos. O mesmo aplicativo também pode expor um cliente e o recurso/API.
  - **URL de logon:** para aplicativos do tipo "Aplicativo Web/API", informe a URL base do seu aplicativo. Por exemplo, `http://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web. 
  - **URI de redirecionamento:** para aplicativos “Nativos”, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo, `http://MyFirstAADApp`

   ![Registrar um novo aplicativo – criar](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Se você desejar exemplos específicos para aplicativos da Web ou aplicativos nativos, confira nossos [inícios rápidos](active-directory-developers-guide.md#get-started).

5. Ao terminar, clique em **Criar**. O Azure AD atribui uma ID exclusiva do aplicativo para seu aplicativo e você é levado à página de registro principal de seu aplicativo. Dependendo de se o seu aplicativo é Web ou nativo, diferentes opções serão fornecidas para adicionar mais recursos ao aplicativo. Consulte a próxima seção para obter uma visão geral de consentimento e detalhes sobre como habilitar recursos adicionais de configuração em seu registro de aplicativo (credenciais, permissões, habilitar entrada para usuários de outros locatários).

  > [!NOTE]
  > Por padrão, o aplicativo recém-registrado é configurado para permitir que **somente** usuários do mesmo locatário entrem no seu aplicativo.
  > 
  > 

## <a name="updating-an-application"></a>Atualizando um aplicativo
Depois que o aplicativo tiver sido registrado no AD do Azure, talvez ele tenha que ser atualizado para fornecer acesso a APIs Web, disponibilizado para outras organizações e muito mais. Esta seção descreve várias maneiras de configurar ainda mais seu aplicativo. Primeiro, vamos começar com uma visão geral da estrutura de consentimento, que é importante entender ao criar aplicativos que precisam ser usados por outros usuários ou aplicativos.

### <a name="overview-of-the-consent-framework"></a>Visão geral da estrutura de consentimento

A estrutura de consentimento do Azure AD facilita desenvolver aplicativos cliente nativos e Web multilocatário, incluindo aplicativos de várias camadas. Esses aplicativos permitem a entrada de contas de usuário de um locatário do Azure AD diferente daquele no qual o aplicativo está registrado. Também poderão precisar acessar APIs da Web, como a API do Microsoft Graph (para acessar o Azure Active Directory, o Intune e serviços no Office 365) e outras APIs de serviços da Microsoft, além das suas próprias APIs da Web. A estrutura se baseia em um usuário ou administrador que dá autorização a um aplicativo que solicita seu registro no diretório, o que pode envolver acesso aos dados do diretório.

Por exemplo, se um aplicativo cliente da Web precisar ler informações de calendário sobre o usuário do Office 365, esse usuário precisará dar consentimento ao aplicativo cliente primeiro. Depois que o autorização for dado, o aplicativo cliente poderá chamar a API do Microsoft Graph em nome do usuário e usar as informações de calendário conforme a necessidade. A [API do Graph da Microsoft](https://graph.microsoft.io) fornece acesso aos dados do Office 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do OneDrive, blocos de anotações do OneNote, tarefas do Planner, pastas de trabalho do Excel etc.), bem como usuários e grupos do Azure AD e outros objetos de dados de outros serviços do Microsoft Cloud. 

A estrutura de consentimento foi criada com base no OAuth 2.0 e seus vários fluxos, como concessão de código de autorização e concessão de credenciais de cliente, usando clientes públicos ou confidenciais. Com o OAuth 2.0, o AD do Azure permite criar muitos tipos diferentes de aplicativo cliente, como em um telefone, tablet, servidor, ou um aplicativo Web, assim como obter acesso aos recursos necessários.

Para obter mais informações sobre como usar a estrutura de consentimento com concessões de autorização do OAuth2.0, consulte [Autorizar o acesso a aplicativos Web usando OAuth 2.0 e Azure AD](active-directory-protocols-oauth-code.md) e [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md). Para obter informações sobre como obter acesso autorizado ao Office 365 por meio do Microsoft Graph, consulte [Autenticação do aplicativo com o Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Exemplo da experiência de consentimento

As etapas a seguir mostram como a experiência de consentimento funciona para o desenvolvedor e o usuário do aplicativo.

1. Suponha que você tenha um aplicativo cliente Web que precise solicitar permissões específicas para acessar uma recurso/API. Você aprenderá como fazer essa configuração na próxima seção, mas o portal do Azure é usado essencialmente para declarar as solicitações de permissão no momento da configuração. Como outras definições de configuração, elas se tornam parte do registro do Azure AD do aplicativo:
   
  ![Permissões para outros aplicativos](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Pense na possibilidade de que as permissões do aplicativo tenham sido atualizadas, de que o aplicativo esteja em execução e de que um usuário esteja prestes a usá-lo pela primeira vez. Primeiro, o aplicativo deve obter um código de autorização do ponto de extremidade `/authorize` do Azure AD. O código de autorização então pode ser usado para adquirir um novo acesso e token de atualização.

3. Se o usuário ainda não tiver sido autenticado, o ponto de extremidade `/authorize` do Azure AD solicitará o logon.
   
  ![Conexão do usuário ou administrador no AD do Azure](./media/active-directory-integrating-applications/usersignin.png)

4. Depois que o usuário tiver se conectado, o AD do Azure determinará se o usuário precisará ver uma página de consentimento. Essa decisão depende do fato de o usuário (ou do administrador da organização) já ter dado o consentimento de aplicativo. Se o consentimento ainda não tiver sido dado, o Azure AD solicitará o consentimento ao usuário e exibirá as permissões necessárias de que ele precisa para funcionar. O conjunto de permissões exibido na caixa de diálogo de autorização corresponde àqueles selecionados nas Permissões Delegadas no portal do Azure.
   
  ![Experiência de consentimento do usuário](./media/active-directory-integrating-applications/consent.png)

5. Depois que o usuário dá consentimento, um código de autorização é retornado ao aplicativo, que é resgatado para aquisição de um token de acesso e de um token de atualização. Para obter mais informações sobre esse fluxo, consulte a seção [aplicativo Web para API Web em Cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Como administrador, você pode também concorda com permissões do aplicativo em nome de todos os usuários em seu locatário. O consentimento administrativo impede que a caixa de diálogo de consentimento apareça para cada usuário no locatário e é feito na página do aplicativo no [portal do Azure](https://portal.azure.com). Na página **Configurações** para seu aplicativo, clique em **Permissões Necessárias** e clique no botão **Conceder Permissões**. 

  ![Conceda permissões para consentimento explícito de admin](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > A concessão explícita de consentimento usando o botão **Conceder Permissões** é necessária no momento para SPAs (aplicativos de página única) que usam o ADAL.js. Caso contrário, o aplicativo falhará quando o token de acesso for solicitado.   

### <a name="configure-a-client-application-to-access-web-apis"></a>Configure um aplicativo cliente para acessar APIs Web
Para que um aplicativo cliente Web/confidencial possa participar de um fluxo de concessão de autorização que requer autenticação (e possa obter um token de acesso), ele deverá estabelecer credenciais seguras. O método de autenticação padrão com suporte no portal do Azure é a ID do Cliente + chave secreta. Esta seção aborda as etapas de configuração necessárias para fornecer a chave secreta das credenciais do seu cliente.

Além disso, antes que um cliente possa acessar uma API Web exposta por um aplicativo de recurso (como a API do Microsoft Graph), a estrutura de autorização garante que o cliente obtenha a concessão de permissão necessária, com base nas permissões solicitadas. Por padrão, todos os aplicativos podem escolher permissões em "Microsoft Azure Active Directory" (API do Graph) e "API de Gerenciamento de Serviços do Microsoft Azure". A [permissão de "Entrar e ler perfil do usuário" da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) também é selecionada por padrão. Se o cliente estiver sendo registrado em um locatário que tenha contas inscritas no Office 365, APIs Web e permissões para o SharePoint e Exchange Online estarão disponíveis para seleção. Você pode selecionar entre [dois tipos de permissões](active-directory-dev-glossary.md#permissions) para cada API Web desejada:

- Permissões de Aplicativo: o aplicativo cliente precisa acessar a API Web diretamente por si só (sem contexto de usuário). Esse tipo de permissão requer o consentimento do administrador e também não está disponível para aplicativos cliente nativos.

- Permissões Delegadas: o aplicativo cliente precisa acessar a API Web como usuário conectado, mas com acesso limitado pela permissão selecionada. Esse tipo de permissão pode ser concedido por um usuário, a menos que a permissão exija consentimento do administrador. 

  > [!NOTE]
  > Adicionar uma permissão delegada a um aplicativo não dá automaticamente consentimento para os usuários dentro do locatário, como ocorria no Portal clássico do Azure. Os usuários ainda devem consentir manualmente com as permissões delegadas adicionadas no tempo de execução, a menos que o administrador clique no botão **Conceder Permissões** da seção **Permissões Necessárias** da página do aplicativo no portal do Azure. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Para adicionar credenciais do aplicativo ou permissões para acessar APIs Web
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar.

   ![Atualizar o registro de um aplicativo](./media/active-directory-integrating-applications/update-app-registration.png)

4. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo. Para adicionar uma chave secreta para credenciais do seu aplicativo Web:
  - Clique na seção **Chaves** na página **Configurações**.  
  - Adicione uma descrição para a sua chave.
  - Selecione uma duração de um ou dois anos.
  - Clique em **Salvar**. A coluna mais à direita conterá o valor da chave, depois que você salvar as alterações de configuração. **Copie a chave** para uso no código do aplicativo cliente, já que ela não estará acessível depois que sair desta página.

  ![Atualizar um registro de aplicativo – chaves](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Para adicionar permissões para acessar as APIs do recurso do seu cliente
  - Clique na seção **Permissões Necessárias** na página **Configurações**. 
  - Clique no botão **Adicionar** .
  - Clique em **Selecionar uma API** para selecionar o tipo de recursos que você deseja escolher.
  - Percorra a lista de APIs disponíveis ou usar a caixa Pesquisar para selecionar os aplicativos de recursos disponíveis em seu diretório que expõem uma API da web. Clique no recurso que você está interessado e clique em **selecione**.
  - Você é levado para a página **Habilitar Acesso**. Selecione as Permissões do Aplicativo e/ou as Permissões Delegadas de que seu aplicativo precisa ao acessar a API.
   
  ![Atualizar um registro de aplicativo – api de permissão](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Atualizar um registro de aplicativo – perms de permissão](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Quando terminar, clique no botão **Selecionar** na página **Habilitar Acesso**, então no botão **Feito** na página **Adicionar acesso à API**. Você é levado de volta à página **Permissões Necessárias**, em que o novo recurso é adicionado à lista de APIs.

  > [!NOTE]
  > Clicar no botão **Concluído** também define automaticamente as permissões para o aplicativo no seu diretório com base nas permissões para outros aplicativos que você configurou.  É possível exibir essas permissões de aplicativo consultando a página **Configurações** do aplicativo.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurando um aplicativo de recurso para expor APIs Web

Você pode desenvolver uma API Web e disponibilizá-la aos aplicativos cliente expondo [escopos](active-directory-dev-glossary.md#scopes) e [funções](active-directory-dev-glossary.md#roles) de acesso. Uma API Web corretamente configurada é disponibilizada assim como outras APIs Web da Microsoft, incluindo a Graph API e as APIs do Office 365 Os escopos de acesso são expostos por meio do [manifesto do aplicativo](active-directory-dev-glossary.md#application-manifest), que é um arquivo JSON que representa a configuração de identidade do aplicativo. 

A seção a seguir mostra como expor escopos de acesso modificando o manifesto do aplicativo de recurso.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Adicionando escopos de acesso ao aplicativo de recurso

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.

3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar.

   ![Atualizar o registro de um aplicativo](./media/active-directory-integrating-applications/update-app-registration.png)

4. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo. Mude para a página **Editar manifesto** clicando em **Manifesto** da página de registro do aplicativo. Abre um editor de manifesto baseado na Web, permitindo a você **Editar** o manifesto dentro do portal. Opcionalmente, você pode clicar em **Baixar** e editar localmente, então usar **Carregar** para reaplicar ao seu aplicativo.

5. Neste exemplo, vamos expor um novo escopo chamado `Employees.Read.All` em nossa API/recursos adicionando o seguinte elemento JSON à coleção `oauth2Permissions`. O escopo `user_impersonation` existente é fornecido por padrão durante o registro. `user_impersonation` permite que um aplicativo cliente solicite permissão para acessar o recurso sob a identidade do usuário conectado. Adicione a vírgula após o elemento de escopo `user_impersonation` existente e mude os valores de propriedade para atender às necessidades do seu recurso. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > O valor "id" deve ser gerado usando uma ferramenta de geração de GUID como [guidgen]](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) ou de maneira programática. Ele representa um identificador exclusivo para o escopo exposto pela API Web. Depois que um cliente está configurado adequadamente com permissões para acessar a sua API Web, o Azure AD emite um token de acesso OAuth2.0. Quando o cliente chama a API Web, ele apresenta o token de acesso que tem a declaração de escopo (scp) definida para as permissões solicitadas no seu registro de aplicativo.
  >
  > É possível expor escopos adicionais posteriormente conforme a necessidade. Lembre-se de que a API Web pode expor vários escopos associados a uma variedade de funções diferentes. O recurso pode controlar o acesso à API Web em tempo de execução, avaliando declarações de escopo (`scp`) no token de acesso OAuth 2.0 recebido.
  > 

6. Ao terminar, clique em **Salvar**. Agora sua API Web está configurada para ser usada por outros aplicativos do seu diretório.  

  ![Atualizar o registro de um aplicativo](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verificar se a API Web está exposta a outros aplicativos em seu locatário
1. Volte para o locatário do Azure AD, clique em **Registros do aplicativo** novamente e, em seguida, localize/clique no aplicativo cliente que você deseja configurar.

   ![Atualizar o registro de um aplicativo](./media/active-directory-integrating-applications/update-app-registration.png)

2. Repita a etapa 5 como fez em [Configurar um aplicativo cliente para acessar APIs Web](#configure-a-client-application-to-access-web-apis). Quando você chega à etapa **Selecionar uma API**, pesquise seu recurso digitando seu nome de aplicativo no campo de pesquisa e clique em **Selecionar**. 

3. Na página **Habilitar Acesso**, você deve ver o novo escopo, disponível para solicitações de permissão do cliente.

  ![Novas permissões são mostradas](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Mais sobre o manifesto do aplicativo

O manifesto do aplicativo, na verdade, funciona como um mecanismo para atualizar a entidade de aplicativo, que define todos os atributos de configuração de identidade de um aplicativo do Azure AD, incluindo os escopos de acesso de API que discutimos. Para obter mais informações sobre a entidade Aplicativo e seu esquema, confira a [Documentação da entidade do Aplicativo da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). O artigo contém informações de referência completas sobre os membros da entidade do Aplicativo usados para especificar permissões para a sua API, incluindo:  

- O membro appRoles, que é uma coleção de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), usado para definir [permissões de aplicativo](active-directory-dev-glossary.md#permissions) para uma API Web. 
- O membro oauth2Permissions, que é uma coleção de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), usado para definir as [permissões delegadas](active-directory-dev-glossary.md#permissions) para uma API Web.

Para obter mais informações sobre os conceitos do manifesto do aplicativo em geral, consulte [Entendendo o manifesto de aplicativo do Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Acessar o Azure AD Graph e o Office 365 pelas APIs do Microsoft Graph  

Conforme mencionado anteriormente, além de expor/acessar APIs para seus próprios aplicativos, você também pode registrar seu aplicativo cliente para acessar APIs expostas pelos recursos da Microsoft. A API do Microsoft Graph, conhecida como "Microsoft Graph" na lista de recursos/APIs do portal, está disponível a todos os aplicativos registrados com o Azure AD. Se você estiver registrando seu aplicativo cliente em um locatário que contenha contas que se inscreveram para uma assinatura do Office 365, você também poderá acessar os escopos expostos pelos vários recursos do Office 365.

Para uma discussão completa sobre os escopos expostos pela API do Microsoft Graph, consulte o artigo [Escopos de permissão | Conceitos da API do Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

> [!NOTE]
> Devido a uma limitação atual, os aplicativos cliente nativos só poderão chamar a Graph API do AD do Azure se eles usarem a permissão "Acessar diretório da sua organização". Essa restrição não se aplica a aplicativos Web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configurando aplicativos multilocatários

Ao registrar um aplicativo no Azure AD, talvez você queira que ele seja acessado apenas por usuários da sua organização. Ou talvez você queira que o aplicativo fique acessível a usuários em organizações externas. Esses dois tipos de aplicativo são chamados de aplicativos de locatário único e multilocatário. Esta seção discute como modificar a configuração de um aplicativo de locatário único para torná-lo um aplicativo multilocatário.

É importante observar as diferenças entre um aplicativo de locatário único e multilocatário:  

- Um aplicativo de locatário único é destinado para uso em uma organização. Normalmente, trata-se de um aplicativo LoB (linha de negócios) escrito por um desenvolvedor corporativo. Um aplicativo de locatário único só pode ser acessado por usuários com contas no mesmo locatário que o registro do aplicativo. Como resultado, ele só precisa ser provisionado em um diretório.
- Um aplicativo multilocatário é destinado a uso em muitas organizações. Chamado de aplicativo Web SaaS (software como serviço), costuma ser escrito por um ISV (fornecedor independente de software). Aplicativos multilocatário devem ser provisionados em cada locatário que os usuários precisem acessar. Para locatários diferentes daquele em que o aplicativo está registrado, o consentimento do usuário ou do administrador é necessário para registrá-los. Observe que os aplicativos cliente nativos são multilocatários por padrão, pois são instalados no dispositivo do proprietário do recurso. Consulte a seção [Visão geral da estrutura de autorização](#overview-of-the-consent-framework) anterior para obter mais detalhes sobre a estrutura de autorização.

Tornar um aplicativo multilocatário requer tanto alterações de registro de aplicativo quanto alterações no próprio aplicativo Web. As seções a seguir cobrem ambas.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Alterar o registro do aplicativo para dar suporte a multilocatário

Se você estiver escrevendo um aplicativo que queira disponibilizar aos seus clientes ou parceiros fora da sua organização, você precisará atualizar a definição de aplicativo no portal do Azure.

> [!IMPORTANT]
> O Azure AD exige que o URI de ID do aplicativo de aplicativos multilocatário seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, é suficiente que o URI da ID do Aplicativo seja exclusivo nesse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários. A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD. Por exemplo, se o nome do seu locatário for contoso.onmicrosoft.com, um URI da ID do Aplicativo válido seria https://contoso.onmicrosoft.com/myapp. Se seu locatário tiver um domínio verificado de contoso.com, um URI de ID do aplicativo válido também seria https://contoso.com/myapp. A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.
> 

Para dar a usuários externos a capacidade de acessar seu aplicativo: 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
4. Na página **Configurações**, clique em **Propriedades** e mude a opção **Multilocatário** para **Sim**.

Depois de ter feito as alterações, usuários e administradores de outras organizações podem conceder aos próprios usuários a capacidade de entrar no aplicativo, permitindo que o aplicativo acesse recursos protegidos pelo locatário deles.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Alterar o aplicativo para dar suporte a multilocatário

O suporte para aplicativos multilocatário depende muito da estrutura de consentimento do Azure AD. O consentimento é o mecanismo que permite que um usuário de outro locatário conceda ao aplicativo acesso aos recursos protegidos pelo locatário do usuário. Essa experiência é chamada de "consentimento do usuário".

Seu aplicativo Web também pode oferecer:

- A capacidade de os administradores "inscreverem a minha empresa". Essa experiência, chamada de "consentimento do administrador", oferece ao administrador a capacidade de conceder consentimento em nome de *todos os usuários* em sua organização. Somente um usuário autenticado com uma conta que pertença à função de Administrador Global pode dar consentimento do administrador; os outros recebem um erro.

- Uma experiência de inscrição para os usuários. Espera-se que o usuário receba um botão de "inscrição" que redirecionará o navegador para o ponto de extremidade `/authorize` do Azure AD OAuth2.0 ou um ponto de extremidade `/userinfo` do OpenID Connect. Esses pontos de extremidade permitem ao aplicativo obter informações sobre o novo usuário inspecionando o id_token. Depois da fase de inscrição, é apresentado ao usuário uma solicitação de consentimento semelhante àquela mostrada na seção [Visão geral da estrutura de autorização](#overview-of-the-consent-framework).

Para obter mais informações sobre as alterações de aplicativo necessárias para dar suporte ao acesso multilocatário e experiências de inscrição/entrada, consulte:

- [Como entrar em qualquer usuário do Azure AD (Active Directory) usando o padrão de aplicativo multilocatário](active-directory-devhowto-multi-tenant-overview.md)
- A lista de [Exemplos de código de multilocatário](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Início rápido: adicionar identidade visual da empresa à sua página de entrada no Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitando a concessão implícita do OAuth 2.0 para Aplicativos de Uma Página

Os SPAs (Aplicativos de Uma Página) normalmente são estruturados com um front-end que usa muito JavaScript e é executado no navegador, que chama o back-end da API Web do aplicativo para executar sua lógica de negócios. Para SPAs hospedados no Azure AD, use a Concessão Implícita do OAuth 2.0 para autenticar o usuário com o Azure AD e obter um token que possa ser usado para proteger chamadas do cliente JavaScript do aplicativo para sua API Web de back-end. 

Depois que o usuário tiver dado consentimento, esse mesmo protocolo de autenticação poderá ser usado para obter tokens para proteger chamadas entre o cliente e outros recursos de API Web configurados para o aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, consulte [Noções básicas sobre o fluxo de concessão implícita de OAuth2 no Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Por padrão, a Concessão Implícita do OAuth 2.0 está desabilitada para aplicativos. Você pode habilitar a Concessão Implícita do OAuth 2.0 para seu aplicativo configurando o valor `oauth2AllowImplicitFlow` em seu [manifesto do aplicativo](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar a Concessão Implícita do OAuth 2.0

> [!NOTE]
> Para obter detalhes sobre como editar o manifesto do aplicativo, primeiro examine a seção anterior, [Configuração de um aplicativo de recurso para expor APIs Web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
4. Mude para a página **Editar manifesto** clicando em **Manifesto** da página de registro do aplicativo. Abre um editor de manifesto baseado na Web, permitindo a você **Editar** o manifesto dentro do portal. Localize e defina o valor de "oauth2AllowImplicitFlow" como "true". Por padrão, é definido como "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Salve o manifesto atualizado. Uma vez salva, a API Web agora está configurada para usar a Concessão Implícita do OAuth 2.0 para autenticar usuários.

## <a name="removing-an-application"></a>Removendo um aplicativo
Esta seção descreve como remover um registro do aplicativo do seu locatário do Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Removendo um aplicativo autorizado pela sua organização
Aplicativos que a sua organização tiver registrado aparecem no filtro "Meus aplicativos" na página "Registros de aplicativo" principal do locatário. Esses aplicativos são aqueles que você registrou manualmente pelo portal do Azure ou programaticamente pelo PowerShell ou pela API do Graph. Mais especificamente, eles são representados por um objeto de Aplicativo e Entidade de Serviço em seu locatário. Para saber mais, consulte [Objetos de aplicativo e objetos de entidade de serviço](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para remover um aplicativo de locatário único do diretório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
4. Na página de registro principal do aplicativo, clique em **Excluir**.
5. Clique em **Sim** na mensagem de confirmação.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Para remover um aplicativo multilocatário do diretório inicial
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
4. Na página **Configurações**, escolha **Propriedades** e altere a opção **Multilocatário** para **Não** para primeiro alterar seu aplicativo para locatário único e, em seguida, clique em **Salvar**. Os objetos da entidade de serviço do aplicativo permanecem em locatários de todas as organizações que já tiverem consentido com isso.
5. Clique no botão **Excluir** da página de registro principal do aplicativo.
6. Clique em **Sim** na mensagem de confirmação.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Removendo um aplicativo multilocatário autorizado por outra organização
Um subconjunto dos aplicativos que aparecem sob o filtro "Todos os aplicativos" (excluindo os registros de "Meus aplicativos") na página principal de "Registros do aplicativo" do seu locatário, é composto por aplicativos multilocatário. Em termos de técnicos, esses aplicativos multilocatários são de outro locatário e foram registrados no seu locatário durante o processo de consentimento. Mais especificamente, eles são representados apenas por um objeto de entidade de serviço em seu locatário, sem objeto de aplicativo correspondente. Para saber mais sobre as diferenças entre os objetos de aplicativo e entidade de serviço, confira [Objetos de aplicativo e entidade de serviço no Azure AD](active-directory-application-objects.md).

Para remover o acesso de um aplicativo multilocatário ao seu diretório (depois de ter dado autorização), o administrador da empresa deve remover sua entidade de serviço. O administrador deve ter acesso de administrador global e pode remover por meio do portal do Azure os [Cmdlets do PowerShell do Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre como a autenticação funciona no Azure AD, consulte [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md).
- Veja [Diretrizes de identidade visual para aplicativos integrados](active-directory-branding-guidelines.md) para obter dicas sobre a orientação visual para seu aplicativo.
- Para obter mais informações sobre a relação entre os objetos de Aplicativo e de Entidade de Serviço do aplicativo, consulte [Objetos de Aplicativo e objetos de Entidade de Serviço](active-directory-application-objects.md).
- Para saber mais sobre a função do manifesto do aplicativo, veja [Noções básicas sobre o manifesto do aplicativo do Azure Active Directory](active-directory-application-manifest.md)
- Veja as definições do [Glossário de desenvolvedor do Azure AD](active-directory-dev-glossary.md) de alguns dos conceitos de desenvolvedor do Azure Active Directory (AD).
- Visite o [Guia do desenvolvedor do Active Directory](active-directory-developers-guide.md) para obter uma visão geral de todo o conteúdo relacionado a desenvolvedor.

