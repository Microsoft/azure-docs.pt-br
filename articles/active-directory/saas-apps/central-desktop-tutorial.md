---
title: 'Tutorial: Integração do Azure Active Directory ao Central Desktop | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e a Área de Trabalho Central.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58fcd21c7e6e3f2336d3e809584451dc38f6276b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105731"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Azure Active Directory ao Central Desktop

Neste tutorial, você aprenderá a integrar a Área de Trabalho Central ao Azure AD (Azure Active Directory).
A integração da Área de Trabalho Central ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso à Área de Trabalho Central.
* Você pode permitir que os usuários sejam conectados automaticamente ao Central Desktop (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD à Área de Trabalho Central, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Central Desktop

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Central Desktop dá suporte ao SSO iniciado por **SP**

## <a name="adding-central-desktop-from-the-gallery"></a>Adicionar a Área de Trabalho Central da galeria

Para configurar a integração da Área de Trabalho Central ao Azure AD, você precisa adicionar a Área de Trabalho Central da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Central Desktop por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Central Desktop**, selecione **Central Desktop** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Área de Trabalho Central na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Central Desktop, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Central Desktop.

Para configurar e testar o logon único do Azure AD com a Área de Trabalho Central, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Central Desktop](#configure-central-desktop-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Central Desktop](#create-central-desktop-test-user)** – para ter um equivalente de Brenda Fernandes no Central Desktop que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Central Desktop, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Central Desktop**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Central Desktop](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.centraldesktop.com`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Central Desktop](https://imeetcentral.com/contact-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o Central Desktop**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-central-desktop-single-sign-on"></a>Configurar o logon único do Central Desktop

1. Faça logon no locatário da sua **Área de Trabalho Central**.

2. Vá para **Configurações**. Selecione **Avançado** e, em seguida, selecione **Logon Único**.

    ![Configuração – Avançada](./media/central-desktop-tutorial/ic769563.png "Configuração – Avançada")

3. Na página **Configurações de Logon Único** , execute as seguintes etapas:

    ![Configurações de logon único](./media/central-desktop-tutorial/ic769564.png "Configurações de Logon Único")

    a. Selecione **Habilitar Logon Único do SAML v2**.

    b. Na caixa **URL do SSO**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa **URL de Logon SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa **URL de Logoff SSO**, cole o valor da **URL de Logoff** copiado do portal do Azure.

4. Na seção **Método de Verificação de Assinatura de Mensagem**, execute as seguintes etapas:

    ![Método de verificação da assinatura da mensagem](./media/central-desktop-tutorial/ic769565.png "Message Signature Verification Method")
    
    a. Selecione **Certificado**.

    b. Na lista **Certificado de SSO**, selecione **RSH SHA256**.

    c. Abra seu certificado baixado no Bloco de Notas. Em seguida, copie o conteúdo do certificado e cole-o no campo **Certificado SSO**.

    d. Selecione **Exibir um link para a sua página de logon do SAMLv2**.

    e. Selecione **Atualização**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Central Desktop.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Central Desktop**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Área de Trabalho Central**.

    ![O link do Central Desktop na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-central-desktop-test-user"></a>Criar um usuário de teste do Central Desktop

Para que usuários do Azure AD consigam entrar, eles devem ser provisionados no aplicativo da Área de Trabalho Central. Esta seção descreve como criar contas de usuário do Azure AD na Área de Trabalho Central.

> [!NOTE]
> Para provisionar contas de usuário do Azure AD, você pode usar qualquer outra ferramenta de criação de conta de usuário da Área de Trabalho Central ou APIs fornecidas pela Área de Trabalho Central.

**Para provisionar contas de usuário no Central Desktop:**

1. Entre no locatário da sua Área de Trabalho Central.

2. Selecione **Pessoas** e, em seguida, **Adicionar Membros Internos**.

    ![Pessoas](./media/central-desktop-tutorial/ic781051.png "Pessoas")

3. Na caixa **Endereço de Email de Novos Membros**, digite uma conta do Azure AD que você deseja provisionar e, em seguida, selecione **Próximo**.

    ![Endereços de Email de Novos Membros](./media/central-desktop-tutorial/ic781052.png "Endereços de Email de Novos Membros")

4. Selecione **Adicionar Membro(s) Interno(s)** .

    ![Adicionar membro interno](./media/central-desktop-tutorial/ic781053.png "Adicionar membro interno")
  
   > [!NOTE]
   > Os usuários que você adicionar recebem um email que inclui um link de confirmação para ativar suas contas.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Central Desktop no Painel de Acesso, você deverá ser conectado automaticamente ao Central Desktop, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
