---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao iProva | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iProva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 717696053a742abae6756655a15416ac81221144
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459740"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao iProva

Neste tutorial, você aprenderá a integrar o iProva ao Azure AD (Azure Active Directory). Quando integrar o iProva ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao iProva.
* Permitir que os usuários sejam conectados automaticamente ao iProva com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do iProva.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O iProva dá suporte ao SSO iniciado por **SP**

* Após configurar o iProva, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Adicionando o iProva por meio da galeria

Para configurar a integração do iProva ao Azure AD, você precisará adicionar o iProva à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **iProva** na caixa de pesquisa.
1. Escolha **iProva** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Configurar e testar logon único do Azure AD para o iProva

Configure e teste o SSO do Azure AD com o iProva usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iProva.

Para configurar e testar o SSO do Azure AD com o iProva, conclua os seguintes blocos de construção:

1. **[Recuperar informações de configuração do iProva](#retrieve-configuration-information-from-iprova)** – como uma preparação para as próximas etapas.
1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Criar um usuário de teste do iProva](#create-iprova-test-user)** – para ter um equivalente de Brenda Fernandes no iProva que esteja vinculado à representação de usuário no Azure AD.
1. **[Configurar o SSO do iProva](#configure-iprova-sso)** – para definir as configurações de logon único no lado do aplicativo.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="retrieve-configuration-information-from-iprova"></a>Recuperar informações de configuração do iProva

Nesta seção, você pode recuperar informações de iProva para configurar o logon único do Azure AD.

1. Abra um navegador da Web e vá para a **página de informações do SAML2** no iProva, usando o seguinte padrão de URL:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![Exibir a página de informações do SAML2 no iProva](media/iprova-tutorial/iprova-saml2-info.png)

1. Deixe a guia do navegador aberta enquanto você continua com as próximas etapas em outra guia do navegador.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iProva**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Preencha a caixa **URL de Logon** com o valor exibido por trás do rótulo **URL de Logon** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    b. Preencha a caixa **Identificador** com o valor exibido por trás do rótulo **EntityID** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    c. Preencha a caixa **URL de resposta** com o valor exibido por trás do rótulo **URL de resposta** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

1. Seu aplicativo iProva espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo iProva espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem| Namespace  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela acesso ao iProva.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **iProva**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="create-iprova-test-user"></a>Criar usuário de teste do iProva

1. Entre no iProva usando a conta **Administrador**.

2. Abra o menu **Ir para**.

3. Selecione **Gerenciamento de aplicativos**.

4. Clique em **Usuários** no painel **Usuários e grupos de usuários**.

5. Selecione **Adicionar**.

6. Na caixa **Nome de usuário**, digite o nome de usuário, como `B.Simon@contoso.com`.

7. Na caixa **Nome completo**, insira o nome completo do usuário, como **B. Fernandes**.

8. Selecione a opção **Nenhuma senha (usar logon único)**.

9. Na caixa **Endereço de email**, digite o endereço de email do usuário, como `B.Simon@contoso.com`.

10. Role para baixo até o final da página e, em seguida, selecione **Concluir**.

## <a name="configure-iprova-sso"></a>Configurar o SSO do iProva

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

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do iProva no Painel de Acesso, você deverá ser conectado automaticamente ao iProva, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o iProva com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o iProva com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)