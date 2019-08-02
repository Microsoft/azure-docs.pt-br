---
title: 'Tutorial: Integração do Azure Active Directory com o Cloud Management Portal for Microsoft Azure | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cloud Management Portal for Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb6e034005902d478b94a12e2cbe44a00228735
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551816"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutorial: Integração do Azure Active Directory com o Cloud Management Portal for Microsoft Azure

O objetivo deste tutorial é mostrar como integrar o Cloud Management Portal do Microsoft Azure ao Azure AD (Azure Active Directory).
A Integração do Cloud Management Portal for Microsoft Azure com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Portal de Gerenciamento de Nuvem para Microsoft Azure.
* É possível permitir que seus usuários entrem automaticamente no Portal de Gerenciamento de Nuvem para Microsoft Azure (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Cloud Management Portal for Microsoft Azure, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Portal de Gerenciamento de Nuvem para Microsoft Azure

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Portal de Gerenciamento de Nuvem para Microsoft Azure é compatível com SSO iniciado por **SP**

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Adição do Cloud Management Portal for Microsoft Azure a partir da galeria

Para configurar a integração do Cloud Management Portal for Microsoft Azure ao Azure AD, você precisará adicionar o Cloud Management Portal for Microsoft Azure da galeria para sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cloud Management Portal for Microsoft Azure a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Portal de Gerenciamento de Nuvem para Microsoft Azure**, selecione **Portal de Gerenciamento de Nuvem para Microsoft Azure** no painel de resultados; em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Portal de Gerenciamento de Nuvem para Microsoft Azure na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Portal de Gerenciamento de Nuvem para Microsoft Azure com base em um usuário de teste chamado **Brenda Fernandes**.
Para o logon único funcionar, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Portal de Gerenciamento de Nuvem para Microsoft Azure.

Para configurar e testar o logon único do Azure AD com o Cloud Management Portal for Microsoft Azure, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Portal de Gerenciamento de Nuvem para Microsoft Azure](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Portal de Gerenciamento de Nuvem para Microsoft Azure](#create-cloud-management-portal-for-microsoft-azure-test-user)** – para ter um equivalente de Brenda Fernandes no Portal de Gerenciamento de Nuvem para Microsoft Azure vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Portal de Gerenciamento de Nuvem para Microsoft Azure, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Portal de Gerenciamento de Nuvem para Microsoft Azure**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Portal de Gerenciamento de Nuvem para Microsoft Azure](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com [a equipe de suporte do cliente do Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Portal de Gerenciamento de Nuvem para Microsoft Azure**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>Configurar o logon único do Portal de Gerenciamento de Nuvem para Microsoft Azure

Para configurar o logon único no lado do **Portal de Gerenciamento de Nuvem para Microsoft Azure**, é necessário enviar o **Certificado (Base64)** baixado e as URLs adequadas copiadas do Portal do Azure para a equipe de suporte do [Portal de Gerenciamento de Nuvem para Microsoft Azure](mailto:jczernuszka@newsignature.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Cloud Management Portal for Microsoft Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Portal de Gerenciamento de Nuvem para Microsoft Azure**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cloud Management Portal for Microsoft Azure**.

    ![O link do Portal de Gerenciamento de Nuvem para Microsoft Azure na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Criar usuário de teste do Portal de Gerenciamento de Nuvem para Microsoft Azure

Nesta seção, você criará um usuário chamado Brenda Fernandes no Portal de Gerenciamento de Nuvem para Microsoft Azure. Trabalhe com a  [equipe de suporte do Portal de Gerenciamento de Nuvem para Microsoft Azure](mailto:jczernuszka@newsignature.com) para adicionar os usuários à plataforma do Portal de Gerenciamento de Nuvem para Microsoft Azure. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco do Portal de Gerenciamento de Nuvem para Microsoft Azure no Painel de Acesso, você deverá ser conectado automaticamente ao Portal de Gerenciamento de Nuvem para Microsoft Azure no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

