---
title: ClaimsSchema – Azure Active Directory B2C | Microsoft Docs
description: Especifica o elemento ClaimsSchema de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ff43408cfa6d95dbd5a235a950269c47d57a416
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654023"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsSchema** define os tipos de declaração que podem ser referenciados como parte da política. O esquema de declarações é o lugar em que você declara suas declarações. Uma declaração pode ser um primeiro nome, sobrenome, nome de exibição, número de telefone e muito mais. O elemento ClaimsSchema contém uma lista de elementos **ClaimType**. O elemento **ClaimType** contém o atributo **Id**, que é o nome da declaração.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

O elemento **ClaimType** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador que é usado para o tipo de declaração. Outros elementos podem usar esse identificador na política. |

O elemento **ClaimType** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | O título que é exibido aos usuários em várias telas. O valor pode ser [localizado](localization.md). |
| Tipo de dados | 1:1 | O tipo da declaração. |
| DefaultPartnerClaimTypes | 0:1 | Os tipos de declaração padrão do parceiro a serem usados para um protocolo especificado. O valor pode ser substituído no **PartnerClaimType** especificado nos elementos **InputClaim** ou **OutputClaim**. Use esse elemento para especificar o nome padrão de um protocolo.  |
| Mask | 0:1 | Uma cadeia de caracteres opcional de caracteres de mascaramento que podem ser aplicados ao exibir a declaração. Por exemplo, o número de telefone 324-232-4343 pode ser mascarado como XXX-XXX-4343. |
| UserHelpText | 0:1 | Uma descrição do tipo de declaração que pode ser útil para os usuários entenderem sua finalidade. O valor pode ser [localizado](localization.md). |
| UserInputType | 0:1 | O tipo de controle de entrada que deve estar disponível para o usuário ao inserir manualmente os dados da declaração para o tipo de declaração. Confira os tipos de entrada do usuário definidos nesta página. |
| AdminHelpText | 0:1 | Uma descrição do tipo de declaração que pode ser útil para os administradores entenderem sua finalidade. |
| Restrição | 0:1 | As restrições de valor para essa declaração, como uma expressão regular (Regex) ou uma lista de valores aceitáveis. O valor pode ser [localizado](localization.md). |
PredicateValidationReference| 0:1 | Uma referência a um elemento **PredicateValidationsInput**. Os elementos **PredicateValidationReference** permitem que você execute um processo de validação para garantir que apenas dados formados corretamente sejam inseridos. Para obter mais informações, confira [Predicados](predicates.md). |



### <a name="datatype"></a>Tipo de dados

O elemento **DataType** dá suporte aos seguintes valores:

| Type | Descrição |
| ------- | ----------- |
|booleano|Representa um valor booliano (`true` ou `false`).|
|date| Representa um instante no tempo, normalmente expresso como uma data de um dia. O valor da data segue a Convenção ISO 8601.|
|dateTime|Representa um momento no tempo, geralmente expresso como uma data e hora do dia. O valor da data segue a Convenção ISO 8601.|
|duration|Representa um intervalo de tempo em anos, meses, dias, horas, minutos e segundos. O formato de é `PnYnMnDTnHnMnS` , onde `P` indica positivo ou `N` para um valor negativo. `nY` é o número de anos seguido por um literal `Y` . `nMo` é o número de meses seguido por um literal `Mo` . `nD` é o número de dias seguido por um literal `D` . Exemplos: `P21Y` representa 21 anos. `P1Y2Mo` representa um ano e dois meses. `P1Y2Mo5D` representa um ano, dois meses e cinco dias.  `P1Y2M5DT8H5M620S` representa um ano, dois meses, cinco dias, oito horas, cinco minutos e vinte segundos.  |
|phoneNumber|Representa um número de telefone. |
|INT| Representa o número entre-2.147.483.648 e 2.147.483.647|
|long| Representa o número entre-9.223.372.036.854.775.808 e 9.223.372.036.854.775.807 |
|string| Representa o texto como uma sequência de unidades de código UTF-16.|
|stringCollection|Representa uma coleção de `string`.|
|userIdentity| Representa uma identidade de usuário.|
|useridentitycollection|Representa uma coleção de `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

O **DefaultPartnerClaimTypes** pode conter o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Protocolo | 1:n | Lista de protocolos com seu nome de tipo de declaração de parceiro padrão. |

O elemento **Protocol** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido com suporte do Azure AD B2C. Os valores possíveis são: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Sim | O nome do tipo de declaração a ser usado. |

No exemplo a seguir, quando o Identity Experience Framework interage com um provedor de identidade SAML2 ou com o aplicativo de terceira parte confiável, a declaração **surname** é mapeada para `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, com OpenIdConnect e OAuth2, a declaração é mapeada para `family_name`.

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Como resultado, o token JWT emitido pelo B2C do Azure AD emite `family_name` ao invés do nome de ClaimType **surname**.

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

O elemento **Mask** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo da máscara de declaração. Valores possíveis: `Simple` ou `Regex`. O valor `Simple` indica que uma máscara de texto simples é aplicada à parte à esquerda de uma declaração de cadeia de caracteres. O valor `Regex` indica que uma expressão regular é aplicada à declaração de cadeia de caracteres como um todo.  Se o valor `Regex` for especificado, um atributo opcional também deverá ser definido com a expressão regular a ser usada. |
| `Regex` | Não | Se **`Type`** for definido como `Regex` , especifique a expressão regular a ser usada.

O exemplo a seguir configura uma declaração **PhoneNumber** com a máscara `Simple`:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

O Identity Experience Framework renderiza o número de telefone enquanto oculta os primeiros seis dígitos:

![Declaração de número de telefone mostrada no navegador com os seis primeiros dígitos mascarados por XS](./media/claimsschema/mask.png)

O exemplo a seguir configura uma declaração **AlternateEmail** com a máscara `Regex`:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

O Identity Experience Framework renderiza apenas a primeira letra de endereço de email e o nome de domínio de email:

![Declaração de email mostrada no navegador com caracteres mascarados por asteriscos](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrição

O elemento **Restriction** pode conter o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| MergeBehavior | Não | O método usado para mesclar valores de enumeração com um ClaimType em uma política pai com o mesmo identificador. Use esse atributo quando substituir uma declaração especificada na política de base. Valores possíveis: `Append`, `Prepend` ou `ReplaceAll`. O valor `Append` é uma coleção de dados que deve ser acrescentada ao final da coleção especificada na política pai. O valor `Prepend` é uma coleção de dados que deve ser adicionada antes da coleção especificada na política pai. O valor `ReplaceAll` é uma coleção de dados especificados na política pai que deve ser ignorada. |

O elemento **Restriction** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Enumeração | 1:n | As opções disponíveis na interface do usuário para o usuário selecionar para uma declaração, como um valor em uma lista suspensa. |
| Padrão | 1:1 | A expressão regular a ser usada. |

#### <a name="enumeration"></a>Enumeração

O elemento **Enumeration** define as opções disponíveis para o usuário selecionar para uma declaração na interface do usuário, como um valor em um `CheckboxMultiSelect` , `DropdownSingleSelect` ou `RadioSingleSelect` . Como alternativa, você pode definir e localizar as opções disponíveis com o elemento [LocalizedCollections](localization.md#localizedcollections) . Para pesquisar um item de uma coleção de **Enumeração** de declaração, use [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) Claims Transformation.

O elemento **Enumeration** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Texto | Sim | A cadeia de caracteres de exibição que é mostrada ao usuário na interface do usuário para essa opção. |
|Valor | Sim | O valor da declaração associada à seleção dessa opção. |
| SelectByDefault | Não | Indica se esta opção deve ser selecionada ou não por padrão na interface do usuário. Os valores possíveis: True ou False. |

O exemplo a seguir configura uma declaração de lista suspensa **city** com um valor padrão definido como `New York`:

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Lista suspensa de cidades com um valor padrão definido como Nova York:

![Controle suspenso renderizado no navegador e mostrando o valor padrão](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Padrão

O elemento **Pattern** pode conter os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| RegularExpression | Sim | A expressão regular a que declarações desse tipo devem corresponder para que sejam válidas. |
| HelpText | Não | Uma mensagem de erro para os usuários se a verificação de expressão regular falhar. |

O exemplo a seguir configura uma declaração **email** com o texto de ajuda e a validação de entrada de expressão regular:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

O Identity Experience Framework renderiza a declaração de endereço de email com a validação de entrada de formato de email:

![Caixa de texto mostrando a mensagem de erro disparada pela restrição Regex](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

O Azure AD B2C dá suporte a uma variedade de tipos de entrada do usuário, como uma lista suspensa, uma caixa de texto e uma senha, que podem ser usados ao inserir manualmente dados da declaração para o tipo de declaração. Você deve especificar o **Userinputtype** ao coletar informações do usuário usando um [perfil técnico autodeclarado](self-asserted-technical-profile.md) e [controles de exibição](display-controls.md).

Os tipos de entrada do usuário do elemento **Userinputtype** estão disponíveis:

| UserInputType | ClaimType com suporte | Descrição |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Caixa suspensa seleção múltipla. O valor da declaração é representado em uma cadeia de caracteres delimitadores de vírgula dos valores selecionados. |
|DateTimeDropdown | `date`, `dateTime` |Menus suspensos para selecionar um dia, mês e ano. |
|DropdownSingleSelect |`string` |Caixa suspensa seleção única. O valor da declaração é o valor selecionado.|
|EmailBox | `string` |Campo de entrada de email. |
|Paragraph | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Um campo que mostra texto somente em uma marca de parágrafo. |
|Senha | `string` |Caixa de texto de senha.|
|RadioSingleSelect |`string` | Coleção de botões de opção. O valor da declaração é o valor selecionado.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Caixa de texto somente leitura. |
|TextBox |`boolean`, `int`, `string` |Caixa de texto de linha única. |


#### <a name="textbox"></a>TextBox

O tipo de entrada do usuário **TextBox** é usado para fornecer uma caixa de texto de linha única.

![Caixa de texto mostrando as propriedades especificadas no tipo de declaração](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

O tipo de entrada do usuário **EmailBox** é usado para fornecer um campo de entrada de email básico.

![EmailBox mostrando as propriedades especificadas no tipo de declaração](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Senha

O tipo de entrada do usuário **Password** é usado para registrar uma senha inserida pelo usuário.

![Usando o tipo de declaração com password](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

O tipo de entrada do usuário **DateTimeDropdown** é usado para fornecer um conjunto de menus suspensos para selecionar um dia, mês e ano. Você pode usar os elementos Predicates e PredicateValidations para controlar os valores de data mínimo e máximo. Para obter mais informações, confira a seção **Configurar um intervalo de datas** de [Predicates e PredicateValidations](predicates.md).

![Usando o tipo de declaração com datetimedropdown](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

O tipo de entrada do usuário **RadioSingleSelect** é usado para fornecer uma coleção de botões de opção que permitem ao usuário selecionar uma opção.

![Usando o tipo de declaração com radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

O tipo de entrada do usuário **DropdownSingleSelect** é usado para fornecer uma caixa suspensa que permite ao usuário selecionar uma opção.

![Usando o tipo de declaração com dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

O tipo de entrada do usuário **CheckboxMultiSelect** é usado para fornecer uma coleção de caixas de seleção que permitem ao usuário selecionar várias opções.

![Usando o tipo de declaração com checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

O tipo de entrada do usuário **Readonly** é usado para fornecer um campo somente leitura para exibir a declaração e o valor.

![Usando o tipo de declaração com readonly](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Paragraph

O tipo de entrada do usuário **Paragraph** é usado para fornecer um campo que mostra texto apenas em uma marca de parágrafo.  Por exemplo, &lt;p&gt;texto&lt;/p&gt;. Um  tipo `OutputClaim` de entrada de usuário de parágrafo do perfil técnico autodeclarado, deve definir o `Required` atributo `false` (padrão).

![Usando o tipo de declaração com paragraph](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
