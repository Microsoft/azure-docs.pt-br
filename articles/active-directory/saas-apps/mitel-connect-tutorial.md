---
title: 'Tutorial: Integração do Azure Active Directory com o Mitel Connect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mitel Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015172"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Tutorial: Integração do Azure Active Directory ao Mitel MiCloud Connect ou à CloudLink Platform

Neste tutorial, você aprenderá a usar o aplicativo Mitel Connect para integrar o Azure AD (Azure Active Directory) ao Mitel MiCloud Connect ou à CloudLink Platform. O aplicativo Mitel Connect está disponível na Galeria do Azure. A integração do Azure AD ao MiCloud Connect ou à CloudLink Platform oferece os seguintes benefícios:

* Você pode controlar o acesso dos usuários aos aplicativos MiCloud Connect e CloudLink no Azure AD usando as respectivas credenciais corporativas.
* Você pode permitir que os usuários na sua conta sejam conectados automaticamente ao MiCloud Connect ou ao CloudLink (logon único) com as respectivas contas do Azure AD.

Para obter detalhes sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso a aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar a integração do Azure AD ao Mitel MiCloud Connect ou à CloudLink Platform.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MiCloud Connect, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma conta do Mitel MiCloud Connect ou do Mitel CloudLink, dependendo do aplicativo que você deseja configurar.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO (logon único) do Azure AD.

* O Mitel Connect é compatível com SSO iniciado por **SP**
* Depois de configurar o Mitel Connect, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Adicionar o Mitel Connect por meio da galeria

Para configurar a integração do Mitel Connect ao Azure AD, você precisará adicionar o Mitel Connect por meio da galeria à lista de aplicativos SaaS gerenciados no portal do Azure.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Selecione **Aplicativos Empresariais** e, em seguida, selecione **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Selecione **Novo aplicativo**.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Digite **Mitel Connect** no campo de pesquisa, selecione **Mitel Connect** no painel de resultados e **Adicionar**.

     ![Mitel Connect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o SSO do Azure AD com o MiCloud Connect ou a CloudLink Platform com base em um usuário de teste chamado **_Brenda Fernandes_**. Para que o logon único funcione, é necessário estabelecer um vínculo entre o usuário do portal do Azure AD e o usuário correspondente da plataforma Mitel. Veja as seções a seguir para obter informações sobre como configurar e testar o SSO do Azure AD com o MiCloud Connect ou a CloudLink Platform.
* Configurar e testar o SSO do Azure AD com o MiCloud Connect
* Configurar e testar o SSO do Azure AD com a CloudLink Platform

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Configurar e testar o SSO do Azure AD com o MiCloud Connect

Para configurar e testar o logon único do Azure AD com o MiCloud Connect:

1. **[Configurar o MiCloud Connect para o SSO com o Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – para permitir que os usuários usem esse recurso e definam as configurações de SSO no lado do aplicativo.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
4. **[Criar um usuário de teste do Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** – para ter um equivalente de Brenda Fernandes em sua conta do MiCloud Connect que esteja vinculado à representação do usuário do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurar o MiCloud Connect para o SSO com o Azure AD

Nesta seção, você habilitará o logon único do Azure AD para o MiCloud Connect no portal do Azure e configurará sua conta do MiCloud Connect para permitir o SSO usando o Azure AD.

Para configurar o MiCloud Connect com o SSO do Azure AD, é mais fácil abrir o portal do Azure e o portal da conta do Mitel lado a lado. Você precisará copiar algumas informações do portal do Azure para o portal da conta do Mitel e algumas do portal da conta do Mitel para o portal do Azure.


1. Para abrir a página de configuração no [portal do Azure](https://portal.azure.com/):

    1. Na página de integração de aplicativos do **Mitel Connect**, selecione **Logon único**.

       ![Link Configurar logon único](common/select-sso.png)

    1. Na caixa de diálogo **Selecionar um método de logon único**, escolha **SAML**.
    
       ![Modo de seleção de logon único](common/select-saml-option.png)
    
       A página de entrada baseada no SAML será exibida.

2. Para abrir a caixa de diálogo de configuração no portal da Conta do Mitel:

    1. No menu **Sistema de Telefonia**, selecione **Recursos Complementares**.

    1. À direita de **Logon Único**, selecione **Ativar** ou **Configurações**.
    
    A caixa de diálogo Configurações de Logon Único do Connect será exibida.
    
3. Marque a caixa de seleção **Habilitar Logon Único**.
    
    ![Captura de tela que mostra a página Configurações de Logon Único do Mitel Connect com a caixa de seleção Habilitar Logon Único marcada.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. No portal do Azure, selecione o ícone **Editar** na seção **Configuração Básica do SAML**.
   
    ![A captura de tela mostra a página Configurar Logon Único com SAML com o ícone Editar selecionado.](common/edit-urls.png)

    A caixa de diálogo Configuração Básica do SAML será exibida.

5.  Copie a URL do campo **Mitel Identifier (Entity ID)** (Identificador do Mitel [ID da Entidade]) no portal da conta do Mitel e cole-a no campo **Identificador (ID da Entidade)** no portal do Azure.

6. Copie a URL do campo **Reply URL (Assertion Consumer Service URL)** (URL de Resposta [URL do Serviço do Consumidor de Declaração]) no portal da conta do Mitel e cole-a no campo **URL de Resposta (URL do Serviço do Consumidor de Declaração)** no portal do Azure.

   ![A captura de tela mostra a Configuração Básica do SAML no portal do Azure e a seção Configurar Provedor de Identidade no portal Conta do Mitel com as linhas indicando a relação entre elas.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. Na caixa de texto **URL de Logon**, digite uma das seguintes URLs:

    1. **https://portal.shoretelsky.com** – para usar o portal da conta do Mitel como o aplicativo padrão do Mitel
    1. **https://teamwork.shoretel.com** – para usar o Teamwork como o aplicativo padrão do Mitel

    > [!NOTE]
    > O aplicativo padrão do Mitel é o aplicativo acessado quando um usuário seleciona o bloco do Mitel Connect no Painel de Acesso. Esse também é o aplicativo acessado ao fazer uma configuração do teste por meio do Azure AD.

8. Selecione **Salvar** na caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

9. Na seção **Certificado de Autenticação SAML** na página **Logon baseado em SAML** do portal do Azure, selecione **Baixar** ao lado de **Certificado (Base64)** para baixar o **Certificado de Autenticação** e salvá-lo no computador.

    ![A captura de tela mostra o painel Certificado de Autenticação SAML em que você pode baixar um certificado.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Abra o arquivo de Certificado de Autenticação em um editor de texto, copie todos os dados no arquivo e, em seguida, cole os dados no campo **Certificado de Autenticação** no portal da conta do Mitel. 

      ![A captura de tela mostra o campo Certificado de Autenticação.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Na seção **Configurar o Mitel Connect** na página **Logon baseado em SAML** do portal do Azure:

     1. Copie a URL do campo **URL de Logon** e cole-a no campo **URL de Entrada** no portal da conta do Mitel.

     1. Copie a URL do campo **Identificador do Azure AD** e cole-a no campo **ID da Entidade** no portal da conta do Mitel.
         
         ![A captura de tela mostra a relação entre o página de logon baseado em SAML do portal do Azure e o portal da Conta do Mitel.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Selecione **Salvar** na caixa de diálogo **Configurações de Logon Único do Connect** no portal da Conta do Mitel.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Na caixa de diálogo Propriedades do usuário, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](common/user-properties.png)

    1. No campo **Nome**, digite **BrendaFernandes**.
  
    1. No campo **Nome de usuário**, digite brendafernandes@\<yourcompanydomain\>.\<extension\>.  Por exemplo, BrittaSimon@contoso.com.

    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Mitel Connect.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Mitel Connect**.

    ![O link do Mitel Connect na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e escolha **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, escolha a função de usuário apropriada na lista da caixa de diálogo **Selecionar Função** e **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Criar um usuário de teste do Mitel MiCloud Connect

Nesta seção, você criará um usuário chamado Brenda Fernandes em sua conta do MiCloud Connect. Os usuários precisam ser criados e ativados para usarem o logon único.

Para obter detalhes sobre como adicionar usuários no portal da conta do Mitel, confira o artigo [Adding a User](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) (Como adicionar um usuário) da Base de Dados de Conhecimento da Mitel.

Crie um usuário em sua conta do MiCloud Connect com os seguintes detalhes:

* **Nome:** Brenda Fernandes
* **Endereço de Email Empresarial:** `brittasimon@<yourcompanydomain>.<extension>`   
  (Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **Nome de usuário:** `brittasimon@<yourcompanydomain>.<extension>`  
  (Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); o nome de usuário do usuário normalmente é o mesmo que o endereço de email empresarial do usuário)

> [!NOTE]
> O nome de usuário do MiCloud Connect do usuário precisa ser idêntico ao endereço de email do usuário no Azure.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Mitel Connect no Painel de Acesso, você deverá ser redirecionado automaticamente para entrar no aplicativo MiCloud Connect configurado como o padrão no campo **URL de Logon**. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Configurar e testar o SSO do Azure AD com a CloudLink Platform

Esta seção descreve como habilitar o SSO do Azure AD para a plataforma CloudLink no portal do Azure e como configurar sua conta da plataforma CloudLink para permitir o logon único usando o Azure AD.

Para configurar a plataforma CloudLink com o logon único para o Azure AD, recomendamos abrir o portal do Azure e o portal de Contas do CloudLink lado a lado, pois será necessário copiar algumas informações do portal do Azure para o portal de Contas do CloudLink e vice-versa.

1. Para abrir a página de configuração no [portal do Azure](https://portal.azure.com/):

    1. Na página de integração de aplicativos do **Mitel Connect**, selecione **Logon único**.

       ![Link Configurar logon único](common/select-sso.png)

    1. Na caixa de diálogo **Selecionar um método de logon único**, escolha **SAML**.

       ![Modo de seleção de logon único](common/select-saml-option.png)
    
       A página **Logon baseado em SAML** será aberta, exibindo a seção **Configuração Básica do SAML**.

       ![A captura de tela mostra a página Logon baseado em SAML com a Configuração Básica do SAML.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Para acessar o painel de configuração **Logon Único do Azure AD** no portal de Contas do CloudLink:

    1. Acesse a página **Dados da Conta** da conta do cliente à qual deseja habilitar a integração.

    1. Na seção **Integrações**, selecione **+ Adicionar nova**. Uma tela pop-up exibirá o painel **Integrações**.

    1. Selecione a guia **Terceiros**. Uma lista de aplicativos de terceiros compatíveis será exibida. Selecione o botão **Adicionar** associado ao **Logon Único do Azure AD** e escolha **Concluído**.

       ![A captura de tela mostra a página Integrações em que você pode adicionar o Logon Único do Azure AD.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       O **Logon Único do Azure AD** está habilitado para a conta do cliente e é adicionado à seção **Integrações** da página **Dados da Conta**.   

   1. Selecione **Concluir Instalação**.
    
      ![A captura de tela mostra a opção Configuração Concluída para o Logon Único do Azure AD.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      O painel de configuração **Logon Único do Azure AD** será aberto.
      
       ![A captura de tela mostra a configuração de Logon Único do Azure AD.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       A Mitel recomenda que a caixa de seleção **Habilitar Credenciais do Mitel (Opcional)** da seção **Credenciais opcionais do Mitel** não esteja marcada. Marque essa caixa de seleção somente se quiser que o usuário entre no aplicativo CloudLink usando as credenciais do Mitel além da opção de logon único.

3. No portal do Azure, na página **Logon baseado em SAML**, selecione o ícone **Editar** na seção **Configuração Básica do SAML**. O painel **Configuração Básica do SAML** será aberto.

    ![A captura de tela mostra o painel Configuração Básica do SAML com o ícone Editar selecionado.](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Copie a URL do campo **Identificador do Mitel (ID da Entidade)** no portal de Contas do CloudLink e cole-a no campo **Identificador (ID da Entidade)** no portal do Azure.

 5. Copie a URL do campo **URL de Resposta (URL do Serviço do Consumidor de Declaração)** no portal de Contas do CloudLink e cole-a no campo **URL de Resposta (URL do Serviço do Consumidor de Declaração)** no portal do Azure.  
    
    ![A captura de tela mostra a relação entre páginas no portal Contas do CloudLink e o portal do Azure.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. Na caixa de texto **URL de Logon**, digite a URL `https://accounts.mitel.io` para usar o portal de Contas do CloudLink como o aplicativo padrão do Mitel.
     
     ![A captura de tela mostra a caixa de texto URL de Logon.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > O aplicativo padrão do Mitel é o aplicativo que é aberto quando um usuário seleciona o bloco do Mitel Connect no Painel de Acesso. Esse também é o aplicativo acessado quando o usuário define uma configuração do teste no Azure AD.

7. Selecione **Salvar** na caixa de diálogo **Configuração Básica do SAML**.

8. Na seção **Certificado de Autenticação SAML** na página **Logon baseado em SAML** do portal do Azure, selecione **Baixar** ao lado de **Certificado (Base64)** para baixar o **Certificado de Autenticação**. Salve o certificado no computador.
  
    ![A captura de tela mostra a seção Certificado de Autenticação SAML em que você pode baixar um certificado Base64.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Abra o arquivo do Certificado de Autenticação em um editor de texto, copie todos os dados do arquivo e cole-os no campo **Certificado de Autenticação** no portal de Contas do CloudLink.  

    > [!NOTE]
    > Caso você tenha mais de um certificado, recomendamos colá-los um após o outro. 
       
    ![A captura de tela mostra a etapa dois do procedimento em que você preenche valores de sua integração do Azure AD.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Na seção **Configurar o Mitel Connect** da página **Logon baseado em SAML** do portal do Azure:

     1. Copie a URL do campo **URL de Logon** e cole-a no campo **URL de Entrada** no portal de Contas do CloudLink.

     1. Copie a URL do campo **Identificador do Azure AD** e cole-a no campo **Identificador do IdP (ID da Entidade)** no portal de Contas do CloudLink.
     
        ![A captura de tela mostra a origem do valores descritos aqui no Mintel Connect.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Selecione **Salvar** no painel **Logon Único do Azure AD** no portal de Contas do CloudLink.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Na caixa de diálogo Propriedades do usuário, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](common/user-properties.png)

    1. No campo **Nome**, digite **BrendaFernandes**.
  
    1. No campo **Nome de usuário**, digite brendafernandes@\<yourcompanydomain\>.\<extension\>.  Por exemplo, BrittaSimon@contoso.com.

    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Mitel Connect.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Mitel Connect**.

    ![O link do Mitel Connect na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e escolha **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, escolha a função de usuário apropriada na lista da caixa de diálogo **Selecionar Função** e **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-cloudlink-test-user"></a>Criar um usuário de teste do CloudLink

Esta seção descreve como criar um usuário de teste chamado **_Brenda Fernandes_** na plataforma CloudLink. Os usuários precisarão ser criados e ativados para que usem o logon único.

Para obter detalhes sobre como adicionar usuários no portal de Contas do CloudLink, confira **_Como gerenciar usuários_** na [documentação de Contas do CloudLink](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html).

Crie um usuário no portal de Contas do CloudLink com os seguintes detalhes:

* Nome: Brenda Fernandes
* Nome: Brenda
* Sobrenome: Fernandes
* Email: BrittaSimon@contoso.com

> [!NOTE]
> o endereço de email do CloudLink do usuário precisa ser idêntico ao **Nome UPN** no portal do Azure.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Mitel Connect no Painel de Acesso, você será redirecionado automaticamente para entrar no aplicativo CloudLink configurado como o padrão no campo **URL de Logon**. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)