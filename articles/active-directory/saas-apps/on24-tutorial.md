---
title: 'Tutorial: Integração do Azure Active Directory com o ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 801a631b56a11e68c444ede846ff82195cd7627f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095718"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Integração do Azure Active Directory com o ON24 Virtual Environment SAML Connection

Neste tutorial, você aprenderá a integrar o ON24 Virtual Environment SAML Connection ao Azure AD (Azure Active Directory).
A integração do ON24 Virtual Environment SAML Connection com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao ON24 Virtual Environment SAML Connection.
* Você pode permitir que os usuários entrem automaticamente no ON24 Virtual Environment SAML Connection (Logon Único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ON24 Virtual Environment SAML Connection, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para o logon único no ON24 Virtual Environment SAML Connection

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ON24 Virtual Environment SAML Connection é compatível com SSO iniciado por **SP** e **IDP**

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Adicionando o ON24 Virtual Environment SAML Connection da galeria

Para configurar a integração do ON24 Virtual Environment SAML Connection ao Azure AD, você precisa adicionar o ON24 Virtual Environment SAML Connection da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ON24 Virtual Environment SAML Connection da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ON24 Virtual Environment SAML Connection**, selecione **ON24 Virtual Environment SAML Connection** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ON24 Virtual Environment SAML Connection na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ON24 Virtual Environment SAML Connection com base em um usuário de teste chamado **Brenda Fernandes**.
Para o logon único funcionar, precisa ser estabelecida uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ON24 Virtual Environment SAML Connection.

Para configurar e testar o logon único do Azure AD com o ON24 Virtual Environment SAML Connection, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar Logon Único do ON24 Virtual Environment SAML Connection](#configure-on24-virtual-environment-saml-connection-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do ON24 Virtual Environment SAML Connection](#create-on24-virtual-environment-saml-connection-test-user)** – para ter um equivalente de Brenda Fernandes no ON24 Virtual Environment SAML Connection que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ON24 Virtual Environment SAML Connection, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **ON24 Virtual Environment SAML Connection**, selecione **logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de URLs e Domínio de Conexão SAML do Ambiente Virtual ON24](common/idp-relay.png)

    a. Na caixa de texto **Identificador**, digite uma URL:

     **URL do ambiente de produção**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL do ambiente de garantia de qualidade**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Na caixa de texto **URL de resposta**, insira uma URL:

     **URL do ambiente de produção**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL do ambiente de garantia de qualidade**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Clique em **Definir URLs adicionais**. 

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Se quiser configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Informações de logon único de URLs e Domínio de Conexão SAML do Ambiente Virtual ON24](common/both-signonurl.png)

    Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Estado de Retransmissão reais. Entre em contato com [equipe de suporte ao cliente do ON24 Virtual Environment SAML Connection](https://www.on24.com/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o ON24 Virtual Environment SAML Connection**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Configurar logon único do ON24 Virtual Environment SAML Connection

Para configurar o logon único no lado do **ON24 Virtual Environment SAML Connection**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs copiadas adequadas do portal do Azure para a [equipe de suporte do ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure permitindo seu acesso ao ON24 Virtual Environment SAML Connection.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **ON24 Virtual Environment SAML Connection**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ON24 Virtual Environment SAML Connection**.

    ![O link ON24 Virtual Environment SAML Connection na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Criar usuário de teste do ON24 Virtual Environment SAML Connection

Nesta seção, você criará um usuário chamado Brenda Fernandes no ON24 Virtual Environment SAML Connection. Trabalhe com a  [equipe de suporte do ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) para adicionar usuários na plataforma do ON24 Virtual Environment SAML Connection. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ON24 Virtual Environment SAML Connection no Painel de Acesso, você entra automaticamente no ON24 Virtual Environment SAML Connection para o qual configurou SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

