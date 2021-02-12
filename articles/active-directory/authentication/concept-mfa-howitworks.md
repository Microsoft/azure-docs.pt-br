---
title: Visão geral da autenticação multifator do Azure AD
description: Saiba como a autenticação multifator do Azure AD ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda do usuário por um processo de entrada simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744047"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Como funciona: autenticação multifator do Azure AD

A autenticação multifator é um processo em que um usuário é solicitado durante o processo de conexão para obter uma forma adicional de identificação, como inserir um código no celular ou fornecer uma verificação de impressão digital.

Se você usar apenas uma senha para autenticar um usuário, isso deixará um vetor inseguro para ataque. Se a senha for fraca ou se tiver sido exposta em outro lugar, será realmente o usuário entrando com o nome de usuário e a senha ou um invasor? Quando você precisar de uma segunda forma de autenticação, a segurança será aprimorada, pois esse fator adicional não será algo fácil de ser obtido ou duplicado por um invasor.

![Imagem conceitual das diferentes formas de autenticação multifator](./media/concept-mfa-howitworks/methods.png)

A Autenticação Multifator do Azure AD funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe, normalmente, uma senha.
* Algo que você tem, como um dispositivo confiável que não seja facilmente duplicado, como um telefone ou uma chave de hardware.
* Algo que você é: uma biometria, como uma impressão digital ou uma verificação facial.

Os usuários podem se registrar na redefinição de senha por autoatendimento e na Autenticação Multifator do Azure AD em uma etapa para simplificar a experiência de integração. Os administradores podem definir quais formas de autenticação secundária podem ser usadas. A Autenticação Multifator do Azure AD também pode ser necessária quando os usuários executam uma redefinição de senha por autoatendimento para proteger ainda mais esse processo.

![Métodos de autenticação em uso na tela de login](media/concept-authentication-methods/overview-login.png)

A autenticação multifator do Azure AD ajuda a proteger o acesso a dados e aplicativos, mantendo a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de [métodos de autenticação](concept-authentication-methods.md) fáceis de usar. Os usuários podem ou não ser desafiados para MFA com base em decisões de configuração tomadas por um administrador.

Seus aplicativos ou serviços não precisam fazer nenhuma alteração para usar a autenticação multifator do Azure AD. Os prompts de verificação fazem parte do evento de entrada do Azure AD, que solicita e processa automaticamente o desafio MFA quando necessário.

## <a name="available-verification-methods"></a>Métodos de verificação disponíveis

Quando um usuário entra em um aplicativo ou serviço e recebe um prompt do MFA, ele pode escolher um de seus formulários registrados de verificação adicional. Um administrador pode exigir o registro desses métodos de verificação da autenticação multifator do Azure AD ou o usuário pode acessar seu próprio [meu perfil](https://myprofile.microsoft.com) para editar ou adicionar métodos de verificação.

As seguintes formas adicionais de verificação podem ser usadas com a autenticação multifator do Azure AD:

* Aplicativo Microsoft Authenticator
* Token OATH de hardware
* SMS
* Chamada de voz

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Como habilitar e usar a autenticação multifator do Azure AD

Os usuários e grupos podem ser habilitados para autenticação multifator do Azure AD para solicitar verificação adicional durante o evento de entrada. Os [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) estão disponíveis para todos os locatários do Azure ad para habilitar rapidamente o uso do aplicativo Microsoft Authenticator para todos os usuários.

Para controles mais granulares, as políticas de [acesso condicional](../conditional-access/overview.md) podem ser usadas para definir eventos ou aplicativos que exigem MFA. Essas políticas podem permitir eventos de entrada regulares quando o usuário está na rede corporativa ou em um dispositivo registrado, mas solicita fatores de verificação adicionais quando remoto ou em um dispositivo pessoal.

![Diagrama de visão geral de como o Acesso Condicional funciona para proteger o processo de entrada](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre licenciamento, consulte [recursos e licenças para a autenticação multifator do Azure ad](concept-mfa-licensing.md).

Para ver a MFA em ação, habilite a autenticação multifator do Azure AD para um conjunto de usuários de teste no seguinte tutorial:

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Azure AD](./tutorial-enable-azure-mfa.md)