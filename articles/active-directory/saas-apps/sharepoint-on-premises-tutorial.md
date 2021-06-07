---
title: 'Tutorial: Integração do Azure Active Directory com SharePoint no local | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o SharePoint local.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862302"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Tutorial: Integração do logon único do Azure Active Directory ao SharePoint local

Neste tutorial, você aprenderá como integrar o SharePoint local ao Azure AD (Azure Active Directory). Ao integrar o SharePoint local ao Azure AD, você pode:

* Controlar quem tem acesso ao SharePoint local no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao SharePoint local com suas contas do Azure AD.
* Gerenciar suas contas no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SharePoint local, você precisa desse itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Um farm do SharePoint 2013 ou mais recente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o SSO (logon único) do Azure AD em um ambiente de teste. Os usuários do Azure AD são capazes de acessar seu SharePoint local.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Criar aplicativos empresariais no portal do Azure

Para configurar a integração do SharePoint local ao Azure AD, você precisa adicionar o SharePoint local da galeria à sua lista de aplicativos SaaS gerenciados.

Para adicionar o SharePoint local da galeria:

1. No portal do Azure, no painel mais à esquerda, selecione **Azure Active Directory**.

   > [!NOTE]
   > Se o elemento não estiver disponível, você também poderá abri-lo por meio do link **Todos os serviços**, na parte superior do painel mais à esquerda. Na visão geral a seguir, o link do **Azure Active Directory** está localizado na seção **Identidade**. Você também pode procurá-lo usando a caixa de filtro.

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

1. Na caixa de pesquisa, insira **SharePoint local**. Selecione **SharePoint local** no painel de resultados.

    <kbd>![SharePoint local na lista de resultados](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Especifique um nome para a instância do SharePoint local e selecione **Adicionar** para adicionar o aplicativo.

1. No novo aplicativo empresarial, selecione **Propriedades** e verifique o valor da opção **Requer atribuição de usuário?** .

   <kbd>![opção Requer atribuição de usuário?](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   Nesse cenário, o valor é definido como **Não**.

## <a name="configure-and-test-azure-ad"></a>Configurar e testar o Azure AD

Nesta seção, você configura o SSO do Azure AD com o SharePoint local. Para que o SSO funcione, você estabelece uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SharePoint local.

Para configurar e testar o SSO do Microsoft Azure AD com o SharePoint local, conclua esses blocos de construção:

- [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
- [Configurar o SharePoint local](#configure-sharepoint-on-premises) para definir as configurações de SSO no lado do aplicativo.
- [Criar um usuário de teste do Azure AD no portal do Azure](#create-an-azure-ad-test-user-in-the-azure-portal) para criar um usuário no Azure AD para o SSO.
- [Criar um grupo de segurança do Azure AD no portal do Azure](#create-an-azure-ad-security-group-in-the-azure-portal) para criar um grupo de segurança no Azure AD para SSO.
- [Conceder permissões para uma conta do Azure AD no SharePoint local](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) para conceder permissões a um usuário do Azure AD.
- [Conceder permissões para um grupo do Azure AD no SharePoint local](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) para conceder permissões a um grupo do Azure AD.
- [Permitir acesso a uma conta de convidado no SharePoint local no portal do Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) para conceder permissões para uma conta de convidado no Azure AD para SharePoint local.
- [Configurar o provedor de identidade confiável para vários aplicativos Web](#configure-the-trusted-identity-provider-for-multiple-web-applications) para usar o mesmo provedor de identidade confiável para vários aplicativos Web.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o SSO do Azure AD no portal do Azure.

Para configurar o SSO do Azure AD com o SharePoint local:

1. No portal do Azure, selecione **Azure Active Directory** > **Aplicativos empresariais**. Selecione o nome do aplicativo empresarial criado anteriormente e escolha **Logon único**.

1. Na caixa de diálogo **Selecionar um método de logon único**, escolha o modo **SAML** para habilitar o SSO.
 
1. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

1. Na seção **Configuração Básica de SAML**, siga estas etapas:

    ![Informações de SSO de domínio e URLs do SharePoint local](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Na caixa **Identificador**, insira uma URL usando este padrão: `urn:<sharepointFarmName>:<federationName>`.

    1. Na caixa **URL de Resposta**, insira uma URL usando este padrão: `https://<YourSharePointSiteURL>/_trust/`.

    1. Na caixa **URL de Logon**, insira uma URL usando este padrão: `https://<YourSharePointSiteURL>/`.
    1. Clique em **Salvar**.

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com as informações reais de URL de logon, Identificador e URL de Resposta.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, selecione **Baixar** para baixar o **Certificado (Base64)** das opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do certificado](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. Na seção **Configurar o SharePoint local**, copie as URLs apropriadas de acordo com seus requisitos:
    
    - **URL de logon**
    
        Copie a URL de logon e substitua **/saml2**, no final, por **/wsfed** para que se pareça com https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed. (Essa URL não é precisa.)

    - **Identificador do Azure AD**
    - **URL de logoff**

    > [!NOTE]
    > Essa URL não pode ser usada no SharePoint da forma como está. Você deve substituir **/saml2** por **/wsfed**. O aplicativo SharePoint local usa um token do SAML 1.1. Portanto, o Azure AD espera uma solicitação WS Fed do servidor do SharePoint. Após a autenticação, ele emite o token do SAML 1.1.

### <a name="configure-sharepoint-on-premises"></a>Configurar o SharePoint local

1. Crie um provedor de identidade confiável no SharePoint Server 2016.

    Entre no servidor do SharePoint e abra o Shell de Gerenciamento do SharePoint. Preencha os valores:
    - **$realm** é o valor do identificador da seção de domínio e URLs do SharePoint local no portal do Azure.
    - **$wsfedurl** é a URL de serviço do SSO.
    - **$filepath** é o caminho de arquivo para o qual você baixou o arquivo de certificado do portal do Azure.

    Execute os comandos a seguir para configurar um novo provedor de identidade confiável.

    > [!TIP]
    > Se você for novo no uso do PowerShell ou se quiser saber mais sobre como funciona o PowerShell, veja [PowerShell do SharePoint](/powershell/sharepoint/overview).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. Habilitar o provedor de identidade confiável para o aplicativo.

    1. Na **Administração Central**, vá para **Gerenciar Aplicativo Web** e selecione o aplicativo Web que você deseja proteger com o Azure AD.

    1. Na faixa de opções, selecione **Provedores de Autenticação** e escolha a zona que você deseja usar.

    1. Selecione **Provedor de Identidade Confiável** e selecione o provedor de identidade chamado *AzureAD* que você acabou de registrar.

    1. Selecione **OK**.

    ![Como configurar seu provedor de autenticação](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Criar um usuário de teste do Azure AD no portal do Azure

O objetivo desta seção é criar um usuário de teste no portal do Azure.

1. No portal do Azure, no painel mais à esquerda, selecione **Azure Active Directory**. No painel **Gerenciar**, selecione **Usuários**.

1. Selecione **Todos os usuários** > **Novo usuário** na parte superior da tela.

1. Selecione **Criar usuário** e, nas propriedades do usuário, siga essas etapas. Você pode criar usuários em seu Azure AD usando seu sufixo de locatário ou qualquer domínio verificado. 

    1. Na caixa **Nome**, insira o nome do usuário. Nós usamos **TestUser**.
  
    1. Na caixa **Nome de Usuário**, insira `TestUser@yourcompanydomain.extension`. Este exemplo mostra `TestUser@contoso.com`.

       ![A caixa de diálogo Usuário](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Selecione a caixa de seleção **Mostrar senha** e anote o valor que aparece na caixa **Senha**.

    1. Selecione **Criar**.

    1. Agora você pode compartilhar o site com TestUser@contoso.com e permitir que esse usuário o acesse.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Azure AD no portal do Azure

1. Selecione **Azure Active Directory** > **Grupos**.

1. Selecione **Novo grupo**.

1. Preencha as caixas **Tipo de grupo**, **Nome do grupo**, **Descrição do grupo** e **Tipo de associação**. Selecione as setas para escolher os membros e, em seguida, procure ou marque os membros que deseja adicionar ao grupo. Clique em **Selecionar** para adicionar os membros marcados e, em seguida, clique em **Criar**.

![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Conceder permissões a uma conta do Azure AD no SharePoint local

Para permitir acesso a um usuário do Azure AD no SharePoint local, compartilhe o conjunto de sites ou adicione o usuário do Azure AD a um dos grupos do conjunto de sites. Agora, os usuários podem entrar no SharePoint 201x usando identidades do Azure AD, mas ainda há oportunidades de melhoria para a experiência do usuário. Por exemplo, a pesquisa por um usuário apresenta vários resultados de pesquisa no seletor de pessoas. Há um resultado de pesquisa para cada um dos tipos de declaração criados no mapeamento de declarações. Para escolher um usuário por meio do seletor de pessoas, você deve digitar o nome de usuário dele com exatidão e escolher o resultado de declaração de **nome**.

![Reivindica resultados da pesquisa](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Não há validação nos valores que você pesquisa, o que pode levar a erros de ortografia ou fazer com que os usuários escolham acidentalmente o tipo de declaração incorreto. Essa situação pode impedir que os usuários acessem recursos com sucesso.

Para corrigir esse cenário com o seletor de pessoas, uma solução de software livre chamada [AzureCP](https://yvand.github.io/AzureCP/) fornece um provedor de declarações personalizado para o SharePoint 2013, 2016 e 2019. Ele usa a API do Microsoft Graph para resolver o que os usuários inserem e realiza a validação. Para obter mais informações, confira a [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Sem a AzureCP, você pode adicionar grupos adicionando a ID do grupo do Azure AD, mas esse método não é confiável e nem fácil de usar. A aparência é a seguinte:
  > 
  >![Adicionar um grupo do Azure AD a um grupo do SharePoint por ID](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Conceder permissões a um grupo do Azure AD no SharePoint local

Para atribuir grupos de segurança do Azure AD ao SharePoint local, é necessário usar um provedor de declarações personalizado para o servidor do SharePoint. Esse exemplo usa AzureCP.

 > [!NOTE]
 > A AzureCP não é um produto da Microsoft e não é compatível com o Suporte da Microsoft. Para baixar, instalar e configurar a AzureCP no farm local do SharePoint, confira o site da [AzureCP](https://yvand.github.io/AzureCP/). 

1. Configure a AzureCP no farm do SharePoint local ou uma solução alternativa de provedor de declarações personalizadas. Para configurar a AzureCP, consulte este site da [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html).

1. No portal do Azure, selecione **Azure Active Directory** > **Aplicativos empresariais**. Selecione o nome do aplicativo empresarial criado anteriormente e escolha **Logon único**.

1. Na página **Configurar o logon único com o SAML**, edite a seção **Atributos e Declarações do Usuário**.

1. Selecione **Adicionar uma declaração de grupo**.

1. Selecione quais grupos associados ao usuário devem ser retornados na declaração. Nesse caso, selecione **Todos os grupos**. Na seção **atributo de origem**, selecione **ID do grupo** e depois **Salvar**.

Para permitir acesso ao grupo de segurança do Azure AD no SharePoint local, compartilhe o conjunto de sites ou adicione o grupo de segurança do Azure AD a um dos grupos do conjunto de sites.

1. Navegue para o **Conjunto de Sites do SharePoint**. Nas **Configurações do Site** do conjunto de sites, selecione **Pessoas e grupos**. 

1. Selecione o grupo do SharePoint e, em seguida, selecione **Novo** > **Adicionar Usuários a esse Grupo**. À medida que você digita o nome do grupo, o seletor pessoas exibe o grupo de segurança do Azure AD.

    ![Adicionar um grupo do Azure AD a um grupo do SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Permitir acesso a uma conta de convidado para o SharePoint local no portal do Azure

Você pode permitir acesso ao site do SharePoint para uma conta de convidado de maneira consistente, pois agora o UPN fica modificado. Por exemplo, o usuário `jdoe@outlook.com` é representado como `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Para compartilhar o site com usuários externos, você precisa adicionar algumas modificações na seção **Atributos e Declarações do Usuário** no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory** > **Aplicativos empresariais**. Selecione o nome do aplicativo empresarial criado anteriormente e escolha **Logon único**.

1. Na página **Configurar o logon único com o SAML**, edite a seção **Atributos e Declarações do Usuário**.

1. Na zona **Declaração obrigatória**, selecione **Identificação de Usuário Exclusiva (ID do Nome)** .

1. Altere a propriedade **Atributo de Origem** para o valor **user.localuserprincipalname** e selecione **Salvar**.

    ![Atributo de Origem de Atributos de usuário e declarações iniciais](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Usando a faixa de opções, volte para o **Logon baseado em SAML**. Agora, a seção **Atributos e Declarações do Usuário** tem a seguinte aparência: 

    ![Atributos de usuário e declarações finais](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > O sobrenome e o nome fornecido não são obrigatórios nesta configuração.

1. No portal do Azure, no painel mais à esquerda, selecione **Azure Active Directory** e, em seguida, **Usuários**.

1. Selecione **Novo usuário convidado**.

1. Selecione a opção **Convidar usuário**. Preencha as propriedades do usuário e selecione **Convidar**.

1. Agora você pode compartilhar o site com MyGuestAccount@outlook.com e permitir que esse usuário o acesse.

    ![Como compartilhar um site com uma conta de convidado](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Configurar o provedor de identidade confiável para vários aplicativos Web

A configuração funciona para um aplicativo Web, mas precisa de ajustes adicionais se você pretende usar o mesmo provedor de identidade confiável para vários aplicativos Web. Por exemplo, suponha que você tenha estendido um aplicativo Web para usar a URL `https://sales.contoso.com` e agora deseja autenticar os usuários em `https://marketing.contoso.com`. Para fazer isso, atualize o provedor de identidade para respeitar o parâmetro WReply e atualize o registro de aplicativo do Azure AD para adicionar uma URL de resposta.

1. No portal do Azure, selecione **Azure Active Directory** > **Aplicativos empresariais**. Selecione o nome do aplicativo empresarial criado anteriormente e escolha **Logon único**.

1. Na página **Configurar o logon único com o SAML**, edite a **Configuração Básica do SAML**.

    ![Configuração Básica do SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. Na **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , adicione a URL para os aplicativo Web adicionais e selecione **Salvar**.

    ![Editar a configuração básica do SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. No servidor do SharePoint, abra o Shell de Gerenciamento do SharePoint 201x e execute os comandos a seguir. Use o nome do emissor do token de identidade confiável que você usou anteriormente.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. Na **Administração Central**, vá para o aplicativo Web e habilite o provedor de identidade confiável existente.

Pode haver outros cenários em que você deseja permitir acesso à instância SharePoint local para os usuários internos. Para esse cenário, você precisa implantar o Microsoft Azure Active Directory Connect a fim de permitir a sincronização dos usuários locais com o Azure AD. Essa configuração é discutida em outro artigo.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SharePoint local, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
