---
title: Adicionar o Facebook como um provedor de identidade – Azure AD
description: Federar com o Facebook para permitir que usuários externos (convidados) entrem em seus aplicativos do Azure AD com suas próprias contas do Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcacfc8cede0ab91241fd431aace10cbbfbea916
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709677"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Adicionar o Facebook como um provedor de identidade para identidades externas

Você pode adicionar o Facebook a seus fluxos de usuário de inscrição de autoatendimento para que os usuários possam entrar em seus aplicativos usando suas próprias contas do Facebook. Para permitir que os usuários entrem usando o Facebook, primeiro você precisa [habilitar a inscrição por autoatendimento](self-service-sign-up-user-flow.md) para seu locatário. Depois de adicionar o Facebook como um provedor de identidade, configure um fluxo de usuário do aplicativo e selecione o Facebook como uma das opções de credenciais.

Depois de adicionar o Facebook como uma das opções de entrada do seu aplicativo, na página de **entrada** , um usuário pode simplesmente inserir o email que eles usam para entrar no Facebook ou pode selecionar **as opções de entrada** e escolher **entrar com o Facebook**. Em ambos os casos, eles serão redirecionados para a página de logon do Facebook para autenticação.

![Opções de entrada para usuários do Facebook](media/facebook-federation/sign-in-with-facebook-overview.png)

> [!NOTE]
> Os usuários só podem usar suas contas do Facebook para inscreverem-se por meio de aplicativos usando a inscrição e os fluxos dos usuários de autoatendimento. Os usuários não podem ser convidados e resgatar o convite usando uma conta do Facebook.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Criar um aplicativo no console de desenvolvedores do Facebook

Para usar uma conta do Facebook como [provedor de identidade](identity-providers.md), você precisa criar um aplicativo no console de desenvolvedores do Facebook. Se ainda não tiver uma conta do Facebook, abra uma em [https://www.facebook.com/](https://www.facebook.com).

> [!NOTE]  
> Use as URLs a seguir nas etapas 9 e 16 abaixo.
> - Para **URL do site**, insira o endereço do seu aplicativo, como `https://contoso.com`.
> - Para **URIs de redirecionamento do OAuth válidos**, insira `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`. Você encontra seu `<tenant-ID>` na folha Visão Geral do Azure Active Directory.


1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
2. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para isso, escolha **Começar**, no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
3. Escolha **Meus Aplicativos** e **Criar Aplicativo**.
4. Insira um **Nome de Exibição** e um **Email de Contato** válido.
5. Selecione **Criar ID do aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
6. Escolha **Configurações** > **Básicas**.
7. Escolha uma **Categoria**, por exemplo, Negócio e Páginas. Esse valor é exigido pelo Facebook, mas não é usado no Azure AD.
8. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
9. Em **URL do Site**, insira a URL apropriada (anotada acima).
10. Em **URL da Política de Privacidade**, insira a URL da página em que você mantém informações de privacidade do seu aplicativo; por exemplo, `http://www.contoso.com`.
11. Selecione **Salvar alterações**.
12. Na parte superior da página, copie o valor de **ID do Aplicativo**.
13. Escolha **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você usará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
14. Selecione o sinal de mais ao lado de **PRODUTOS** e escolha **Configurar** em **Login do Facebook**.
15. Em **Login do Facebook**, escolha **Configurações**.
16. Em **URIs de redirecionamento do OAuth válidos**, insira a URL apropriada (anotada acima).
17. Escolha **Salvar Alterações** na parte inferior da página.
18. Para disponibilizar seu aplicativo do Facebook no Azure AD, escolha o seletor de Status, na parte superior direita da página, e **ative-o** para tornar o aplicativo público. Em seguida, escolha **Alternar modo**. Neste ponto, o Status deverá mudar de **Desenvolvimento** para **Ativo**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar o Facebook como um provedor de identidade
Agora, você definirá a ID do cliente e o segredo do cliente do Facebook, seja inserindo-o no portal do Azure AD ou usando o PowerShell. Você pode testar a configuração do Facebook inscrevendo-se por meio de um fluxo de usuário em um aplicativo habilitado para inscrição de autoatendimento.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Como configurar a federação do Facebook no portal do Azure AD
1. Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **Todos os provedores de identidade** e escolha **Facebook**.
5. Para **ID do cliente**, insira a **ID do aplicativo** do Facebook que criou anteriormente.
6. Para **Segredo do cliente**, insira o **Segredo do Aplicativo** que você registrou.

   ![Captura de tela mostrando a página Adicionar o provedor de identidade social](media/facebook-federation/add-social-identity-provider-page.png)

7. Clique em **Salvar**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Para configurar a Federação do Facebook usando o PowerShell
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute o seguinte comando: `Connect-AzureAD`.
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
4. Execute o comando a seguir: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Use a ID do cliente e o segredo do cliente do aplicativo que você criou acima no console do desenvolvedor do Facebook. Para obter mais informações, consulte o artigo [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="how-do-i-remove-facebook-federation"></a>Como faço para remover a federação do Facebook?
Você pode excluir a configuração da federação do Facebook. Se você fizer isso, os usuários que se inscreveram por meio de fluxos dos usuários com suas contas do Facebook não poderão mais fazer logon. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Para excluir a federação do Facebook no portal do Azure AD: 
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os provedores de identidade**.
4. Na linha **Facebook**, selecione o menu de contexto ( **...** ) e, em seguida, selecione **Excluir**. 
5. Clique em **Sim** para confirmar a exclusão.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Para excluir a Federação do Facebook usando o PowerShell: 
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute `Connect-AzureAD`.  
4. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
5. Insira o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [AzureADMSIdentityProvider remover](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="next-steps"></a>Próximas etapas

- [Adicionar inscrição por autoatendimento a um aplicativo](self-service-sign-up-user-flow.md)