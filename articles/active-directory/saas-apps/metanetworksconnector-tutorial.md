---
title: 'Tutorial: Integração do Azure Active Directory com o Meta Networks Connector | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o Meta Networks Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 82097b075ab86c4de5ab45e3110c9bc192ac7edb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097363"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Integração do Azure Active Directory com o Meta Networks Connector

Neste tutorial, você aprenderá a integrar o Meta Networks Connector ao Azure AD (Azure Active Directory).
A integração do Meta Networks Connector com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Meta Networks Connector.
* É possível permitir que seus usuários entrem automaticamente no Meta Networks Connector (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Meta Networks Connector, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único do Meta Networks Connector

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Meta Networks Connector é compatível com SSO iniciado por **SP** e **IDP**
 
* O Meta Networks Connector é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Adicionando o Meta Networks Connector da galeria

Para configurar a integração do Meta Networks Connector ao Azure AD, você precisa adicionar o Meta Networks Connector da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Meta Networks Connector da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Meta Networks Connector**, selecione **Meta Networks Connector** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![Meta redes conector na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Meta Networks Connector com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Meta Networks Connector.

Para configurar e testar o logon único do Azure AD com o Meta Networks Connector, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Meta Networks Connector](#configure-meta-networks-connector-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Meta Networks Connector](#create-meta-networks-connector-test-user)** – para ter um equivalente de Britta Simon no Meta Networks Connector que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Meta Networks Connector, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Meta Networks Connector**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Meta Networks Connector Domain e informações de conexão única de URLs](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Meta Networks Connector Domain e informações de conexão única de URLs](common/both-advanced-urls.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador real, o URL de resposta e o URL de logon explicados posteriormente no tutorial.

6. O aplicativo Meta Networks Connector espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)
    
7. Além do indicado acima, o aplicativo Meta Networks Connector espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | NOME | Atributo de origem | Namespace|
    | ---------------| --------------- | -------- |
    | nome | user.givenname | |
    | sobrenome | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Nome | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

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

9. Na seção **Configurar o Meta Networks Connector**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-meta-networks-connector-single-sign-on"></a>Configurar logon único do Meta Networks Connector

1. Abra uma nova guia no seu navegador e faça login na sua conta de administrador do Meta Networks Connector.
    
    > [!NOTE]
    > O Meta Networks Connector é um sistema seguro. Portanto, antes de acessar o portal, você precisa ter seu endereço IP público adicionado a uma lista de permissões do lado do portal. Para obter seu endereço IP público, siga o link especificado abaixo [aqui](https://whatismyipaddress.com/). Enviar seu endereço IP à [equipe de suporte do cliente de conector de redes de Meta](mailto:support@metanetworks.com) para ter seu endereço IP adicionado a uma lista de permissões.
    
2. Vá para **Administrator** e selecione **configurações**.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Certifique-se **Log de tráfego da Internet** e **força VPN MFA** estiver definida como off.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Vá para **Administrator** e selecione **SAML**.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Execute as seguintes etapas na **detalhes** página:
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Copie o valor de **SSO URL** e cole-o na caixa de texto **URL de login** na seção **Domínio do conector de Meta Networks e URLs**.
    
    b. Copie **o URL do destinatário** e cole-o na caixa de texto **URL de resposta** na seção **Domínio e URLs do conector do Meta Networks**.
    
    c. Cópia **URI de audiência (ID da entidade SP)** de valor e cole-o na **identificador (ID da entidade)** caixa de texto o **Meta redes conector do domínio e URLs da** seção.
    
    d. Habilitar o SAML
    
6. Sobre o **geral** guia execute as seguintes etapas:

    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure5.png)

    a. Na **URL de Logon único do Provedor de identidade**, cole o valor da **URL de logon**, que você copiou do portal do Azure.

    b. Em **Emissor do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no bloco de notas, cole-o na **certificado x. 509** caixa de texto.

    d. Habilitar o **provisionamento Just-in-Time**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que o Brenda Fernandes use o logon único do Azure, concedendo acesso ao Meta Networks Connector.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, **Meta Networks Connector**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Meta redes conector**.

    ![O link de Meta redes conector na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-meta-networks-connector-test-user"></a>Criar usuário de teste do Meta Networks Connector

Nesta seção, um usuário chamado Brenda Fernandes é criado no Meta Networks Connector. O Meta Networks Connector dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Meta Networks Connector, um será criado quando você tentar acessar o Meta Networks Connector.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com [equipe de suporte do cliente de conector de redes de Meta](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco do Meta Networks Connector no Painel de Acesso, deve entrar automaticamente no Meta Networks Connector para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

