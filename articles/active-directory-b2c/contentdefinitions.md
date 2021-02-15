---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Especifica o elemento ContentDefinitions de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 018d90db06948f3fd6a34b56c65088641a9ca874
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108970"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode personalizar a aparência de qualquer [perfil técnico autodeclarado](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) executa o código no navegador do cliente e usa uma abordagem moderna chamada CORS (compartilhamento de recursos entre origens).

Para personalizar a interface do usuário, você pode especificar uma URL no elemento **ContentDefinition** com conteúdo personalizado em HTML. No perfil técnico autodeclarado ou **OrchestrationStep**, aponte para esse identificador de definição de conteúdo. A definição de conteúdo pode conter um elemento **LocalizedResourcesReferences** que especifica uma lista de recursos localizados a serem carregados. O Azure AD B2C mescla os elementos da interface do usuário com o conteúdo HTML carregado da URL e exibe a página ao usuário.

O elemento **ContentDefinitions** contém as URLs para modelos de HTML5 que podem ser usados em um percurso do usuário. O URI da página HTML5 é usado para uma etapa especificada da interface do usuário. Por exemplo, a redefinição de senha de entrada ou de inscrição ou páginas de erro. Você pode modificar a aparência substituindo o LoadUri pelo arquivo HTML5. Você pode criar definições de novo conteúdo de acordo com suas necessidades. Esse elemento pode conter uma referência de recursos localizados, como o identificador de localização especificado no elemento [localização](localization.md).

O exemplo a seguir mostra o identificador de definição de conteúdo e a definição de recursos localizados:

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Os metadados do perfil técnico autodeclarado **LocalAccountSignUpWithLogonEmail** contêm o identificador de definição de conteúdo **ContentDefinitionReferenceId** definido como `api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

O elemento **ContentDefinition** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador para uma definição de conteúdo. O valor é especificado na seção **ID de definição de conteúdo** mais adiante nesta página. |

O elemento **ContentDefinition** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Uma cadeia de caracteres que contém a URL da página HTML5 para a definição de conteúdo. |
| RecoveryUri | 1:1 | Uma cadeia de caracteres que contém a URL da página HTML para exibir um erro relacionado à definição de conteúdo. Não usado no momento, o valor deve ser `~/common/default_page_error.html` . |
| DataUri | 1:1 | Uma cadeia de caracteres que contém a URL relativa de um arquivo HTML que fornece a experiência do usuário a ser invocada para a etapa. |
| Metadados | 0:1 | Uma coleção de pares chave/valor que contém os metadados utilizados pela definição de conteúdo. |
| LocalizedResourcesReferences | 0:1 | Uma coleção de referências de recursos localizados. Use esse elemento para personalizar a localização de um atributo de declarações e a interface do usuário. |

### <a name="datauri"></a>DataUri

O elemento **DataUri** é usado para especificar o identificador de página. O Azure AD B2C usa o identificador de página para carregar e iniciar a elementos de interface do usuário e o JavaScript do lado do cliente. O formato do valor é `urn:com:microsoft:aad:b2c:elements:page-name:version`. A tabela a seguir lista os identificadores de página que você pode usar.

| Identificador de página | Descrição |
| ----- | ----------- |
| `globalexception` | Exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| `providerselection`, `idpselection` | Lista os provedores de identidade que os usuários podem escolher durante a entrada.  |
| `unifiedssp` | Exibe um formulário de entrada para entrar com uma conta local baseada em endereço de email ou nome de usuário. Esse valor também oferece as funcionalidades "Mantenha-me conectado" e "Esqueceu sua senha?" . |
| `unifiedssd` | Exibe um formulário de entrada para entrar com uma conta local baseada em endereço de email ou nome de usuário. |
| `multifactor` | Verifica os números de telefone usando o texto ou voz durante a inscrição ou entrada. |
| `selfasserted` | Exibe um formulário para coletar dados de um usuário. Por exemplo, permite que os usuários criem ou atualizem seu perfil. |

### <a name="select-a-page-layout"></a>Selecionar um layout de página

Você pode habilitar o [código do lado do cliente JavaScript](javascript-and-page-layout.md) inserindo `contract` entre `elements` e o tipo de página. Por exemplo, `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A parte da [versão](page-layout.md) do `DataUri` especifica o pacote de conteúdo que contém HTML, CSS e JavaScript para os elementos da interface do usuário em sua política. Se você pretende habilitar o código JavaScript do lado do cliente, os elementos dos quais você baseia seu JavaScript devem ser imutáveis. Se não forem imutáveis, as alterações poderão causar um comportamento inesperado nas páginas do usuário. Para evitar esses problemas, aplique o uso de um layout de página e especifique uma versão de layout de página. Isso garante que todas as definições de conteúdo com base no seu JavaScript sejam imutáveis. Mesmo que você não pretenda habilitar o JavaScript, ainda precisará especificar a versão de layout da página para suas páginas.

O exemplo a seguir mostra o **DataUri** da `selfasserted` versão `1.2.0` :

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrando para o layout da página

O formato do valor deve conter a palavra `contract` : _urn: com: Microsoft: AAD: B2C: elements:page-Name: Version_. Para especificar um layout de página em suas políticas personalizadas que usam um valor antigo de **DataUri** , use a tabela a seguir para migrar para o novo formato.

| Valor antigo de DataUri | Novo valor de DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |

O exemplo a seguir mostra os identificadores de definição de conteúdo e o **DataUri** correspondente com o contrato de página: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

### <a name="metadata"></a>Metadados

Um elemento **Metadata** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados relacionados à definição de conteúdo. |

O elemento **Item** do elemento **Metadata** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Chave | Sim | A chave de metadados.  |

#### <a name="metadata-keys"></a>Chaves de metadados

A definição de conteúdo dá suporte aos seguintes itens de metadados:

| Chave | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DisplayName | Não | Uma cadeia de caracteres que contém o nome da definição de conteúdo. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

O elemento **LocalizedResourcesReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Uma lista de referências de recurso localizado para a definição de conteúdo. |

O elemento **LocalizedResourcesReference** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Idioma | Sim | Uma cadeia de caracteres que contém uma linguagem com suporte para a política de acordo com a RFC 5646 – Marcas para identificar idiomas. |
| LocalizedResourcesReferenceId | Sim | O identificador do elemento **LocalizedResources**. |

O exemplo a seguir mostra uma definição de conteúdo de entrada ou inscrição com uma referência à localização para inglês, francês e espanhol:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Para saber como adicionar suporte de localização a definições de conteúdo, veja [Localização](localization.md).

## <a name="content-definition-ids"></a>IDs da definição de conteúdo

O atributo de ID do elemento **ContentDefinition** especifica o tipo de página relacionada à definição de conteúdo. O elemento define o contexto que um modelo personalizado HTML5/CSS aplicará. A tabela a seguir descreve o conjunto de IDs de definição de conteúdo reconhecidas pelo Identity Experience Framework e os tipos de página relacionados a elas. Você pode criar suas próprias definições de conteúdo com uma ID arbitrária.

| ID | Modelo padrão | Descrição |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro** – exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção do provedor de identidade** – lista provedores de identidade entre os quais os usuários podem escolher durante a entrada. Normalmente, as opções são provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de provedor de identidade para inscrição** – lista provedores de identidade entre os quais os usuários podem escolher durante a inscrição. Normalmente, as opções são provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. |
| **api.localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página Esqueci a senha** – exibe um formulário que os usuários precisam preencher para iniciar uma redefinição de senha. |
| **api.localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de entrada em conta local** – exibe um formulário para entrar com uma conta local com base em um endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| **api.localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta local** – exibe um formulário para se inscrever para uma conta local com base em um endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada, por exemplo: caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator** – verifica os números de telefone usando texto ou voz durante a inscrição ou a entrada. |
| **api.selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição com conta social** – exibe um formulário que os usuários devem preencher ao se inscreverem usando uma conta existente de um provedor de identidade social. Esta página é semelhante à página de inscrição de conta social anterior, exceto pelos campos de entrada de senha. |
| **api.selfasserted.profileupdate** | [updateProfile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil** – exibe um formulário que os usuários podem acessar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto pelos campos de entrada de senha. |
| **api.signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada** – trata do processo de inscrição e entrada do usuário. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. |

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo de personalização da interface do usuário usando definições de conteúdo, consulte:

[Personalizar a interface do usuário do seu aplicativo usando uma política personalizada](customize-ui-with-html.md)
