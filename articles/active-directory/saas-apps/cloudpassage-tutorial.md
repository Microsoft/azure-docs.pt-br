---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CloudPassage | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o CloudPassage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: b720f7e49fc0679de5c3f430122bab05d5b706f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455741"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CloudPassage

Neste tutorial, você aprende a integrar o CloudPassage ao Azure AD (Azure Active Directory). Ao integrar o CloudPassage ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao CloudPassage.
* Permitir que os usuários sejam conectados automaticamente ao CloudPassage com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do CloudPassage habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O CloudPassage dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-cloudpassage-from-the-gallery"></a>Adicionando CloudPassage da Galeria

Para configurar a integração do CloudPassage com o AD do Azure, você precisa adicionar o CloudPassage, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **CloudPassage** na caixa de pesquisa.
1. Selecione **CloudPassage** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Configurar e testar o logon único do Azure AD para o CloudPassage

Configure e teste o SSO do Azure AD com o CloudPassage com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do CloudPassage.

Para configurar e testar o SSO do Azure AD com o CloudPassage, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do CloudPassage](#configure-cloudpassage-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar usuário de teste do CloudPassage](#create-cloudpassage-test-user)** – para ter um equivalente de B.Fernandes no CloudPassage vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **CloudPassage**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

     a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o padrão a seguir: `https://portal.cloudpassage.com/saml/consume/accountid`. Você pode obter o valor para este atributo clicando em **documentação de instalação do SSO** na seção **Configurações de Logon Único** do seu portal CloudPassage.

    ![Captura de tela que mostra o portal do CloudPassage com o link Documentação de Instalação do SSO em destaque.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do CloudPassage](https://www.cloudpassage.com/company/contact/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo CloudPassage espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo CloudPassage espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | nome |user.givenname |
    | sobrenome |user.surname |
    | email |user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar CloudPassage**, copie as URLs apropriadas conforme suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao CloudPassage.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **CloudPassage**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cloudpassage-sso"></a>Configurar o SSO do CloudPassage

1. Em outra janela do navegador, entre em seu site de empresa CloudPassage como administrador.

1. No menu, na parte superior, clique em **Configurações** e clique em **Administração do Site**. 
   
    ![Captura de tela que mostra o site do CloudPassage com Administração do Site selecionado.][12]

1. Clique na guia **Configurações de Autenticação** . 
   
    ![Captura de tela que mostra o site do CloudPassage com a guia Configurações de Autenticação selecionada.][13]

1. Na seção **Configurações de Logon Único** , realize as seguintes etapas: 
   
    ![Captura de tela que mostra a seção Configurações de Logon Único, em que você pode inserir as informações nesta etapa.][14]

    a. Marque a caixa de seleção **Habilitar SSO (Logon Único) (Documentação de instalação de SSO)** .
    
    b. Colar o **Identificador do Azure AD** na caixa de texto **URL do Emissor do SAML**.
  
    c. Cole a **URL de logon** na caixa de texto **URL do Ponto de Extremidade SAML**.
  
    d. Cole **URL de Logoff** na caixa de texto **Página de aterrissagem de logoff**.
  
    e. Abra seu certificado baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado x 509**.
  
    f. Clique em **Save** (Salvar).

### <a name="create-cloudpassage-test-user"></a>Criar um usuário de teste CloudPassage

O objetivo desta seção é criar um usuário chamado B.Fernandes no CloudPassage.

**Para criar um usuário chamado B.Fernandes no CloudPassage, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **CloudPassage** como administrador. 

1. Na barra de ferramentas, na parte superior, clique em **Configurações** e clique em **Administração do Site**. 
   
    ![Captura de tela que mostra o CloudPassage com Administração do Site selecionado.][22] 

1. Clique na guia **Usuários** e clique em **Adicionar Novo Usuário**. 
   
    ![Captura de tela que mostra a Administração do Site do CloudPassage com a guia Usuários selecionada e a opção para Adicionar Novo Usuário.][23]

1. Na seção **Adicionar Novo Usuário** , realize as seguintes etapas: 
   
    ![Captura de tela que mostra a seção Adicionar Novo Usuário, em que você pode e especificar informações do usuário.][24]
    
    a. Na caixa de texto **Nome** , digite Brenda. 
  
    b. Na caixa de texto **Sobrenome** , digite Fernandes.
  
    c. Nas caixas de texto **Nome de usuário**, **E-mail** e **Digite novamente o e-mail**, digite o nome de usuário da Brenda no AD do Azure.
  
    d. Como **Tipo de acesso**, selecione **Habilitar acesso do Portal de Halo**.
  
    e. Clique em **Adicionar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do CloudPassage no Painel de Acesso, você deverá ser conectado automaticamente ao CloudPassage no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o CloudPassage com o Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png