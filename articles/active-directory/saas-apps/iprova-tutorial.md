---
title: 'Tutorial: Integração do Azure Active Directory ao iProva | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf685919879a9ee82cbaa3863826c891422d3013
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099814"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Tutorial: Integração do Azure Active Directory ao iProva

Neste tutorial, você aprenderá a integrar o iProva ao Azure AD (Azure Active Directory).
A integração do iProva com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao iProva.
* Você pode permitir que os usuários sejam conectados automaticamente ao iProva (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iProva, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura habilitada para logon único do iProva

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O iProva dá suporte ao SSO iniciado por **SP**

## <a name="adding-iprova-from-the-gallery"></a>Adicionando o iProva por meio da galeria

Para configurar a integração do iProva ao Azure AD, você precisará adicionar o iProva à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o iProva por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **iProva**, selecione **iProva** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![iProva na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o iProva, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iProva.

Para configurar e testar o logon único do Azure AD com o iProva, você precisará concluir os seguintes blocos de construção:

1. **[Recuperar informações de configuração do iProva](#retrieve-configuration-information-from-iprova)** como uma preparação para as próximas etapas.
2. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
3. **[Configurar o Logon Único do iProva](#configure-iprova-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
4. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
5. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
6. **[Criar um usuário de teste do iProva](#create-iprova-test-user)** – para ter um equivalente de Brenda Fernandes no iProva que esteja vinculado à representação de usuário no Azure AD.
7. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="retrieve-configuration-information-from-iprova"></a>Recuperar informações de configuração do iProva

Nesta seção, você pode recuperar informações de iProva para configurar o logon único do Azure AD.

1. Abra um navegador da Web e vá para a **página de informações do SAML2** no iProva, usando o seguinte padrão de URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Exibir a página de informações do SAML2 no iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Deixe a guia do navegador aberta enquanto você continua com as próximas etapas em outra guia do navegador.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o iProva, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iProva**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do iProva](common/sp-identifier-reply.png)

    a. Preencha a caixa **Identificador** com o valor exibido por trás do rótulo **EntityID** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    b. Preencha a caixa **URL de resposta** com o valor exibido por trás do rótulo **URL de resposta** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    c. Preencha a caixa **URL de Logon** com o valor exibido por trás do rótulo **URL de Logon** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

5. O aplicativo iProva espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME | Atributo de Origem| Namespace  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurar o Logon Único do iProva

1. Entre no iProva usando a conta **Administrador**.

2. Abra o menu **Ir para**.

3. Selecione **Gerenciamento de aplicativos**.

4. Selecione **Geral** no painel **Configurações do sistema**.

5. Selecione **Editar**.

6. Role a tela para baixo até **Controle de acesso**.

    ![Configurações de controle de acesso do iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Encontre a configuração **Os usuários são conectados automaticamente com suas contas de rede** e altere-a para **Sim, autenticação via SAML**. Opções adicionais aparecem agora.

8. Selecione **Configurar**.

9. Selecione **Avançar**.

10. O iProva pergunta se você deseja baixar os dados de federação de uma URL ou carregá-los de um arquivo. Selecione a opção **Da URL**.

    ![Baixar metadados do Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Cole a URL de metadados salva na última etapa da seção "Configurar o logon único do Azure AD".

12. Selecione o botão em forma de seta para baixar os metadados do Azure AD.

13. Quando o download for concluído, a mensagem de confirmação **Arquivo de Dados de Federação válido baixado** será exibida.

14. Selecione **Avançar**.

15. Ignore a opção **Testar logon** por enquanto e, em seguida, selecione **Avançar**.

16. Na caixa suspensa **Declaração para uso**, selecione **windowsaccountname**.

17. Selecione **Concluir**.

18. Agora você retornará para a tela **Editar configurações gerais**. Role para baixo até a parte inferior da página e, em seguida, selecione **OK** para salvar sua configuração.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao iProva.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **iProva**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **iProva**.

    ![O link do iProva na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-iprova-test-user"></a>Criar usuário de teste do iProva

1. Entre no iProva usando a conta **Administrador**.

2. Abra o menu **Ir para**.

3. Selecione **Gerenciamento de aplicativos**.

4. Clique em **Usuários** no painel **Usuários e grupos de usuários**.

5. Selecione **Adicionar**.

6. Na caixa **Nome de usuário**, digite o nome de usuário, como `BrittaSimon@contoso.com`.

7. Na caixa **Nome completo**, insira o nome completo do usuário, como **BrendaFernandes**.

8. Selecione a opção **Nenhuma senha (usar logon único)** .

9. Na caixa **Endereço de email**, digite o endereço de email do usuário, como `BrittaSimon@contoso.com`.

10. Role para baixo até o final da página e, em seguida, selecione **Concluir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do iProva no Painel de Acesso, você deverá ser conectado automaticamente ao iProva, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)