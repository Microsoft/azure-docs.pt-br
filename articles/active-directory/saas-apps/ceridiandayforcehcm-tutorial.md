---
title: 'Tutorial: integração do Azure Active Directory com o Ceridian Dayforce HCM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651840"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: integração do Azure Active Directory ao Ceridian Dayforce HCM

Neste tutorial, você aprenderá a integrar o Ceridian Dayforce HCM ao Azure AD (Azure Active Directory). Ao integrar o Ceridian Dayforce HCM ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Ceridian Dayforce HCM.
* Permitir que seus usuários façam logon automaticamente no Ceridian Dayforce HCM com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Ceridian Dayforce HCM habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Ceridian Dayforce HCM é compatível com o SSO iniciado por **SP**

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Adição do Ceridian Dayforce HCM por meio da galeria

Para configurar a integração do Ceridian Dayforce HCM ao Azure AD, você precisa adicionar o Ceridian Dayforce HCM à sua lista de aplicativos SaaS gerenciados desde a galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Ceridian Dayforce HCM** na caixa de pesquisa.
1. Selecione **Ceridian Dayforce HCM** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Configurar e testar o SSO do Azure AD para o Ceridian Dayforce HCM

Configure e teste o SSO do Azure AD com o Ceridian Dayforce HCM usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Ceridian Dayforce HCM.

Para configurar e testar o SSO do Azure AD com o Ceridian Dayforce HCM, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)** – para ter um equivalente de B.Fernandes no Ceridian Dayforce HCM que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD 

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Ceridian Dayforce HCM**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de Domínio e URLs do HCM do Ceridian Dayforce HCM para logon único](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** , digite a URL usada pelos usuários para fazer logon em seu aplicativo do Ceridian Dayforce HCM.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para teste | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: 

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp` |

    c. Na caixa de texto **URL de Resposta** , digite a URL usada pelo Azure AD para postar a resposta.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Ceridian Dayforce HCM](https://www.ceridian.com/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Ceridian Dayforce HCM espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Captura de tela que mostra atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | Nome | Atributo de Origem|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Captura de tela que mostra Declarações de usuário com a opção de Adicionar declaração.](common/new-save-attribute.png)

    ![Captura de tela que mostra a caixa de diálogo Gerenciar declarações do usuário, na qual você pode inserir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, selecione o atributo de usuário que você deseja usar na sua implementação. Por exemplo, se você quiser usar EmployeeID como identificador exclusivo de usuário e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione user.extensionattribute2.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o Ceridian Dayforce HCM**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Ceridian Dayforce HCM.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Ceridian Dayforce HCM**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Configurar o SSO do Ceridian Dayforce HCM

Para configurar o logon único no lado do **Ceridian Dayforce HCM**, é necessário enviar o **XML de Metadados** baixado e as URLs corretas copiadas do portal do Azure para a [equipe de suporte do Ceridian Dayforce HCM](https://www.ceridian.com/support). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Criar um usuário de teste do Ceridian Dayforce HCM

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Ceridian Dayforce HCM. Trabalhe com a [equipe de suporte do Ceridian Dayforce HCM](https://www.ceridian.com/support) para adicionar os usuários na plataforma do Ceridian Dayforce HCM. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Ceridian Dayforce HCM, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Ceridian Dayforce HCM e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco Ceridian Dayforce HCM em Meus Aplicativos, isso redirecionará você à URL de Logon do Ceridian Dayforce HCM. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Ceridian Dayforce HCM, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).