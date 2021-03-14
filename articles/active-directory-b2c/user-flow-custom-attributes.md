---
title: Definir atributos personalizados no Azure Active Directory B2C | Microsoft Docs
description: Defina os atributos personalizados para o seu aplicativo no Azure Active Directory B2C para coletar informações sobre seus clientes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607910"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definir atributos personalizados no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

No artigo [adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas](configure-user-input.md) , você aprende a usar os [atributos de perfil do usuário](user-profile-attributes.md)interno. Neste artigo, você habilita um atributo personalizado em seu diretório Azure Active Directory B2C (Azure AD B2C). Posteriormente, você pode usar o novo atributo como uma declaração personalizada em [fluxos de usuário](user-flow-overview.md) ou [políticas personalizadas](custom-policy-get-started.md) simultaneamente.

Seu diretório do Microsoft Azure Active Directory B2C é fornecido com um [conjunto interno de atributos](user-profile-attributes.md). No entanto, geralmente você precisa criar seus próprios atributos para gerenciar seu cenário específico, por exemplo, quando:

* Um aplicativo voltado para o cliente precisa manter um atributo de **fidelidadeid** .
* Um provedor de identidade tem um identificador de usuário exclusivo, **uniqueUserGUID**, que deve ser persistido.
* Uma jornada de usuário personalizado precisa manter o estado do usuário, **migrationStatus**, para que outra lógica opere.

Os termos *propriedade de extensão*, *atributo personalizado* e *declaração personalizada*, se referem à mesma coisa no contexto deste artigo. O nome varia dependendo do contexto, como o aplicativo, objeto ou política.

Azure AD B2C permite que você estenda o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e gravar esses atributos usando a [API do Microsoft Graph](microsoft-graph-operations.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Como criar um atributo personalizado

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Atributos de usuário** e, em seguida, selecione **Adicionar**.
1. Forneça um **Nome** para o atributo personalizado (por exemplo, "ShoeSize")
1. Escolha um **Tipo de Dados**. Somente **Cadeia de Caracteres**, **Booliano** e **Int** estão disponíveis.
1. Opcionalmente, insira uma **Descrição** para fins informativos.
1. Clique em **Criar**.

O atributo personalizado agora está disponível na lista de **atributos de usuário** e para uso em seus fluxos de usuário. Um atributo personalizado só é criado na primeira vez em que é usado em qualquer fluxo de usuário, e não quando você o adiciona à lista de **atributos de usuário**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Usar um atributo personalizado em sua política

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **Atributos de inscrição** e, em seguida, selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Salvar**.
1. Selecione **Declarações de aplicativo** e selecione o atributo personalizado.
1. Clique em **Salvar**.

Depois de criar um novo usuário usando um fluxo de usuário, que usa o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Como alternativa, você pode usar o recurso [executar fluxo de usuário](./tutorial-create-user-flows.md) no fluxo do usuário para verificar a experiência do cliente. Agora você deve ver **ShoeSize** na lista de atributos coletados durante a jornada de inscrição, e vê-lo no token enviado de volta ao seu aplicativo.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Aplicativo de extensões de Azure AD B2C

Atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado `b2c-extensions-app` . Não modifique esse aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar esse aplicativo em Azure AD B2C, registros de aplicativo. Obter as propriedades do aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **registros de aplicativo** e, em seguida, selecione **todos os aplicativos**.
1. Selecione o aplicativo `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copiar os identificadores a seguir para a sua área de transferência e salve-os:
    * **ID do aplicativo**. Exemplo: `11111111-1111-1111-1111-111111111111`.
    * **ID do objeto**. Exemplo: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Modificar sua política personalizada

Para habilitar atributos personalizados em sua política, forneça a **ID do aplicativo** e a ID de **objeto** do aplicativo nos metadados do perfil técnico do AAD-Common. O perfil técnico do *AAD-comum* é encontrado na base [Azure Active Directory](active-directory-technical-profile.md) perfil técnico e fornece suporte para o gerenciamento de usuários do Azure AD. Outros perfis técnicos do Azure AD incluem o AAD-Common para aproveitar sua configuração. Substitua o perfil técnico AAD-Common no arquivo de extensão.

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Localize o elemento ClaimsProviders. Adicione um novo Claimprovider ao elemento ClaimsProviders.
1. Insira a **ID do aplicativo** que você registrou anteriormente, entre os elementos de abertura `<Item Key="ClientId">` e fechamento `</Item>` .
1. Insira o **ObjectID do aplicativo** que você registrou anteriormente, entre os elementos de abertura `<Item Key="ApplicationObjectId">` e fechamento `</Item>` .

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="AAD-Common">
            <Metadata>
              <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
              <Item Key="ClientId"></Item>
              <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
              <Item Key="ApplicationObjectId"></Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles> 
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Carregar sua política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário de Azure ad B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **carregar política personalizada** e, em seguida, carregue os arquivos de política de TrustFrameworkExtensions.xml que você alterou.

> [!NOTE]
> Na primeira vez que o perfil técnico do Azure AD persistir a declaração para o diretório, ele verificará se o atributo personalizado existe. Caso contrário, ele cria o atributo personalizado.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Criar um atributo personalizado por meio de portal do Azure

Os mesmos atributos de extensão são compartilhados entre políticas internas e personalizadas. Quando você adiciona atributos personalizados por meio da experiência do portal, esses atributos são registrados usando o **B2C-Extensions-app** que existe em todos os locatários do B2C.

Você pode criar esses atributos usando a interface do usuário do portal antes ou depois de usá-los em suas políticas personalizadas. Ao criar um atributo **lealdadeid** no portal, você deve consultá-lo da seguinte maneira:

|Name     |Usado em |
|---------|---------|
|`extension_loyaltyId`  | Política personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API do Microsoft Graph](microsoft-graph-operations.md)|

O exemplo a seguir demonstra o uso de atributos personalizados em uma definição de declaração de política personalizada Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

O exemplo a seguir demonstra o uso de um atributo personalizado em Azure AD B2C política personalizada em um perfil técnico, entrada, saída e declarações persistentes.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Usando o atributo personalizado com MS API do Graph

A API do Microsoft Graph dá suporte à criação e à atualização de um usuário com atributos de extensão. Os atributos de extensão no API do Graph são nomeados usando a Convenção `extension_ApplicationClientID_attributename` , em que o `ApplicationClientID` é a ID do aplicativo **(cliente)** do `b2c-extensions-app` aplicativo. Observe que a **ID do aplicativo (cliente)** , como é representada no nome do atributo de extensão, não inclui hifens. Por exemplo:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Próximas etapas

Siga as orientações sobre como [adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas](configure-user-input.md). Este exemplo usa uma declaração interna "City". Para usar um atributo personalizado, substitua ' City ' por seus próprios atributos personalizados.
