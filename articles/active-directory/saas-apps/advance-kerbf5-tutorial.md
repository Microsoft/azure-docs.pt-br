---
title: 'Tutorial: Integração do logon único do Azure Active Directory ao F5 | Microsoft Docs'
description: Neste artigo, saiba quais etapas você precisa executar para integrar o F5 ao Azure AD (Azure Active Directory).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: db8977e484e8d1f2cf4b30427d47ba45969f2147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654452"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Tutorial: Integração do SSO (logon único) do Azure AD (Active Directory) ao F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure AD (Azure Active Directory). Ao integrar o F5 ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao F5.
* Permitir que os usuários sejam conectados automaticamente ao F5 com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do F5.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

O F5 dá suporte ao SSO iniciado por **SP e IDP**.

O SSO do F5 pode ser configurado de três maneiras diferentes:

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adicionar o F5 da galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com o F5 usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do F5](#create-f5-test-user)** – para ter um equivalente de B. Fernandes no F5 vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **F5**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o F5**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao F5.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Configurar logon único do F5 para aplicativo Kerberos Avançado

1. Abra uma nova janela do navegador da Web, entre no site da empresa F5 (Kerberos Avançado) como administrador e execute as seguintes etapas:

1. Você precisa importar o Certificado de Metadados no F5 (Kerberos Avançado) que será usado posteriormente no processo de instalação. Acesse **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego >> Lista de Certificados SSL**. Clique em **Importar** do canto direito.

    ![Captura de tela que realça o botão Importar para importar o Certificado de Metadados.](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Para configurar o IDP do SAML, vá até **Acessar > Federação > Provedor de Serviços SAML > Criar > De Metadados**.

    ![Captura de tela que realça como criar o IDP do SAML com base em metadados.](./media/advance-kerbf5-tutorial/configure02.png)

    ![Captura de tela que mostra Criar Conector IdP SAML.](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configuração do F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Captura de tela que mostra Configurações do Serviço de Logon Único. ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Especificar o Certificado carregado da Tarefa 3

    ![Captura de tela que mostra Editar Conector IdP SAML.](./media/advance-kerbf5-tutorial/configure06.png)

    ![Captura de tela que mostra Configurações do Serviço de Logoff Único.](./media/advance-kerbf5-tutorial/configure07.png)

 1. Para configurar o SP do SAML, vá para **Acessar > Federação > Federação de Serviços SAML > Serviços SP Locais > Criar**.

    ![Captura de tela que mostra a tela na qual você cria um serviço de SP local.](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Clique em **OK**.

1. Selecione a Configuração do SP e clique em **Associar/desassociar conectores IdP**.

     ![Captura de tela que mostra o Provedor de Serviços SAML.](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Clique em **Adicionar Nova Linha** e selecione o **Conector IdP externo** criado na etapa anterior.

    ![Captura de tela que realça o botão Adicionar Linha.](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Para configurar o SSO do Kerberos, **Acessar > Logon Único > Kerberos**

    >[!Note]
    >Será necessário criar e especificar a Conta de Delegação do Kerberos. Confira a Seção do KCD (Confira o Apêndice para obter Referências de Variável)

    • Origem do nome de usuário `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Origem de realm do usuário `session.logon.last.domain`

    ![Captura de tela que realça Acesso > Logon Único.](./media/advance-kerbf5-tutorial/configure11.png)

1. Para configurar o Perfil de Acesso, **Acessar > Perfil/Políticas > Perfil de Acesso (por políticas de sessão)** .

    ![Captura de tela que realça a guia Propriedades na opção de menu Perfis/Políticas.](./media/advance-kerbf5-tutorial/configure12.png)

    ![Captura de tela que mostra a guia Domínios SSO/Autenticação.](./media/advance-kerbf5-tutorial/configure13.png)

    ![Captura de tela que mostra a guia Política de Acesso.](./media/advance-kerbf5-tutorial/configure14.png)

    ![Captura de tela que mostra a guia Propriedades na Política de Acesso.](./media/advance-kerbf5-tutorial/configure15.png)

    ![Captura de tela que mostra as propriedades de Atribuição de Variável.](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![Captura de tela que mostra as propriedades de Consulta do AD.](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Captura de tela que mostra a guia Regras de Ramificação e a regra Verificar Conta.](./media/advance-kerbf5-tutorial/configure18.png)

    ![Captura de tela que mostra as caixas de texto da variável personalizada e da expressão personalizada.](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Captura de tela que mostra os valores nos campos Nome do Token SSO e Senha do Token de SSO.](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Para adicionar um novo nó, acesse **Tráfego Local > Nós > Lista de Nós > +** .

    ![Captura de tela que realça Tráfego Local > Nós.](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Para criar um pool, acesse **Tráfego Local > Pools > Lista de Pools > Criar**.

     ![Captura de tela que realça Tráfego Local > Pools.](./media/advance-kerbf5-tutorial/configure22.png)

 1. Para criar um servidor virtual, acesse **Tráfego Local > Servidores Virtuais > Lista de Servidores Virtuais >** +.

    ![Captura de tela que realça Tráfego Local > Servidores Virtuais.](./media/advance-kerbf5-tutorial/configure23.png)

1. Especifique o Perfil de Acesso criado na etapa anterior.

    ![Captura de tela que mostra em que local você especifica o perfil de acesso criado.](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Configurar Delegação do Kerberos 

>[!Note]
>Para obter mais detalhes, confira [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Etapa 1: Criar uma Conta de Delegação**

    * Exemplo
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Etapa 2: Definir SPN (na Conta de Delegação APM)**

    *  Exemplo
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Etapa 3: Delegação de SPN (para a Conta de Serviço de Aplicativo)**

    * Configure a Delegação adequada para a Conta de Delegação da F5.
    * No exemplo abaixo, a conta de Delegação APM está sendo configurada para KCD para o aplicativo FRP-App1.superdemo.live.

        ![Captura de tela que mostra a guia Propriedades da Conta de Delegação do APM > Delegação.](./media/advance-kerbf5-tutorial/configure25.png)

1. Forneça os detalhes conforme mencionado no documento de referência [aqui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Apêndice – SAML – Mapeamentos de Variável BIG-IP do F5 mostrados abaixo:

    ![Captura de tela que mostra a guia Visão Geral > Sessões Ativas.](./media/advance-kerbf5-tutorial/configure26.png)

    ![Captura de tela que mostra as variáveis e as chaves de sessão.](./media/advance-kerbf5-tutorial/configure27.png) 

1. Abaixo encontra-se a lista completa de Atributos padrão do SAML. GivenName é representado usando a seguinte cadeia de caracteres.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Session | Atributo |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2,0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B.Fernandes no F5. Trabalhe com a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários na plataforma do F5. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do F5 no Painel de Acesso, você deverá ser conectado automaticamente ao F5 no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

- [Integração do F5 BIG-IP APM e do Azure AD para acesso híbrido seguro](../manage-apps/f5-aad-integration.md)

- [Tutorial para implantar a VM do F5 BIG-IP Virtual Edition na IaaS do Azure para acesso híbrido seguro](../manage-apps/f5-bigip-deployment-guide.md)

- [Tutorial para a integração de logon único do Azure Active Directory ao F5 BIG-IP para VPN sem senha](../manage-apps/f5-aad-password-less-vpn.md)