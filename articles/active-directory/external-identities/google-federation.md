---
title: Adicionar o Google como provedor de identidade para B2B – Azure AD
description: Federar com o Google para permitir que os usuários convidados entrem em seus aplicativos do Azure AD com suas próprias contas do gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d2369e93052ef28191dd1862034c1aaa488add
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355589"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicionar o Google como provedor de identidade para usuários convidados B2B

Ao configurar a Federação com o Google, você pode permitir que os usuários convidados entrem em seus aplicativos e recursos compartilhados com suas próprias contas do Gmail, sem precisar criar contas da Microsoft. 

> [!NOTE]
> A federação do Google foi criada especificamente para usuários do Gmail. Para federar com domínios do G Suite, use a [Federação direta](direct-federation.md).

> [!IMPORTANT]
> A **partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se você estiver usando a inscrição do Google Federation ou autoatendimento com o Gmail, deverá [testar seus aplicativos nativos de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência do usuário do Google?
Quando você envia um convite para usuários do Google Gmail, os usuários convidados devem acessar seus aplicativos ou recursos compartilhados usando um link que inclui o contexto do locatário. A experiência varia dependendo de a conexão ao Google já ter ocorrido ou não:
  - Os usuários convidados que não estiverem conectados ao Google serão solicitados a fazê-lo.
  - Os usuários convidados que já estiverem conectados ao Google serão solicitados a escolher a conta que desejam usar. Ele precisa escolher a conta que foi usada para convidá-lo.

Os usuários convidados que veem um erro de "cabeçalho muito longo" podem limpar seus cookies ou abrir uma janela particular ou Incognito e tentar entrar novamente.

![Captura de tela que mostra a página de entrada do Google.](media/google-federation/google-sign-in.png)

## <a name="deprecation-of-webview-sign-in-support"></a>Substituição do suporte de entrada do WebView

A partir de 4 de janeiro de 2021, o Google está [preterindo o suporte de entrada do WebView incorporado](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se você estiver usando a inscrição do Google Federation ou [autoatendimento com o Gmail](identity-providers.md), deverá testar seus aplicativos nativos de linha de negócios para compatibilidade. Se seus aplicativos incluírem conteúdo do WebView que requer autenticação, os usuários do Google Gmail não poderão se autenticar. Veja a seguir os cenários conhecidos que afetarão os usuários do Gmail:

- Aplicativos do Windows que usam o modo de exibição da Web incorporado ou o WAM ($ accountmanager) em versões anteriores do Windows.
- Outros aplicativos nativos que você desenvolveu que usam uma estrutura de navegador incorporada para autenticação.

Essa alteração não afeta:

- Aplicativos do Windows que usam o modo de exibição da Web incorporado ou o WAM ($ accountmanager) nas versões mais recentes do Windows
- Aplicativos do Microsoft iOS
- Identidades do g Suite, por exemplo, quando você está usando a [Federação direta](direct-federation.md) baseada em SAML com o G Suite

Estamos continuando testando várias plataformas e cenários e atualizaremos este artigo de forma adequada.
### <a name="to-test-your-apps-for-compatibility"></a>Para testar a compatibilidade de seus aplicativos

1. Siga as [diretrizes do Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) para determinar se seus aplicativos foram afetados.
2. Usando o Fiddler ou outra ferramenta de teste, insira um cabeçalho durante a entrada e use uma identidade externa do Google para testar a entrada:

   1. Adicione Google-accounts-check-OAuth-login: true aos cabeçalhos de solicitação HTTP quando as solicitações forem enviadas para accounts.google.com.
   1. Tente entrar no aplicativo inserindo um endereço do Gmail na página de entrada do accounts.google.com.
   1. Se a entrada falhar e você vir um erro como "Este navegador ou aplicativo pode não ser seguro", suas identidades externas do Google serão impedidas de entrar.

3. Resolva o problema seguindo um destes procedimentos:

   - Se seu aplicativo do Windows usar o modo de exibição da web embutido ou o WAM ($ accountmanager) em uma versão anterior do Windows, atualize para a versão mais recente do Windows.
   - Modifique seus aplicativos para usar o navegador do sistema para entrar. Para obter detalhes, consulte [interface do usuário da Web do vs System inserida](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) na documentação do MSAL.net.  

## <a name="sign-in-endpoints"></a>Pontos de extremidade de entrada

O Teams dá suporte total aos usuários convidados do Google em todos os dispositivos. Os usuários do Google podem entrar no Teams de um ponto de extremidade comum, como `https://teams.microsoft.com`.

Os pontos de extremidade comuns de outros aplicativos podem não dar suporte a usuários do Google. Os usuários convidados do Google devem entrar usando um link que inclui suas informações de locatário. Veja a seguir exemplos:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se os usuários convidados do Google tentarem usar um link como `https://myapps.microsoft.com` ou `https://portal.azure.com` , eles receberão um erro.

Você também pode conceder aos usuários convidados do Google um link direto para um aplicativo ou recurso, desde que o link inclua suas informações de locatário. Por exemplo, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 
## <a name="step-1-configure-a-google-developer-project"></a>Etapa 1: Configurar um projeto de desenvolvedor do Google
Primeiro, crie um novo projeto no console de desenvolvedores do Google para obter uma ID do cliente e um segredo do cliente que você pode adicionar posteriormente ao Azure Active Directory (Azure AD). 
1. Acesse as APIs do Google em https://console.developers.google.com e entre com sua conta do Google. É recomendável que você use uma conta do Google de equipe compartilhada.
2. Aceite os termos de serviço se você for solicitado a fazê-lo.
3. Criar um novo projeto: no painel, selecione **criar projeto**, dê um nome ao projeto (por exemplo, **Azure ad B2B**) e, em seguida, selecione **criar**: 
   
   ![Captura de tela que mostra uma nova página de projeto.](media/google-federation/google-new-project.png)

4. Na página de **serviços & de APIs** , selecione **Exibir** em seu novo projeto.

5. Selecione **ir para APIs visão geral** no cartão de APIs. Selecione a **tela de consentimento do OAuth**.

6. Selecione **Firewall** e, em seguida, **Criar**. 

7. Na **tela de consentimento do OAuth**, insira um nome de **aplicativo**:

   ![Captura de tela que mostra o consentimento de OAuth do Google.](media/google-federation/google-oauth-consent-screen.png)

8. Role até a seção **domínios autorizados** e insira **microsoftonline.com**:

   ![Captura de tela que mostra a seção domínios autorizados.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Clique em **Salvar**.

10. Selecione **Credenciais**. No menu **criar credenciais** , selecione **ID do cliente OAuth**:

    ![Captura de tela que mostra o menu criar credenciais do Google APIs.](media/google-federation/google-api-credentials.png)

11. Em **Tipo de aplicativo**, selecione **Aplicativo Web**. Dê ao aplicativo um nome adequado, como **B2B do Azure ad**. Em **URIs de redirecionamento autorizados**, insira os seguintes URIs:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(onde `<tenant ID>` é sua ID de locatário)
   
    > [!NOTE]
    > Para localizar sua ID de locatário, vá para a [portal do Azure](https://portal.azure.com). Em **Azure Active Directory**, selecione **Propriedades** e copie a **ID do locatário**.

    ![Captura de tela que mostra a seção URIs de redirecionamento autorizados.](media/google-federation/google-create-oauth-client-id.png)

12. Selecione **Criar**. Copie a ID do cliente e o segredo do cliente. Você os usará quando adicionar o provedor de identidade no portal do Azure.

    ![Captura de tela que mostra a ID do cliente OAuth e o segredo do cliente.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Etapa 2: Configurar a federação do Google no Microsoft Azure Active Directory 
Agora você vai definir a ID do cliente do Google e o segredo do cliente. Você pode usar o portal do Azure ou o PowerShell para fazer isso. Certifique-se de testar sua configuração do Google Federation convidando-se. Use um endereço do Gmail e tente resgatar o convite com sua conta do Google convidada. 

**Para configurar o Google Federation no portal do Azure** 
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **todos os provedores de identidade** e, em seguida, selecione o botão **Google** .
4. Insira a ID do cliente e o segredo do cliente obtidos anteriormente. Selecione **salvar**: 

   ![Captura de tela que mostra a página Adicionar provedor de identidade do Google.](media/google-federation/google-identity-provider.png)

**Como configurar a federação do Google usando o PowerShell**
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute este comando: `Connect-AzureAD`
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
4. Execute o comando a seguir: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Use a ID do cliente e o segredo do cliente do aplicativo criado em "etapa 1: configurar um projeto de desenvolvedor do Google". Para obter mais informações, consulte [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Como fazer a remoção da federação do Google?
É possível excluir sua configuração da federação do Google. Se você fizer isso, os usuários convidados do Google que já tiverem resgatado seu convite não poderão entrar. Mas você pode permitir a eles acesso a seus recursos novamente, excluindo-os do diretório e convidando-os novamente. 
 
**Para excluir o Google Federation no portal do Azure AD**
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os provedores de identidade**.
4. Na linha do **Google** , selecione o botão de reticências (**...**) e, em seguida, selecione **excluir**. 
   
   ![Captura de tela que mostra o botão excluir do provedor de identidade social.](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a exclusão. 

**Para excluir o Google Federation usando o PowerShell** 
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute `Connect-AzureAD`.  
4. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
5. Insira o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [AzureADMSIdentityProvider remover](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
