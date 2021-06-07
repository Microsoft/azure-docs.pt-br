---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Firmex VDR | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Firmex VDR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Firmex VDR

Neste tutorial, você aprenderá a integrar o Firmex VDR ao Azure AD (Azure Active Directory). Ao integrar o Firmex VDR ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Firmex VDR.
* Permitir que os usuários sejam conectados automaticamente ao Firmex VDR com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Firmex VDR.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Firmex VDR dá suporte ao SSO iniciado por **SP e IdP**

* Depois de configurar o Firmex, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Como adicionar o Firmex VDR por meio da galeria

Para configurar a integração do Firmex VDR ao Azure AD, você precisará adicionar o Firmex VDR por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Firmex VDR** na caixa de pesquisa.
1. Selecione **Firmex VDR** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Configurar e testar o logon único do Azure AD para o Firmex VDR

Configure e teste o SSO do Azure AD com o Firmex VDR usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Firmex VDR.

Para configurar e testar o SSO do Azure AD com o Firmex VDR, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Firmex VDR](#configure-firmex-vdr-sso)**: para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Firmex VDR](#create-firmex-vdr-test-user)**: para ter um equivalente de B.Fernandes no Firmex VDR que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Firmex VDR**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://login.firmex.com`

1. Clique em **Save** (Salvar).

1. O aplicativo Firmex VDR espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Firmex VDR espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ------------ | --------- |
    | email | user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Firmex VDR**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Firmex VDR.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Firmex VDR**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-firmex-vdr-sso"></a>Configurar o SSO do Firmex VDR

### <a name="before-you-get-started"></a>Antes de começar

#### <a name="what-youll-need"></a>O que será necessário

-   Uma assinatura ativa do Firmex
-   Azure AD como o serviço de SSO
-   O administrador de TI para configurar o SSO
-   Depois que o SSO for habilitado, todos os usuários da sua empresa precisarão fazer logon no Firmex usando o SSO, não usando um logon/uma senha.

#### <a name="how-long-will-this-take"></a>Quanto tempo isso levará?

A implementação do SSO leva alguns minutos. Praticamente não há nenhum tempo de inatividade entre a habilitação do SSO pelo suporte do Firmex para o site e a autenticação com o SSO pelos usuários da sua empresa. Basta seguir as etapas abaixo.

### <a name="step-1---identify-your-companys-domains"></a>Etapa 1: identificar os domínios da sua empresa

Identifique os domínios com os quais os usuários da sua empresa estão fazendo logon.

Por exemplo:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Etapa 2: contatar o suporte do Firmex com os domínios

Envie um email à [equipe de suporte do Firmex](mailto:support@firmex.com) ou ligue para 1888 688 4042 x.11 para falar com o suporte do Firmex. Informe as informações do domínio. O suporte do Firmex adicionará os domínios ao seu VDR como **domínios solicitados**. Agora, o administrador precisará configurar o SSO.

Aviso: até que o administrador do site configure os domínios solicitados, os usuários da sua empresa não poderão fazer logon no VDR. Os usuários que não são da empresa (ou seja, usuários convidados) ainda poderão fazer logon usando o email/a senha. A configuração deverá levar alguns minutos.

### <a name="step-3---configure-the-claimed-domains"></a>Etapa 3: configurar os domínios solicitados

1. Faça logon no Firmex como administrador de site.
1. No canto superior esquerdo, clique no logotipo da sua empresa.
1. Selecione a guia **SSO**. Em seguida, selecione **Configuração de SSO**. Clique no domínio que deseja configurar.

    ![Domínios solicitados](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Peça ao administrador de TI que preencha os campos a seguir. Os campos deverão ser obtidos com o provedor de identidade:  

    ![Configuração de SSO](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Na caixa de texto **URL do Provedor de Identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. **Certificado de Chave Pública**: para fins de autenticação, uma mensagem SAML poderá ser assinada digitalmente pelo emissor. Para verificar a assinatura na mensagem, o destinatário da mensagem usa uma chave pública conhecida como pertencente ao emissor. Da mesma forma, para criptografar uma mensagem, uma chave de criptografia pública que pertence ao destinatário final precisará ser conhecida pelo emissor. Em ambas as situações, assinatura e criptografia, as chaves públicas confiáveis precisam ser compartilhadas com antecedência.  Este é o **X509Certificate** do **XML de Metadados de Federação**

    d. Clique em **Salvar** para concluir a configuração de SSO. As alterações entrarão em vigor imediatamente.

1. Neste momento, o SSO está habilitado para o seu site.

### <a name="create-firmex-vdr-test-user"></a>Criar um usuário de teste do Firmex VDR

Nesta seção, você criará um usuário chamado B.Fernandes no Firmex. Trabalhe com a [equipe de suporte do Firmex](mailto:support@firmex.com) para adicionar os usuários à plataforma Firmex. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Firmex VDR no Painel de Acesso, você deverá ser conectado automaticamente ao Firmex VDR, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Firmex VDR com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Firmex com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)