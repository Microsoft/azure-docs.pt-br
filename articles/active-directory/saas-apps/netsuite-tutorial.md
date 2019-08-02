---
title: 'Tutorial: Integração do Azure Active Directory ao NetSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2600989273d6ebfe4319a048cc65c8c3ff9ecdbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096311"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Integração do Azure Active Directory ao NetSuite

Neste tutorial, você aprende a integrar o NetSuite ao Azure AD (Azure Active Directory).
A integração do NetSuite com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao NetSuite.
* Você pode permitir que seus usuários entrem automaticamente no NetSuite (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o NetSuite, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do NetSuite habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O NetSuite é compatível com SSO iniciado por **IDP**
* O NetSuite é compatível com o provisionamento de usuário **Just-In-Time**
* O NetSuite é compatível com o [provisionamento de usuário automatizado](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Adicionar o NetSuite da galeria

Para configurar a integração do NetSuite com o Azure AD, é necessário adicionar o NetSuite da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o NetSuite da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **NetSuite**, selecione **NetSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![NetSuite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o NetSuite com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetSuite.

Para configurar e testar o logon único do Azure AD com o NetSuite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do NetSuite](#configure-netsuite-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do NetSuite](#create-netsuite-test-user)** – para ter um equivalente de Brenda Fernandes no NetSuite que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o NetSuite, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **NetSuite**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do NetSuite](common/idp-reply.png)

    Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao Cliente do NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo NetSuite espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:
    
    | NOME | Atributo de Origem | 
    | ---------------| --------------- |
    | conta  | `account id` |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

    >[!NOTE]
    >O valor do atributo de conta não é real. Você atualizará esse valor, o que é explicado posteriormente no tutorial.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar NetSuite**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-netsuite-single-sign-on"></a>Configurar o logon único do NetSuite

1. Abra uma nova guia no navegador e entre no site do NetSuite de sua empresa como administrador.

2. Na barra de ferramentas na parte superior da página, clique em **Configuração**, navegue até **Empresa** e clique em **Habilitar Recursos**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, clique em **SuiteCloud**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Na seção **Gerenciar Autenticação**, selecione **LOGON ÚNICO DO SAML** para habilitar a opção de LOGON ÚNICO DO DAML no NetSuite.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de ferramentas na parte superior da página, clique em **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **TAREFAS DE INSTALAÇÃO**, selecione **Integração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-integration.png)

7. Na seção **GERENCIAR AUTENTICAÇÃO**, clique em **Logon Único do SAML**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml.png)

8. Na página de **Configuração do SAML**, na seção **Configuração do NetSuite**, execute as seguintes etapas:

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione **MÉTODO DE AUTENTICAÇÃO PRIMÁRIA**.

    b. Para o campo rotulado como **METADADOS DO PROVEDOR DE IDENTIDADE DO SAMLV2**, selecione **CARREGAR ARQUIVO DE METADADOS DO IDP**. Em seguida, clique em **Procurar** para carregar o arquivo de metadados baixado no portal do Azure.

    c. Clique em **Enviar**.

9. No NetSuite, clique em **Configuração**, navegue até **Empresa** e clique em **Informações da Empresa** no menu de navegação superior.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na página **Informações da Empresa**, na coluna à direita, copie a **ID DA CONTA**.

    c. Cole a **ID da Conta** que você copiou da conta do NetSuite no campo **Valor do Atributo** no Azure AD. 

10. Antes que os usuários possam realizar logon único no NetSuite, eles devem primeiro ter as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    a. No menu de navegação superior, clique em **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    b. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Funções**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Clique em **Nova Função**.

    d. Digite um **Nome** para sua nova função.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Save** (Salvar).

    f. No menu na parte superior, clique em **Permissões**. Em seguida, clique em **Instalação**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **Logon Único do SAML** e, em seguida, clique em **Adicionar**.

    h. Clique em **Save** (Salvar).

    i. No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    j. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Usuários**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste. Depois, clique em **Editar** e, em seguida, navegue até a guia **Acesso**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo Funções, atribua a função correta que você criou.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Clique em **Save** (Salvar).

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao NetSuite.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **NetSuite**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e escolha **NetSuite**.

    ![O link do NetSuite na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-netsuite-test-user"></a>Criar um usuário de teste do NetSuite

Nesta seção, um usuário chamado Brenda Fernandes é criado no NetSuite. O NetSuite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do NetSuite no Painel de Acesso, você deverá ser conectado automaticamente ao NetSuite no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](NetSuite-provisioning-tutorial.md)

