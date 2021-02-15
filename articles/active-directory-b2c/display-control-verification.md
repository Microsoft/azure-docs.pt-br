---
title: Verificar declarações com controles de exibição
titleSuffix: Azure AD B2C
description: Saiba como usar Azure AD B2C controles de exibição para verificar as declarações nos percursos do usuário fornecidos por suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd90be6d93dc5ca399ac87daba0ca44fa7e88ff8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532487"
---
# <a name="verification-display-control"></a>Controle de exibição de verificação

Use um [controle de exibição](display-controls.md) de verificação para verificar uma declaração, por exemplo, um endereço de email ou número de telefone, com um código de verificação enviado ao usuário.

## <a name="verificationcontrol-actions"></a>Ações de VerificationControl

O controle de exibição de verificação consiste em duas etapas (ações):

1. Solicite um destino do usuário, como um endereço de email ou número de telefone, ao qual o código de verificação deve ser enviado. Quando o usuário seleciona o botão **enviar código** , a **ação SendCode** do controle de exibição de verificação é executada. A **ação SendCode** gera um código, constrói o conteúdo a ser enviado e o envia para o usuário. O valor do endereço pode ser preenchido previamente e servir como uma autenticação de dois fatores.

    ![Página de exemplo para enviar ação de código](media/display-control-verification/display-control-verification-email-action-01.png)

1. Depois que o código tiver sido enviado, o usuário lê a mensagem, insere o código de verificação no controle fornecido pelo controle de exibição e seleciona **verificar código**. Ao selecionar **verificar código** , a **ação VerifyCode** é executada para verificar o código associado ao endereço. Se o usuário selecionar **Enviar novo código** , a primeira ação será executada novamente.

    ![Página de exemplo para verificar a ação de código](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>Elementos necessários do VerificationControl

O **VerificationControl** deve conter os seguintes elementos:

- O tipo de `DisplayControl` é `VerificationControl` .
- `DisplayClaims`
  - **Enviar para** -uma ou mais declarações especificando para onde enviar o código de verificação. Por exemplo, *email* ou *código do país* e *número de telefone*.
  - **Código de verificação** -a declaração de código de verificação que o usuário fornece depois que o código foi enviado. Essa declaração deve ser definida conforme necessário e `ControlClaimType` deve ser definida como `VerificationCode` .
- Declaração de saída (opcional) a ser retornada para a página autodeclarada após o usuário concluir o processo de verificação. Por exemplo, *email* ou *código do país* e *número de telefone*. O perfil técnico autodeclarado usa as declarações para persistir os dados ou emergir as declarações de saída para a próxima etapa de orquestração.
- Dois `Action` s com os seguintes nomes:
  - **SendCode** – envia um código para o usuário. Essa ação geralmente contém dois perfis técnicos de validação, para gerar um código e enviá-lo.
  - **VerifyCode** -verifica o código. Essa ação geralmente contém um único perfil técnico de validação.

No exemplo a seguir, uma caixa de texto de **email** é exibida na página. Quando o usuário insere seu endereço de email e seleciona **SendCode** , a ação **SendCode** é disparada no back-end Azure ad B2C.

Em seguida, o usuário insere o **verificationCode** e seleciona **VerifyCode** para disparar a ação **VerifyCode** no back-end. Se todas as validações forem aprovadas, o **VerificationControl** será considerado concluído e o usuário poderá continuar para a próxima etapa.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
