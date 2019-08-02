---
title: 'Tutorial: Integração do Azure Active Directory com Symantec WSS (Web Security Service) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Symantec WSS (Web Security Service).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ce68547ca13d2395fcd447990c42c48c04eb5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089383"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integração do Azure Active Directory com Symantec WSS (Web Security Service)

Neste tutorial, você aprenderá a como integrar sua conta do Symantec Web Security Service (WSS) com sua conta do Azure Active Directory (Azure AD) para que o WSS possa autenticar um usuário final provisionado no Azure AD usando a autenticação do SAML e impor regras de política no nível de usuário ou de grupo.

A integração do Symantec WSS (Web Security Service) ao Azure AD oferece os seguintes benefícios:

- Gerenciar todos os usuários finais e grupos usados pela sua conta do WSS pelo portal do Azure AD.

- Permitir que os usuários finais se autentiquem no WSS usando suas credenciais do Azure AD.

- Habilitar a imposição de regras de política no nível de usuário e de grupo definidas em sua conta do WSS.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Symantec WSS (Web Security Service), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único do Symantec WSS (Web Security Service)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Symantec WSS (Web Security Service) dá suporte a SSO iniciado por **IDP**

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Adicionando o Symantec WSS (Web Security Service) por meio da galeria

Para configurar a integração do Symantec WSS (Web Security Service) ao Azure AD, é necessário adicionar o Symantec WSS (Web Security Service) à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Symantec WSS (Web Security Service) por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Symantec Web Security Service (WSS)** , selecione **Symantec Web Security Service (WSS)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Symantec Web Security Service (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Symantec WSS (Web Security Service), com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Symantec WSS (Web Security Service).

Para configurar e testar o logon único do Azure AD com o Symantec WSS (Web Security Service), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **Configurar o Logon Único do Symantec WSS (Web Security Service)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Symantec WSS (Web Security Service)](#create-symantec-web-security-service-wss-test-user)** – para ter um equivalente de Brenda Fernandes no Symantec WSS (Web Security Service) que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Symantec WSS (Web Security Service), realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Symantec WSS (Web Security Service)** , selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na caixa de diálogo **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Symantec Web Security Service (WSS)](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na caixa de texto **URL de Resposta**, insira uma URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Entre em contato com a [equipe de suporte ao cliente do Symantec WSS (Web Security Service)](https://www.symantec.com/contact-us) se os valores do **Identificador** e da **URL de Resposta** não estiverem funcionando por algum motivo. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Configurar o Logon Único do Symantec WSS (Web Security Service)

Para configurar o logon único no lado do Symantec Web Security Service (WSS), consulte a documentação online do WSS. O arquivo **XML de Metadados de Federação** baixado precisará ser importado para o portal do WSS. Entre em contato com a [equipe de suporte do Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se precisar de ajuda com a configuração no portal do WSS.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Symantec WSS (Web Security Service).

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Symantec WSS (Web Security Service)** .

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Symantec WSS (Web Security Service)** .

    ![O link do Symantec WSS (Web Security Service) na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Criar um usuário de teste do Symantec WSS (Web Security Service)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Symantec WSS (Web Security Service). O nome de usuário final correspondentes pode ser criado manualmente no portal do WSS ou você pode esperar os usuários/grupos provisionados no Azure AD serem sincronizados ao portal do WSS após alguns minutos (aproximadamente 15 minutos). Os usuários devem ser criados e ativados antes de usar o logon único. O endereço IP público do computador do usuário final, que será usada para navegar em sites, também precisa ser provisionado no portal do Symantec Web Security Service (WSS).

> [!NOTE]
> Clique [aqui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter o endereço IP público do computador.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você vai testar a funcionalidade de logon único agora que configurou sua conta do WSS para usar o Azure AD para autenticação de SAML.

Depois de configurar seu navegador da Web para o tráfego de proxy para WSS, quando você abre o navegador da Web e tenta navegar para um site, você será redirecionado para a página de logon do Azure. Insira as credenciais do usuário final de teste que foram provisionadas no Azure AD (ou seja, BrendaFernandes) e a senha associada. Uma vez autenticado, você poderá navegar até o site que desejar. Caso você crie uma regra de política no lado do WSS para bloquear a navegação de BrendaFernandes em um site específico, você verá a página de bloqueio do WSS quando tentar navegar ao site como o usuário BrendaFernandes.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

