---
title: 'Tutorial: Integração do Azure Active Directory com o Workday | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba2d3bab7d709b4bb9ac18e4a9c6ed052a5fb83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086974"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Tutorial: Integrar o Workday ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Workday ao Azure AD (Azure Active Directory). Quando integrar o Workday ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Workday.
* Permitir que os usuários sejam conectados automaticamente ao Workday com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Workday.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Workday é compatível com o SSO iniciado por **SP**.

## <a name="adding-workday-from-the-gallery"></a>Adicionar o Workday da galeria

Para configurar a integração do Workday com o Azure AD, é necessário adicionar o Workday da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Workday** na caixa de pesquisa.
1. Escolha **Workday** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Workday usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workday.

Para configurar e testar o SSO do Azure AD com o Workday, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configure o Workday](#configure-workday)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Crie um usuário de teste do Workday](#create-workday-test-user)** para ter um equivalente de Brenda Fernandes no Workday que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Workday**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.workday.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Esses não são os valores reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Sua URL de resposta deve ter um subdomínio, por exemplo: www, wd2, wd3, wd3-impl, wd5, wd5-impl.
    > Usar algo como `http://www.myworkday.com` funciona, mas `http://myworkday.com` não. Contate a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Seu aplicativo Workday espera as instruções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Workday espera que **nameidentifier** seja mapeado com **user.mail**, **UPN**, etc. Portanto, você precisa editar o mapeamento de atributos clicando no ícone **Editar** e alterar o mapeamento de atributos.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Aqui nós Mapeamos a identificação do nome UPN (userPrincipalName) como padrão. Você precisa mapear a ID de nome com a ID de usuário real em sua conta do Workday (seu email, UPN, etc.) para que o SSO funcione corretamente.

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Para modificar as opções de **Autenticação** de acordo com seus requisitos, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Escolha **Assinar resposta SAML e declaração** na **Opção de Assinatura**.

    b. Clique em **Salvar**

1. Na seção **Configurar o Workday**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Configurar o Workday

1. Em uma janela diferente do navegador da Web, faça logon no site da empresa Workday como administrador.

2. Na **Caixa de pesquisa**, procure com o nome **Editar Configuração de Locatário – Segurança** no lado superior esquerdo da home page.

    ![Editar segurança de locatário](./media/workday-tutorial/IC782925.png "Editar segurança de locatário")

3. Na seção **URLs de Redirecionamento** , execute as seguintes etapas:

    ![URLs de redirecionamento](./media/workday-tutorial/IC7829581.png "URLs de redirecionamento")

    a. Clique em **Adicionar Linha**.

    b. Na caixa de texto **URL de redirecionamento de logon**, **URL de redirecionamento de tempo limite** e **URL de redirecionamento móvel**, cole a **URL de logon** que você copiou da seção **Configurar o Workday** do portal do Azure.

    c. Na caixa de texto **URL de redirecionamento de logout**, cole a **URL de logout** que você copiou da seção **Configurar o Workday** do portal do Azure.

    d. Na caixa de texto **Usado para Ambientes**, selecione o nome do ambiente.  

   > [!NOTE]
   > O valor do atributo Ambiente é vinculado ao valor da URL do locatário:  
   > –Se o nome de domínio da URL do locatário do Workday começar com impl, por exemplo, *https:\//impl.workday.com/\<tenant\>/login-saml2.flex*, o atributo **Ambiente** deverá ser definido como Implementação.  
   > –Se o nome de domínio começar de outra forma, será necessário contatar a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter o valor de **Ambiente** correspondente.

4. Na seção **Configuração do SAML** , execute as seguintes etapas:

    ![Instalação do SAML](./media/workday-tutorial/IC782926.png "Instalação do SAML")

    a.  Selecione **Habilitar Autenticação SAML**.

    b.  Clique em **Adicionar Linha**.

5. Na seção **Provedores de Identidade do SAML**, execute as seguintes etapas:

    ![Provedores de Identidade SAML](./media/workday-tutorial/IC7829271.png "Provedores de Identidade SAML")

    a. Na caixa de texto **Nome do Provedor de Identidade**, digite um nome de provedor (por exemplo: *SPInitiatedSSO*).

    b. No Portal do Azure, na janela **Configurar Workday**, o valor do **Identificador do Azure AD** e, em seguida, cole-o na caixa de texto **Emissor**.

    ![Provedores de Identidade SAML](./media/workday-tutorial/IC7829272.png "Provedores de Identidade SAML")

    c. No Portal do Azure, na janela **Configurar Workday**, copie a **URL de Logoff** e, em seguida, cole-a na caixa de texto **URL de Resposta de Logoff**.

    d. No Portal do Azure, na janela **Configurar Workday**, copie a **URL de Logon** e, em seguida, cole-a na caixa de texto **URL do Serviço de SSO do IdP**.

    e. Na caixa de texto **Usado para Ambientes**, selecione o nome do ambiente.

    f. Clique em **Certificado de Chave Pública do Provedor de Identidade** e em **Criar**.

    ![Criar](./media/workday-tutorial/IC782928.png "Criar")

    g. Clique em **Criar Chave Pública x509**.

    ![Criar](./media/workday-tutorial/IC782929.png "Criar")

6. Na seção **Exibir Chave Pública x509** , realize as seguintes etapas:

    ![Exibir chave pública x509](./media/workday-tutorial/IC782930.png "Exibir chave pública x509")

    a. Na caixa de texto **Nome**, digite um nome para o seu certificado (por exemplo: *PPE\_SP*).

    b. Na caixa de texto **Válido de** , digite o valor do atributo “válido de” do seu certificado.

    c.  Na caixa de texto **Válido até** , digite o valor do atributo “válido até” do seu certificado.

    > [!NOTE]
    > Você pode obter as datas de “válido a partir de” e “válido até” do certificado baixado clicando duas vezes nele.  As datas são listadas na guia **Detalhes** .
    >
    >

    d.  Abra seu certificado codificado em base-64 no bloco de notas e copie o conteúdo dele.

    e.  Na caixa de texto **Certificado** , cole o conteúdo da área de transferência.

    f.  Clique em **OK**.

7. Execute as seguintes etapas:

    ![Configuração de SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuração de SSO")

    a.  Na caixa de texto **ID do Provedor de Serviço**, digite **https://www.workday.com** .

    b. Selecione **Não Esvazie a Solicitação de Autenticação iniciada por SP**.

    c. Para **Solicitação de Método de Autenticação de Assinatura** , selecione **SHA256**.

    ![Método de assinatura da solicitação de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "Método de assinatura da solicitação de autenticação") 

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Verifique se que você configurar o logon único corretamente. Caso você habilite o logon único com a configuração incorreta, talvez não seja possível inserir o aplicativo com suas credenciais e ser bloqueado. Nessa situação, o Workday fornece uma URL de login de backup em que os usuários podem entrar usando seu nome de usuário e senha normais no seguinte formato: [Your Workday URL] /login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Workday.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Workday**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-workday-test-user"></a>Criar um usuário de teste do Workday

Nesta seção, você cria um usuário chamado Brenda Fernandes no Workday. Trabalhe com [a equipe de suporte do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os usuários à plataforma Workday. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Quando escolher o bloco do Workday no Painel de Acesso, você deverá ser conectado automaticamente ao Workday, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
