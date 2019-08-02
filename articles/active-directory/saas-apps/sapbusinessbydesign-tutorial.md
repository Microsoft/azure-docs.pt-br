---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Business ByDesign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091685"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração do Azure Active Directory ao SAP Business ByDesign

Neste tutorial, você aprenderá a integrar o SAP Business ByDesign ao Azure AD (Azure Active Directory).
A integração do SAP Business ByDesign ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SAP Business ByDesign.
* Você pode habilitar os usuários a fazer logon automaticamente no SAP Business ByDesign (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Business ByDesign, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do SAP Business ByDesign habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP Business ByDesign é compatível com o SSO iniciado por **SP**

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adicionar o SAP Business ByDesign da galeria

Para configurar a integração do SAP Business ByDesign ao Azure AD, você precisará adicionar o SAP Business ByDesign da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Business ByDesign da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAP Business ByDesign**, selecione **SAP Business ByDesign** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SAP Business ByDesign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Business ByDesign, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Business ByDesign.

Para configurar e testar o logon único do Azure AD com o SAP Business ByDesign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SAP Business ByDesign](#create-sap-business-bydesign-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Business ByDesign vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP Business ByDesign, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SAP Business ByDesign**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SAP Business ByDesign](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<servername>.sapbydesign.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo SAP Business ByDesign espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Clique no ícone **Editar** para editar o valor de **Valor do identificador de nome**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na seção **Gerenciar declarações do usuário**, realize as seguintes etapas: ![imagem](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecione **Transformação** como uma **Origem**.

    b. Na lista suspensa **Transformação**, selecione **ExtractMailPrefix()** .

    c. Na lista suspensa **Parâmetro 1**, selecione o atributo de usuário que você deseja usar na implementação. Por exemplo, se você quiser usar EmployeeID como identificador exclusivo de usuário e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione user.extensionattribute2.

    d. Clique em **Save** (Salvar).

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o SAP Business ByDesign**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Configurar o logon único do SAP Business ByDesign

1. Faça logon no portal do SAP Business ByDesign com direitos de administrador.

2. Navegue até **Tarefa Comum de Gerenciamento de Aplicativos e Usuários** e clique na guia **Provedor de Identidade**.

3. Clique em **Novo Provedor de Identidade** e selecione o arquivo XML de metadados baixado no Portal do Azure. Com a importação dos metadados, o sistema carrega automaticamente o certificado de assinatura e o certificado de criptografia necessários.

    ![Configurar o logon único](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir a **URL de Serviço de Consumidor de Declaração** na solicitação SAML, selecione **Incluir URL de Serviço de Consumidor de Declaração**.

5. Clique em **Ativar Logon Único**.

6. Salve suas alterações.

7. Clique na guia **Meu Sistema** .

    ![Configurar o logon único](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Na caixa de texto **URL de Logon do Azure AD**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    ![Configurar o logon único](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especifique se o funcionário pode escolher manualmente entre fazer logon com uma ID de usuário e senha ou SSO, selecionando **Seleção de Provedor de Identidade Manual**.

10. Na seção **URL de SSO**, especifique a URL que deve ser usada pelo funcionário para fazer logon no sistema.
    Na lista suspensa URL Enviada ao Funcionário, você pode escolher entre as seguintes opções:

    **URL não SSO**

    O sistema envia apenas a URL normal do sistema ao funcionário. O funcionário não pode fazer logon usando o SSO e deve usar uma senha ou um certificado em vez disso.

    **URL de SSO**

    O sistema envia apenas a URL de SSO ao funcionário. O funcionário pode fazer logon usando o SSO. A solicitação de autenticação é redirecionada por meio do IdP.

    **Seleção Automática**

    Se o SSO não estiver ativo, o sistema enviará a URL normal do sistema ao funcionário. Se o SSO estiver ativo, o sistema verificará se o funcionário tem uma senha. Se uma senha estiver disponível, a URL de SSO e a URL não SSO serão enviadas ao funcionário. No entanto, se o funcionário não tiver uma senha, apenas a URL de SSO será enviada ao funcionário.

11. Salve suas alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP Business ByDesign.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SAP Business ByDesign**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SAP Business ByDesign**.

    ![O link do SAP Business ByDesign na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sap-business-bydesign-test-user"></a>Criar um usuário de teste do SAP Business ByDesign

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SAP Business ByDesign. Trabalhe com a [equipe de suporte ao cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os usuários à plataforma SAP Business ByDesign. 

> [!NOTE]
> Verifique se o valor de NameID corresponde ao campo de nome de usuário na plataforma SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do SAP Business ByDesign no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo SAP Business ByDesign para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
