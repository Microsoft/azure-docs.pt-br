---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao FortiGate SSL VPN | Microsoft Docs'
description: Confira as etapas você precisa executar para integrar o FortiGate SSL VPN ao Azure AD (Azure Active Directory).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: 9852752799fd010ebb069637f55008d9c4f68bf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732031"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao FortiGate SSL VPN

Neste tutorial, você aprenderá a integrar o FortiGate SSL VPN ao Azure AD (Azure Active Directory). Ao integrar o FortiGate SSL VPN ao Azure AD, você poderá:

* Usar o Azure AD para controlar quem pode acessar o FortiGate SSL VPN.
* Permitir que os usuários sejam conectados automaticamente ao FortiGate SSL VPN com suas respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do FortiGate SSL VPN com o SSO (logon único) habilitado.

## <a name="tutorial-description"></a>Descrição do tutorial

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

O FortiGate SSL VPN é compatível com o SSO iniciado por SP.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Adicionar o FortiGate SSL VPN da galeria

Para configurar a integração do FortiGate SSL VPN ao Azure AD, você precisará adicionar o FortiGate SSL VPN da galeria à lista de aplicativos SaaS gerenciados:

1. Entre no portal do Azure com uma conta corporativa ou de estudante ou com uma conta Microsoft pessoal.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Acesse **Aplicativos empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **FortiGate SSL VPN** na caixa de pesquisa.
1. Selecione **FortiGate SSL VPN** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configurar e testar o SSO do Azure AD para o FortiGate SSL VPN

Você vai configurar e testar o SSO do Azure AD com o FortiGate SSL VPN usando um usuário de teste chamado B.Fernandes. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente do FortiGate SSL VPN.

Para configurar e testar o SSO do Azure AD com o FortiGate SSL VPN, conclua as seguintes etapas de alto nível:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** para habilitar esse recurso para seus usuários.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
    1. **[Permita acesso ao usuário de teste](#grant-access-to-the-test-user)** para habilitar o logon único do Azure AD para esse usuário.
1. **[Configure o SSO do FortiGate SSL VPN](#configure-fortigate-ssl-vpn-sso)** no lado do aplicativo.
    1. **Crie um usuário de teste do FortiGate SSL VPN** como um equivalente à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No portal do Azure, na página de integração de aplicativos do **FortiGate SSL VPN**, na seção **Gerenciar**, selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o botão de lápis da **Configuração Básica de SAML** para editar as configurações:

   ![Captura de tela que mostra o ícone de lápis para edição da configuração básica de SAML.](common/edit-urls.png)

1. Na página **Configurar o Logon Único com SAML**, insira os seguintes valores:

    a. Na caixa **URL de Logon**, insira uma URL no padrão `https://<FQDN>/remote/login`.

    b. Na caixa **Identificador**, insira uma URL no padrão `https://<FQDN>/remote/saml/metadata`.

    c. Na caixa **URL de Resposta**, insira uma URL no padrão `https://<FQDN>/remote/saml/login`.

    d. Na caixa **URL de Logoff**, insira uma URL no padrão `https://<FQDN>/remote/saml/logout`.

    > [!NOTE]
    > Esses valores são apenas padrões. Você precisará usar os valores reais de **URL de Logon**, **Identificador**, **URL de Resposta** e **URL de Logoff**. Entre em contato com o [suporte do Fortinet](https://support.fortinet.com) para obter diretrizes. Veja também os padrões de exemplo mostrados na documentação do Fortinet e a seção **Configuração Básica do SAML** no portal do Azure.

1. O aplicativo FortiGate SSL VPN espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela que mostra os atributos padrão.](common/default-attributes.png)

1. As duas declarações adicionais exigidas pelo FortiGate SSL VPN são mostradas na tabela a seguir. Os nomes dessas declarações devem corresponder aos nomes usados na seção **Executar a configuração de linha de comando do FortiGate** deste tutorial. 

   | Nome |  Atributo de origem|
   | ------------ | --------- |
   | Nome de Usuário | user.userprincipalname |
   | group | user.groups |
   
   Para criar essas declarações adicionais:

   a. Ao lado de **Atributos e Declarações do Usuário**, selecione **Editar**.

   b. Selecione **Adicionar nova declaração**.

   c. Para **Nome**, insira **nome de usuário**.

   d. Para **Atributo de origem**, selecione **user.userprincipalname**.

   e. Clique em **Salvar**.

   f. Selecione **Adicionar uma declaração de grupo**.

   g. Selecione **Todos os grupos**.

   h. Marque a caixa de seleção **Personalizar o nome da declaração de grupo**.

   i. Para **Nome**, insira **grupo**.
   
   j. Selecione **Salvar**.   

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Download**, ao lado de **Certificado (Base64)** , para baixar o certificado e salvá-lo no computador:

    ![Captura de tela que mostra o link de download do certificado.](common/certificatebase64.png)

1. Na seção **Configurar o FortiGate SSL VPN**, copie as URLs apropriadas de acordo com seus requisitos:

    ![Captura de tela que mostra as URLs de configuração.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**. Selecione **Usuários** e, em seguida, selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, conclua estas etapas:
   1. Na caixa **Nome**, insira **B.Fernandes**.  
   1. Na caixa **Nome de usuário**, insira \<username>@\<companydomain>.\<extension>. Por exemplo, `B.Simon@contoso.com`.
   1. Marque **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

#### <a name="grant-access-to-the-test-user"></a>Permitir acesso ao usuário de teste

Nesta seção, você habilitará B.Fernandes para usar o logon único do Azure permitindo a esse usuário acesso ao FortiGate SSL VPN.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **FortiGate SSL VPN**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de **Usuários** e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

#### <a name="create-a-security-group-for-the-test-user"></a>Criar um grupo de segurança para o usuário de teste

Nesta seção, você criará um grupo de segurança no Azure Active Directory para o usuário de teste. O FortiGate usará esse grupo de segurança para permitir ao usuário acesso à rede por meio da VPN.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**. Em seguida, selecione **Grupos**.
1. Selecione **Novo grupo** na parte superior da tela.
1. Nas propriedades do **Novo Grupo**, execute as seguintes etapas:
   1. Na lista **Tipo de grupo**, selecione **Segurança**.
   1. Na caixa **Nome do grupo**, insira **FortiGateAccess**.
   1. Na caixa **Descrição do grupo**, insira **Grupo para permitir acesso ao FortiGate VPN**.
   1. Na configuração **As funções do Azure AD podem ser atribuídas ao grupo (Versão prévia)** , selecione **Não**.
   1. No caixa **Tipo de associação**, selecione **Atribuído**.
   1. Em **Membros**, selecione **Nenhum membro selecionado**.
   1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e clique no botão **Selecionar**, na parte inferior da tela.
   1. Selecione **Criar**.
1. Após voltar para a seção **Grupos** do Azure Active Directory, localize o grupo **Acesso ao FortiGate** e anote a **ID do Objeto**. Você precisará dela mais tarde.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Configurar o SSO do FortiGate SSL VPN

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Carregar o certificado SAML Base64 para o dispositivo FortiGate

Após concluir a configuração de SAML do aplicativo FortiGate no locatário, você baixou o certificado SAML codificado em Base64. Você precisa carregar esse certificado no dispositivo FortiGate:

1. Entre no portal de gerenciamento do dispositivo FortiGate.
1. No painel esquerdo, selecione **Sistema**.
1. Em **Sistema**, selecione **Certificados**.
1. Selecione **Importar** > **Certificado Remoto**.
1. Procure o certificado baixado da implantação do aplicativo FortiGate no locatário do Azure, selecione-o e clique em **OK**.

Depois que o certificado for carregado, anote o nome dele em **Sistema** > **Certificados** > **Certificado Remoto**. Por padrão, ele será chamado de REMOTE_Cert_ *N*, em que *N* é um valor inteiro.

#### <a name="complete-fortigate-command-line-configuration"></a>Concluir a configuração de linha de comando do FortiGate

As etapas a seguir exigem que você configure a URL de Logoff do Azure. Essa URL contém um ponto de interrogação (?). Você precisa executar etapas específicas para enviar esse caractere com sucesso. Não é possível concluir essas etapas no Console da CLI do FortiGate. Em vez disso, estabeleça uma sessão SSH para o dispositivo FortiGate usando uma ferramenta como o PuTTY. Se o dispositivo FortiGate for uma máquina virtual do Azure, você poderá concluir as etapas a seguir no console serial para máquinas virtuais do Azure.

Para concluir essas etapas, você precisará dos valores registrados anteriormente:

- ID da Entidade
- URL de resposta
- URL de logoff
- URL de Logon do Azure
- Identificador do Azure AD
- URL de Logoff do Azure
- Nome do certificado SAML Base64 (REMOTE_Cert_ *N*)

1. Estabeleça uma sessão SSH para o dispositivo FortiGate e entre com uma conta de administrador do FortiGate.
1. Execute estes comandos:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > A URL de Logoff do Azure contém um caractere `?`. É necessário inserir uma sequência de chave especial para fornecer corretamente essa URL ao console serial do FortiGate. A URL geralmente é `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Para inserir a URL de logoff do Azure no console serial, digite `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > Depois, selecione CTRL+V e cole o restante da URL para completar a linha: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

#### <a name="configure-fortigate-for-group-matching"></a>Configurar o FortiGate para correspondência de grupo

Nesta seção, você vai configurar o FortiGate para reconhecer a ID do Objeto do grupo de segurança que inclui o usuário de teste. Essa configuração permitirá que o FortiGate tome decisões de acesso com base na associação ao grupo.

Para executar essas etapas, você precisará da ID do Objeto do grupo de segurança FortiGateAccess, criado anteriormente neste tutorial.

1. Estabeleça uma sessão SSH para o dispositivo FortiGate e entre com uma conta de administrador do FortiGate.
1. Execute estes comandos:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Criar uma Política de Firewall e Portais para o FortiGate VPN

Nesta seção, você vai configurar uma Política de Firewall e Portais para o FortiGate VPN que permita acesso ao grupo de segurança FortiGateAccess, criado anteriormente neste tutorial.

Trabalhe com a equipe de suporte do [FortiGate](mailto:tac_amer@fortinet.com) para adicionar os portais de VPN e a política de firewall à plataforma de VPN do FortiGate. Você precisa concluir esta etapa antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do FortiGate VPN, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do FortiGate VPN diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do FortiGate VPN em Meus Aplicativos, isso redirecionará para a URL de logon do FortiGate VPN. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o FortiGate VPN, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)