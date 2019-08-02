---
title: 'Tutorial: Integração do Azure Active Directory ao FreshDesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ed903e71a019d0bdb1b62503913bd696e9d8a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102055"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração do Azure Active Directory ao FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk ao Azure AD (Azure Active Directory).
A integração do FreshDesk ao Azure AD oferece os seguintes benefícios:

* No Microsoft Azure AD, é possível controlar quem tem acesso ao FreshDesk.
* Você pode permitir que os usuários sejam conectados automaticamente ao FreshDesk (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao FreshDesk, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do FreshDesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O FreshDesk dá suporte ao SSO iniciado por **SP**

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar o FreshDesk da galeria

Para configurar a integração do FreshDesk ao Azure AD, você precisa adicionar o FreshDesk da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FreshDesk por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **FreshDesk**, selecione **FreshDesk** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![FreshDesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o FreshDesk, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FreshDesk.

Para configurar e testar o logon único do Azure AD com o FreshDesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do FreshDesk](#configure-freshdesk-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do FreshDesk](#create-freshdesk-test-user)** – para ter um equivalente de Brenda Fernandes no FreshDesk que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o FreshDesk, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **FreshDesk**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Domínio FreshDesk e informações de logon único de URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o padrão `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o padrão `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com [equipe de suporte do cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo FreshDesk espera as asserções de SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão de **Identificador de Usuário Único** é **user.userprincipalname**, mas o FreshDesk espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização. 

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:
    
    | NOME | Atributo de Origem |
    | ---------------| --------------- |
    | Identificador Exclusivo do Usuário | user.mail |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Abra o **Prompt de Comando** e execute os seguintes comandos:

    a. Insira o valor `certutil.exe -dump FreshDesk.cer` no prompt de comando.

    > [!NOTE]
    > Aqui, **FreshDesk.cer** é o certificado que você baixou do portal do Azure.

    b. Cópia de **Cert Hash(sha256)** valor e cole-o bloco de notas. 

9. Na seção **Configurar o FreshDesk**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-freshdesk-single-sign-on"></a>Configurar o Logon Único do FreshDesk

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

2. Selecione o **Ícone de Configurações** e, na seção **Segurança**, realize as seguintes etapas:

    ![Logon Único](./media/freshdesk-tutorial/IC776770.png "Logon Único")
  
    a. Para **SSO (Logon Único)** , selecione **Ativado**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de Logon SAML**, cole o valor do **URL de Login**, que você copiou do portal do Azure.

    d. Na caixa de texto **URL de logoff**, cole o **valor do URL de logout**, copiado do portal do Azure.

    e. Na caixa de texto **Impressão digital de certificado de segurança**, cole o valor **Cert Hash (sha256)** que você obteve anteriormente.
  
    f. Clique em **Save** (Salvar).

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

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao FreshDesk.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **FreshDesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **FreshDesk**.

    ![O link do FreshDesk na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-freshdesk-test-user"></a>Criar um usuário de teste do FreshDesk

Para permitir que os usuários do Azure AD façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Freshdesk** .

2. No menu na parte superior, clique em **Administrador**.

    ![Admin](./media/freshdesk-tutorial/IC776772.png "Admin")

3. Na guia **Configurações Gerais**, clique em **Agentes**.
  
    ![Agentes](./media/freshdesk-tutorial/IC776773.png "Agentes")

4. Clique em **Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/IC776774.png "Novo Agente")

5. Na caixa de diálogo Informações sobre o Agente, execute as seguintes etapas:

    ![Informações de Agente](./media/freshdesk-tutorial/IC776775.png "Informações de Agente")

    a. Na caixa de texto **Email** , digite o endereço de email do Azure AD da conta do Azure AD que você deseja provisionar.

    b. Na caixa de texto **Nome Completo** , digite o nome da conta do Azure AD que você deseja provisionar.

    c. Na caixa de texto **Título** , digite a conta do Azure AD que você deseja provisionar.

    d. Clique em **Save** (Salvar).

    >[!NOTE]
    >O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.
    >
    >[!NOTE]
    >É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do AAD para o FreshDesk.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do FreshDesk no Painel de Acesso, você deverá ser conectado automaticamente ao FreshDesk, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

