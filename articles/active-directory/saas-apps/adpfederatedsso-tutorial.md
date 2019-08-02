---
title: 'Tutorial: Integração do Azure Active Directory com o ADP | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1597a4ca9cac7ba3885e863502f156d4c83aeed1
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516385"
---
# <a name="tutorial-integrate-adp-with-azure-active-directory"></a>Tutorial: Integrar o ADP com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o ADP ao Azure AD (Azure Active Directory). Ao integrar o ADP ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao ADP.
* Permitir que os usuários sejam conectados automaticamente ao ADP com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ADP habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ADP é compatível com SSO iniciado por **IDP**

## <a name="adding-adp-from-the-gallery"></a>Adicionar ADP da galeria

Para configurar a integração de ADP ao Microsoft Azure Active Directory, você precisa adicionar ADP por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ADP** na caixa de pesquisa.
1. Selecione **ADP** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o ADP usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ADP.

Para configurar e testar o SSO do Azure AD com o ADP, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do ADP](#configure-adp-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do ADP](#create-adp-test-user)** – para ter um equivalente de B. Fernandes no ADP que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **ADP**, clique na **guia Propriedades** e execute as seguintes etapas: 

    ![Propriedades de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Defina o valor do campo **Habilitado para os usuários entrarem** como **Sim**.

    b. Copie a **URL de acesso do usuário** e cole-a na **seção Configurar URL de Entrada**, que é explicado mais adiante no tutorial.

    c. Defina o valor do campo **Atribuição de usuário necessária** como **Sim**.

    d. Defina o valor do campo **Visível para os usuários** como **Não**.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `https://fed.adp.com`

5. O aplicativo ADP espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário. O nome da declaração sempre será **PersonImmutableID** e o valor que mapeamos para **employeeid**.

    O mapeamento de usuário do Azure AD para o ADP será feito na **employeeid**, mas isso pode ser mapeado para um valor diferente baseado nas configurações de aplicativo. Portanto, trabalhe com a [equipe de suporte do ADP](https://www.adp.com/contact-us/overview.aspx) primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração **PersonImmutableID**.

    ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo ADP espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção Declarações de Usuário da caixa de diálogo Atributos de Usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | NOME | Atributo de Origem|
    | ---------------| --------- |
    | PersonImmutableID  | user.employeeid |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Save** (Salvar).

    > [!NOTE] 
    > Antes de configurar a declaração SAML, é necessário contatar a [equipe de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) e solicitar o valor do atributo de identificador exclusivo do usuário para seu locatário. Você precisa desse valor para configurar a declaração personalizada para seu aplicativo. 

4. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e clique em **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o ADP**, copie as URLs corretas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-adp-sso"></a>Configurar o SSO do ADP

Para configurar o logon único do lado do **ADP**, você precisa carregar o **XML de Metadados** baixado no [site do ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Esse processo pode levar alguns dias.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar os serviços do ADP para acesso federado

>[!Important]
> Os funcionários que precisam de acesso federado aos serviços do ADP precisam ser atribuídos ao aplicativo de serviço do ADP e posteriormente, os usuários precisam ser reatribuídos para o serviço do ADP específico.
Após o recebimento da confirmação de seu representante da ADP, configure os serviços do ADP e atribua/gerencie usuários para controlar o acesso do usuário ao serviço do ADP específico.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ADP** na caixa de pesquisa.
1. Selecione **ADP** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.
1. No portal do Azure, na página de integração de aplicativos do seu **ADP**, clique na **guia Propriedades** e execute as seguintes etapas:  

    ![Propriedades vinculadas de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Defina o valor do campo **Habilitado para os usuários entrarem** como **Sim**.

    b.  Defina o valor do campo **Atribuição de usuário necessária** como **Sim**.

    c.  Defina o valor do campo **Visível para os usuários** como **Sim**.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP**, localize a seção **Gerenciar** e selecione **Logon único**.

1. Na caixa de diálogo **Selecione um método de logon único**, selecione **Modo** como **Vinculado**. para vincular seu aplicativo ao **ADP**.

    ![Logon único vinculado](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navegue para a seção **Configurar URL de Entrada** e execute as seguintes etapas:

    ![Propriedades de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Cole a **URL de acesso do usuário**, copiada da **guia de propriedades** acima (do aplicativo principal do ADP).
                                                             
    b. Veja a seguir os cinco aplicativos que dão suporte a diferentes **URLs de Estado de Retransmissão**. Você precisa acrescentar o valor **URL do Estado de Retransmissão** apropriado de determinado aplicativo manualmente à **URL de acesso do usuário**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Salve** suas alterações.

10. Após o recebimento da confirmação de seu representante da ADP, inicie o teste com um ou dois usuários.

    a. Atribua alguns usuários ao Aplicativo do serviço ADP para testar o acesso federado.

    b. O teste é bem-sucedido quando os usuários acessam o aplicativo de serviço do ADP na galeria e podem acessar seus serviços do ADP.
 
11. Após a confirmação de um teste bem-sucedido, atribua o serviço ADP federado a usuários individuais ou grupos de usuários, que é explicado mais adiante no tutorial, e distribua-o a seus funcionários.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao ADP.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **ADP**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adp-test-user"></a>Criar um usuário de teste do ADP

O objetivo desta seção é criar um usuário chamado B.Fernandes no ADP. Trabalhe com a [equipe de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os usuários à conta do ADP. 

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ADP no Painel de Acesso, você deverá ser conectado automaticamente ao ADP no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

