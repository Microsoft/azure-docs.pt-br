---
title: 'Tutorial: Integração do Azure Active Directory ao SSO do Kantega para o FishEye/Crucible | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do Kantega para o FishEye/Crucible.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 06a4e8aa1ad74f47526f3a39931632953bfaaec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459179"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Tutorial: Integração do Azure Active Directory ao SSO do Kantega para o FishEye/Crucible

Neste tutorial, você aprende a integrar o SSO do Kantega para o FishEye/Crucible ao Azure AD (Azure Active Directory).
A integração do SSO do Kantega para o FishEye/Crucible ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SSO do Kantega para o FishEye/Crucible.
* É possível permitir que os usuários se conectem automaticamente com o SSO (Logon Único) do Kantega para o FishEye/Crucible com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao SSO do Kantega para o FishEye/Crucible, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SSO do Kantega para o FishEye/Crucible

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O logon único do Kantega para o FishEye/Crucible dá suporte ao logon único iniciado por **SP e IDP**

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Adicionando o SSO do Kantega para o FishEye/Crucible por meio da galeria

Para configurar a integração do SSO do Kantega para o FishEye/Crucible ao Azure AD, é necessário adicionar o SSO do Kantega para o FishEye/Crucible à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o SSO do Kantega para o FishEye/Crucible por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Logon único do Kantega para o FishEye/Crucible**, selecione **Logon único do Kantega para o FishEye/Crucible** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SSO do Kantega para o FishEye/Crucible na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO do Kantega para o FishEye/Crucible com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do logon único do Kantega para o FishEye/Crucible.

Para configurar e testar o logon único do Azure AD com o SSO do Kantega para o FishEye/Crucible, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Kantega para o FishEye/Crucible](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do SSO do Kantega para o FishEye/Crucible](#create-kantega-sso-for-fisheyecrucible-test-user)** – para ter um equivalente de Brenda Fernandes no SSO do Kantega para o FishEye/Crucible que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SSO do Kantega para o FishEye/Crucible, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SSO do Kantega para o FishEye/Crucible**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![A captura de tela mostra a seção "Configuração Básica do SAML" com as caixas de texto "Identificador" e "URL de Resposta" realçadas, bem como o botão "Salvar" selecionado.](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e domínio de logon único do Kantega para o FishEye/Crucible](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Esses valores são recebidos durante a configuração do plug-in do FishEye/Crucible, que é explicada adiante no tutorial.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o logon único do Kantega para o FishEye/Crucible**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Configurar o logon único do SSO do Kantega para o FishEye/Crucible

1. Em outra janela do navegador da Web, entre no servidor local do FishEye/Crucible como administrador.

1. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![Captura de tela que mostra o ícone de "Engrenagem" e a opção "Complementos" selecionada.](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Na seção Configurações do Sistema, clique em **Localizar novos complementos**. 

    ![Captura de tela que mostra a seção "Configurações do Sistema" com a opção "Encontrar Novos Complementos" selecionada.](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Pesquise **SSO do Kantega para o Crucible** e clique no botão **Instalar** para instalar o novo plug-in do SAML.

    ![Captura de tela que mostra a página "Attlassian Marketplace para FishEye" com "SSO do Kantega para Crucible" na caixa de pesquisa e o botão "Instalar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. A instalação do plug-in é iniciada. 

    ![Captura de tela que mostra a caixa de diálogo "Instalando" para o plug-in.](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Quando a instalação for concluída. Clique em **fechar**

    ![Captura de tela que mostra a caixa de diálogo "Instalado e pronto para começar" e o botão "Fechar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Clique em **Gerenciar**.

    ![Captura de tela que mostra a página de aplicativo do "SSO do Kantega para SAML do Crucible e Kerberos" e o botão "Gerenciar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Clique em **Configurar** para configurar o novo plug-in. 

    ![Captura de tela que mostra a página "Complementos instalados pelo usuário" e o botão "Configurar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. Na seção **SAML**. Selecione **Azure AD (Azure Active Directory)** na lista suspensa **Adicionar provedor de identidade**.

    ![Captura de tela que mostra a página "Complementos – Logon Único do Kantega" com a lista suspensa "Adicionar provedor de identidade" e a opção "Azure AD (Azure Active Directory)" selecionada. ](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Selecione o nível de assinatura como **Básico**.

    ![Captura de tela que mostra a seção "Preparando o Azure AD" com a opção "Básico" selecionada.](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. Na seção **Propriedades do aplicativo**, realize as seguintes etapas:

    ![Captura de tela que mostra a seção "Propriedades do aplicativo" com a caixa de texto "URI da ID do Aplicativo" e o botão Copiar selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Copie o valor da **URI da ID do Aplicativo** e use-o como **o Identificador, a URL de Resposta e a URL de Logon** na seção **Configuração básica do SAML** do portal do Azure.

    b. Clique em **Próximo**.

1. Na seção **Importação de metadados**, realize as seguintes etapas:

    ![Captura de tela que mostra a seção "Importação de metadados" com a opção "Arquivo de metadados em meu computador" selecionada.](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Selecione **Arquivo de metadados no meu computador** e carregue um arquivo de metadados baixado no portal do Azure.

    b. Clique em **Próximo**.

1. Na seção **Nome e localização de SSO**, realize as seguintes etapas:

    ![Captura de tela que mostra o "Nome e local do SSO" com a caixa de texto "Nome do provedor de identidade" realçada e o botão "Avançar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Adicione Nome do Provedor de Identidade à caixa de texto **Nome do provedor de identidade** (por exemplo, Azure AD).

    b. Clique em **Próximo**.

1. Verifique o Certificado de autenticação e clique em **Avançar**.   

    ![Captura de tela que mostra as informações da seção "Verificação de assinatura" com o botão "Avançar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. Na seção **Contas de usuário do FishEye**, realize as seguintes etapas:

    ![Captura de tela que mostra a seção "Contas de usuário do FishEye" com a opção "Criar usuários no Diretório Interno do FishEye, se necessário" e o botão "Avançar" selecionados.](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Selecione **Criar usuários no Diretório interno do FishEye, se necessário** e insira o nome apropriado do grupo de usuários (podem ser vários números de grupos separados por vírgula).

    b. Clique em **Próximo**.

1. Clique em **Concluir**.

    ![Captura de tela que mostra a seção "Resumo" com o botão "Concluir" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. Na seção **Domínios conhecidos do Azure AD**, realize as seguintes etapas:  

    ![Captura de tela que mostra a seção "Domínios conhecidos para o Azure AD" com o botão "Salvar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

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
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SSO do Kantega para o FishEye/Crucible.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Logon único do Kantega para o FishEye/Crucible**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SSO do Kantega para o FishEye/Crucible**.

    ![O link do SSO do Kantega para o FishEye/Crucible na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Criar usuário de teste do SSO do Kantega para o FishEye/Crucible

Para permitir que os usuários do Azure AD entrem no FishEye/Crucible, eles devem ser provisionados no FishEye/Crucible. No SSO do Kantega para o FishEye/Crucible, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no servidor local do Crucible como administrador.

1. Focalize a engrenagem e clique em **Usuários**.

    ![Captura de tela que mostra o ícone de "Engrenagem" selecionado e a opção "Usuários" selecionada na lista suspensa.](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. Na seção da guia **Usuários**, clique em **Adicionar usuário**.

    ![A captura de tela mostra a seção "Usuários" com o botão "Adicionar usuário" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Na página da caixa de diálogo **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![Adicionar Funcionário](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome de Exibição**, digite o nome de exibição do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Na caixa de texto **Confirmar Senha**, insira novamente a senha do usuário.

    f. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do logon único do Kantega para o FishEye/Crucible no Painel de Acesso, você deverá ser conectado automaticamente ao logon único do Kantega para o FishEye/Crucible para o qual você configurar o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)