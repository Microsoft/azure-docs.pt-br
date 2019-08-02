---
title: 'Tutorial: Integração do Azure Active Directory ao Adobe Experience Manager | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81032fbad21b18b0b7ca2e7662b0c4b4b6c10901
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107279"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integração do Azure Active Directory ao Adobe Experience Manager

Neste tutorial, você aprenderá como integrar o Adobe Experience Manager ao Azure AD (Azure Active Directory).
A integração do Azure Experience Manager ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Adobe Experience Manager.
* Você pode permitir que os usuários sejam conectados automaticamente ao Adobe Experience Manager (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adobe Experience Manager, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Adobe Experience Manager

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Experience Manager dá suporte ao SSO iniciado por **SP e IDP**

* O Adobe Experience Manager dá suporte ao provisionamento de usuário **Just-in-Time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionando o Adobe Experience Manager da galeria

Para configurar a integração do Adobe Experience Manager ao Azure AD, será necessário adicionar o Adobe Experience Manager da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Experience Manager da galeria, siga as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Experience Manager**, selecione **Adobe Experience Manager** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Adobe Experience Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Adobe Experience Manager](#create-adobe-experience-manager-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Experience Manager que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do AD do Azure com o [nome do aplicativo], execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adobe Experience Manager**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único sobre Domínio e URLs do Adobe Experience Manager](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite um valor exclusivo definido também no servidor do AEM.

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor da URL de Resposta com a URL de Resposta real. Para obter esse valor, contate a [equipe de suporte ao Cliente do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único sobre Domínio e URLs do Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite a URL de servidor do Adobe Experience Manager.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Adobe Experience Manager**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Configurar o Logon Único do Adobe Experience Manager

1. Abra o portal de administração do **Adobe Experience Manager** em outra janela do navegador.

2. Selecione **Configurações** > **Segurança** > **Usuários**.

    ![Configurar o botão salvar do logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selecione **Administrador** ou qualquer outro usuário relevante.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selecione **Configurações da conta** > **Gerenciar TrustStore**.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Em **Adicionar Certificado do arquivo CER**, clique no botão **Selecionar Arquivo de Certificado**. Navegue até o arquivo de certificado que você baixou do Portal do Azure e selecione-o.

    ![Configurar o botão salvar do logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. O certificado é adicionado ao TrustStore. Observe o alias do certificado.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na página **Usuários**, selecione **authentication-service**.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selecione **Configurações de conta** > **Criar/gerenciar repositório de chaves**. Criar repositório de chaves fornecendo uma senha.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Volte para a tela de administrador. Em seguida, selecione **Configurações** > **Operações** > **Console Web**.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Isso abre a página de configuração.

    ![Configurar o botão salvar do logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Localize **Manipulador de autenticação do Adobe Granite SAML 2.0**. Selecione então o ícone **Adicionar**.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Siga as ações a seguir nesta página.

    ![Botão Salvar em Configurar Logon Único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na caixa **Caminho**, digite **/** .

    b. Na caixa **URL do IDP**, insira o valor da **URL de Logon** copiado do portal do Azure.

    c. No **Alias do certificado IDP**, digite o valor do **Alias do Certificado** que você adicionou na TrustStore.

    d. Na caixa **ID da Entidade Fornecida pela Segurança**, insira o valor exclusivo do **Identificador do Azure AD** configurado no portal do Azure.

    e. Na caixa **URL do Serviço do Consumidor de Declaração**, digite o valor da **URL de Resposta** que você configurou no Portal do Azure.

    f. Na caixa **Senha do Repositório de Chaves**, digite a **Senha** que você definiu no repositório de chaves.

    g. Na caixa **ID do Atributo de Usuário**, insira a **ID do Nome** ou outra ID de usuário relevante em seu caso.

    h. Selecione **Criar Usuários CRX Automaticamente**.

    i. Na caixa **URL de Logoff**, insira o valor exclusivo da **URL de Logoff** obtido no portal do Azure.

    j. Clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Adobe Experience Manager.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Adobe Experience Manager**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Adobe Experience Manager**.

    ![O link do Adobe Experience Manager na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adobe-experience-manager-test-user"></a>Criar um usuário de teste do Adobe Experience Manager

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Adobe Experience Manager. Se você selecionar a opção **Criar Usuários CRX Automaticamente**, os usuários serão criados automaticamente após a autenticação bem-sucedida.

Caso deseje criar usuários manualmente, trabalhe com a [equipe de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os usuários à plataforma Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adobe Experience Manager no Painel de Acesso, você deverá ser conectado automaticamente ao Adobe Experience Manager, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
