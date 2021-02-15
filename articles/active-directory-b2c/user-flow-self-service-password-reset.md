---
title: Redefinição de senha de autoatendimento no Azure Active Directory B2C | Microsoft Docs
description: Demonstra como configurar a redefinição de senha de autoatendimento para seus clientes no Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481488"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configure a redefinição de senha de autoatendimento para seus clientes

Com o recurso de redefinição de senha de autoatendimento, seus clientes que tenham se registrado com contas locais poderão redefinir as respectivas senhas por conta própria. Isso reduz significativamente a sobrecarga da equipe de suporte, principalmente se milhões de clientes usarem seu aplicativo regularmente. Atualmente, usar um endereço de email verificado é o único método de recuperação com suporte.

> [!NOTE]
> Este artigo se aplica à redefinição de senha de autoatendimento usada no contexto do fluxo de usuário de **entrada** padrão, que usa a **entrada de conta local** como o provedor de identidade. Se você precisar de fluxos de usuários de redefinição de senha totalmente personalizáveis invocados do seu aplicativo, consulte [este artigo](user-flow-overview.md).
>
>

Por padrão, o diretório não tem a redefinição de senha de autoatendimento ativada. Use as etapas a seguir para ativá-la:

1. Entre no [portal do Azure](https://portal.azure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para criar o diretório.
2. Abra o **Azure Active Directory** (na barra de navegação à esquerda).
3. Role para baixo na folha opções e selecione **redefinição de senha**.
4. Configure a **Redefinição de senha de autoatendimento habilitada** como **Tudo**.
5. Clique em **Salvar** na parte superior da página. Pronto!

Para testar, use o recurso "Executar agora" em qualquer fluxo de usuário de entrada que tenha contas locais como um provedor de identidade. Na página de entrada da conta local (na qual você insere um endereço de email e a senha ou um nome de usuário e a senha), clique em **Não consegue acessar sua conta?** para verificar a experiência do cliente.

> [!NOTE]
> As páginas do autoatendimento de redefinição de senha podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/fundamentals/customize-branding.md).
>
>

