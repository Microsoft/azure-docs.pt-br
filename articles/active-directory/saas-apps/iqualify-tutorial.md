---
title: 'Tutorial: Integração do Azure Active Directory com o iQualify LMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: f7ca163b93a68525e80018051baa626bc378c200
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099765"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integração do Azure Active Directory com o iQualify LMS

Neste tutorial, você aprenderá a integrar o iQualify LMS ao Azure AD (Azure Active Directory).
A integração do iQualify LMS ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao iQualify LMS.
* Você pode permitir que os usuários entrem automaticamente no iQualify LMS (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iQualify LMS, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do iQualify LMS habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O iQualify LMS é compatível com o SSO iniciado por **SP e IDP**
* O iQualify LMS é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Adicionar o iQualify LMS da galeria

Para configurar a integração do iQualify LMS com o Azure AD, você precisa adicionar o iQualify LMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iQualify LMS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **iQualify LMS**, selecione **iQualify LMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![iQualify LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o iQualify LMS usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iQualify LMS.

Para configurar e testar o logon único do Azure AD com o iQualify LMS, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do iQualify LMS](#configure-iqualify-lms-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do iQualify LMS](#create-iqualify-lms-test-user)** – para que haja um equivalente de Brenda Fernandes no iQualify LMS que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o iQualify, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **iQualify LMS** clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do iQualify LMS](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/`|
    | Ambiente de teste: `https://<yourorg>.iqualify.io`|

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente de teste: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do iQualify LMS](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/login` |
    | Ambiente de teste: `https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte do cliente iQualify LMS](https://www.iqualify.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo iQualify LMS espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME | Atributo de Origem|
    | --- | --- |
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "your attribute" |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

    > [!Note]
    > O atributo **person_id** é **opcional**

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o iQualify LMS**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-iqualify-lms-single-sign-on"></a>Configurar o logon único do iQualify LMS

1. Abra uma nova janela do navegador e conecte-se ao ambiente do iQualify como administrador.

1. Quando estiver conectado, clique no avatar no canto superior direito e escolha **Configurações da conta**

    ![Configurações da conta](./media/iqualify-tutorial/setting1.png)

1. Na área de configurações de conta, clique no menu da faixa de opções à esquerda e escolha **INTEGRAÇÕES**

    ![INTEGRAÇÕES](./media/iqualify-tutorial/setting2.png)

1. Em INTEGRAÇÕES, clique no ícone **SAML**.

    ![Ícone SAML](./media/iqualify-tutorial/setting3.png)

1. Na caixa de diálogo **Configurações de Autenticação de SAML**, realize as seguintes etapas:

    ![Configurações de Autenticação SAML](./media/iqualify-tutorial/setting4.png)

    a. Na caixa **URL DO SERVIÇO DE LOGON ÚNICO SAML**, cole o valor copiado da **URL de Logon** na janela de configuração de aplicativos do Azure AD.

    b. Na caixa **URL DE LOGOFF SAML**, cole o valor da **URL de Logoff** copiado da janela de configuração de aplicativos do Azure AD.

    c. Abra o arquivo de certificados baixado no bloco de notas, copie o conteúdo e cole-o na caixa **CERTIFICADO PÚBLICO**.

    d. Em **RÓTULO DO BOTÃO DE LOGON**, insira o nome do botão a ser exibido na página de logon.

    e. Clique em **SALVAR**.

    f. Clique em **ATUALIZAR**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concederá acesso ao iQualify LMS a Brenda Fernandes para habilitá-la a usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **iQualify LMS**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **iQualify LMS**.

    ![O link do iQualify LMS na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-iqualify-lms-test-user"></a>Criar usuário de teste do iQualify LMS

Nesta seção, um usuário chamado Brenda Fernandes é criado no iQualify LMS. O iQualify LMS é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no iQualify LMS, ele será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco iQualify LMS no Painel de Acesso, você deve acessar a página de logon do aplicativo iQualify LMS. 

   ![página de logon](./media/iqualify-tutorial/login.png) 

Clique no botão **Entrar com o Azure AD** e você deverá ser automaticamente conectado no seu aplicativo do iQualify LMS.

Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)