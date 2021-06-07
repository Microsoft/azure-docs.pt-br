---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Jamf Pro | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Jamf Pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 7aaaa854ef7d0fd44bdccbe07cb38112475be367
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727606"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Tutorial: Integração do SSO do Azure Active Directory ao Jamf Pro

Neste tutorial, você aprenderá a integrar o Jamf Pro ao Azure AD (Azure Active Directory). Ao integrar o Jamf Pro ao Azure AD, você pode:

* Usar o Azure AD para controlar quem tem acesso ao Jamf Pro.
* Conectar os usuários automaticamente ao Jamf Pro com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Jamf Pro com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O Jamf Pro dá suporte ao SSO **iniciado por SP** e **IdP**.

## <a name="add-jamf-pro-from-the-gallery"></a>Adicionar o Jamf Pro por meio da galeria

Para configurar a integração do Jamf Pro ao AD do Azure, você precisará adicionar o Jamf Pro da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou a sua conta Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira *Jamf Pro* na caixa de pesquisa.
1. Selecione **Jamf Pro** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Configurar e testar o SSO no Azure AD para o Jamf Pro

Configure e teste o SSO do Azure AD com o Jamf Pro usando um usuário de teste chamado B.Fernandes. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jamf Pro.

Nesta seção, você configurará e testará o SSO do Azure AD com o Jamf Pro.

1. [Configurar o SSO no Azure AD](#configure-sso-in-azure-ad) para que os usuários possam usar esse recurso.
    1. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o SSO do Azure AD com a conta de B.Fernandes.
    1. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para que B.Fernandes possa usar o SSO no Azure AD.
1. [Configurar o SSO no Jamf Pro](#configure-sso-in-jamf-pro) para definir as configurações de SSO no lado do aplicativo.
    1. [Criar um usuário de teste do Jamf Pro](#create-a-jamf-pro-test-user) para ter um equivalente de B.Fernandes no Jamf Pro que esteja vinculado à representação do usuário do Azure AD.
1. [Testar a configuração de SSO](#test-the-sso-configuration) para verificar se a configuração funciona.

## <a name="configure-sso-in-azure-ad"></a>Configurar o SSO no Azure AD

Nesta seção, você habilitará o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Jamf Pro**, localize a seção **Gerenciar** e selecione **Logon Único**.
1. Na página **Selecionar um Método de Logon Único**, selecione **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Edite a página Configuração Básica do SAML.](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo **iniciado por IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, insira uma URL que use a seguinte fórmula: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Na caixa de texto **URL de Resposta**, insira uma URL que use a seguinte fórmula: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecione **Definir URLs adicionais**. Caso deseje configurar o aplicativo no modo **iniciado pelo SP**, na caixa de texto **URL de Logon**, insira uma URL que use a seguinte fórmula: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Você obterá o valor real do identificador na seção **Logon Único** no portal do Jamf Pro, que será explicado mais adiante no tutorial. Você pode extrair o valor real do subdomínio do valor do identificador e usar essas informações do subdomínio como a URL de logon e a URL de resposta. Veja também as fórmulas mostradas na seção **Configuração Básica do SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, acesse a seção **Certificado de Autenticação SAML**, selecione o botão **Copiar** para copiar a **URL de Metadados de Federação do Aplicativo** e, em seguida, salve-a no computador.

    ![O link de download do Certificado de Autenticação SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.
   1. No campo **Nome de usuário**, insira [nome]@[domíniodaempresa].[extensão]. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá a B.Fernandes acesso ao Jamf Pro.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Jamf Pro**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Selecione **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e, em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-sso-in-jamf-pro"></a>Configurar o SSO no Jamf Pro

1. Para automatizar a configuração no Jamf Pro, instale a **extensão do navegador Entrada Segura dos Meus Aplicativos** selecionando **Instalar a extensão**.

    ![Página da extensão do navegador Entrada Segura dos Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecione **Configurar o Jamf Pro**. Quando o aplicativo Jamf Pro for aberto, forneça as credenciais de administrador para entrar. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas 3 a 7.

    ![Página de configuração da instalação no Jamf Pro](common/setup-sso.png)

3. Para configurar o Jamf Pro manualmente, abra uma nova janela do navegador da Web e entre em seu site da empresa do Jamf Pro como administrador. Em seguida, execute as etapas a seguir.

4. Selecione o **ícone Configurações** no canto superior direito da página.

    ![Selecionar o ícone de configurações no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecione **Logon Único**.

    ![Selecionar Logon Único no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na página **Logon Único**, execute as etapas a seguir.

    ![A página Logon Único no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecione **Editar**.

    b. Marque a caixa de seleção **Habilitar Autenticação de Logon Único**.

    c. Selecione **Azure** como uma opção no menu suspenso **Provedor de Identidade**.

    d. Copie o valor da **ID DA ENTIDADE** e cole-o no campo **Identificador (ID da Entidade)** na seção **Configuração Básica do SAML** no portal do Azure.

    > [!NOTE]
    > Use o valor no campo `<SUBDOMAIN>` para completar a URL de logon e a URL de resposta na seção **Configuração Básica do SAML** no portal do Azure.

    e. Selecione **URL de Metadados** no menu suspenso **Fonte de Metadados do Provedor de Identidade**. No campo exibido, cole o valor da **URL de Metadados de Federação do Aplicativo** copiado do portal do Azure.

    f. (Opcional) Edite o valor de expiração do token ou selecione "Desabilitar expiração do token SAML".

7. Na mesma página, role a página para baixo até a seção **Mapeamento de Usuário**. Em seguida, execute as etapas a seguir.

    ![A seção Mapeamento de Usuário da página Logon Único no Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Selecione a opção **NameID** para **Mapeamento de Usuário do Provedor de Identidade**. Por padrão, essa opção é definida como **NameID**, mas você pode definir um atributo personalizado.

    b. Selecione **Email** para **Mapeamento de Usuário do Jamf Pro**. O Jamf Pro mapeia os atributos SAML enviados pelo IdP primeiro por usuários e, em seguida, por grupos. Quando um usuário tenta acessar o Jamf Pro, o Jamf Pro obtém informações sobre o usuário do provedor de identidade e faz sua correspondência com todas as contas de usuário do Jamf Pro. Se a conta de usuário de entrada não for encontrada, o Jamf Pro tentará fazer a correspondência dela por nome de grupo.

    c. Cole o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` no campo **NOME DO ATRIBUTO DE GRUPO DO PROVEDOR DE IDENTIDADE**.

    d. Na mesma página, role para baixo até a seção **Segurança** e selecione **Permitir que os usuários ignorem a autenticação de logon único**. Como resultado, os usuários não serão redirecionados para a página de entrada do provedor de identidade para autenticação e poderão entrar no Jamf Pro diretamente. Quando um usuário tenta acessar o Jamf Pro por meio do Provedor de Identidade, ocorre a autenticação e autorização de SSO iniciada pelo IdP.

    e. Clique em **Salvar**.

### <a name="create-a-jamf-pro-test-user"></a>Criar um usuário de teste do Jamf Pro

Para permitir que os usuários do Azure AD entrem no Jamf Pro, eles precisam ser provisionados no Jamf Pro. O provisionamento no Jamf Pro é uma tarefa manual.

Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre em seu site corporativo do Jamf Pro como administrador.

2. Selecione o ícone **Configurações** no canto superior direito da página.

    ![O ícone de configurações no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecione **Contas de Usuário e Grupos do Jamf Pro**.

    ![O ícone de Contas de Usuário e Grupos do Jamf Pro nas configurações do Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecione **Novo**.

    ![Página de configurações do sistema de Contas de Usuário e Grupos do Jamf Pro](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecione **Criar Conta Padrão**.

    ![A opção Criar Conta Padrão na página Contas de Usuário e Grupos do Jamf Pro](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na caixa de diálogo **Nova Conta**, execute as seguintes etapas:

    ![Opções de configuração da nova conta nas configurações do sistema do Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. No campo **NOME DE USUÁRIO**, insira `Britta Simon`, o nome completo do usuário de teste.

    b. Selecione as opções de **NÍVEL DE ACESSO**, **CONJUNTO DE PRIVILÉGIOS** e **STATUS DE ACESSO** que estejam de acordo com sua organização.

    c. No campo **NOME COMPLETO**, insira `Britta Simon`.

    d. No campo **ENDEREÇO DE EMAIL**, insira o endereço de email da conta de Brenda Fernandes.

    e. No campo **SENHA**, insira a senha do usuário.

    f. No campo **VERIFICAR SENHA**, insira a senha do usuário novamente.

    g. Selecione **Salvar**.

## <a name="test-the-sso-configuration"></a>Testar a configuração de SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Jamf Pro, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Jamf Pro e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Jamf Pro para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Jamf Pro, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Jamf Pro para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Jamf Pro, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).