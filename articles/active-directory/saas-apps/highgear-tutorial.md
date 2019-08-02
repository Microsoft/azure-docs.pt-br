---
title: 'Tutorial: Integração do Azure Active Directory ao HighGear | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7aedf90ead4b21bab661bdaf85316cd648fd75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101266"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Tutorial: Integração do Azure Active Directory ao HighGear

Neste tutorial, você pode aprender como integrar o HighGear ao Azure Active Directory (Azure AD).
A integração do HighGear ao Azure AD oferece os benefícios a seguir:

* Você pode controlar no Azure AD quem terá acesso ao HighGear.
* Você pode permitir que seus usuários entrem automaticamente no HighGear (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HighGear, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você poderá obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Um sistema HighGear com licença Enterprise ou ilimitada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você pode aprender como configurar e testar o logon único do Azure AD em um ambiente de teste.

* O HighGear é compatível com SSO iniciado por **SP e IdP**

## <a name="adding-highgear-from-the-gallery"></a>Adição do HighGear por meio da galeria

Para configurar a integração do HighGear ao Azure AD, você precisará adicionar o HighGear por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HighGear por meio da galeria, execute as etapas a seguir:**

1. No **[portal do Microsoft Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **HighGear**, selecione **HighGear** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![HighGear na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você pode aprender como configurar e testar o logon único do Azure AD com o sistema HighGear, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do sistema HighGear.

Para configurar e testar o logon único do Azure AD com o sistema HighGear, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do HighGear](#configure-highgear-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo HighGear.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do HighGear](#create-highgear-test-user)** – para ter um equivalente de Brenda Fernandes no HighGear que esteja vinculado à representação de usuário do Azure AD. 
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você pode aprender como habilitar o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o sistema HighGear, execute as etapas a seguir:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **HighGear**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em domínio e URLs do HighGear](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, cole o valor do campo **ID da entidade do provedor de serviço** que está na página Configurações de logon único no sistema HighGear.

    ![O campo ID da entidade do provedor de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Você precisará fazer logon no sistema HighGear para acessar a página Configurações de logon único. Depois de se conectar, passe o mouse sobre a guia Administração no HighGear e clique no item de menu Configurações de logon único.
    
    ![O item de menu Configurações de logon único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Na caixa de texto **URL de resposta**, cole o valor da **URL ACS (serviço do consumidor de declaração)** da página Configurações de logon único no sistema HighGear.

    ![O campo da URL ACS (serviço do consumidor de declaração)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     ![Informações de logon único em domínio e URLs do HighGear](common/metadata-upload-additional-signon.png)

     Na caixa de texto **URL de logon**, cole o valor do campo **ID da entidade do provedor de serviço** que está na página Configurações de logon único no sistema HighGear. (Essa ID da entidade também é a URL base do sistema HighGear que deve ser usada para o logon iniciado por SP.)

    ![O campo ID da entidade do provedor de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de resposta e a URL de logon da página **Configurações de logon único** no sistema HighGear. Se você precisar de ajuda, entre em contato com a [equipe de suporte do HighGear](mailto:support@highgear.com).

4. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do certificado **(Base64)** e salve no seu computador. Você precisará dele em uma etapa posterior da configuração de logon único.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o HighGear**, anote a localização das URLs a seguir.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon. Você precisará desse valor na etapa 2 em **Configurar logon único do HighGear** abaixo.

    b. Identificador do Azure AD. Você precisará desse valor na etapa 3 em **Configurar logon único do HighGear** abaixo.

    c. URL de logoff. Você precisará desse valor na etapa 4 em **Configurar logon único do HighGear** abaixo.

### <a name="configure-highgear-single-sign-on"></a>Configurar o logon único do HighGear

Para configurar o HighGear para logon único, faça logon no seu sistema HighGear. Depois de se conectar, passe o mouse sobre a guia Administração no HighGear e clique no item de menu Configurações de logon único.

![O item de menu Configurações de logon único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Em **Nome do provedor de identidade**, digite uma breve descrição que aparecerá no botão Logon único do HighGear na página Logon. Por exemplo:  AD do Azure

2. No campo **URL de SSO (logon único)** no HighGear, cole o valor do campo **URL de logon** que está na seção **Configurar o HighGear** no Azure.

3. No campo **ID da entidade do provedor de identidade** no HighGear, cole o valor do campo **Identificador do Azure AD** que está na seção **Configurar o HighGear** no Azure.

4. No campo **URL de SLO (logoff único)** no HighGear, cole o valor do campo **URL de logoff** que está na seção **Configurar o HighGear** no Azure.

5. Use o bloco de notas para abrir o certificado que você baixou da seção **Certificado de autenticação SAML** no Azure. Você deve ter feito o download do formato **Certificado (Base64)** . Copie o conteúdo do certificado no bloco de notas e cole-o no campo **Certificado do provedor de identidade** no HighGear.

6. Envie um email à [equipe de suporte do HighGear](mailto:support@highgear.com) para solicitar seu certificado do HighGear. Siga as instruções recebidas para preencher os campos **Certificado do HighGear** e **Senha do certificado do HighGear**.

7. Clique no botão **Salvar** para salvar a configuração de logon único do HighGear.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao HighGear.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, **HighGear**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **HighGear**.

    ![O link do HighGear na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-highgear-test-user"></a>Criar usuário de teste do HighGear

Para criar um usuário de teste do HighGear para testar sua configuração de logon único, faça logon no sistema HighGear.

1. Clique no botão **Criar novo contato**.

    ![O botão Criar novo contato](media/highgear-tutorial/create-new-contact-button.png)

    Será exibido um menu que permitirá escolher o tipo de contato que deseja criar.

2. Clique no item de menu **Individual** para criar um usuário do HighGear.

    Um painel deslizará à direita para que você possa digitar as informações para o novo usuário.  
    ![O formulário Novo contato](media/highgear-tutorial/new-contact-form.png)

3. No campo **Nome**, digite um nome para o contato. Por exemplo:  Brenda Fernandes

4. Clique no menu **Mais opções** e selecione o item de menu **Informações da conta**.

    ![Clicar no item de menu Informações da conta](media/highgear-tutorial/account-info-menu-item.png)

5. Defina o campo **É possível fazer logon** como Sim.

    O campo **Habilitar logon único** será automaticamente definido como Sim também.

6. No campo **ID do usuário de logon único**, digite a ID do usuário. Por exemplo: BrittaSimon@contoso.com

    A seção Informações da conta deve ficar assim:  
    ![A seção Informações da conta encerrada](media/highgear-tutorial/finished-account-info-section.png)

7. Para salvar o contato, clique no botão **Salvar** na parte inferior do painel.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do HighGear no Painel de Acesso, você deverá ser conectado automaticamente ao HighGear no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

