---
title: Configurar um aplicativo OpenID/OAuth da galeria de aplicativo do Azure AD | Microsoft Docs
description: Etapas para configurar um aplicativo OpenID/OAuth da galeria de aplicativo do Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 32f79f24df6fe705146b39750c710450ef8f1f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735916"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configurar um aplicativo OpenID/OAuth da galeria de aplicativo do Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processo para adicionar um aplicativo OpenID da galeria

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite o nome do aplicativo. Selecione o aplicativo desejado no painel de resultados e inscreva-se no aplicativo.

    ![OpenID na lista de resultados](common/search-new-app.png)


1. Na página Nome do aplicativo, clique no botão **Inscrever-se**.

    ![Botão Adicionar](./media/openidoauth-tutorial/addbutton.png)

    > [!NOTE]
    > Aqui o administrador de locatário deve selecionar o botão para se inscrever e dar o consentimento ao aplicativo. Em seguida, o aplicativo é adicionado ao locatário do cliente, onde você pode definir as configurações. Não é necessário adicionar o aplicativo explicitamente.

5. Você será redirecionado para a página Logon do Aplicativo ou para a página do Azure AD (Azure Active Directory) das credenciais de entrada.

6. Após a autenticação bem-sucedida, você deve aceitar o consentimento da página de consentimento. Depois disso, é exibida a home page do aplicativo.

    > [!NOTE]
    > Você só pode adicionar uma instância do aplicativo. Caso já tenha adicionado uma e tenha tentado fornecer o consentimento novamente, ela não será adicionada outra vez no locatário. Portanto, logicamente, só é possível usar uma instância de aplicativo no locatário.

1. Siga o vídeo abaixo para adicionar um aplicativo OpenID por meio da galeria.
    >[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando o OpenID Connect

O fluxo de entrada mais básico contém as seguintes etapas:

![Fluxo de autenticação usando o OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplicativo multilocatário
Um aplicativo multilocatário é destinado para uso em muitas organizações, não em apenas uma. Esses são normalmente aplicativos de software como serviço (SaaS) escritos por um fornecedor de software independente (ISV).

Aplicativos multilocatários precisam ser provisionados em cada diretório em que serão usados. Eles exigem o consentimento do usuário ou do administrador para registrá-los. Esse processo de consentimento é iniciado quando um aplicativo tiver sido registrado no diretório e receber acesso à API do Graph ou talvez a outra API da Web. Quando um usuário ou administrador de uma organização diferente se inscrever para usar o aplicativo, uma caixa de diálogo exibe as permissões das quais o aplicativo precisa.

O usuário ou administrador pode então dar consentimento ao aplicativo. O consentimento fornece ao aplicativo acesso aos dados declarados e, por fim, registra o aplicativo no diretório.

> [!NOTE]
> Se você estiver disponibilizando seu aplicativo a usuários em múltiplos diretórios, será necessário um mecanismo para determinar em qual locatário eles estão. Um aplicativo de locatário único só precisa procurar por um usuário em seu próprio diretório. Um aplicativo multilocatário precisa identificar um usuário específico em todos os diretórios no Azure AD.
>
> Para realizar essa tarefa, o Azure AD fornece um ponto de extremidade de autenticação comum em que qualquer aplicativo multilocatário pode direcionar solicitações de entrada, em vez de um ponto de extremidade específico de locatário. Esse ponto de extremidade é `https://login.microsoftonline.com/common` para todos os diretórios no Azure AD. Um ponto de extremidade específico de locatário pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com`.
>
> É importante levar em consideração o ponto de extremidade comum quando estiver desenvolvendo seu aplicativo. Você precisará da lógica adequada para lidar com vários locatários durante a entrada, saída e validação de token.

Por padrão, o Azure AD promove aplicativos multilocatários. Eles são acessados com facilidade entre organizações e são fáceis de usar depois que o consentimento foi aceito.

## <a name="consent-framework"></a>Estrutura de consentimento

É possível usar a estrutura de consentimento do Azure AD para desenvolver aplicativos Web multilocatários e clientes nativos. Esses aplicativos permitem a entrada de contas de usuário de um locatário do Azure AD diferente daquele no qual o aplicativo está registrado. Eles também precisam acessar APIs Web como:
- A API do Microsoft Graph, para acessar o Azure AD, o Intune e serviços no Microsoft 365.
- Outras APIs de serviços da Microsoft.
- Suas próprias APIs Web.

A estrutura se baseia em um usuário ou administrador que dá autorização a um aplicativo que solicita seu registro no diretório. O registro pode envolver acesso aos dados de diretório. Depois que o consentimento for dado, o aplicativo cliente pode chamar a API do Microsoft Graph em nome do usuário e usar as informações conforme o necessário.

A [API do Microsoft Graph](https://developer.microsoft.com/graph/) fornece acesso aos dados no Microsoft 365, como:

- Calendários e mensagens do Exchange.
- Sites e listas do SharePoint.
- Documentos do OneDrive.
- Blocos de notas do OneNote.
- Tarefas do Planner.
- Pastas de trabalho do Excel.

A API do Graph também fornece acesso a usuários e grupos do Azure AD e outros objetos de dados de mais serviços em nuvem da Microsoft.

As etapas a seguir mostram como a experiência de consentimento funciona para o desenvolvedor e o usuário do aplicativo:

1. Suponha que você tenha um aplicativo cliente Web que precise solicitar permissões específicas para acessar um recurso ou API. O portal do Azure é usado para declarar as solicitações de permissão em tempo de configuração. Como outras definições de configuração, elas se tornam parte dos registros do Azure AD do aplicativo. Para o caminho de solicitação da permissão, é necessário seguir as etapas abaixo:

    a. Clique nos **Registros de aplicativo** do lado esquerdo do menu e abra seu aplicativo, digitando o nome dele na caixa de pesquisa.

    ![Uma captura de tela que mostra a opção "Registros de aplicativo" selecionada no menu do lado esquerdo e a caixa de pesquisa "ID de aplicativo" realçada.](./media/openidoauth-tutorial/application.png)

    b. Clique em **Exibir Permissões de API**.

    ![Uma captura de tela que mostra a página "Chamar API" com o botão "Exibir Permissões de API" selecionado.](./media/openidoauth-tutorial/api-permission.png)

    c. Clique em **Adicionar uma permissão**.

    ![Uma captura de tela que mostra a seção "Permissões de API" com o botão "Adicionar uma permissão" selecionado.](./media/openidoauth-tutorial/add-permission.png)

    d. Clique em **Microsoft Graph**.

    ![Uma captura de tela que mostra a página "Solicitar Permissões de API" com a guia "API da Microsoft" e o bloco "Microsoft Graph" selecionados.](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Selecione as opções obrigatórias de **Permissões delegadas** e **Permissões do Aplicativo**.

    ![API do Graph](./media/openidoauth-tutorial/graphapi.png)

2. Considere que as permissões do aplicativo tenham sido atualizadas. O aplicativo está em execução e um usuário está prestes a usá-lo pela primeira vez. Primeiro, o aplicativo precisa obter um código de autorização do ponto de extremidade /authorize do Azure AD. O código de autorização então pode ser usado para adquirir um novo acesso e token de atualização.

3. Se o usuário ainda não tiver sido autenticado, o ponto de extremidade /authorize do Azure AD solicitará a entrada.

    ![Uma captura de tela do prompt de entrada da conta](./media/openidoauth-tutorial/authentication.png)

4. Depois que o usuário tiver entrado, o Azure AD determinará se o usuário precisará ver uma página de consentimento. Essa decisão depende do fato de o usuário (ou do administrador da organização) já ter dado o consentimento de aplicativo.

   Se o consentimento não tiver sido dado, o Azure AD solicitará o consentimento ao usuário e exibirá as permissões adequadas de que ele precisa para funcionar. As permissões exibidas na caixa de diálogo de consentimento correspondem àquelas selecionadas nas permissões delegadas no portal do Azure.

    ![Página de consentimento](./media/openidoauth-tutorial/consentpage.png)

Um usuário normal pode dar consentimento a algumas permissões. Outras permissões exigem um consentimento do administrador de locatários.

## <a name="difference-between-admin-consent-and-user-consent"></a>A diferença entre o consentimento do administrador e o consentimento do usuário

Como administrador, você pode também concorda com permissões do aplicativo em nome de todos os usuários em seu locatário. O consentimento administrativo impedirá que a caixa de diálogo de consentimento apareça para cada usuário no locatário. Os usuários que têm a função de administrador podem fornecer consentimento no portal do Azure. Na página **Configurações** do seu aplicativo, selecione **Permissões Necessárias** > **Conceder consentimento do administrador**.

![Botão Conceder permissões](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A concessão explícita usando o botão **Conceder consentimento do administrador** agora é necessária para SPAs (aplicativos de página única) que usam o ADAL.js. Caso contrário, o aplicativo falhará quando o token de acesso for solicitado.

As permissões somente do aplicativo sempre exigem o consentimento do administrador de locatários. Se o aplicativo solicitar uma permissão somente aplicativo e um usuário tentar entrar nele, uma mensagem de erro será exibida. A mensagem informa que o usuário não pode consentir.

Se o aplicativo usar permissões que exigem o consentimento do administrador, você precisará ter um gesto, como um botão ou link, em que o administrador pode iniciar a ação. A solicitação que seu aplicativo envia para essa ação é uma solicitação de autorização usual do OAuth2/OpenID Connect. Essa solicitação inclui o parâmetro de cadeia de consulta *prompt = admin_consent*.

Depois que o administrador fornecer seu consentimento e a entidade de serviço for criada no locatário do cliente, as solicitações posteriores de conexão não precisarão do parâmetro *prompt=admin_consent*. Como o administrador decidiu que as permissões solicitadas são aceitáveis, não será solicitado o consentimento de nenhum outro usuário no locatário daquele ponto em diante.

Um administrador de locatários pode desabilitar a capacidade dos usuários regulares consentirem aplicativos. Se essa funcionalidade estiver desabilitada, o consentimento do administrador sempre será necessário para o aplicativo a ser usado no locatário. Se desejar testar seu aplicativo com o consentimento do usuário final desabilitado, é possível localizar a opção de configuração no [portal do Azure](https://portal.azure.com/). Ela está na seção [Configurações de usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) em **Aplicativos empresariais**.

O parâmetro *prompt=admin_consent* também pode ser usado por aplicativos que solicitam permissões que não necessitam do consentimento do administrador. Um exemplo é um aplicativo que exige uma experiência na qual o administrador de locatários "se inscreve" uma vez, e não é solicitado o consentimento de nenhum outro usuário desse ponto em diante.

Imagine que um aplicativo exige o consentimento do administrador e que um administrador entra sem o parâmetro *prompt = admin_consent* ser enviado. Quando o administrador fornece consentimento ao aplicativo com sucesso, este se aplica somente à sua conta de usuário. Os usuários normais ainda não poderão entrar ou dar consentimento ao aplicativo. Esse recurso é útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.

## <a name="next-steps"></a>Próximas etapas

[Configurar o SSO (logon único) baseado em OIDC para um aplicativo no locatário do Azure AD (Azure Active Directory)](../manage-apps/add-application-portal-setup-oidc-sso.md)