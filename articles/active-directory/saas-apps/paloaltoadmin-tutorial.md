---
title: 'Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Admin UI | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – Admin UI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 57b1d47fa40c0af4bced1e4169fe60cd759ee2f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963631"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Admin UI

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – Admin UI ao Azure AD (Azure Active Directory).
A integração do Palo Alto Networks – Admin UI ao Azure AD lhe oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Palo Alto Networks – Admin UI.
* Você pode permitir que os usuários sejam conectados automaticamente ao Palo Alto Networks – Admin UI (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Palo Alto Networks – Admin UI, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Palo Alto Networks – Admin UI

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Palo Alto Networks – Admin UI dá suporte ao SSO iniciado por **SP**
* O Palo Alto Networks – Admin UI dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Adicionando o Palo Alto Networks – Admin UI da Galeria

Para configurar a integração do Palo Alto Networks – Admin UI com o Azure AD, você precisará adicionar o Palo Alto Networks – Admin UI da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Palo Alto Networks – Admin UI** na caixa de pesquisa.
1. Selecione **Palo Alto Networks – Admin UI** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você vai configurar e vai testar o logon único do Azure AD com o Palo Alto Networks – Admin UI, com base em um usuário de teste chamado **B.Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks – Admin UI.

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks – Admin UI, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Palo Alto Networks – Admin UI](#configure-palo-alto-networks---admin-ui-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Palo Alto Networks – Admin UI](#create-palo-alto-networks---admin-ui-test-user)** – para ter um equivalente de B.Fernandes no Palo Alto Networks – Admin UI que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Palo Alto Networks – Admin UI**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<Customer Firewall FQDN>/php/login.php`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Na caixa de texto **URL de Resposta**, digite a URL do ACS (Serviço do Consumidor de Declaração) usando o seguinte formato: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Admin UI](https://support.paloaltonetworks.com/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.
    >
    > A porta 443 é necessária no **Identificador** e na **URL de Resposta**, pois esses valores são embutidos em código no Firewall do Palo Alto. A remoção do número da porta resultará em um erro durante o logon, se for removido.

    > A porta 443 é necessária no **Identificador** e na **URL de Resposta**, pois esses valores são embutidos em código no Firewall do Palo Alto. A remoção do número da porta resultará em um erro durante o logon, se for removido.

1. O aplicativo Palo Alto Networks – interface do usuário do administrador espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Como os valores de atributo são apenas exemplos, mapeie os valores apropriados para *username* e *adminrole*. Há outro atributo opcional, *accessdomain*, que é usado para restringir o acesso de administrador para sistemas virtuais específicos no firewall.

1. Além do indicado acima, o aplicativo Palo Alto Networks – interface do usuário do administrador espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | --- | --- |
    | Nome de Usuário | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > O valor _adminrole_ deve ser o mesmo que o nome da função configurada no **Palo Alto Networks** conforme mencionado na etapa 9. 

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os seguintes artigos:
    > * [Perfil de função administrativa para Interface do Usuário de Administrador (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de dispositivo de acesso para Interface do Usuário de Administrador (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Palo Alto Networks – Admin UI**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Palo Alto Networks – Admin UI.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Palo Alto Networks – Admin UI**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Configurar SSO do Palo Alto Networks – Admin UI

1. Abra a Interface do Usuário do Administrador do Firewall do Palo Alto Networks como um administrador em outra janela do navegador.

2. Selecione a guia **Dispositivo**.

    ![A guia Dispositivo](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No painel esquerdo, selecione **Provedor de Identidade SAML** e, em seguida, selecione **Importar** para importar o arquivo de metadados.

    ![O botão Importar arquivo de metadados](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na janela do **Perfil do Servidor de Provedor de Identidade SAML**, faça o seguinte:

    ![A janela "Perfil do Servidor de Provedor de Identidade SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Na caixa **Nome do Perfil**, forneça um nome (por exemplo, **AzureAD Admin UI**).

    b. Em **Metadados do Provedor de Identidade**, selecione **Procurar** e selecione o arquivo metadata.xml que você baixou anteriormente no portal do Azure.

    c. Limpe a caixa de seleção **Validar Certificado do Provedor de Identidade**.

    d. Selecione **OK**.

    e. Para confirmar as configurações de firewall, selecione **Confirmar**.

5. No painel esquerdo, selecione **Provedor de Identidade SAML** e, em seguida, selecione o perfil do Provedor de Identidade SAML (por exemplo, **AzureAD Admin UI**) que você criou na etapa anterior.

    ![O Perfil de Provedor de Identidade do SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Na janela do **Perfil do Servidor de Provedor de Identidade SAML**, faça o seguinte:

    ![A janela "Perfil do Servidor de Provedor de Identidade do SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na caixa **URL de SLO do Provedor de Identidade**, substitua a URL de SLO importada anteriormente pela seguinte URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecione **OK**.

7. Na Interface do Usuário do Administrador do Firewall do Palo Alto Networks, selecione **Dispositivo** e, em seguida, selecione **Funções do Administrador**.

8. Selecione o botão **Adicionar**.

9. Na janela **Perfil da Função de Administrador**, na caixa **Nome**, forneça um nome para a função de administrador (por exemplo, **fwadmin**). O nome da função de administrador deve corresponder ao nome de atributo da Função de Administrador de SAML enviado pelo Provedor de Identidade. O nome da função de administrador e o valor foram criados na seção **Atributos de usuário** no portal do Azure.

    ![Configurar Função do Administrador de Redes Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Na Interface do Usuário do Administrador do Firewall, selecione **Dispositivo** e em seguida, selecione **Perfil de Autenticação**.

11. Selecione o botão **Adicionar**.

12. Na janela **Perfil de Autenticação**, faça o seguinte: 

    ![A janela "Perfil de Autenticação"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Na caixa de texto **Nome**, forneça um nome (por exemplo, **AzureSAML_Admin_AuthProfile**).

    b. Na lista suspensa **Tipo**, escolha **SAML**. 

    c. Na lista suspensa **Perfil do Servidor de Provedor de Identidade**, selecione o perfil do Servidor de Provedor de Identidade SAML apropriado (por exemplo, **AzureAD Admin UI**).

    c. Selecione a caixa de seleção **Ativar o Logout Único**.

    d. Na caixa **Atributo da Função de Administrador**, digite o nome do atributo (por exemplo, **adminrole**).

    e. Selecione a guia **Avançado** e, em seguida, na **Lista de Permissões**, selecione **Adicionar**.

    ![Botão Adicionar na guia Avançado](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Selecione a caixa de seleção **Todos**, ou selecione os usuários e grupos que podem se autenticar com esse perfil.  
    Quando um usuário é autenticado, o firewall faz a correspondência do nome de usuário ou do grupo associado com base nas entradas dessa lista. Se você não adicionar entradas, nenhum usuário poder autenticar.

    g. Selecione **OK**.

13. Para habilitar os administradores a usarem o SSO do SAML usando o Azure, selecione **Dispositivo** > **Instalação**. No painel **Instalação**, selecione a guia **Gerenciamento** e, em seguida, em **Configurações de Autenticação**, selecione o botão **Configurações** ("engrenagem").

    ![O botão Configurações](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecione o perfil de Autenticação SAML criado na janela Perfil de Autenticação (por exemplo, **AzureSAML_Admin_AuthProfile**).

    ![O campo Perfil de Autenticação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecione **OK**.

16. Para confirmar a configuração, selecione **Confirmar**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Criar um usuário de teste do Palo Alto Networks – Admin UI

O Palo Alto Networks - Admin UI dá suporte ao provisionamento de usuário just-in-time. Se um usuário ainda não existir, ele é automaticamente criado no sistema após uma autenticação bem-sucedida. Nenhuma ação é requerida para criar o usuário.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Palo Alto Networks – Admin UI, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Palo Alto Networks – Admin UI diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Palo Alto Networks – Admin UI em Meus Aplicativos, você deverá ser conectado automaticamente ao Palo Alto Networks – Admin UI, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Palo Alto Networks – Admin UI, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).