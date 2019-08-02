---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft Docs
description: Especifica o elemento TrustFrameworkPolicy de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 904893d4881de6be2c9055fefa9a8267cb045afd
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849418"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem os elementos da política, como o esquema de declarações, as transformações de declarações, as definições de conteúdo, os provedores de declarações, os perfis técnicos, o percurso do usuário e as etapas de orquestração. Cada arquivo de política é definido dentro de elemento **TrustFrameworkPolicy** de nível superior de um arquivo de política.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


O elemento **TrustFrameworkPolicy** contém os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sim | A versão do esquema a ser usado para executar a política. O valor deve ser `0.3.0.0` |
| TenantObjectId | Não | O identificador de objeto exclusivo do locatário do Azure AD (Azure Active Directory) B2C. |
| TenantId | Sim | O identificador exclusivo do locatário ao qual essa política pertence. |
| `PolicyId` | Sim | O identificador exclusivo da política. Esse identificador deve ter o *B2C_1A_* como prefixo |
| PublicPolicyUri | Sim | O URI para a política, o que é uma combinação de ID do locatário e ID da política. |
| DeploymentMode | Não | Valores possíveis: `Production`, `Debugging` ou `Development`. O padrão é `Production`. Use essa propriedade para depurar sua política. Para obter mais informações, veja [Coleta de Logs](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Não | O ponto de extremidade usado quando **DeploymentMode** é definido como `Development`. O valor deve ser `urn:journeyrecorder:applicationinsights`. Para obter mais informações, veja [Coleta de Logs](active-directory-b2c-troubleshoot-custom.md). |


O exemplo a seguir mostra como especificar o elemento **TrustFrameworkPolicy**:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Modelo de herança

Esses tipos de arquivos de política normalmente são usados em um percurso do usuário:

- Um arquivo de **Base** que contém a maioria das definições. Para ajudar na solução de problemas e na manutenção de longo prazo das suas políticas, é recomendável que você fazer um número mínimo de alterações nesse arquivo.
- Um arquivo de **Extensões** que contém as alterações de configuração exclusivas para seu locatário. Esse arquivo de política é derivado do arquivo de Base. Use esse arquivo para adicionar novas funcionalidades ou substituir a funcionalidade existente. Por exemplo, use esse arquivo para federar com novos provedores de identidade.
- Um arquivo **RP (Terceira Parte Confiável)** que é o único arquivo centrado em tarefa invocado diretamente pelo aplicativo de terceira parte confiável, como seus aplicativos Web, móveis ou da área de trabalho. Cada tarefa exclusiva, como inscrição ou entrada, redefinição de senha ou edição de perfil, requer o próprio arquivo de política de RP. Este arquivo de política é derivado do arquivo de Extensões.

Um aplicativo de terceira parte confiável chama o arquivo de política de RP para executar uma tarefa específica. Por exemplo, para iniciar o fluxo de entrada. A Estrutura de Experiência de Identidade no Azure AD B2C adiciona todos os elementos primeiro do arquivo Base, então do arquivo de Extensões e, por fim, do arquivo de política de RP para montar a política atual em vigor. Elementos do mesmo tipo e nome no arquivo RP substituem aqueles nas Extensões, e as Extensões substituem a Base. O diagrama a seguir mostra a relação entre os arquivos de política e os aplicativos de terceira parte confiável.

![Diagrama mostrando o modelo de herança de política de estrutura confiável](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

O modelo de herança é assim:

- A política pai e a política filho são do mesmo esquema.
- A política filho em qualquer nível pode herdar de política pai e estendê-la adicionando novos elementos.
- Não há nenhum limite ao número de níveis.

Para obter mais informações, consulte [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Política de base

Para herdar de uma política de outra política, um elemento **BasePolicy** deve ser declarado no elemento **TrustFrameworkPolicy** do arquivo de política. O elemento **BasePolicy** é uma referência à política de base da qual essa política é derivada.

O elemento **BasePolicy** contém os seguintes elementos:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do locatário do Azure AD B2C. |
| `PolicyId` | 1:1 | O identificador da política pai. |


O exemplo a seguir mostra como especificar uma política de base. Essa política **B2C_1A_TrustFrameworkExtensions** é derivada da política **B2C_1A_TrustFrameworkBase**.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Execução de política

Um aplicativo de terceira parte confiável, como um aplicativo Web, móvel ou da área de trabalho, chama a [política de RP (terceira parte confiável)](relyingparty.md). O arquivo de política de RP executa uma tarefa específica, como entrar, redefinir senha ou editar perfil. A política de RP configura a lista de declarações que o aplicativo de terceira parte confiável recebe como parte do token emitido. Vários aplicativos podem usar a mesma política. Todos os aplicativos recebem o mesmo token com declarações e o usuário passa pela mesma jornada do usuário. Um único aplicativo pode usar várias políticas.

Dentro do arquivo de política RP, você deve especificar o elemento **DefaultUserJourney**, que aponta para [UserJourney](userjourneys.md). O percurso do usuário geralmente é definido na política de Base ou de Extensões.

Política B2C_1A_signup_signin:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase ou B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Um percurso do usuário define a lógica de negócios pela qual um usuário passa. Cada percurso do usuário é um conjunto de etapas de orquestração que executa uma série de ações em sequência em termos de coleta de informações e autenticação.

O arquivo de política **SocialAndLocalAccounts** no [starter pack](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) contém os percursos do usuário SignUpOrSignIn, ProfileEdit, PasswordReset. Você pode adicionar mais viagens de usuário para outros cenários, como alterar um endereço de email ou vincular e desvincular uma conta social.

As etapas de orquestração podem chamar um [Perfil Técnico](technicalprofiles.md). Um perfil técnico fornece uma estrutura com um mecanismo interno para se comunicar com diferentes tipos de partes. Por exemplo, um perfil técnico pode executar estas ações, entre outras:

- Renderize uma experiência de usuário.
- Permitir que os usuários entrem usando uma conta social ou empresarial, como Facebook, conta Microsoft, Google, Salesforce ou qualquer outro provedor de identidade.
- Configure a verificação de telefone para MFA.
- Leia e grave dados de e para um repositório de identidades do Azure AD B2C.
- Chame um serviço de API Restful personalizado.

![Diagrama mostrando o fluxo de execução da política](./media/trustframeworkpolicy/custom-policy-execution.png)

 O elemento **TrustFrameworkPolicy** contém os seguintes elementos:

- BasePolicy conforme especificada acima
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
