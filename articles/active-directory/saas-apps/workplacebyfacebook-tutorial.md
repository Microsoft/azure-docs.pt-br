---
title: 'Tutorial: Integração do Azure Active Directory ao Workplace by Facebook| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db14df2dc24afbc8f7982d8d1e4e6429c1c4b1ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086793"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Azure Active Directory ao Workplace by Facebook

Neste tutorial, você aprenderá a integrar o Workplace by Facebook ao Azure AD (Azure Active Directory).
A integração do Workplace by Facebook ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Workplace by Facebook.
* Você pode permitir que seus usuários entrem automaticamente no Workplace (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workplace by Facebook, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Workplace by Facebook habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> O Facebook tem dois produtos, o Workplace Standard (gratuito) e o Workplace Premium (pago). Qualquer locatário do Workplace Premium pode configurar a integração de SCIM e SSO sem outras implicações para o custo ou as licenças necessárias. O SSO e o SCIM não estão disponíveis nas instâncias do Workplace Standard.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Workplace by Facebook dá suporte a SSO iniciado por **SP**
* O Workplace by Facebook dá suporte a **provisionamento Just-In-Time**
* O Workplace by Facebook dá suporte a **[provisionamento de usuário automático](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar o Workplace by Facebook da Galeria

Para configurar a integração do Workplace by Facebook ao Azure AD, você precisa adicionar o Workplace by Facebook da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workplace by Facebook da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Workplace by Facebook**, selecione **Workplace by Facebook** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Workplace by Facebook na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Workplace by Facebook, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workplace by Facebook.

Para configurar e testar o logon único do Azure AD com o Workplace by Facebook, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Workplace by Facebook](#configure-workplace-by-facebook-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Workplace by Facebook](#create-workplace-by-facebook-test-user)** – para ter um equivalente de Brenda Fernandes no Workplace by Facebook que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Workplace by Facebook, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Workplace by Facebook**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Workplace by Facebook](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instancename>.facebook.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Consulte a página Autenticação do Workplace Company Dashboard a fim de obter os valores corretos para a comunidade do Workplace.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Workplace**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Configurar o logon único do Workplace by Facebook

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Workplace by Facebook como administrador.
  
    > [!NOTE]
    > Como parte do processo de autenticação SAML, o Workplace poderá utilizar cadeias de consulta de até 2,5 quilobytes para passar parâmetros para o Azure AD.

2. No **Painel de Administração**, acesse a guia **Segurança**.

    ![Painel de Administração](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Na guia **Autenticação**, selecione **SSO (logon único)** e execute as seguintes etapas:

    ![Guia Autenticação](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Na caixa de texto **URL do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na **caixa de texto URI do Emissor do SAML**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Em **Redirecionamento de Logoff SAML** (Opcional), cole o valor da **URL de Logout** copiado do portal do Azure.

    d. Abra o **Certificado codificado em Base64** no bloco de notas baixado do Portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado SAML**.

    e. Copie a **URL do Público-alvo** da instância e cole-a na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica do SAML** no portal do Azure.

    f. Copie a **URL do Destinatário** da instância e cole-a na caixa de texto **URL de Logon** na seção **Configuração Básica do SAML** no portal do Azure.

    g. Role até o final da seção e clique no botão **Testar SSO**. Isso resultará na exibição de uma janela pop-up com a página de logon do Azure AD apresentada. Insira suas credenciais como de costume para se autenticar.

    **Solução de problemas:** Verifique se o endereço de email retornado do Azure AD é o mesmo da conta do Workplace na qual você está conectado.

    h. Depois que o teste for concluído com êxito, role até a parte inferior da página e clique no botão **Salvar**.

    i. Agora, todos os usuários que usam o Workplace verão a página de logon do Azure AD para autenticação.

4. **Redirecionamento de Logoff SAML (opcional)**  -

    Você pode optar por configurar uma URL de Logoff SAML, que pode ser usada para apontar para a página de logoff do Azure AD. Quando essa configuração for habilitada e configurada, o usuário não será mais direcionado para a página de logoff do Workplace. Em vez disso, o usuário será redirecionado para a URL que foi adicionada à configuração Redirecionamento de Logoff SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurando a frequência de reautenticação

É possível configurar o Workplace para solicitar uma verificação SAML todos os dias, a cada três dias, toda semana, a cada duas semanas, todos os meses ou nunca.

> [!NOTE]
> O valor mínimo para a verificação SAML em aplicativos móveis é definido como uma semana.

Você também pode forçar uma redefinição de SAML para todos os usuários usando o botão: Exigir autenticação SAML para todos os usuários agora.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Workplace by Facebook.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Workplace by Facebook**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Workplace by Facebook**.

    ![O link Workplace by Facebook na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-workplace-by-facebook-test-user"></a>Criar um usuário de teste do Workplace by Facebook

Nesta seção, um usuário chamado Brenda Fernandes será criado no Workplace by Facebook. O Workplace by Facebook dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no Workplace by Facebook, um novo será criado quando você tentar acessar o Workplace by Facebook.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Workplace by Facebook](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Workplace by Facebook no painel de acesso, você deve ser conectado automaticamente ao Workplace by Facebook para os quais você configura o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](workplacebyfacebook-provisioning-tutorial.md)
