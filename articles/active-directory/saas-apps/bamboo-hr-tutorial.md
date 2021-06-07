---
title: 'Tutorial: Integração do Azure Active Directory ao BambooHR | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180329"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Integração do Azure Active Directory ao BambooHR

Neste tutorial, você aprenderá a integrar o BambooHR ao Azure AD (Azure Active Directory). Quando você integrar o BambooHR ao Azure AD, será possível:

* Controlar quem tem acesso ao BambooHR no Azure AD.
* Permitir que usuários sejam conectados de modo automático ao BambooHR usando contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do BambooHR.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O BambooHR dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.


## <a name="adding-bamboohr-from-the-gallery"></a>Adicionando o BambooHR por meio da galeria

Para configurar a integração do BambooHR ao Azure AD, você precisa adicionar o BambooHR à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **BambooHR** na caixa de pesquisa.
1. Selecione **BambooHR** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Configurar e testar o SSO do Azure AD para o BambooHR

Configure e teste o SSO do Azure AD usando o BambooHR com um usuário de teste chamado **B.Simon**. É necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no BambooHR para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o BambooHR:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do BambooHR](#configure-bamboohr-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    * **[Criar um usuário de teste do BambooHR](#create-bamboohr-test-user)** – para ter um equivalente de Brenda Fernandes no BambooHR que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **BambooHR**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company>.bamboohr.com`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | URL de resposta |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao Cliente do BambooHR](https://www.bamboohr.com/contact.php) para obter os valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o BambooHR**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao BambooHR.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **BambooHR**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-bamboohr-sso"></a>Configurar o SSO do BambooHR

1. Em uma nova janela, entre no site da empresa do BambooHR como administrador.

2. Na página inicial, faça o seguinte:
   
    ![A página de Logon Único do BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Logon Único")   

    a. Selecione **Aplicativos**.
   
    b. No painel **Aplicativos**, selecione **Logon único**.
   
    c. Selecione **Logon Único do SAML**.

3. No painel **Logon único do SAML**, faça o seguinte:
   
    ![O painel de Logon Único do SAML](./media/bamboo-hr-tutorial/IC796692.png "Logon Único do SAML")
   
    a. Na caixa **URL de logon SSO**, cole a **URL de Logon** que você copiou do portal do Azure na etapa 6.
      
    b. No Bloco de Notas, abra o certificado codificado em Base 64 baixado no Portal do Azure, copie o conteúdo e cole-o na caixa **Certificado X.509**.
   
    c. Selecione **Salvar**.

### <a name="create-bamboohr-test-user"></a>Crie um usuário de teste do BambooHR

Para permitir que os usuários do Microsoft Azure Active Directory façam logon no BambooHR, configure-os manualmente no BambooHR da seguinte maneira:

1. Entre em seu site de empresa do **BambooHR** como administrador.

2. Na barra de ferramentas na parte superior, selecione **Configurações**.
   
    ![O botão Configurações](./media/bamboo-hr-tutorial/IC796694.png "Configuração")

3. Selecione **Visão geral**.

4. No painel esquerdo, selecione **Segurança** > **Usuários**.

5. Digite o nome de usuário, a senha e o endereço de email da conta válida do Microsoft Azure Active Directory que você deseja configurar.

6. Selecione **Salvar**.
        
>[!NOTE]
>Para configurar contas de usuários do Microsoft Azure Active Directory, você também pode usar a ferramenta de criação de contas de usuários do BambooHR ou APIs.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Entrada do BambooHR, onde poderá iniciar o fluxo de logon. 

2. Acesse a URL de Entrada do BambooHR diretamente e inicie o fluxo de logon desse local.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco BambooHR do Painel de Acesso, você será redirecionado para a URL de Entrada do BambooHR. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o BambooHR, será possível impor o controle de sessão, que protegerá contra a exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).