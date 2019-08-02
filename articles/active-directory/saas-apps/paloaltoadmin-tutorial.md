---
title: 'Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Admin UI | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0ca3bb356319f4661e24b192a5f7e776d14cd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095080"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Admin UI

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – Admin UI ao Azure AD (Azure Active Directory).
A integração do Palo Alto Networks – Admin UI ao Azure AD lhe oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Palo Alto Networks – Admin UI.
* Você pode permitir que os usuários sejam conectados automaticamente ao Palo Alto Networks – Admin UI (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

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

**Para adicionar o Palo Alto Networks – Admin UI da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Palo Alto Networks – Admin UI**, selecione **Palo Alto Networks – Admin UI** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Palo Alto Networks – Admin UI na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Palo Alto Networks – Admin UI, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks – Admin UI.

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks – Admin UI, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Palo Alto Networks – Admin UI](#configure-palo-alto-networks---admin-ui-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Palo Alto Networks – Admin UI](#create-palo-alto-networks---admin-ui-test-user)** – para ter um equivalente de Brenda Fernandes no Palo Alto Networks – Admin UI que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Palo Alto Networks – Admin UI, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Palo Alto Networks – Admin UI**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Palo Alto Networks – Admin UI](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<Customer Firewall FQDN>/php/login.php`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Na caixa de texto **URL de Resposta**, digite a URL do ACS (Serviço do Consumidor de Declaração) usando o seguinte formato: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Admin UI](https://support.paloaltonetworks.com/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Palo Alto Networks – Admin UI espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Como os valores de atributo são apenas exemplos, mapeie os valores apropriados para *username* e *adminrole*. Há outro atributo opcional, *accessdomain*, que é usado para restringir o acesso de administrador para sistemas virtuais específicos no firewall.
   >

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME |  Atributo de Origem|
    | --- | --- |
    | Nome de Usuário | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os seguintes artigos:
    > * [Perfil de função administrativa para Interface do Usuário de Administrador (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de dispositivo de acesso para Interface do Usuário de Administrador (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o Palo Alto Networks – Admin UI**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Configurar o Logon Único do Palo Alto Networks – Admin UI

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

5. No painel esquerdo, selecione **Provedor de Identidade SAML**e, em seguida, selecione o perfil do Provedor de Identidade SAML (por exemplo, **AzureAD Admin UI**) que você criou na etapa anterior.

    ![O Perfil de Provedor de Identidade do SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Na janela do **Perfil do Servidor de Provedor de Identidade SAML**, faça o seguinte:

    ![A janela "Perfil do Servidor de Provedor de Identidade do SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na caixa **URL de SLO do Provedor de Identidade**, substitua a URL de SLO importada anteriormente pela seguinte URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecione **OK**.

7. Na Interface do Usuário do Administrador do Firewall do Palo Alto Networks, selecione **Dispositivo**e, em seguida, selecione **Funções do Administrador**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Palo Alto Networks – Admin UI.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Palo Alto Networks – Admin UI**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Palo Alto Networks – Admin UI**.

    ![O link Palo Alto Networks – Admin UI na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Criar um usuário de teste do Palo Alto Networks – Admin UI

O Palo Alto Networks - Admin UI dá suporte ao provisionamento de usuário just-in-time. Se um usuário ainda não existir, ele é automaticamente criado no sistema após uma autenticação bem-sucedida. Nenhuma ação é requerida para criar o usuário.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Palo Alto Networks – Admin UI no Painel de Acesso, você deverá ser conectado automaticamente ao Palo Alto Networks – Admin UI, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
