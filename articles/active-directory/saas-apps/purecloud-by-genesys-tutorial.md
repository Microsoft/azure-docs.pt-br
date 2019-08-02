---
title: 'Tutorial: Integração do Azure Active Directory ao PureCloud by Genesys | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: ac476e2a66f7fd1d315adb37258917b3ff47373f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093456"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Tutorial: Integração do Azure Active Directory ao PureCloud by Genesys

Neste tutorial, você aprenderá a integrar o PureCloud by Genesys ao Azure AD (Azure Active Directory).
A integração do PureCloud by Genesys ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao PureCloud by Genesys.
* Você pode permitir que os usuários sejam conectados automaticamente ao PureCloud by Genesys (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PureCloud by Genesys, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do PureCloud by Genesys

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O PureCloud by Genesys dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando o PureCloud by Genesys por meio da galeria

Para configurar a integração do PureCloud by Genesys ao Azure AD, você precisará adicionar o PureCloud by Genesys por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o PureCloud by Genesys por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **PureCloud by Genesys**, selecione **PureCloud by Genesys** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![PureCloud by Genesys na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o PureCloud by Genesys, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PureCloud by Genesys.

Para configurar e testar o logon único do Azure AD com o PureCloud by Genesys, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do PureCloud by Genesys](#configure-purecloud-by-genesys-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** – para ter um equivalente de Brenda Fernandes no PureCloud by Genesys que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o PureCloud by Genesys, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PureCloud by Genesys**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do PureCloud by Genesys](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL de acordo com sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Na caixa de texto **URL de Resposta**, digite uma URL de acordo com sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do PureCloud by Genesys](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL de acordo com sua região:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. O aplicativo PureCloud by Genesys espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo PureCloud by Genesys espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o PureCloud by Genesys**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Configurar o logon único do PureCloud by Genesys

1. Em outra janela do navegador da Web, faça logon no PureCloud by Genesys como Administrador.

2. Clique em **Administrador** na parte superior e navegue para **Logon Único** em **Integrações**.

    ![Configurar o logon único](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Alterne para a guia **ADFS/Azure AD (Premium)** e execute as seguintes etapas:

    ![Configurar o logon único](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Clique em **Procurar** para carregar o certificado codificado em Base 64 baixado no portal do Azure no **Certificado do ADFS**.

    b. Na caixa de texto **URI do Emissor do ADFS**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URI de Destino**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Para o valor do **Identificador de Terceira Parte Confiável**, você precisará acessar o portal do Azure e, na página de integração de aplicativos do **PureCloud by Genesys**, clicar na guia **Propriedades** e copiar o valor da **ID do Aplicativo**. Cole-o na caixa de texto **Identificador de Terceira Parte Confiável**. 

    ![Configurar o logon único](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Clique em **Salvar**   

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao PureCloud by Genesys.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **PureCloud by Genesys**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **PureCloud by Genesys**.

    ![O link do PureCloud by Genesys na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-purecloud-by-genesys-test-user"></a>Criar um usuário de teste do PureCloud by Genesys

Para permitir que os usuários do Azure AD façam logon no PureCloud by Genesys, eles precisam ser provisionados no PureCloud by Genesys. No PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no PureCloud by Genesys como Administrador.

2. Clique em **Administrador** na parte superior e navegue para **Pessoas** em **Pessoas e Permissões**.

    ![Configurar o logon único](./media/purecloud-by-genesys-tutorial/configure03.png)

3. Na página Pessoas, clique em **Adicionar Pessoa**.

    ![Configurar o logon único](./media/purecloud-by-genesys-tutorial/configure04.png)

4. No pop-up **Adicionar Pessoas à Organização**, execute as seguintes etapas:

    ![Configurar o logon único](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na caixa de texto **Nome Completo**, insira o nome do usuário como **Brendafernandes**.

    b. Na caixa de texto **Email**, insira o email do usuário como **brendafernandes\@contoso.com**.
    
    c. Clique em **Criar**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do PureCloud by Genesys no Painel de Acesso, você deverá ser conectado automaticamente ao PureCloud by Genesys, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

