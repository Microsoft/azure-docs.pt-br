---
title: 'Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eff07f157f6011b9b561334c00b3a4d94989ff5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091848"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication

Neste tutorial, você aprenderá a integrar o SAP Cloud Platform Identity Authentication ao Azure Active Directory (Azure AD).
Integrar a Autenticação de Identidade da SAP Cloud Platform ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso à SAP Cloud Platform Identity Authentication.
* Você pode permitir que seus usuários entrem automaticamente na SAP Cloud Platform Identity Authentication (logon único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SAP Cloud Platform Identity Authentication, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura da SAP Cloud Platform Identity Authentication habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A SAP Cloud Platform Identity Authentication é compatível com SSO iniciado por **SP** e **IDP**

Antes de se aprofundar nos detalhes técnicos, é essencial entender os conceitos que serão examinados. Os AD FS (Serviços de Federação do Active Directory) e a Autenticação de Identidade da SAP Cloud Platform permitem que você implemente o SSO em aplicativos ou serviços protegidos pelo Azure AD (como um IdP) com aplicativos SAP e serviços protegidos pela Autenticação de Identidade da SAP Cloud Platform.

Atualmente, o SAP Cloud Platform Identity Authentication atua como um provedor de identidade de proxy para aplicativos SAP. O Azure Active Directory por sua vez atua como o principal provedor de identidade nessa configuração. 

O diagrama a seguir ilustra esse relacionamento:

![Criação de um usuário de teste do AD do Azure](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com essa configuração, seu locatário de SAP Cloud Platform Identity Authentication é configurado como um aplicativo confiável no Azure Active Directory.

Todos os serviços e aplicativos SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de Autenticação de Identidade da SAP Cloud Platform.

Portanto, a autorização para conceder acesso aos serviços e aplicativos SAP precisa ocorrer na Autenticação de Identidade da SAP Cloud Platform (diferentemente do Azure Active Directory).

Ao configurar a autenticação de identidade da SAP Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa configurar declarações individuais ou declarações SAML.

> [!NOTE]
> Atualmente, somente o SSO da Web foi testado por ambas as partes. Os fluxos necessários para a comunicação de aplicativo a API ou de API para API devem funcionar, mas ainda não foram testados. Eles serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar SAP Cloud Platform Identity Authentication a partir da galeria

Para configurar a integração da Autenticação de Identidade da SAP Cloud Platform no Azure AD, você precisa adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Autenticação de Identidade da SAP Cloud Platform**, selecione **Autenticação de Identidade da SAP Cloud Platform** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Adicionar SAP Cloud Platform Identity Authentication na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único da SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste da SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)** – para ter um equivalente de Brenda Fernandes na SAP Cloud Platform Identity Authentication que esteja vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do AD do Azure com o [nome do aplicativo], execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SAP Cloud Platform Identity Authentication**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar no modo **IDP** iniciado:

    ![Informações sobre logon único de Domínio e URLs do SAP Cloud Platform Identity Authentication](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Cliente de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esses valores. Se você não entender esse valor de identificador, leia a documentação do SAP Cloud Platform Identity Authentication sobre [Configuração do SAML 2.0 do Locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações sobre logon único de Domínio e URLs do SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com o URL de Logon real. Use a URL de logon do aplicativo comercial específica. Entre em contato com a [equipe de suporte do Cliente de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) se você tiver qualquer dúvida.

6. O aplicativo de SAP Cloud Platform Identity Authentication espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

7. Se o seu aplicativo SAP espera um atributo, como **firstName**, adicione o atributo **firstName** na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome**, digite o nome do atributo “firstName”.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, selecione o valor do atributo "user.givenname".

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar a SAP Cloud Platform Identity Authentication**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Configurar logon único da SAP Cloud Platform Identity Authentication

1. Para configurar o SSO para o seu aplicativo, vá para o Console de administração da Autenticação de Identidade da SAP Cloud Platform. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, leia a documentação sobre SAP Cloud Platform Identity Authentication em [Integração com o Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. No portal do Azure, selecione o botão **Salvar**.

3. Continue com o seguinte somente se quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção **Adicionar SAP Cloud Platform Identity Authentication por meio da galeria**.

4. No portal do Azure, na página de integração de aplicativos de **SAP Cloud Platform Identity Authentication**, selecione **Logon vinculado**.

    ![Configurar o logon vinculado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Salve a configuração.

> [!NOTE]
> O novo aplicativo aproveita a configuração de logon único do aplicativo SAP anterior. Certifique-se de usar os mesmos Provedores de Identidade Corporativa no Console de Administração de Autenticação de Identidade da SAP Cloud Platform.

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

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao SAP Cloud Platform Identity Authentication.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **SAP Cloud Platform Identity Authentication**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SAP Cloud Platform Identity Authentication**.

    ![Link do SAP Cloud Platform Identity Authentication na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar um usuário de teste da SAP Cloud Platform Identity Authentication

Você não precisa criar um usuário no SAP Cloud Platform Identity Authentication. Os usuários que estão no repositório de usuários do Azure AD podem usar a funcionalidade de SSO.

O SAP Cloud Platform Identity Authentication é compatível com a opção de Federação de Identidades. Essa opção permite que o aplicativo verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários do SAP Cloud Platform Identity Authentication.

A opção de Federação de Identidades é desabilitada por padrão. Se a Federação de Identidades estiver habilitada, somente os usuários que são importados na Autenticação de Identidade da SAP Cloud Platform poderão acessar o aplicativo.

Para obter mais informações sobre como habilitar ou desabilitar a Federação de Identidades com a Autenticação de Identidade da SAP Cloud Platform, consulte Habilitar a Federação de Identidades com a Autenticação de Identidade da SAP Cloud Platform em [Configurar a Federação de Identidades com o repositório de usuários da Autenticação de Identidade da SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco da SAP Cloud Platform Identity Authentication no Painel de Acesso, você deverá fazer logon automaticamente no SAP Cloud Platform Identity Authentication, para o qual configurou o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
