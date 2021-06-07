---
title: Personalizar a interface do usuário
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do usuário para seus aplicativos que usam Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055607"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A identidade visual e a personalização da interface do usuário que o Azure Active Directory B2C (Azure AD B2C) exibe para seus clientes, ajuda a fornecer uma experiência de usuário tranqüila em seu aplicativo. Essas experiências incluem a inscrição, a entrada, a edição de perfil e a redefinição de senha. Neste artigo, você personaliza suas páginas de Azure AD B2C usando o modelo de página e a identidade visual da empresa. 

> [!TIP]
> Para personalizar outros aspectos de suas páginas de fluxo de usuário além do modelo de página, logotipo de faixa, imagem de tela de fundo ou cor da tela de fundo, consulte como [Personalizar a interface do usuário com o modelo HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Visão geral

Azure AD B2C fornecer vários modelos internos que você pode escolher para dar às suas páginas de experiência do usuário uma aparência profissional. Esses modelos de página também podem e servem como ponto de partida para sua própria personalização, usando o recurso de [identidade visual da empresa](#company-branding) .

### <a name="ocean-blue"></a>Azul-marinho

Exemplo do modelo azul do oceano renderizado na página de entrada de inscrição:

![Captura de tela do modelo azul do oceano](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Cinza-acinzentado

Exemplo do modelo cinza-acinzentado renderizado na página de entrada de inscrição:

![Captura de tela do modelo cinza-ardósia](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Clássico

Exemplo do modelo clássico renderizado na página de entrada de inscrição:

![Captura de tela do modelo clássico](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Identidade visual da empresa

Você pode personalizar suas páginas de Azure AD B2C com um logotipo de faixa, uma imagem de tela de fundo e uma cor de plano de fundo usando Azure Active Directory [identidade visual da empresa](../active-directory/fundamentals/customize-branding.md). A identidade visual da empresa inclui a inscrição, a entrada, a edição de perfil e a redefinição de senha. 

O exemplo a seguir mostra uma página *inscrever-se e entrar* com um logotipo personalizado, uma imagem de plano de fundo, usando o modelo azul do oceano:

![Página de inscrição/entrada com marca fornecida por Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Selecionar um modelo de página

::: zone pivot="b2c-user-flow"

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione um fluxo de usuário que você deseja personalizar.
1. Em **Personalizar** no menu à esquerda, selecione **layouts de página** e, em seguida, selecione um **modelo**.
    ![Lista suspensa seleção de modelos na página fluxo do usuário do portal do Azure](./media/customize-ui/select-page-template.png)

Quando você escolhe um modelo, o modelo selecionado é aplicado a todas as páginas em seu fluxo de usuário. O URI para cada página é visível no campo **URI de página personalizada** .

::: zone-end

::: zone pivot="b2c-custom-policy"

Para selecionar um modelo de página, defina o `LoadUri` elemento das [definições de conteúdo](contentdefinitions.md). O exemplo a seguir mostra os identificadores de definição de conteúdo e o correspondente `LoadUri` . 

Azul-marinho:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Cinza-acinzentado:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Clássico 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Configurar identidade visual da empresa

Para personalizar suas páginas de fluxo de usuário, primeiro configure a identidade visual da empresa no Azure Active Directory, em seguida, habilite-a em seus fluxos de usuário no Azure AD B2C.

Comece definindo o logotipo de faixa, a imagem de tela de fundo e a cor da tela de fundo dentro **da identidade visual da empresa**.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **gerenciar**, selecione **identidade visual da empresa**.
1. Siga as etapas em [Adicionar identidade visual à página de entrada Azure Active Directory de sua organização](../active-directory/fundamentals/customize-branding.md).

Lembre-se destas coisas ao configurar a identidade visual da empresa no Azure AD B2C:

* A identidade visual da empresa no Azure AD B2C está limitada atualmente à **imagem de plano de fundo**, ao logotipo da **faixa** e à personalização da cor da **tela de fundo** . *Não há suporte* para as outras propriedades no painel de identidade visual da empresa, por exemplo, **Configurações avançadas**.
* Nas páginas de fluxo do usuário, a cor do plano de fundo é mostrada antes da imagem de plano de fundo ser carregada. Recomendamos que você escolha uma cor de plano de fundo que corresponda melhor às cores na sua imagem de plano de fundo para uma experiência de carregamento mais suave.
* O logotipo de faixa aparece nos emails de verificação enviados aos usuários quando eles iniciam um fluxo de usuário de inscrição.



## <a name="enable-company-branding-in-user-flow-pages"></a>Habilitar identidade visual da empresa em páginas de fluxo do usuário

::: zone pivot="b2c-user-flow"

Depois de configurar a identidade visual da empresa, habilite-a em seus fluxos de usuário.

1. No menu à esquerda da portal do Azure, selecione **Azure ad B2C**.
1. Em **Políticas**, selecione **Fluxos de usuários (políticas)** .
1. Selecione o fluxo de usuário para o qual você deseja habilitar a identidade visual da empresa. A identidade visual da empresa **não tem suporte** para a *entrada* padrão e os tipos de fluxo de usuário de *edição de perfil* padrão.
1. Em **Personalizar**, selecione **layouts de página** e, em seguida, selecione a página que você deseja marcar. Por exemplo, selecione **página de inscrição ou inscrição unificada**.
1. Para a **versão de layout de página (visualização)**, escolha a versão **1.2.0** ou superior.
1. Clique em **Salvar**.

Se você quiser marcar todas as páginas no fluxo do usuário, defina a versão de layout da página para cada layout de página no fluxo do usuário.

![Seleção de layout de página em Azure AD B2C no portal do Azure](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Depois de configurar a identidade visual da empresa, habilite-a em sua política personalizada. Configure a [versão de layout de página](contentdefinitions.md#migrating-to-page-layout) com a versão de página `contract` para *todas* as definições de conteúdo em sua política personalizada. O formato do valor deve conter a palavra `contract` : _urn: com: Microsoft: AAD: B2C: elements:page-Name: Version_. Para especificar um layout de página em suas políticas personalizadas que usam um valor antigo de **DataUri** . Para obter mais informações, saiba como [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página.

O exemplo a seguir mostra as definições de conteúdo com seu contrato de página correspondente e o modelo de página *azul do oceano* : 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Encontre mais informações sobre como você pode personalizar a interface do usuário de seus aplicativos em [Personalizar a interface do usuário do seu aplicativo no Azure Active Directory B2C](customize-ui-with-html.md).
