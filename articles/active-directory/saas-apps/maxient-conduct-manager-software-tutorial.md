---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Maxient Conduct Manager Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Maxient Conduct Manager Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458193"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Maxient Conduct Manager Software

Neste tutorial, você aprenderá a integrar o Maxient Conduct Manager Software ao Azure Active Directory (Azure AD). Ao integrar o Maxient Conduct Manager Software ao Azure AD, você pode:

* Utilize o Azure AD para autenticar seus usuários para o Maxient Conduct Manager Software
* Permitir que seus usuários entrem automaticamente no Maxient Conduct Manager Software com suas contas do Azure AD.


Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Maxient Conduct Manager Software.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar seu Azure AD para uso com o Maxient Conduct Manager Software.


* O Maxient Conduct Manager Software é compatível com SSO iniciado por **SP e IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Como adicionar o Maxient Conduct Manager Software da galeria

Para configurar a integração do Maxient Conduct Manager Software ao Azure AD, você precisará adicionar o Maxient Conduct Manager Software da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da Galeria**, digite **Maxient Conduct Manager Software** na caixa de pesquisa.
1. Selecione **Maxient Conduct Manager Software** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Configurar e testar o logon único do Azure AD para o Maxient Conduct Manager Software

Configurar e testar o SSO do Azure AD com o Maxient Conduct Manager Software. Para que o SSO funcione, você precisa estabelecer uma conexão entre o Azure AD e o Maxient Conduct Manager Software.

Para configurar e testar o SSO do Azure AD com o Maxient Conduct Manager Software, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários se autentiquem para uso com o Maxient Conduct Manager Software
    1. **[Atribuir todos os usuários para usar o Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** – para permitir que todos em sua instituição possam se autenticar.
1. **[Testar a instalação do Azure AD com o Maxient](#test-with-maxient)** – para verificar se a configuração funciona e se os atributos corretos estão sendo liberados

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Maxient Conduct Manager Software**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado no modo iniciado do **IDP** e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com seu representante de implementação/suporte do Maxient para obter o valor.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.  Você precisará fornecer essa URL ao representante de implementação/suporte do Maxient.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Atribuir a todos os usuários a autenticação no Maxient Conduct Manager Software

Nesta seção, você concederá acesso a todas as contas para autenticação usando o sistema do Azure para o Maxient Conduct Manager Software.  É importante observar que essa etapa é **OBRIGATÓRIA** para que o Maxient funcione corretamente.  O Maxient usa seu sistema do Azure AD para *autenticar* os usuários. A *autorização* de usuários é executada no sistema Maxient para a função específica que eles estão tentando executar. O Maxient não usa atributos do seu diretório para tomar essas decisões.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Maxient Conduct Manager Software**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione todos os usuários (ou os grupos apropriados) e **atribua**-os para que possam ser autenticados com o Maxient.

## <a name="test-with-maxient"></a>Testar com o Maxient 

Se um tíquete de suporte ainda não tiver sido aberto com um representante de implementação/suporte do Maxient, envie um email para [support@maxient.com](mailto:support@maxient.com) com o assunto "Autenticação baseada em campus/instalação do Azure – \<\<School Name\>\>". No corpo do email, forneça a **URL de Metadados de Federação do Aplicativo**. A equipe do Maxient responderá com um link de teste para verificar se os atributos adequados estão sendo lançados.  
    
## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Maxient Conduct Manager Software com o Azure AD](https://aad.portal.azure.com/)