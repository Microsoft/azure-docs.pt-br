---
title: 'Tutorial: Integração do Azure Active Directory com SharePoint no local | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o SharePoint local.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 21c10e142802914bcf3292ef7f25066675e6cdd1
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143915"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Integração do Azure Active Directory com o SharePoint no local

Neste tutorial, você aprenderá como integrar o SharePoint local ao Azure AD (Azure Active Directory).
A integração do SharePoint local com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao SharePoint no local.
* Você pode permitir que seus usuários entrem automaticamente no SharePoint local (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SharePoint local, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura do SharePoint local habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SharePoint local dá suporte a SSO iniciado por **SP**

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Adicionando o SharePoint no local da galeria

Para configurar a integração do SharePoint local ao Azure AD, você precisa adicionar o SharePoint local da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SharePoint local da galeria, execute as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Se o elemento não estiverem disponíveis, também é possível abrir pelo link fixo **Todos os serviços** na parte superior do painel de navegação à esquerda. Na visão geral a seguir, o link do **Azure Active Directory** está localizado na seção **Identidade** ou ele pode ser pesquisado usando a caixa de texto de filtro.

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SharePoint local**, selecione **SharePoint local** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SharePoint local na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SharePoint local, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SharePoint local.

Para configurar e testar o logon único do Azure AD com o SharePoint local, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SharePoint local](#configure-sharepoint-on-premises-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Criar um grupo de segurança do Microsoft Azure Active Directory no portal do Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** : para habilitar um novo grupo de segurança para logon único no Microsoft Azure Active Directory.
5. **[Permitir acesso ao grupo de segurança local no SharePoint](#grant-access-to-sharepoint-on-premises-security-group)** – permitir acesso a determinado grupo para o Azure AD.
6. **[Atribuir o grupo de segurança do Microsoft Azure Active Directory no portal do Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)** : para atribuir determinado grupo ao Microsoft Azure Active Directory para autenticação.
7. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SharePoint local, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SharePoint local**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SharePoint local](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `urn:sharepoint:federation`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do SharePoint local](https://support.office.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

    > [!Note]
    > Anote o caminho do arquivo em que você baixou o arquivo de certificado, pois será necessário utilizá-lo posteriormente no script do PowerShell para configuração.

6. Na seção **Configurar o SharePoint local**, copie as URLs apropriadas de acordo com suas necessidades. Para a **URL do Serviço de Logon Único**, use um valor do seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a ID do locatário da assinatura do Microsoft Directory Azure.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    > [!NOTE]
    > O aplicativo SharePoint local usa SAML 1.1 token, portanto o Azure AD espera a solicitação WS Fed de servidor do SharePoint e após a autenticação, ele emite SAML 1.1. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Configurar o logon único no SharePoint local

1. Em outra janela do navegador da Web, faça logon em seu site de empresa local do SharePoint como um administrador.

2. **Configurar um novo provedor de identidade confiável no SharePoint Server 2016**

    Faça logon no servidor do SharePoint Server 2016 e abra o Shell de Gerenciamento do SharePoint 2016. Preencha os valores de $realm (valor do Identificador da seção Domínio e URLs locais do SharePoint no portal do Azure), $wsfedurl (URL do Serviço de Logon Único) e $filepath (caminho do arquivo em que você baixou o arquivo de certificado) do portal do Azure e execute os comandos a seguir para configurar um novo provedor de identidade confiável.

    > [!TIP]
    > Se você for novo no uso do PowerShell ou se quiser saber mais sobre como funciona o PowerShell, veja [PowerShell do SharePoint](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Em seguida, siga estas etapas para habilitar o provedor de identidade confiável para seu aplicativo:

    a. Na Administração Central, navegue até **Gerenciar Aplicativo de Web** e selecione o aplicativo Web que você deseja proteger com o Azure AD.

    b. Na faixa de opções, clique em **Provedores de Autenticação** e escolha a região que você deseja usar.

    c. Selecione **Provedor de Identidade Confiável** e selecione o provedor de identidade *AzureAD* que você acabou de registrar.

    d. Na configuração da URL de página de entrada, selecione **Página de entrada personalizada** e forneça o valor "/_trust/".

    e. Clique em **OK**.

    ![Como configurar seu provedor de autenticação](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Alguns usuários externos não poderão usar essa integração de logon único, já que o UPN terá um valor danificado como `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Em breve, permitiremos que os clientes configurem o aplicativo sobre como tratar o UPN, dependendo do tipo de usuário. Depois disso, todos os usuários convidados deverão ser capazes de usar o SSO diretamente como os funcionários da organização.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Microsoft Azure Active Directory no portal do Azure

1. Clique em **Azure Active Directory > Todos os grupos**.

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Clique em **Novo grupo**:

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Preencha **Tipo de grupo**, **Nome do grupo**, **Descrição do grupo**, **Tipo de associação**. Clique na seta para selecionar membros; em seguida, pesquise ou clique no membro que deseja adicionar ao grupo. Clique em **Selecionar** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Para atribuir grupos de segurança do Azure Active Directory ao SharePoint local, será necessário instalar e configurar o [AzureCP](https://yvand.github.io/AzureCP/) no farm do SharePoint local OU desenvolver e configurar um provedor de declarações personalizado alternativo para o SharePoint.  Confira a seção Mais informações no final do documento para saber como criar seu próprio provedor de declarações personalizadas, caso não use o AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Permitir acesso a um grupo de segurança do SharePoint local

**Configurar grupos de segurança e permissões no registro de aplicativo**

1. No portal do Azure, selecione **Microsoft Azure Active Directory** e, em seguida, selecione **Registros de aplicativo**.

    ![Folha de aplicativos empresariais](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Na caixa de pesquisa, digite e selecione **SharePoint local**.

    ![SharePoint local na lista de resultados](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Clique em **Manifesto**.

    ![Opção de manifesto](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modifique `groupMembershipClaims`: `NULL`, para `groupMembershipClaims`: `SecurityGroup`. Em seguida, clique em Salvar

    ![Editar manifesto](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Clique em **Configurações** e em **Permissões necessárias**.

    ![Permissões necessárias](./media/sharepoint-on-premises-tutorial/settings.png)

6. Clique em **Adicionar** e, em seguida, **Selecionar uma API**.

    ![Acesso à API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adicione **Microsoft Azure Active Directory do Windows** e **API do Microsoft Graph**, mas só será possível selecionar um de cada vez.

    ![Seleção de API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Selecione Microsoft Azure Active Directory do Windows, marque Ler dados do diretório e clique em Selecionar. Retorne, adicione o Microsoft Graph e selecione Ler dados do diretório para ele.  Clique em Selecionar e em Concluído.

    ![Habilitar acesso](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Agora, em Configurações necessárias, clique em **Conceder permissões** e, em seguida, clique em Sim para conceder permissões.

    ![Conceder permissões](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Em Notificações, verifique para determinar se as permissões foram concedidas com êxito.  Caso contrário, o AzureCP não funcionará adequadamente e não será possível configurar o SharePoint local com grupos de segurança do Microsoft Azure Active Directory.

10. Configure o AzureCP no farm do SharePoint local ou uma solução alternativa de provedor de declarações personalizadas.  Neste exemplo, estamos usando o AzureCP.

    > [!NOTE]
    > O AzureCP não é um produto da Microsoft ou com suporte pelo suporte técnico da Microsoft. Baixar, instalar e configurar o AzureCP no farm do SharePoint local por https://yvand.github.io/AzureCP/ 

11. **Permitir acesso ao grupo de segurança do Microsoft Azure Active Directory no SharePoint local**: os grupos devem ter acesso ao aplicativo no SharePoint local.  Use as etapas a seguir para definir as permissões para acessar o aplicativo Web.

12. Na Administração Central, clique em Gerenciamento de aplicativos, Gerenciar aplicativos Web. Em seguida, selecione o aplicativo Web para ativar a faixa de opções e clique em Política de usuário.

    ![Administração Central](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Em Política para aplicativo Web, clique em Adicionar usuários e, em seguida, selecione a zona e clique em Avançar.  Clique em Catálogo de endereços.

    ![Política para aplicativo Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Em seguida, pesquise e adicione o grupo de segurança do Microsoft Azure Active Directory e clique em OK.

    ![Adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Selecione as Permissões e, em seguida, clique em Concluir.

    ![Adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Em Política para aplicativo Web, verifique se o grupo do Microsoft Azure Active Directory foi adicionado.  A declaração do grupo mostra a ID de objeto do grupo de segurança do Microsoft Azure Active Directory para o nome de usuário.

    ![Adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Navegue até o conjunto de sites do SharePoint e adicione o grupo ali também. Clique em Configurações do site e em Permissões do site e Conceder permissões.  Pesquise a declaração de Função do grupo, atribua o nível de permissão e clique em Compartilhar.

    ![Adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurando um provedor de identidade confiável para vários aplicativos Web

A configuração funciona para um único aplicativo Web, mas precisa de configuração adicional se você pretende usar o mesmo provedor de identidade confiável para vários aplicativos Web. Por exemplo, imagine que nós estendemos um aplicativo Web para usar a URL `https://portal.contoso.local` e agora queremos autenticar os usuários em `https://sales.contoso.local` também. Para fazer isso, precisamos atualizar o provedor de identidade para respeitar o parâmetro WReply e atualizar o registro do aplicativo no Azure AD para adicionar uma URL de resposta.

1. No portal do Azure, abra o diretório do Microsoft Azure Active Directory. Clique em **Registros de aplicativo** e, em seguida, clique em **Exibir todos os aplicativos**. Clique no aplicativo que você criou anteriormente (integração SAML do SharePoint).

2. Clique em **Configurações**.

3. Na folha de configurações, clique em **URLs de Resposta**.

4. Adicione a URL do aplicativo Web adicional com `/_trust/default.aspx` anexado à URL (como `https://sales.contoso.local/_trust/default.aspx`) e clique em **Salvar**.

5. No servidor do SharePoint, abra o **Shell de Gerenciamento do SharePoint 2016** e execute os comandos a seguir, usando o nome do emissor do token de identidade confiável que você usou anteriormente.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Em Administração Central, vá para o aplicativo Web e habilite o provedor de identidade confiável existente. Lembre-se de também configurar a URL da página de entrada como uma página de entrada personalizada `/_trust/`.

7. Em Administração Central, clique no aplicativo Web e escolha **Política de Usuário**. Adicione um usuário com as permissões apropriadas, conforme demonstrado anteriormente neste artigo.

### <a name="fixing-people-picker"></a>Como corrigir o Seletor de Pessoas

Agora, os usuários podem fazer logon no SharePoint 2016 usando identidades do Microsoft Azure Active Directory, mas ainda há oportunidades de melhoria para a experiência do usuário. Por exemplo, a pesquisa por um usuário apresenta vários resultados de pesquisa no seletor de pessoas. Há um resultado de pesquisa para cada um dos 3 tipos de declarações que foram criados no mapeamento de declaração. Para escolher um usuário usando o seletor de pessoas, você deve digitar o nome de usuário dele exatamente e escolher o resultado da reivindicação **nome**.

![Reivindica resultados da pesquisa](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Não há validação nos valores que você pesquisa, o que pode levar a erros de ortografia ou a usuários que escolherem acidentalmente o tipo de declaração errado para atribuir, como a reivindicação de **SurName**. Isso pode impedir que os usuários acessem recursos com êxito.

Para ajudar nesse cenário, há uma solução de código aberto chamada [ AzureCP ](https://yvand.github.io/AzureCP/) que fornece um provedor de declarações personalizado para o SharePoint 2016. Ele usará o Gráfico do Azure AD para resolver o que os usuários inserem e realizam a validação. Saiba mais em [ AzureCP ](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o grupo de segurança do Microsoft Azure Active Directory no portal do Azure

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **SharePoint local**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **SharePoint local**.

    ![O link do SharePoint local na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Clique em **Adicionar usuário**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Pesquise o grupo de segurança que você deseja usar e clique no grupo para adicioná-lo à seção Selecionar membros. Clique em **Selecionar** e, em seguida, clique em **Atribuir**.

    ![Pesquisar grupo de segurança](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus para ser avisado de que o grupo foi atribuído com êxito ao aplicativo empresarial no portal do Azure.

### <a name="create-sharepoint-on-premises-test-user"></a>Criar um usuário de teste do SharePoint local

Nesta seção, você cria um usuário chamado Brenda Fernandes no SharePoint local. Trabalhe com a [Equipe de suporte do SharePoint local](https://support.office.com/) para adicionar os usuários na plataforma do SharePoint local. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SharePoint local no Painel de Acesso, você deverá entrar automaticamente no SharePoint local para o qual tiver configurado o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
