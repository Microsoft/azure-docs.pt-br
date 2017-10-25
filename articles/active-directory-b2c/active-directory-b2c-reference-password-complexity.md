---
title: Complexidade de senha - Azure AD B2C | Microsoft Docs
description: Como configurar os requisitos de complexidade de senha fornecida pelos consumidores no Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: ab5547ef76121aa395168844bd69759613ffc045
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: como configurar os requisitos de complexidade de senhas

O Azure Active Directory B2C (Azure AD B2C) oferece suporte à alteração de requisitos de complexidade para senhas fornecidas por um usuário final ao criar uma conta.  Por padrão, o Azure AD B2C usa senhas `Strong`.  O Azure AD B2C também oferece suporte a opções de configuração para controlar a complexidade de senhas que os clientes podem usar.

## <a name="when-password-rules-are-enforced"></a>Quando as regras de senha são aplicadas

Durante a inscrição ou redefinição de senha, um usuário final deve fornecer uma senha que atenda às regras de complexidade.  Regras de complexidade de senha são impostas por política.  É possível ter uma política que exija uma senha de quatro dígitos, e outra que solicite uma cadeia de oito caracteres durante a inscrição.  Por exemplo, você pode usar uma política de complexidade de senha diferente para adultos e crianças.

A complexidade de senha nunca é aplicada durante a inscrição.  Nunca será solicitado que os usuários mudem sua senha ao entrar por ela não atender aos requisitos de complexidade atual.

Esses estão os tipos de políticas nas quais a complexidade de senha pode ser configurada:

* Política de inscrição ou início de sessão
* Política de redefinição de senha
* Política personalizada ([Como configurar a complexidade de senha na política personalizada](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Como configurar a complexidade de senha

1. Siga estas etapas para [navegar até as configurações do Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Acesse as **Políticas de entrada ou de inscrição**.
1. Selecione uma política e, em seguida, clique em **Editar**.
1. Abra a **Complexidade de senha**.
1. Altere a complexidade de senha desta política para **Simples**, **Forte** ou **Personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Descrição |
| --- | --- |
| Simples | Uma senha que tenha 8 a 64 caracteres. |
| Strong | Uma senha que tenha 8 a 64 caracteres. São necessários de 3 a 4 caracteres minúsculos, maiúsculos, números ou símbolos. |
| Personalizado | Essa opção fornece mais controle sobre as regras de complexidade de senha.  Ela permite configurar um tamanho personalizado.  E permite aceitar somente senhas numéricas (pins). |

## <a name="options-available-under-custom"></a>Opções disponíveis de personalização

### <a name="character-set"></a>Conjunto de caracteres

Permite que você aceite somente dígitos (pins) ou o conjunto completo de caracteres.

* **Somente números** permite somente dígitos (0-9) ao digitar uma senha.
* **Todos** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite que você controle as exigências de comprimento da senha.

* **Comprimento mínimo** deve ser pelo menos 4.
* **Comprimento máximo** deve ser igual ou maior que o mínimo, mas não pode ultrapassar 64 caracteres.

### <a name="character-classes"></a>Classes de caracteres

Permite que você controle os diferentes tipos de caracteres usados na senha.

* **2 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** a senha deve ter, no mínimo, dois tipos de caracteres. Por exemplo, um número e um caractere minúsculo.
* **3 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** a senha deve ter, no mínimo, dois tipos de caracteres. Por exemplo, um número, um caractere minúsculo e um caractere maiúsculos.
* **4 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** a senha deve ter todos os tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do usuário final. Alguns estudos mostraram que esse requisito não melhora a entropia de senha. Confira as [Diretrizes de senha NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
