---
title: 'Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Captive Portal | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – Captive Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1b9c9182b8ffc6e9ea2e05fd4863783f111f81dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727142"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Captive Portal

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – Captive Portal ao Azure AD (Azure Active Directory).
A integração do Palo Alto Networks Captive Portal ao Azure AD oferece os seguintes benefícios:

* Controlar, no Azure AD, quem tem acesso ao Palo Alto Networks Captive Portal.
* Você pode habilitar os usuários a fazer logon automaticamente no Palo Alto Networks Captive Portal (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Azure AD ao Palo Alto Networks – Captive Portal, você precisa dos itens a seguir:

* Uma assinatura do Azure Active Directory. Se não tiver o Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Palo Alto Networks – Captive Portal habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Palo Alto Networks Captive Portal dá suporte ao SSO iniciado por **IDP**
* O Palo Alto Networks Captive Portal dá suporte ao provisionamento do usuário **Just-In-Time**

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicionar Palo Alto Networks Captive Portal da galeria

Para configurar a integração do Palo Alto Networks Captive Portal ao Azure AD, você precisará adicionar o Palo Alto Networks Captive Portal da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Palo Alto Networks Captive Portal** na caixa de pesquisa.
1. Selecione **Palo Alto Networks Captive Portal** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Palo Alto Networks Captive Portal com base em uma usuária de teste chamada **B. Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks Captive Portal.

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks Captive Portal, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – habilite o usuário a usar esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – teste o logon único do Azure AD com o usuário B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – configure B.Fernandes para usar o logon único do Microsoft Azure AD.
2. **[Configurar o SSO do Palo Alto Networks Captive Portal](#configure-palo-alto-networks-captive-portal-sso)** – defina as configurações de logon único no aplicativo.
    * **[Criar um usuário de teste do Palo Alto Networks Captive Portal](#create-a-palo-alto-networks-captive-portal-test-user)** – para ter um equivalente de B. Fernandes no Palo Alto Networks Captive Portal vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – verifique se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativo **Palo Alto Networks Captive Portal**, encontre a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. No painel **Configuração Básica do SAML**, realize as seguintes etapas:

   1. Para **Identificador**, insira uma URL com o padrão `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Para **URL de resposta**, insira uma URL com o padrão `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Atualize os valores de espaço reservado nesta etapa com o identificador real e URLs de resposta. Para obter os valores reais, entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Captive Portal](https://support.paloaltonetworks.com/support).

5. Na seção **Certificado de autenticação SAML**, ao lado de **XML de metadados de federação**, selecione **Baixar**. Salve o arquivo baixado em seu computador.

    ![O link de download do XML de metadados de federação](common/metadataxml.png)

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

Nesta seção, você habilitará B. Fernandes a usar o logon único do Azure concedendo a ela acesso ao Palo Alto Networks Captive Portal.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Palo Alto Networks Captive Portal**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Configurar o SSO do Palo Alto Networks Captive Portal

Em seguida, configure o logon único do Palo Alto Networks – Captive Portal:

1. Em outra janela do navegador, entre no site Palo Alto Networks como administrador.

2. Selecione a guia **Dispositivo**.

    ![Guia Dispositivo do site Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No menu, selecione **Provedor de identidade SAML** e, em seguida, selecione **Importar**.

    ![O botão Importar](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na caixa de diálogo **Importar perfil do servidor do provedor de identidade SAML**, conclua as etapas a seguir:

    ![Configurar o logon único do Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Em **Nome do perfil**, insira um nome, como **AzureAD-CaptivePortal**.
    
    2. Ao lado de **Metadados do provedor de identidade**, selecione **Procurar**. Selecione o arquivo metadata.xml que você baixou no portal do Azure.
    
    3. Selecione **OK**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um usuário de teste do Palo Alto Networks – Captive Portal

Em seguida, crie um usuário chamado *Brenda Fernandes* no Palo Alto Networks – Captive Portal. O Palo Alto Networks – Captive Portal dá suporte ao provisionamento do usuário Just-In-Time, que é habilitado por padrão. Você não precisará concluir as tarefas nesta seção. Se um usuário ainda não existir no Palo Alto Networks – Captive Portal, um novo usuário será criado após a autenticação.

> [!NOTE]
> Se quiser criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Captive Portal](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você entrará automaticamente no Palo Alto Networks Captive Portal, para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Palo Alto Networks Captive Portal em Meus Aplicativos, você deverá ser conectado automaticamente ao Palo Alto Networks Captive Portal, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Palo Alto Networks Captive Portal, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).