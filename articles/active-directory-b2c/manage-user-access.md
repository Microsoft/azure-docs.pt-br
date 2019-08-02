---
title: Gerenciar o acesso de usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba como identificar os pequenos, coletar dados de nascimento e de país/região e obter a aceitação dos termos de uso em seu aplicativo usando Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ba36ece6b221908bfbaae58430a52b4753c2ed6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846767"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gerenciar o acesso do usuário no Azure Active Directory B2C

Este artigo descreve como gerenciar o acesso do usuário aos seus aplicativos usando o B2C do Azure Active Directory (Azure AD). O gerenciamento de acesso no seu aplicativo inclui:

- Identificação de menores e controle de acesso de usuário para usar o aplicativo.
- Exigência do consentimento dos pais para que menores usem seus aplicativos.
- Coletando dados de nascimento e de país/região dos usuários.
- Capturando um contrato de termos de uso e restrição de acesso.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Controle de acesso de menor

Os aplicativos e as organizações podem optar por impedir que menores usem aplicativos e serviços que não sejam direcionados para essa audiência. Como alternativa, os aplicativos e as organizações podem optar por aceitar o uso por menores, gerenciar o consentimento dos pais e entregar experiências permitidas para menores, conforme a determinação das regras de negócios e o permitido pela lei.

Se um usuário for identificado como menor, você pode configurar o fluxo de usuário no Azure AD B2C como uma de três opções:

- **Enviar um id_token JWT assinado para o aplicativo**: o usuário está registrado no diretório e um token é retornado para o aplicativo. Em seguida, o aplicativo continua aplicando regras de negócio. Por exemplo, o aplicativo pode continuar com um processo de consentimento dos pais. Para usar este método, escolha receber as declarações **ageGroup** e **consentProvidedForMinor** do aplicativo.

- **Enviar um token JSON não assinado para o aplicativo**: o Azure AD B2C notifica o aplicativo de que o usuário é menor e fornece o status de consentimento parental do usuário. Em seguida, o aplicativo continua aplicando regras de negócio. Um token JSON não conclui uma autenticação bem-sucedida com o aplicativo. O aplicativo precisa processar o usuário não autenticado de acordo com as declarações incluídas no token JSON, que podem incluir **nome**, **email**, **ageGroup** e **consentProvidedForMinor**.

- **Bloquear o usuário**: Se um usuário for um secundário e o consentimento dos pais não tiver sido fornecido, Azure AD B2C poderá notificar o usuário de que eles estão bloqueados. Nenhum token será emitido, o acesso será bloqueado e a conta de usuário não será criada durante um percurso de registro. Para implementar esta notificação, você fornece uma página de conteúdo HTML/CSS adequada para informar o usuário e apresentar as opções apropriadas. Nenhuma ação adicional do aplicativo é necessária para novos registros.

## <a name="get-parental-consent"></a>Obter o consentimento dos pais

Dependendo do regulamento do aplicativo, pode ser necessário que o consentimento dos pais seja concedido por um usuário verificado como adulto. O Azure AD B2C não oferece uma experiência para verificar a idade da pessoa e permitir que um adulto verificado dê o consentimento dos pais a um menor. Essa experiência precisa ser fornecida pelo aplicativo ou por outro provedor de serviços.

Este é um exemplo de um fluxo de usuário para a coleta de consentimento dos pais:

1. Uma operação da [API do Graph do Azure Active Directory](/previous-versions/azure/ad/graph/api/api-catalog) identifica o usuário como menor e retorna os dados do usuário ao aplicativo na forma de um token JSON não assinado.

2. O aplicativo processa o token JSON e mostra uma tela para o secundário, notificando-os de que o consentimento dos pais é necessário e solicitando o consentimento de um pai online.

3. O Azure AD B2C mostra uma jornada de logon na qual o usuário pode entrar normalmente e emite um token para o aplicativo que está definido para incluir **legalAgeGroupClassification = “minorWithParentalConsent”** . O aplicativo coleta o endereço de email do pai e verifica se o pai é um adulto. Para fazer isso, ele usa uma fonte confiável, como um escritório de identificação nacional, verificação de licença ou comprovante de cartão de crédito. Se a verificação for bem sucedida, o aplicativo solicita que o menor entre usando o fluxo de usuário do Azure AD B2C. Se a autorização for negada (por exemplo, **legalAgeGroupClassification = “minorWithoutParentalConsent”** ), o Azure AD B2C retornará um token JSON (não um logon) ao aplicativo para reiniciar o processo de consentimento. Opcionalmente, é possível personalizar o fluxo de usuário em que um menor ou um adulto pode recuperar o acesso à conta do menor enviando um código de registro para o endereço de email do menor ou para o endereço de email do adulto no registro.

4. O aplicativo oferece uma opção para o menor revogar o consentimento.

5. Quando tanto o menor ou o adulto revoga o consentimento, a API do Graph do Azure AD pode ser usada para alterar **consentProvidedForMinor** para **negado**. Como alternativa, o aplicativo pode optar por excluir um menor cujo consentimento foi revogado. Opcionalmente, é possível personalizar o fluxo de usuário para que o menor autenticado (ou o pai que estiver usando a conta do menor) possa revogar o consentimento. O Azure AD B2C registra **consentProvidedForMinor** como **negado**.

Para obter mais informações sobre o **legalAgeGroupClassification**, o **consentProvidedForMinor** e o **ageGroup**, confira [typo de recurso de Usuário](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Para obter mais informações sobre atributos personalizados, confira [Usar atributos personalizados para coletar informações sobre seus consumidores](active-directory-b2c-reference-custom-attr.md). Quando você abordar atributos estendidos usando a API do Graph do Azure AD, você deve usar a versão longa do atributo precisa ser usada, como *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Coletar dados de nascimento e país/região

Os aplicativos podem contar com Azure AD B2C para reunir a data de nascimento (DOB) e informações de país/região de todos os usuários durante o registro. Se essa informação ainda não existir, o aplicativo poderá solicitá-la ao usuário durante a próxima jornada de autenticação (entrada). Os usuários não podem continuar sem fornecer suas informações de DOB e país/região. Azure AD B2C usa as informações para determinar se o indivíduo é considerado um secundário de acordo com os padrões regulatórios desse país/região.

Um fluxo de usuário personalizado pode coletar informações de DOB e país/região e usar Azure AD B2C transformação de declarações para determinar o **ageGroup** e persistir o resultado (ou manter as informações de DOB e país/região diretamente) no diretório.

As etapas a seguir mostram a lógica que é usada para calcular o **ageGroup** usando a data de nascimento do usuário:

1. Tente localizar o país pelo código do país na lista. Se o país não for localizado, retorne ao **Padrão**.

2. Se o nó **MinorConsent** estiver presente no elemento de país:

    a. Calcule a data em que o usuário deve ter nascido para ser considerado um adulto. Por exemplo, se a data atual for 14 de março de 2015 e **MinorConsent** for 18, a data de nascimento deverá ser o mais tardar em 14 de março de 2000.

    b. Compare a data de nascimento mínima com a data de nascimento real. Se a data de nascimento mínima for antes da data de nascimento do usuário, o cálculo retornará **Menor** como o cálculo de faixa etária.

3. Se o nó **MinorNoConsentRequired** estiver presente no elemento de país, repita as etapas 2a e 2b usando o valor de **MinorNoConsentRequired**. O resultado de 2b retornará **MinorNoConsentRequired** se a data de nascimento mínima for antes da data de nascimento do usuário.

4. Se nenhum dos cálculos retornar true, o cálculo retornará **Adulto**.

Se um aplicativo tiver coletado de forma confiável DOB ou dados de país/região por outros métodos, o aplicativo poderá usar o API do Graph para atualizar o registro de usuário com essas informações. Por exemplo:

- Se um usuário já tiver sido determinado como adulto, atualize o atributo de diretório **ageGroup** com o valor **Adulto**.
- Se um usuário já tiver sido determinado como menor, atualize o atributo de diretório **ageGroup** com o valor **Menor** e defina **consentProvidedForMinor** conforme o necessário.

Para obter mais informações sobre a coleta de dados de DOB, confira [Use a restrição de idade no Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Capturar contrato de termos de uso

Ao desenvolver seu aplicativo, normalmente você captura a aceitação de termos de uso do usuário em seus aplicativos, com uma participação nula ou mínima do diretório de usuários. No entanto, é possível usar um fluxo de usuário do Azure AD B2C para reunir a aceitação de usuário dos termos de uso, restringir o acesso se aceitação não for garantida e impor a aceitação das alterações futuras nos termos de uso com base na data da última aceitação e na data da versão mais recente dos termos de uso.

Os **Termos de Uso** também podem incluir "Consentimento para compartilhar dados com terceiros". Dependendo das regulamentações e regras de negócios locais, você pode reunir a aceitação de ambas as condições pelo usuário, ou pode permitir que o usuário aceite uma condição e não a outra.

As etapas a seguir descrevem como você pode gerenciar os termos de uso:

1. Registrar a aceitação dos termos de uso e a data da aceitação do uso da API do Graph e de seus atributos estendidos. Você pode fazer isso usando os dois fluxos de usuário, interno e personalizado. Nós recomendamos que você crie e use os atributos **extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion**.

2. Criar uma caixa de seleção necessária rotulada "Aceitar termos de uso" e gravar o resultado durante a inscrição. Você pode fazer isso usando os dois fluxos de usuário, interno e personalizado.

3. O Azure AD B2C armazena o contrato de termos de uso e a aceitação de usuário. Você pode usar A API do Graph para consultar o status de qualquer usuário lendo o atributo de extensão usado para registrar a resposta (por exemplo, lendo **termsOfUseTestUpdateDateTime**). Você pode fazer isso usando os dois fluxos de usuário, interno e personalizado.

4. Exigir a aceitação dos termos de uso atualizados comparando a data da aceitação com a data da última versão dos termos de uso. Você pode comparar as datas usando um fluxo de usuário personalizado. Use o atributo estendido **extension_termsOfUseConsentDateTime** e compare o valor com a afirmação de **termsOfUseTextUpdateDateTime**. Se a aceitação for velha, force uma nova aceitação exibindo uma tela autoafirmativa. Do contrário, bloqueie acesso usando lógica de política.

5. Exigir a aceitação dos termos de uso do atualizados comparando o número de versão da aceitação com número de versão mais recente aceito. Você pode comparar os números de versão usando um fluxo de usuário personalizado. Use o atributo estendido **extension_termsOfUseConsentDateTime** e compare o valor com a afirmação de **extension_termsOfUseConsentVersion**. Se a aceitação for velha, force uma nova aceitação exibindo uma tela autoafirmativa. Do contrário, bloqueie acesso usando lógica de política.

Você pode capturar os termos de uso de aceitação nos seguintes cenários:

- Um novo usuário está se inscrevendo. Os termos de uso são exibidos, e o resultado de aceitação é armazenado.
- Um usuário está fazendo login e aceitou os termos de uso mais recentes ou ativos. Os termos de uso não são exibidos.
- Um usuário está entrando que ainda não aceitou os termos de uso mais recentes ou ativos. Os termos de uso são exibidos, e o resultado de aceitação é armazenado.
- Um usuário está fazendo login e já aceitou uma versão mais antiga dos termos de uso, que agora são atualizados para a versão mais recente. Os termos de uso são exibidos, e o resultado de aceitação é armazenado.

A imagem a seguir mostra o fluxo de usuário recomendado:

![Diagrama do fluxograma de fluxo mostrando o fluxo de usuário de aceitação recomendado](./media/manage-user-access/user-flow.png)

Este é um exemplo de consentimento para termos de uso baseado em DateTime em uma declaração:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Este é um exemplo de consentimento para termos de uso baseado em Versão em uma declaração:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como excluir e exportar dados do usuário em [Gerenciar dados de usuário](manage-user-data.md).
