---
title: 'Tutorial: Integração do Azure Active Directory ao Zscaler Beta | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07341c1ad30f1242bdff430826fdc82c45e09dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086065"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Azure Active Directory ao Zscaler Beta

Neste tutorial, você aprenderá como integrar o Zscaler Beta ao Azure AD (Azure Active Directory).
Ao integrar o Zscaler Beta ao Azure AD, você pode:

* Controlar quem tem acesso ao Zscaler Beta no Azure AD.
* Permitir que seus usuários entrem automaticamente no Zscaler Beta usando suas contas do Azure AD. Esse controle de acesso é chamado de logon único (SSO).
* Gerenciar suas contas em um local central usando o portal do Azure.

Para obter mais informações sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler Beta, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Zscaler Beta que usa logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zscaler Beta dá suporte ao SSO iniciado por SP.
* O Zscaler Beta dá suporte ao provisionamento de usuário Just-In-Time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Adicionar o Zscaler Beta do Azure Marketplace

Para configurar a integração do Zscaler Beta ao Azure AD, adicione o Zscaler Beta do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Zscaler Beta do Azure Marketplace, siga estas etapas.

1. No [Portal do Azure Microsoft](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![Botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Zscaler Beta**. Selecione **Zscaler Beta** no painel de resultados e, em seguida, selecione **Adicionar**.

     ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler Beta com base em um usuário de teste chamado Brenda Fernandes.
Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler Beta.

Para configurar e testar o logon único do Azure AD com o Zscaler Beta, conclua os seguintes blocos de construção:

- [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
- [Configurar o logon único do Zscaler Beta](#configure-zscaler-beta-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
- [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
- [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
- [Criar um usuário de teste do Zscaler Beta](#create-a-zscaler-beta-test-user) para ter um equivalente de Brenda Fernandes no Zscaler Beta que esteja vinculado à representação de usuário no Azure AD.
- [Testar o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com Zscaler Beta, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zscaler Beta**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com o SAML**, selecione **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, siga esta etapa:

    ![Informações de logon único em domínio e URLs do Zscaler Beta](common/sp-intiated.png)

    - Na caixa **URL de Entrada**, insira a URL usada pelos usuários para entrar no aplicativo Zscaler Beta.

    > [!NOTE]
    > O valor não é real. Atualize o valor com o valor da URL de Entrada real. Para obtê-lo, entre em contato com a [equipe de suporte ao cliente do Zscaler Beta](https://www.zscaler.com/company/contact).

5. O aplicativo Zscaler Beta espera que as declarações SAML estejam em um formato específico. Você deve adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Selecione  **Editar** para abrir a caixa de diálogo **Atributos do Usuário** .

    ![Caixa de diálogo Atributos do Usuário](common/edit-attribute.png)

6. O aplicativo Zscaler Beta espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, siga as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo.
    
    | NOME | Atributo de origem | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Caixa de diálogo Declarações de usuário](common/new-save-attribute.png)

    ![Gerenciar a caixa de diálogo de declarações do usuário](common/new-attribute-details.png)

    b. Na caixa **Nome**, insira o nome do atributo mostrado para aquela linha.

    c. Deixe a caixa **Namespace** em branco.

    d. Para **Origem**, selecione **Atributo**.

    e. Na lista **Atributo de origem**, insira o valor do atributo mostrado para essa linha.

    f. Selecione **OK**.

    g. Clique em **Salvar**.

    > [!NOTE]
    > Para saber como configurar funções no Azure AD, confira [Configurar a declaração de função](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, selecione **Download** para baixar o certificado **(Base64)** . Salve-o no computador.

    ![Link de download do certificado](common/certificatebase64.png)

8. Na seção **Configurar o Zscaler Beta**, copie as URLs necessárias para seus requisitos:

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    - URL de logon
    - Identificador do Azure AD
    - URL de logoff

### <a name="configure-zscaler-beta-single-sign-on"></a>Configurar logon único do Zscaler Beta

1. Para automatizar a configuração no Zscaler Beta, instale a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, selecionando **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecionar **Configurar o Zscaler Beta** direciona você para o aplicativo Zscaler Beta. Em seguida, forneça as credenciais de administrador para entrar no Zscaler Beta. A extensão do navegador configura automaticamente o aplicativo e automatiza as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Para configurar o Zscaler Beta manualmente, abra uma nova janela do navegador da Web. Entre em seu site de empresa do Zscaler Beta como administrador e siga estas etapas.

4. Acesse **Administração** > **Autenticação** > **Configurações de Autenticação**, e siga estas etapas.
   
    ![Administração](./media/zscaler-beta-tutorial/ic800206.png "Administração")

    a. Em **Tipo de Autenticação**, selecione **SAML**.

    b. Selecione **Configurar SAML**.

5. Na janela **Editar SAML**, siga estas etapas: 
            
    ![Gerenciar usuários e autenticação](./media/zscaler-beta-tutorial/ic800208.png "Gerenciar usuários e autenticação")
    
    a. Na caixa **URL do Portal SAML**, cole a **URL de Logon** copiada do portal do Azure.

    b. Na caixa **Atributo de Nome de Logon**, insira **NameID**.

    c. Na caixa **Certificado SSL Público**, selecione **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure.

    d. Alternar **Habilitar Provisionamento Automático do SAML**.

    e. Na caixa **Atributo de Nome de Exibição do Usuário**, insira **displayName** se você quiser habilitar o provisionamento automático do SAML para atributos de displayName.

    f. Na caixa **Atributo de Nome do Grupo**, insira **memberOf** se você quiser habilitar o provisionamento automático do SAML para atributos de memberOf.

    g. Na caixa **Atributo de Nome do Departamento**, insira **departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Salvar**.

6. Na página de caixa de diálogo **Configurar Autenticação de Usuário**, siga as etapas:

    ![Menu Ativação e botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Selecione **Ativar**.

## <a name="configure-proxy-settings"></a>Definir configurações de proxy
Para definir as configurações de proxy no Internet Explorer, siga estas etapas.

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**. 
    
     ![Caixa de diálogo Opções da Internet](./media/zscaler-beta-tutorial/ic769492.png "Opções da Internet")

3. Selecione a guia **Conexões**. 
  
     ![Guia Conexões](./media/zscaler-beta-tutorial/ic769493.png "Conexões")

4. Selecione **Configurações da LAN** para abrir a caixa de diálogo **Configurações da Rede Local (LAN)** .

5. Na seção **Servidor Proxy**, siga estas etapas: 
   
    ![Seção Servidor Proxy](./media/zscaler-beta-tutorial/ic769494.png "Servidor Proxy")

    a. Selecione a caixa de seleção **Usar um Servidor Proxy para LAN**.

    b. Na caixa **Endereço**, insira **gateway.Zscaler Beta.net**.

    c. Na caixa **Porta**, insira **80**.

    d. Selecione **Não usar servidor proxy para endereços locais**.

    e. Selecione **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)** .

6. Selecione **OK** para fechar a caixa de diálogo **Opções da Internet**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Crie um usuário de teste no portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![Links Usuários e Todos os Usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, siga estas etapas:

    ![Caixa de diálogo Usuário](common/user-properties.png)

    a. Na caixa **Nome**, insira **BrendaFernandes**.
  
    b. Na caixa **Nome de Usuário**, insira `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione a caixa de seleção **Mostrar Senha**. Anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Permita que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zscaler Beta.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **Zscaler Beta**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, insira e selecione **Zscaler Beta**.

    ![Link do Zscaler Beta na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![Link Usuários e Grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Botão Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e Grupos**, selecione o usuário **Brenda Fernandes** na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.

    ![Caixa de diálogo Usuários e Grupos](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Na caixa de diálogo **Selecionar Função**, selecione a função de usuário apropriada na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.

    ![Caixa de diálogo Selecionar Função](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

    ![Caixa de diálogo Adicionar Atribuição](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Criar um usuário de teste do Zscaler Beta

Nesta seção, a usuária Brenda Fernandes será criada no Zscaler Beta. O Zscaler Beta dá suporte ao **provisionamento de usuário Just-In-Time**, que está habilitado por padrão. Não há nada a fazer nesta seção. Se um usuário ainda não existir no Zscaler Beta, um novo será criado após a autenticação.

>[!Note]
>Para criar um usuário manualmente, entre em contato com a [Equipe de suporte do Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar logon único 

Teste sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar a peça do Zscaler Beta no Painel de Acesso, você deverá ser conectado automaticamente ao Zscaler Beta para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

