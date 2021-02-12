---
title: Acessar APIs locais com o Azure Proxy de Aplicativo do AD
description: O proxy de aplicativo do Azure Active Directory permite que aplicativos nativos acessem APIs e lógica de negócios com segurança que você hospeda no local ou em VMs de nuvem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258975"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Acesso seguro a APIs locais com o Azure Proxy de Aplicativo do AD

Você pode ter APIs de lógica de negócios em execução localmente ou hospedadas em máquinas virtuais na nuvem. Seus aplicativos nativos do Android, iOS, Mac ou Windows precisam interagir com os pontos de extremidade da API para usar dados ou fornecer interação do usuário. O Azure Proxy de Aplicativo do AD e a [MSAL (biblioteca de autenticação da Microsoft)](../azuread-dev/active-directory-authentication-libraries.md) permitem que seus aplicativos nativos acessem com segurança suas APIs locais. Proxy de Aplicativo do Azure Active Directory é uma solução mais rápida e segura do que abrir portas de firewall e controlar a autenticação e a autorização na camada de aplicativo.

Este artigo orienta você pela configuração de uma solução de Proxy de Aplicativo do AD do Azure para hospedar um serviço de API Web que aplicativos nativos podem acessar.

## <a name="overview"></a>Visão geral

O diagrama a seguir mostra uma maneira tradicional de publicar APIs locais. Essa abordagem requer a abertura de portas de entrada 80 e 443.

![Acesso tradicional à API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

O diagrama a seguir mostra como você pode usar o Azure Proxy de Aplicativo do AD para publicar APIs com segurança sem abrir nenhuma porta de entrada:

![Acesso à API do Azure Proxy de Aplicativo do AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

O Proxy de Aplicativo do AD do Azure forma o backbone da solução, trabalhando como um ponto de extremidade público para acesso à API e fornecendo autenticação e autorização. Você pode acessar suas APIs de uma vasta gama de plataformas usando as bibliotecas [MSAL (biblioteca de autenticação da Microsoft)](../azuread-dev/active-directory-authentication-libraries.md) .

Como a autenticação e a autorização do Azure Proxy de Aplicativo do AD são criadas com base no Azure AD, você pode usar o acesso condicional do Azure AD para garantir que somente dispositivos confiáveis possam acessar APIs publicadas por meio do proxy de aplicativo. Use o ingresso no Azure AD ou o Azure AD híbrido Unido para desktops e o Intune gerenciado para dispositivos. Você também pode aproveitar os recursos Azure Active Directory Premium como a autenticação multifator do Azure AD e a segurança com suporte do Machine Learning da [proteção de identidade do Azure](../identity-protection/overview-identity-protection.md).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este passo a passos, você precisa de:

- Acesso de administrador a um diretório do Azure, com uma conta que pode criar e registrar aplicativos
- A API Web de exemplo e os aplicativos de cliente nativo do [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Publicar a API por meio do proxy de aplicativo

Para publicar uma API fora de sua intranet por meio do proxy de aplicativo, você segue o mesmo padrão para publicar aplicativos Web. Para obter mais informações, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

Para publicar a API Web do SecretAPI por meio do proxy de aplicativo:

1. Crie e publique o projeto SecretAPI de exemplo como um aplicativo Web ASP.NET no computador local ou na intranet. Verifique se você pode acessar o aplicativo Web localmente.

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**. Em seguida, selecione **aplicativos empresariais**.

1. Na parte superior da página **aplicativos empresariais – todos os aplicativos** , selecione **novo aplicativo**.

1. Na página **Adicionar um aplicativo** , selecione **aplicativos locais**. A página **Adicionar seu próprio aplicativo local** é exibida.

1. Se você não tiver um conector de proxy de aplicativo instalado, será solicitado a instalá-lo. Selecione **baixar conector de proxy de aplicativo** para baixar e instalar o conector.

1. Depois de instalar o conector de proxy de aplicativo, na página **Adicionar seu próprio aplicativo local** :

   1. Ao lado de **nome**, insira *SecretAPI*.

   1. Ao lado de **URL interna**, insira a URL que você usa para acessar a API de dentro de sua intranet.

   1. Verifique se a **pré-autenticação** está definida como **Azure Active Directory**.

   1. Selecione **Adicionar** na parte superior da página e aguarde até que o aplicativo seja criado.

   ![Adicionar aplicativo de API](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Na página **aplicativos empresariais – todos os aplicativos** , selecione o aplicativo **SecretAPI** .

1. Na página **SecretAPI – visão geral** , selecione **Propriedades** no painel de navegação esquerdo.

1. Você não quer que as APIs estejam disponíveis para os usuários finais no painel **myapps** , portanto, defina **visível para os usuários** como **não** na parte inferior da página **Propriedades** e, em seguida, selecione **salvar**.

   ![Não é visível para os usuários](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Você publicou sua API Web por meio do Azure Proxy de Aplicativo do AD. Agora, adicione usuários que podem acessar o aplicativo.

1. Na página **SecretAPI – visão geral** , selecione **usuários e grupos** no painel de navegação esquerdo.

1. Na página **usuários e grupos** , selecione **Adicionar usuário**.

1. Na página **Adicionar atribuição** , selecione **usuários e grupos**.

1. Na página **usuários e grupos** , procure e selecione os usuários que podem acessar o aplicativo, incluindo pelo menos por conta própria. Depois de selecionar todos os usuários, selecione **selecionar**.

   ![Selecionar e atribuir usuário](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. De volta à página **Adicionar atribuição** , selecione **atribuir**.

> [!NOTE]
> As APIs que usam a autenticação integrada do Windows podem exigir [etapas adicionais](./application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrar o aplicativo nativo e conceder acesso à API

Aplicativos nativos são programas desenvolvidos para uso em uma plataforma ou dispositivo específico. Antes que seu aplicativo nativo possa se conectar e acessar uma API, você deve registrá-lo no Azure AD. As etapas a seguir mostram como registrar um aplicativo nativo e fornecer acesso à API Web que você publicou por meio do proxy de aplicativo.

Para registrar o aplicativo nativo AppProxyNativeAppSample:

1. Na página **visão geral** do Azure Active Directory, **selecione registros de aplicativo** e, na parte superior do painel **registros de aplicativo** , selecione **novo registro**.

1. Na página **Registrar um aplicativo**:

   1. Em **nome**, insira *AppProxyNativeAppSample*.

   1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.

   1. Em **URL de redirecionamento**, menu suspenso e selecione **cliente público (Mobile & Desktop)** e, em seguida, insira *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. Selecione **registrar** e aguarde até que o aplicativo seja registrado com êxito.

      ![Novo registro de aplicativo](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Agora você registrou o aplicativo AppProxyNativeAppSample no Azure Active Directory. Para dar acesso ao aplicativo nativo à API Web do SecretAPI:

1. Na página Azure Active Directory **visão geral**  >  **registros do aplicativo** , selecione o aplicativo **AppProxyNativeAppSample** .

1. Na página **AppProxyNativeAppSample** , selecione **permissões de API** no painel de navegação esquerdo.

1. Na página **permissões de API** , selecione **Adicionar uma permissão**.

1. Na página primeiro **solicitar permissões de API** , selecione a guia **APIs que minha organização usa** e, em seguida, procure e selecione **SecretAPI**.

1. Na página próximo **permissões de API de solicitação** , marque a caixa de seleção ao lado de **user_impersonation** e, em seguida, selecione **adicionar permissões**.

    ![Selecionar uma API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. De volta à página **permissões de API** , você pode selecionar **conceder consentimento de administrador para a contoso** para impedir que outros usuários tenham que consentir individualmente o aplicativo.

## <a name="configure-the-native-app-code"></a>Configurar o código do aplicativo nativo

A última etapa é configurar o aplicativo nativo. O trecho de código a seguir do arquivo *Form1.cs* no aplicativo de exemplo NativeClient faz com que a biblioteca MSAL adquira o token para solicitar a chamada à API e a anexe como portador ao cabeçalho do aplicativo.

   ```
   // Acquire Access Token from AAD for Proxy Application
 IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }
 
if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application
  
  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Para configurar o aplicativo nativo para se conectar ao Azure Active Directory e chamar o proxy de aplicativo de API, atualize os valores de espaço reservado no arquivo *App.config* do aplicativo de exemplo NativeClient com valores do Azure AD:

- Cole a **ID do diretório (locatário)** no `<add key="ida:Tenant" value="" />` campo. Você pode encontrar e copiar esse valor (um GUID) da página **visão geral** de qualquer um dos seus aplicativos.

- Cole a **ID do aplicativo (cliente)** AppProxyNativeAppSample no `<add key="ida:ClientId" value="" />` campo. Você pode encontrar e copiar esse valor (um GUID) na página **visão geral** do AppProxyNativeAppSample.

- Cole o **URI de redirecionamento** AppProxyNativeAppSample no `<add key="ida:RedirectUri" value="" />` campo. Você pode encontrar e copiar esse valor (um URI) da página de **autenticação** do AppProxyNativeAppSample.

- Cole o **URI da ID do aplicativo** SecretAPI no `<add key="todo:TodoListResourceId" value="" />` campo. Você pode encontrar e copiar esse valor (um URI) do SecretAPI **expor uma página de API** .

- Cole a **URL da Home Page** do SecretAPI no `<add key="todo:TodoListBaseAddress" value="" />` campo. Você pode encontrar e copiar esse valor (uma URL) da página de **identidade visual** do SecretAPI.

Depois de configurar os parâmetros, compile e execute o aplicativo nativo. Quando você seleciona o botão **entrar** , o aplicativo permite que você entre e, em seguida, exibe uma tela de êxito para confirmar que ele se conectou com êxito ao SecretAPI.

![A captura de tela mostra um segredo de mensagem A P I successful e um botão OK.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Início Rápido: Configurar um aplicativo cliente para acessar APIs Web](../develop/quickstart-configure-app-access-web-apis.md)
- [Como habilitar aplicativos cliente nativos para interagir com os aplicativos do proxy](application-proxy-configure-native-client-application.md)