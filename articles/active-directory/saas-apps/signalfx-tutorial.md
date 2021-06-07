---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SignalFx | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o SignalFx

Neste tutorial, você aprenderá a integrar o SignalFx ao Azure AD (Azure Active Directory). Ao integrar o SignalFx no Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao SignalFx;
* Permitir que os usuários sejam conectados automaticamente ao SignalFx com as respectivas contas do Azure AD; e
* Gerenciar suas contas em um só local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará do seguinte:

* Uma assinatura do Azure AD
    * Se você não tiver uma assinatura, obtenha uma [conta gratuita aqui](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SignalFx

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SignalFx dá suporte ao SSO iniciado por **IdP**
* O SignalFx dá suporte ao provisionamento de usuário **just-in-time**
* Depois de configurar o SignalFx, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Etapa 1: Adicionar o aplicativo SignalFx ao Azure

Use estas instruções para adicionar o aplicativo SignalFx à lista de aplicativos SaaS gerenciados.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Na janela de navegação do lado esquerdo, selecione **Azure Active Directory**.
1. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, na caixa de pesquisa, insira e selecione **SignalFx**.
     * Talvez seja necessário aguardar alguns minutos para que o aplicativo seja adicionado ao seu locatário.
1. Mantenha o portal do Azure aberto e, em seguida, abra uma nova guia da Web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Etapa 2: Iniciar a configuração do SSO do SignalFx

Use estas instruções para iniciar o processo de configuração do SSO do SignalFx.

1. Na guia recém-aberta, acesse a interface do usuário do SignalFx e faça logon nela. 
1. No menu superior, clique em **Integrações**. 
1. No campo de pesquisa, insira e selecione **Azure Active Directory**.
1. Clique em **Criar Integração**.
1. Em **Nome**, insira um nome que seja facilmente reconhecível e compreensível para os usuários.
1. Marque a opção **Mostrar na página de logon**.
    * Esse recurso exibirá um botão personalizado na página de logon no qual os usuários poderão clicar. 
    * As informações inseridas em **Nome** serão exibidas no botão. Como resultado, insira um **Nome** que os usuários reconhecerão. 
    * Esta opção só funcionará se você usar um subdomínio personalizado para o aplicativo SignalFx, como **yourcompanyname.signalfx.com**. Para obter um subdomínio personalizado, entre em contato com o suporte do SignalFx. 
1. Copie a **ID de Integração**. Você precisará dessas informações em uma etapa posterior. 
1. Mantenha a interface do usuário do SignalFx aberta. 

## <a name="step-3-configure-azure-ad-sso"></a>Etapa 3: Configurar o SSO do Azure AD

Use estas instruções para habilitar o SSO do Azure AD no portal do Azure.

1. Volte ao [portal do Azure](https://portal.azure.com/) e, na página de integração de aplicativos do **SignalFx**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta (edição) da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar o logon único com o SAML**, preencha os seguintes campos: 

    a. Em **Identificador**, insira a URL `https://api.<realm>.signalfx.com/v1/saml/metadata` a seguir e substitua `<realm>` pelo realm do SignalFx. 

    b. Em **URL de Resposta**, insira a URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` a seguir e substitua `<realm>` pelo realm do SignalFx, bem como `<integration ID>` pela **ID de Integração** copiada anteriormente da interface do usuário do SignalFx.

1. Seu aplicativo SignalFx espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. 
    
1. Examine as declarações a seguir e verifique se elas são mapeadas para os atributos de origem preenchidos no Active Directory. 

    | Nome |  Atributo de Origem|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Esse processo exige que o Active Directory esteja configurado com, pelo menos, um domínio personalizado verificado e tenha acesso às contas de email desse domínio. Se você não tiver certeza dessa configuração ou precisar de assistência, entre em contato com o suporte do SignalFx.  

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, encontre **Certificado (Base64)** e, em seguida, selecione **Baixar**. Baixe o certificado e salve-o no computador. Em seguida, copie o valor da **URL de Metadados de Federação do Aplicativo**; você precisará dessas informações em uma etapa posterior na interface do usuário do SignalFx. 

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SignalFx**, copie o valor do **Identificador do Azure AD**. Você precisará dessas informações em uma etapa posterior na interface do usuário do SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Etapa 4: Criar um usuário de teste do Azure AD

Use estas instruções para criar um usuário de teste no portal do Azure chamado **B.Fernandes**.

1. No portal do Azure, na janela de navegação do lado esquerdo, selecione **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Na parte superior da página, selecione **Novo usuário**.
1. Nas propriedades de **Usuário**:
   1. Em **Nome de usuário**, insira `username@companydomain.extension`, como `b.simon@contoso.com`.
   1. Em **Nome**, insira `B.Simon`.
   1. Marque a opção **Mostrar senha** e, em seguida, copie o valor exibido em **Senha**. Você precisará dessas informações em etapas posteriores para testar essa integração. 
   1. Clique em **Criar**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Etapa 5: Atribuir o usuário de teste do Azure AD

Use estas instruções para permitir que o usuário de teste use o logon único do Azure para o SignalFx.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SignalFx**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e, em seguida, na parte inferior da página, clique em **Selecionar**.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função de usuário apropriada para o usuário na lista e, em seguida, clique em **Selecionar** na parte inferior da página.
1. Na caixa de diálogo **Adicionar Atribuição**, clique em **Atribuir**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Etapa 6: Concluir a configuração do SSO do SignalFx 

1. Abra a guia anterior e volte à interface do usuário do SignalFx para exibir a página atual de integração do Azure Active Directory. 
1. Ao lado de **Certificado (Base64)** , clique em **Carregar Arquivo** e, em seguida, localize o arquivo de **certificado codificado em Base64** baixado anteriormente do portal do Azure.
1. Ao lado do **Identificador do Azure AD**, cole o valor do **Identificador do Azure AD** copiado anteriormente do portal do Azure. 
1. Ao lado da **URL de Metadados de Federação**, cole o valor da **URL de Metadados de Federação do Aplicativo** copiado anteriormente do portal do Azure. 
1. Clique em **Save** (Salvar).

## <a name="step-7-test-sso"></a>Etapa 7: Testar o SSO

Examine as informações a seguir sobre como testar o SSO, bem como as expectativas para fazer logon no SignalFx pela primeira vez. 

### <a name="test-logins"></a>Testar logons

* Para testar o logon, você deverá usar uma janela particular/anônima ou fazer logoff do portal do Azure. Caso contrário, os cookies do usuário que configurou o aplicativo vão interferir e impedir um logon bem-sucedido com o usuário de teste.

* Quando um novo usuário de teste fizer logon pela primeira vez, o Azure forçará uma alteração de senha. Quando isso ocorrer, o processo de logon SSO não será concluído; o usuário de teste será direcionado para o portal do Azure. Para solucionar problemas, o usuário de teste deve alterar a senha e navegar até a página de logon do SignalFx ou o Painel de Acesso e tentar novamente.
    * Ao clicar no bloco do SignalFx no Painel de Acesso, você deverá ser conectado automaticamente ao SignalFx. 
        * Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

* O aplicativo SignalFx pode ser acessado no Painel de Acesso ou por meio de uma página de logon personalizada atribuída à organização. O usuário de teste deve testar a integração em uma dessas localizações.
    * O usuário de teste pode usar as credenciais criadas anteriormente nesse processo para **b.simon\@contoso.com**.

### <a name="first-time-logins"></a>Primeiros logons

* Quando um usuário fizer logon no SignalFx por meio do SSO do SAML pela primeira vez, ele receberá um email do SignalFx com um link. O usuário precisará clicar no link para fins de autenticação. Essa validação de email só ocorrerá para usuários novos. 

* O SignalFx dá suporte à criação de usuário **just-in-time**, o que significa que, se um usuário não existir no SignalFx, a conta do usuário será criada na primeira tentativa de logon.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Experimente o SignalFx com o Azure AD](https://aad.portal.azure.com/)