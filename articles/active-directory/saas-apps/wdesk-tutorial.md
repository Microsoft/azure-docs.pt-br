---
title: 'Tutorial: Integração do Azure Active Directory com o Wdesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 11539e7d6e685703810537cf69e0e7ed3ec0be4c
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609081"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Wdesk

Neste tutorial, você aprenderá a integrar o Wdesk ao Azure AD (Azure Active Directory). Ao integrar o Wdesk ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Wdesk.
* Permitir que os usuários sejam conectados automaticamente ao Wdesk com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Wdesk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Wdesk dá suporte ao SSO iniciado por **SP** e **IDP**
* Depois de configurar o Wdesk, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Adicionar o Wdesk da galeria

Para configurar a integração do Wdesk ao Azure AD, você precisará adicionar o Wdesk a partir da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Wdesk** na caixa de pesquisa.
1. Selecione **Wdesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Wdesk, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Wdesk.

Para configurar e testar o SSO do Azure AD com o Wdesk, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Wdesk](#configure-wdesk-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Wdesk](#create-wdesk-test-user)** : para ter um equivalente de B.Fernandes no Wdesk que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Wdesk, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Wdesk**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Captura de tela que mostra a Configuração Básica de SAML, em que você pode inserir o Identificador e a URL de Resposta e selecionar Salvar.](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Captura de tela que mostra Definir URLs adicionais, em que você pode inserir uma URL de Logon.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você pode obter esses valores no portal do WDesk ao configurar o SSO.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Wdesk**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Wdesk.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Wdesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Wdesk**.

    ![O link do Wdesk na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-wdesk-sso"></a>Configurar o SSO do Wdesk

1. Em outra janela do navegador da Web, entre no Wdesk como Administrador de segurança.

1. No canto inferior esquerdo, clique em **Administrador** e escolha **Conta de Administrador**:
 
    ![A captura de tela mostra o Administrador da Conta selecionado no menu Administrador.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Na Administração do Wdesk, navegue até **Segurança**, **SAML** > **Configurações de SAML**:

    ![A captura de tela mostra a opção Configurações do SAML selecionada na guia SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Em **Configurações de ID de Usuário SAML**, verifique se a **ID de Usuário SAML é o Nome de Usuário do Wdesk**.

    ![A captura de tela mostra a opção Configurações de ID de Usuário SAML, em que você pode selecionar a ID de Usuário SAML é o Nome de Usuário do Wdesk.](./media/wdesk-tutorial/wdesk-username.png)

4. Em **Configurações Gerais**, marque **Habilitar Logon Único do SAML**:

    ![A captura de tela mostra a opção Editar Configurações do SAML, em que você pode selecionar a opção Habilitar Logon Único do SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Em **Detalhes do Provedor de Serviço**, execute as seguintes etapas:

    ![A captura de tela mostra os Detalhes do Provedor de Serviço, em que você pode inserir os valores descritos.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

    1. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Logon** no Portal do Azure.

    1. Copie a **URL de Metadados** e cole-a na caixa de texto **Identificador** no Portal do Azure.

    1. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Resposta** no Portal do Azure.

    1. Clique em **Salvar** no Portal do Azure para salvar as alterações.      

1. Clique em **Definir Configurações do IdP** para abrir a caixa de diálogo **Editar Configurações do IdP**. Clique em **Escolher Arquivo** para localizar o arquivo **Metadata.xml** salvo no Portal do Azure, depois carregue-o.
    
    ![A captura de tela mostra a opção Editar Configurações de IdP, em que você pode carregar metadados.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Clique em **Salvar alterações**.

    ![A captura de tela mostra o botão Salvar alterações.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Criar um usuário de teste do Wdesk

Para permitir que os usuários do Azure AD entrem no Wdesk, eles precisam ser provisionados no Wdesk. No Wdesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Wdesk como Administrador de segurança.

2. Navegue até **Administrador** > **Conta de Administrador**.

     ![A captura de tela mostra o Administrador da Conta selecionado no menu Administrador.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Clique em **Membros**, sob **Pessoas**.

4. Agora clique em **Adicionar Membro** para abrir a caixa de diálogo **Adicionar Membro**. 
   
    ![A captura de tela mostra a guia Membros, em que você pode selecionar Adicionar Membro.](./media/wdesk-tutorial/createuser1.png)  

5. Na caixa de texto **Usuário**, insira o nome do usuário, como b.simon@contoso.com e clique no botão **Continuar**.

    ![A captura de tela mostra a caixa de diálogo Adicionar Membro, em que você pode inserir um usuário.](./media/wdesk-tutorial/createuser3.png)

6.  Insira os detalhes, conforme mostrado abaixo:
  
    ![A captura de tela mostra a caixa de diálogo Adicionar Membro, em que você pode adicionar Informações Básicas para um usuário.](./media/wdesk-tutorial/createuser4.png)
 
    a. Na caixa de texto **Email**, insira o email do usuário como b.simon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

7. Clique no botão **Salvar Membro**.  

    ![A captura de tela mostra a opção Enviar email de boas-vindas com o botão Salvar Membro.](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Wdesk no Painel de Acesso, você deverá ser conectado automaticamente ao Wdesk, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)