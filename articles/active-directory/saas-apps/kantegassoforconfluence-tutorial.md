---
title: 'Tutorial: Integração do Azure Active Directory com o SSO do Kantega para o Confluence | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do Kantega para o Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646190"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Tutorial: Integração do Azure Active Directory com o SSO do Kantega para o Confluence

Neste tutorial, você aprende a integrar o SSO do Kantega para o Confluence ao Azure AD (Azure Active Directory).
A integração do SSO do Kantega para o Confluence ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SSO do Kantega para o Confluence.
* Você pode permitir que os usuários se conectem automaticamente ao SSO do Kantega para o Confluence (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SSO do Kantega para o Confluence, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SSO do Kantega para o Confluence

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO do Kantega para o Confluence dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Adicionando o SSO do Kantega para o Confluence por meio da galeria

Para configurar a integração do SSO do Kantega para o Confluence ao Azure AD, é necessário adicionar o SSO do Kantega para o Confluence à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o SSO do Kantega para o Confluence por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do Kantega para o Confluence**, selecione **SSO do Kantega para o Confluence** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SSO do Kantega para o Confluence na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO do Kantega para o Confluence, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSO do Kantega para o Confluence.

Para configurar e testar o logon único do Azure AD com o SSO do Kantega para o Confluence, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Kantega para o Confluence](#configure-kantega-sso-for-confluence-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do SSO do Kantega para o Confluence](#create-kantega-sso-for-confluence-test-user)** – para ter um equivalente de Brenda Fernandes no SSO do Kantega para o Confluence que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SSO do Kantega para o Confluence, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SSO do Kantega para o Confluence**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![A captura de tela mostra a seção "Configuração Básica do SAML" com os campos "Identificador" e "URL de Resposta" realçados, bem como o botão "Salvar" selecionado.](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e domínio de logon único do Kantega para o Confluence](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Esses valores são recebidos durante a configuração do plug-in do Confluence, que é explicada adiante no tutorial.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o logon único do Kantega para o Confluence**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Configurar o logon único do SSO do Kantega para o Confluence

1. Em outra janela do navegador da Web, faça logon no **portal de administração do Confluence** como administrador.

1. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![Captura de tela que mostra o ícone de menu de "Engrenagem" e a opção "Complementos" selecionada.](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Na guia **ATLASSIAN MARKETPLACE**, clique em **Localizar novos complementos**.

    ![Captura de tela que mostra a guia "ATTLASSIAN MARKETPLACE" com a opção "Encontrar novos complementos" selecionada.](./media/kantegassoforconfluence-tutorial/addon.png)

1. Pesquise **SSO do Kantega para o Confluence SAML e Kerberos** e clique no botão **Instalar** para instalar o novo plug-in do SAML.

    ![Captura de tela que mostra a página "Encontrar novos complementos" com "SSO do Kantega para o SAML e o Kerberos do Confluence" na caixa de pesquisa e o botão "Instalar" selecionado.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. A instalação do plug-in é iniciada.

    ![Captura de tela que mostra a tela "Instalando" do plug-in.](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Quando a instalação for concluída. Clique em **fechar**

    ![Captura de tela que mostra a tela "Instalado e pronto para começar" e a ação "Fechar" selecionada.](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Clique em **Gerenciar**.

    ![Captura de tela que mostra o plug-in "Logon Único do Kantega com Kerberos e SAML" com o botão "Gerenciar" selecionado.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Clique em **Configurar** para configurar o novo plug-in.

    ![Captura de tela que mostra a página "Logon Único do Kantega com Kerberos e SAML" com o botão "Configurar" selecionado.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Este novo plug-in também pode ser encontrado na guia **USUÁRIOS E SEGURANÇA**.

    ![Captura de tela que mostra a guia "USUÁRIOS E SEGURANÇA" com a ação "Logon Único do Kantega" selecionada.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. Na seção **SAML**. Selecione **Azure AD (Azure Active Directory)** na lista suspensa **Adicionar provedor de identidade**.

    ![Captura de tela que mostra a seção "SAML" com as opções "Adicionar provedor de identidade" e "Azure AD (Azure Active Directory)" selecionadas.](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Selecione o nível de assinatura como **Básico**.

    ![Captura de tela que mostra a página "Preparando o Azure AD" com a opção "Básico" selecionada.](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Na seção **Propriedades do aplicativo**, realize as seguintes etapas:

    ![Captura de tela que mostra a seção "Propriedades do aplicativo" com o campo "URL da ID do Aplicativo" e o botão "Copiar" realçados, bem como o botão "Avançar" selecionado.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Copie o valor da **URI da ID do Aplicativo** e use-o como **o Identificador, a URL de Resposta e a URL de Logon** na seção **Configuração básica do SAML** do portal do Azure.

    b. Clique em **Próximo**.

1. Na seção **Importação de metadados**, realize as seguintes etapas: 

    ![Captura de tela que mostra a seção "Importação de metadados" com a opção "Arquivo de metadados em meu computador" selecionada.](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Selecione **Arquivo de metadados no meu computador** e carregue um arquivo de metadados baixado no portal do Azure.

    b. Clique em **Próximo**.

1. Na seção **Nome e localização de SSO**, realize as seguintes etapas:

    ![Captura de tela que mostra o "Nome e local do SSO" com a caixa de texto "Nome do provedor de identidade" realçada e o botão "Avançar" selecionado.](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Adicione Nome do Provedor de Identidade à caixa de texto **Nome do provedor de identidade** (por exemplo, Azure AD).

    b. Clique em **Próximo**.

1. Verifique o Certificado de autenticação e clique em **Avançar**.

    ![Captura de tela que mostra a seção "Verificação de assinatura" com o botão "Avançar" selecionado.](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Na seção **Contas de usuário do Confluence**, realize as seguintes etapas:

    ![Captura de tela que mostra a seção "Contas de usuário do Confluence" com a opção "Criar usuários no Diretório Interno do Confluence, se necessário" e o botão "Avançar" selecionados.](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Selecione **Criar usuários no Diretório interno do Confluence, se necessário** e insira o nome apropriado do grupo de usuários (podem ser vários números de grupos separados por vírgula).

    b. Clique em **Próximo**.

1. Clique em **Concluir**.

    ![Captura de tela da página "Resumo" com o botão "Concluir" selecionado.](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Na seção **Domínios conhecidos do Azure AD**, realize as seguintes etapas: 

    ![Captura de tela que mostra a página "Domínios conhecidos para o Azure AD" com a caixa de texto "Domínios conhecidos" realçada e o botão "Salvar" selecionado.](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Selecione **Domínios conhecidos** no painel esquerdo da página.

    b. Insira o nome de domínio na caixa de texto **Domínios conhecidos**.

    c. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SSO do Kantega para o Confluence.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SSO do Kantega para o Confluence**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SSO do Kantega para o Confluence**.

    ![O link do SSO do Kantega para o Confluence na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Criar usuário de teste do SSO do Kantega para o Confluence

Para permitir que os usuários do Azure AD façam logon no Confluence, eles devem ser provisionados no Confluence. No caso do SSO do Kantega para o Confluence, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do SSO do Kantega para o Confluence como administrador.

1. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![Captura de tela que mostra o ícone de "Engrenagem" e a opção "Gerenciamento de usuário" selecionada.](./media/kantegassoforconfluence-tutorial/user1.png)

1. Na seção Usuários, clique na guia **Adicionar Usuários**. Na página da caixa de diálogo **Adicionar um Usuário**, execute as seguintes etapas:

    ![Adicionar Funcionário](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Confirmar Senha** e redigite a senha.

    f. Clique no botão **Adicionar**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do logon único do Kantega para o Confluence no Painel de Acesso, você deverá ser conectado automaticamente ao logon único do Kantega para o Confluence para o qual você configurar o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)