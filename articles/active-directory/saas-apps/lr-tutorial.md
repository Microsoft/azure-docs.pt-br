---
title: 'Tutorial: Integração do Azure Active Directory ao LoginRadius | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606412"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Tutorial: Integração do Azure Active Directory ao LoginRadius

Neste tutorial, você aprenderá a integrar o LoginRadius ao Azure AD (Azure Active Directory).

A integração do LoginRadius ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao LoginRadius.
* Você pode permitir que os usuários sejam conectados automaticamente ao LoginRadius (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao LoginRadius, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura do LoginRadius habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LoginRadius dá suporte ao SSO iniciado por **SP**

## <a name="adding-loginradius-from-the-gallery"></a>Adicionar o LoginRadius da galeria

Para configurar a integração do LoginRadius ao Azure AD, você precisará adicionar o LoginRadius da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LoginRadius da galeria:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione o ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione o botão **Novo aplicativo**:

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **LoginRadius**, selecione **LoginRadius** no painel de resultados e selecione o botão **Adicionar** para adicionar o aplicativo.

    ![LoginRadius na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o LoginRadius, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LoginRadius.

Para configurar e testar o logon único do Azure AD com o LoginRadius, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do LoginRadius](#configure-loginradius-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do LoginRadius](#create-loginradius-test-user)** – para ter um equivalente de Brenda Fernandes no LoginRadius que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o LoginRadius, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **LoginRadius**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. No painel **Selecione um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir o painel **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**:

   ![Informações de logon único de Domínio e URLs do LoginRadius](common/sp-identifier.png)

   1. Na caixa de texto **URL de Logon**, insira a URL `https://secure.loginradius.com/login`

   1. Na caixa de texto **Identificador (ID da Entidade)** , insira a URL `https://lr.hub.loginradius.com/`

   1. Na caixa de texto **URL de Resposta (URL do Serviço de Consumidor de Declaração)** , insira a URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` do ACS do LoginRadius 

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o LoginRadius**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

   - URL de logon

   - Identificador do Azure AD

   - URL de logoff

## <a name="configure-loginradius-single-sign-on"></a>Configurar o logon único do LoginRadius

Nesta seção, você habilitará o logon único do Azure AD no Console de Administração do LoginRadius.

1. Faça logon na conta do [Console de Administração](https://adminconsole.loginradius.com/login) do LoginRadius.

2. Acesse a seção **Gerenciamento de Equipe** no [Console de Administração do LoginRadius](https://secure.loginradius.com/account/team).

3. Selecione a guia **Logon Único** e selecione **Azure AD**:

   ![Captura de tela que mostra o menu de logon único no console de Gerenciamento de Equipe do LoginRadius](./media/loginradius-tutorial/azure-ad.png)
4. Na página de configuração do Azure AD, execute as seguintes etapas:

   ![Captura de tela que mostra a configuração do Azure Active Directory no console de Gerenciamento de Equipe do LoginRadius](./media/loginradius-tutorial/single-sign-on.png)

    1. Em **Localização do Provedor de ID**, insira o PONTO DE EXTREMIDADE DE LOGON, que você obterá da conta do Azure AD.

    1. Em **URL de Logoff do Provedor de ID**, insira o PONTO DE EXTREMIDADE DE SAÍDA, que você obterá da conta do Azure AD.
 
    1. Em **Certificado do Provedor de ID**, insira o certificado do Azure AD, que você obterá da conta do Azure AD. Insira o valor do certificado com o cabeçalho e o rodapé. Exemplo: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Em **Certificado do Provedor de Serviços** e **Chave do Certificado do Provedor de Servidor**, insira seu certificado e a chave. 

       Você pode criar um certificado autoassinado executando os seguintes comandos na linha de comando (Linux/Mac):

       - Comando para obter a chave de certificado para o SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Comando para obter o certificado para o SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Lembre-se de inserir os valores de certificado de chave de certificado com o cabeçalho e o rodapé:
       > - Formato de valor do certificado de exemplo: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Formato de exemplo do valor de chave do certificado: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Na seção **Mapeamento de Dados**, selecione os campos (campos do SP) e insira os campos do Azure AD correspondentes (campos do IdP).

    A seguir estão listados alguns nomes de campo para o Azure AD.

    | Campos    | Chave de Perfil                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | Nome | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > O mapeamento do campo **Email** é obrigatório. Os mapeamentos dos campos **FirstName** e **LastName** são opcionais.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas propriedades do **Usuário**, execute as etapas a seguir.

   ![A caixa de diálogo Usuário](common/user-properties.png)

   1. No campo **Nome**, insira **BrendaFernandes**.
  
   1. No campo **Nome de usuário**, insira `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao LoginRadius.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **LoginRadius**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **LoginRadius**.

    ![O link do LoginRadius na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione o botão **Adicionar usuário** e escolha **Usuários e grupos** no painel **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e escolha o botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, no painel **Selecionar Função**, selecione na lista a função apropriada para o usuário. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.

7. No painel **Adicionar Atribuição**, selecione o botão **Atribuir**.

### <a name="create-loginradius-test-user"></a>Criar um usuário de teste do LoginRadius

1. Faça logon na conta do [Console de Administração](https://adminconsole.loginradius.com/login) do LoginRadius.

2. Acesse a seção Gerenciamento de Equipe no Console de Administração do LoginRadius.

   ![Captura de tela que mostra o Console de Administração do LoginRadius](./media/loginradius-tutorial/team-management.png)
3. Selecione **Adicionar Membro da Equipe** no menu lateral para abrir o formulário. 

4. No formulário **Adicionar Membro da Equipe**, você criará um usuário chamado Brenda Fernandes no site do LoginRadius fornecendo os detalhes do usuário e atribuindo as permissões que você quer que o usuário tenha. Para saber mais sobre as permissões baseadas em função, confira a seção [Permissões de Acesso de Função](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) do documento [Gerenciar Membros da Equipe](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) do LoginRadius. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

1. Em um navegador, acesse https://accounts.loginradius.com/auth.aspx e selecione **Logon do Fed SSO**.
2. Insira o nome de aplicativo do LoginRadius e selecione **Logon**.
3. Isso deve abrir um pop-up que solicita que você entre na conta do Azure AD.
4. Após a autenticação, o pop-up será fechado e você será conectado ao Console de Administração do LoginRadius.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
