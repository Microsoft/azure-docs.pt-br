---
title: 'Tutorial: Integração do Azure Active Directory ao iLMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2e012a4ce8ac4a9a5afb895d545beb0a0b8946
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100615"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutorial: Integrar o iLMS ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o iLMS ao Azure AD (Azure Active Directory). Quando integrar o iLMS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao iLMS.
* Permitir que os usuários sejam conectados automaticamente ao iLMS com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do iLMS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O iLMS dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-ilms-from-the-gallery"></a>Adicionando o iLMS por meio da galeria

Para configurar a integração do iLMS ao Azure AD, você precisa adicionar o iLMS à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **iLMS** na caixa de pesquisa.
1. Escolha **iLMS** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o iLMS usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iLMS.

Para configurar e testar o SSO do Azure AD com o iLMS, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do iLMS](#configure-ilms-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do iLMS](#create-ilms-test-user)** – para ter um equivalente de Brenda Fernandes no iLMS que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iLMS**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, caso queira configurar o aplicativo no modo iniciado por **IDP**, insira os valores nos seguintes campos:

    a. Na caixa de texto **Identificador**, cole o valor do **Identificador** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS.

    b. Na caixa de texto **URL de Resposta**, cole o valor da **(URL) do Ponto de Extremidade** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS com o seguinte padrão `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, cole o valor da **(URL) do Ponto de Extremidade** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Para habilitar o provisionamento JIT, o aplicativo iLMS espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário.

    > [!NOTE]
    > Você precisa habilitar a opção **Criar Conta de Usuário Não Reconhecido** no iLMS para mapear esses atributos. Siga as instruções [aqui](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) para ter uma ideia sobre a configuração de atributos.

1. Além do indicado acima, o aplicativo iLMS espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o iLMS**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-ilms-sso"></a>Configurar o SSO do iLMS

1. Em outra janela do navegador da Web, faça logon no **portal de administração do iLMS** como administrador.

2. Clique em **SSO:SAML** na guia **Configurações** para abrir as configurações do SAML e realize as seguintes etapas:

    ![Configurar o logon único](./media/ilms-tutorial/1.png)

3. Expanda a seção **Provedor de Serviços** e copie o valor do **Identificador** e da **(URL) do Ponto de Extremidade**.

    ![Configurar o logon único](./media/ilms-tutorial/2.png) 

4. Na seção **Provedor de Identidade**, clique em **Importar Metadados**.

5. Escolha o arquivo de **Metadados de Federação** baixado no portal do Azure na seção **Certificado de Autenticação SAML**.

    ![Configurar o logon único](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se você desejar habilitar o provisionamento JIT para criar contas do iLMS para cancelar o reconhecimento de usuários, realize as seguintes etapas:

    a. Marque a opção **Criar Conta de Usuário Não Reconhecido**.

    ![Configurar o logon único](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapeie os atributos no Azure AD com os atributos no iLMS. Na coluna de atributos, especifique o nome dos atributos ou o valor padrão.

    c. Acesse a guia **Regras de Negócios** e realize as seguintes etapas:

    ![Configurar o logon único](./media/ilms-tutorial/5.png)

    d. Marque a opção **Criar Regiões, Divisões e Departamentos Não Reconhecidos** para criar Regiões, Divisões e Departamentos que ainda não existem no momento do Logon Único.

    e. Marque a opção **Atualizar Perfil de Usuário Durante a Conexão** para especificar se o perfil do usuário é atualizado a cada Logon Único.

    f. Se a opção **Atualizar Valores em Branco para Campos Não Obrigatórios no Perfil do Usuário** estiver marcada, os campos de perfil opcionais que estiverem em branco após a conexão também farão com que o perfil do iLMS do usuário contenha valores em branco nesses campos.

    g. Marque a opção **Enviar Email de Notificação de Erro** e insira o email do usuário em que você deseja receber o email de notificação de erro.

7. Clique no botão **Salvar** para salvar as alterações.

    ![Configurar o logon único](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao iLMS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **iLMS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ilms-test-user"></a>Criar um usuário de teste do iLMS

O aplicativo dá suporte ao provisionamento de usuário Just-In-Time e, após a autenticação, os usuários são criados no aplicativo automaticamente. O JIT funcionará se você tiver clicado na caixa de seleção **Criar Conta de Usuário Não Reconhecido** durante a definição da configuração do SAML no portal de administração do iLMS.

Caso precise criar um usuário manualmente, siga as etapas abaixo:

1. Faça logon no site da empresa iLMS como administrador.

2. Clique em **Registrar Usuário** na guia **Usuários** para abrir a página **Registrar Usuário**.

   ![Adicionar Funcionário](./media/ilms-tutorial/3.png)

3. Na página **Registrar Usuário**, execute as etapas a seguir.

    ![Adicionar Funcionário](./media/ilms-tutorial/create_testuser_add.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário como Brenda.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como Fernandes.

    c. Na caixa de texto **ID de Email**, digite o endereço de email do usuário como BrittaSimon@contoso.com.

    d. Na lista suspensa **Região**, selecione o valor da região.

    e. Na lista suspensa **Divisão**, selecione o valor da divisão.

    f. Na lista suspensa **Departamento**, selecione o valor do departamento.

    g. Clique em **Save** (Salvar).

    > [!NOTE]
    > Você pode enviar o email de registro para o usuário selecionando a caixa de seleção **Enviar Email de Registro**.

### <a name="test-sso"></a>Testar o SSO

Quando escolher o bloco do iLMS no Painel de Acesso, você deverá ser conectado automaticamente ao iLMS para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
