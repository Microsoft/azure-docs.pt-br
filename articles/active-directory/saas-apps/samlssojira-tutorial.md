---
title: 'Tutorial: Integração do Azure Active Directory com o SSO do SAML para Jira da Resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e SSO do SAML para Jira da Resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67be6251b1500ff85b833bbb7c7348a76045496f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092076"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Azure Active Directory com o SSO do SAML para Jira da Resolution GmbH

Neste tutorial, você aprenderá a configurar o SSO do SAML para Jira pela Resolution GmbH com o Azure AD (Azure Active Directory).
A integração de SSO do SAML para Jira da Resolution GmbH com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem entra no Jira com o plug-in de SSO do SAML da resolution GmbH.
* É possível permitir que seus usuários entrem automaticamente no Jira com suas contas do Azure AD usando o SSO do SAML para Jira da resolution GmbH (Logon único).
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD e o SSO do SAML para Jira da resolution GmbH, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você poderá obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do SSO do SAML para Jira da Resolution GmbH habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO do SAML para Jira da resolution GmbH dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Como adicionar um aplicativo empresarial para logon único

Para configurar o logon único no Azure AD, é necessário adicionar um novo aplicativo empresarial. Na galeria, há um aplicativo pré-configurado predefinido para isso, o **SSO do SAML para Jira da resolution GmbH**.

**Para adicionar SSO do SAML para Jira da Resolution GmbH da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, clique em **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do SAML para Jira da resolution GmbH**, selecione **SSO do SAML para Jira da resolution GmbH** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo. Também é possível alterar o nome do aplicativo empresarial.

     ![SSO do SAML para Jira da Resolution GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Configurar e testar o logon único com o plug-in de SSO do SAML e com o Azure AD

Nesta seção, você testará e configurará o logon único para Jira para um usuário do Azure AD. Isso será feito para um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para Jira da Resolution GmbH.

Para configurar e testar o logon único, é necessário concluir as etapas a seguir:

1. **[Configurar o aplicativo empresarial do Azure AD para logon único](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – configure o aplicativo empresarial do Azure AD para o logon único
2. **[Configurar o plug-in de SSO do SAML da sua instância do Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** – configure o Logon Único no aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – crie um usuário de teste no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – como permitir que o usuário de teste use o logon único do Azure.
1. **[Criar o usuário de teste no Jira](#create-the-test-user-also-in-jira)** – Crie um equivalente do usuário de teste no Jira para o usuário de teste do Azure AD.
1. **[Testar logon único](#test-single-sign-on)** – verifique se a configuração funciona.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configurar o aplicativo empresarial do Azure AD para logon único

Nesta seção, você configurará o logon único no portal do Azure.

Para configurar o logon único com o SSO do SAML para Jira da resolution GmbH, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), no aplicativo empresarial **SSO do SAML para Jira da resolution GmbH** recém-criado, selecione **Logon único** no painel esquerdo.

    ![Link Configurar logon único](common/select-sso.png)

2. Para **Selecionar um método de logon único**, selecione o modo **SAML** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Em seguida, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se desejar configurar o aplicativo no modo iniciado por **IDP**, siga estas etapas:

    ![Informações de Domínio e URLs do logon único do SAML SSO para Jira da Resolution GmbH](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **Definir URLs adicionais** e siga esta etapa, se desejar configurar o aplicativo no modo iniciado por **SP**:

    ![Informações de Domínio e URLs do logon único do SAML SSO para Jira da Resolution GmbH](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o Identificador, URL de Resposta e URL de entrada, substitua **\<url-base-do-servidor>** pela URL base de sua instância do Jira. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure. Caso tenha algum problema, contate-nos em [equipe de suporte ao cliente do SSO do SAML para Jira da resolution GmbH](https://www.resolution.de/go/support).

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, baixe o **XML de metadados de Federação** e salve-o em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Configurar o plug-in de SSO do SAML da sua instância do Jira 

1. Em uma janela diferente do navegador da Web, entre na instância do Jira como administrador.

2. Passe o mouse sobre a engrenagem no lado direito e clique em **Gerenciar aplicativos**.
    
    ![Configurar o logon único](./media/samlssojira-tutorial/addon1.png)

3. Se você for redirecionado à página Acesso de Administrador, insira a **Senha** e clique no botão **Confirmar**.

    ![Configurar o logon único](./media/samlssojira-tutorial/addon2.png)

4. Normalmente, o Jira redireciona você ao mercado da Atlassian. Caso contrário, clique em **Encontrar novos aplicativos** no painel esquerdo. Pesquise **SSO (Logon Único) do SAML para JIRA** e clique no botão **Instalar** para instalar o plug-in do SAML.

    ![Configurar o logon único](./media/samlssojira-tutorial/store.png)

5. A instalação do plug-in será iniciada. Quando estiver pronto, clique no botão **Fechar**.

    ![Configurar o logon único](./media/samlssojira-tutorial/store-2.png)

    ![Configurar o logon único](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **Gerenciar**.

    ![Configurar o logon único](./media/samlssojira-tutorial/store-4.png)
    
8. Em seguida, clique em **Configurar** para configurar o plug-in instalado recentemente.

    ![Configurar o logon único](./media/samlssojira-tutorial/store-5.png)

9. No assistente **Configuração do plug-in de Logon Único do SAML**, clique em **Adicionar novo IdP** para configurar o Azure AD como um novo Provedor de Identidade.

    ![Configurar o logon único](./media/samlssojira-tutorial/addon4.png) 

10. Na página **Escolha seu Provedor de Identidade SAML**, siga estas etapas:

    ![Configurar o logon único](./media/samlssojira-tutorial/addon5a.png)
 
    a. Definir o **Azure Active Directory** como o tipo de IdP.
    
    b. Adicione o **Nome** do Provedor de Identidade (por exemplo, Azure AD).
    
    c. Adicione uma **Descrição** (opcional) do Provedor de Identidade (por ex., Azure AD).
    
    d. Clique em **Próximo**.
    
11. Na página **Configuração do provedor de identidade**, clique em **Avançar**.
 
    ![Configurar o logon único](./media/samlssojira-tutorial/addon5b.png)

12. Na página **Importar metadados de IdP do SAML**, execute as seguintes etapas:

    ![Configurar o logon único](./media/samlssojira-tutorial/addon5c.png)

    a. Clique no botão **Selecionar Arquivo XML de Metadados** e selecione o arquivo **XML de Metadados de Federação** baixado anteriormente.

    b. Clique no botão **Importar**.
     
    c. Espere um pouco até que a importação seja bem-sucedida.  
     
    d. Clique no botão **Avançar**.
    
13. Na página **Atributo e transformação da ID de usuário**, clique no botão **Avançar**.

    ![Configurar o logon único](./media/samlssojira-tutorial/addon5d.png)
    
14. Na página **Criação de usuário e atualização**, clique em **Salvar e Avançar** para salvar as configurações.
    
    ![Configurar o logon único](./media/samlssojira-tutorial/addon6a.png)
    
15. Na página **Testar suas configurações**, clique em **Ignorar teste e configurar manualmente** para ignorar o teste de usuário por enquanto. Isso será executado na próxima seção e requer algumas configurações no portal do Azure.
    
    ![Configurar o logon único](./media/samlssojira-tutorial/addon6b.png)
    
16. Clique em **OK** para ignorar o aviso.
    
    ![Configurar o logon único](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes. Com o usuário, você testará o logon único.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Escolha **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas **Propriedades do usuário**, siga estas etapas:

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **Brenda Fernandes**.
  
    b. No campo **Nome de usuário**, insira <b>BrittaSimon@contoso.com</b>.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você adicionará Brenda Fernandes ao aplicativo empresarial, que permite que ela use o logon único.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**. 

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, pesquise o aplicativo empresarial que você criou no início deste tutorial. Se você estiver seguindo as etapas do tutorial, ele se chama **SSO do SAML para Jira da resolution GmbH**. Se você deu outro nome a ele, pesquise esse nome.

    ![SSO do SAML para Jira da Resolution GmbH na lista de Aplicativos](common/all-applications.png)

3. No painel esquerdo, clique em **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na Lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, então, na caixa de diálogo **Selecionar Função**, selecione a função adequada para o usuário na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-the-test-user-also-in-jira"></a>Criar o usuário de teste também no Jira

Para permitir que usuários do Azure AD entrem no SSO do SAML para Jira da resolution GmbH, eles devem ser provisionados no SSO do SAML para Jira da resolution GmbH. No caso deste tutorial, é necessário realizar o provisionamento manualmente. No entanto, também há outros modelos de provisionamento disponíveis para o plug-in do SSO do SAML da resolution, por exemplo, provisionamento **Just-In-Time**. Veja sua documentação em [SSO do SAML da resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se você tiver uma dúvida sobre isso, contate o suporte no [suporte da resolution](https://www.resolution.de/go/support).

**Para provisionar manualmente uma conta de usuário, siga estas etapas:**

1. Entre na instância do Jira como administrador.

2. Passe o mouse sobre a engrenagem e escolha **Gerenciamento de usuário**.

   ![Adicionar Funcionário](./media/samlssojira-tutorial/user1.png)

3. Se você for redirecionado à página Acesso de Administrador, insira a **Senha** e clique no botão **Confirmar**.

    ![Adicionar Funcionário](./media/samlssojira-tutorial/user2.png) 

4. Na seção da guia **Gerenciamento de usuário**, clique em **criar usuário**.

    ![Adicionar Funcionário](./media/samlssojira-tutorial/user3-new.png) 

5. Na página da caixa de diálogo **"Criar usuário"** , execute as etapas a seguir. É necessário criar o usuário exatamente como no Azure AD:

    ![Adicionar Funcionário](./media/samlssojira-tutorial/user4-new.png) 

    a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário: <b>BrittaSimon@contoso.com</b>.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário: **Brenda Fernandes**.

    c. Na caixa de texto **Nome de usuário**, digite o endereço de email do usuário: <b>BrittaSimon@contoso.com</b>. 

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Criar usuário** para concluir a criação do usuário.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SSO do SAML para Jira da Resolution GmbH no Painel de Acesso, você deverá ser conectado automaticamente ao SSO do SAML para Jira da Resolution GmbH para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Também é possível testar o logon único navegando até [https://\<url-base-do-servidor>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Substitua **\<url-base-do-servidor>** pela URL base da sua instância do Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Habilitar redirecionamento de logon único para o Jira

Conforme observado na seção anterior, no momento, há duas maneiras de disparar o logon único. Usando o **portal do Azure** ou usando **um link especial para sua instância do Jira**. O plug-in de SSO do SAML da resolution GmbH também permite que você dispare o logon único; basta **acessar qualquer URL que aponta para sua instância do Jira**.

Em essência, todos os usuários que acessam o Jira serão redirecionados para o logon único após a ativação de uma opção no plug-in.

Para ativar o redirecionamento de SSO, faça o seguinte em **sua instância do Jira**:

1. Acesse a página de configuração do plug-in de SSO do SAML no Jira.
1. Clique em **Redirecionamento** no painel esquerdo.
![](./media/samlssojira-tutorial/ssore1.png)

1. Marque **Habilitar redirecionamento de SSO**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Pressione o botão **Salvar Configurações** no canto superior direito.

Após ativar a opção, ainda será possível chegar ao prompt de nome de usuário/senha se a opção **Habilitar nosso** estiver marcada navegando até [https://\<url-base-do-servidor>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Como sempre, substitua **\<url-base-do-servidor>** pela URL base.


## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

