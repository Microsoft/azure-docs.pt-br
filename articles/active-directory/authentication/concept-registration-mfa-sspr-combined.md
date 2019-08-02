---
title: Combinado de registro para a autenticação multifator (visualização) – Azure Active Directory e o Azure AD SSPR
description: (Visualização) do registro de redefinição de senha de autoatendimento e de autenticação de multifator do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0e80ec7a378d44000565034ce47d95c78b777e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537008"
---
# <a name="combined-security-information-registration-preview"></a>Registro de informações de segurança combinada (visualização)

Antes do registro combinado, os usuários registraram separadamente os métodos de autenticação para redefinição de senha de autoatendimento (SSPR) e de autenticação multifator do Azure. As pessoas estavam confusos que métodos semelhantes foram usados para a autenticação multifator e SSPR, mas eles tinham que se registrar para os dois recursos. Agora, com o registro combinado, os usuários podem registrar uma vez e obter as vantagens da autenticação multifator e o SSPR.

![Mostrando meu perfil registrado informações de segurança para um usuário](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de habilitar a nova experiência, examine esta documentação voltada para administrador e a documentação e focada no usuário para que entender a funcionalidade e o efeito desse recurso. Basear seu treinamento a [documentação do usuário](../user-help/user-help-security-info-overview.md) para preparar seus usuários para a nova experiência e ajuda a garantir uma distribuição bem-sucedida.

Azure AD combinados registro não está disponível atualmente para nuvens nacionais, como Azure US Government, Azure Alemanha ou Azure China 21Vianet de informações de segurança.

|     |
| --- |
| O registro de informações de segurança combinada para a autenticação multifator e redefinição de senha de autoatendimento do Azure Active Directory (Azure AD) é um recurso de visualização pública do Azure AD. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Os usuários que estão habilitados para a visualização original e a experiência de registro combinado aprimorada verá o novo comportamento. Os usuários que estão habilitados para ambas as experiências verão a nova experiência meu perfil. O novo perfil My se alinha com a aparência do registro combinado e fornece uma experiência perfeita para os usuários. Os usuários podem ver meu perfil acessando [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Minhas páginas de perfil são localizadas de acordo com as configurações de idioma do computador acessando a página. Microsoft armazena a linguagem mais recente usada no cache do navegador, para que tentativas subsequentes para acessar as páginas continue a renderizar no último idioma usado. Se você limpar o cache, as páginas serão renderizadas novamente. Se você quiser forçar um idioma específico, você pode adicionar `?lng=<language>` ao final da URL, onde `<language>` é o código do idioma que você deseja renderizar.

![Configurar o SSPR ou outros métodos de verificação de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponíveis no registro combinado

Combinados registro suporta os seguintes métodos de autenticação e ações:

|   | Registrar | Alterar | Excluir |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máximo de 5) | Não | Sim |
| Outro aplicativo autenticador | Sim (máximo de 5) | Não | Sim |
| token de hardware | Não | Não | Sim |
| Telefone | Sim | sim | Sim |
| Telefone alternativo | Sim | sim | Sim |
| Telefone comercial | Não | Não | Não |
| Email | Sim | sim | Sim |
| Perguntas de segurança | Sim | Não | Sim |
| Senhas de aplicativo | Sim | Não | Sim |

> [!NOTE]
> As senhas de aplicativo estão disponíveis somente para usuários que têm sido impostos para a autenticação multifator. As senhas de aplicativo não estão disponíveis para os usuários que estão habilitados para a autenticação multifator por meio de uma política de acesso condicional.

Os usuários podem definir uma das opções a seguir como o método de autenticação multifator padrão:

- Microsoft Authenticator – notificação.
- Hardware ou aplicativo autenticador de token – o código.
- Chamada telefônica.
- Mensagem de texto.

Conforme continuamos a adicionar mais métodos de autenticação ao AD do Azure, esses métodos estarão disponíveis no registro combinado.

## <a name="combined-registration-modes"></a>Modos de registro combinado

Há dois modos de registro combinado: interromper e gerenciar.

- **Modo de interrupção** é uma experiência de assistente, apresentada aos usuários quando eles se registrar ou atualizar suas informações de segurança ao entrar.

- **Gerenciar o modo** faz parte do perfil do usuário e permite que os usuários gerenciem suas informações de segurança.

Para ambos os modos, os usuários que registraram anteriormente um método que pode ser usado para a autenticação multifator serão necessário realizar a autenticação multifator, antes que possam acessar suas informações de segurança.

### <a name="interrupt-mode"></a>Modo de interrupção

Registro combinado respeita as políticas de autenticação multifator e SSPR, se ambos estiverem habilitadas para seu locatário. Essas políticas controlam se um usuário for interrompido por registro durante o logon e quais métodos estarão disponíveis para o registro.

Aqui estão vários cenários em que os usuários podem ser solicitados para registrar ou atualizar suas informações de segurança:

- Registro da autenticação multifator imposto por meio da proteção de identidade: Os usuários são solicitados a registrar durante o logon. Ele pode registrar os métodos de autenticação multifator e SSPR (se o usuário está habilitado para SSPR).
- Registro da autenticação multifator imposto por meio da autenticação de multifator por usuário: Os usuários são solicitados a registrar durante o logon. Ele pode registrar os métodos de autenticação multifator e SSPR (se o usuário está habilitado para SSPR).
- Registro da autenticação multifator imposto por meio do acesso condicional ou outras políticas: Os usuários são solicitados a registrar quando eles usam um recurso que exige autenticação multifator. Ele pode registrar os métodos de autenticação multifator e SSPR (se o usuário está habilitado para SSPR).
- Registro SSPR imposto: Os usuários são solicitados a registrar durante o logon. Ele registrar apenas os métodos SSPR.
- Atualização SSPR imposta: Os usuários devem revisar suas informações de segurança em um intervalo definido pelo administrador. Os usuários são mostrados suas informações e podem confirmar as informações atuais ou fazer alterações, se necessário.

Quando o registro é imposto, os usuários recebem o número mínimo de métodos necessários para estar em conformidade com as políticas de autenticação multifator e SSPR, do mais específico para o menos seguro.

Por exemplo:

- Um usuário está habilitado para SSPR. A política SSPR necessários dois métodos para redefinir e tiver habilitado o telefone, email e código de aplicativo móvel.
   - Esse usuário é necessária para registrar os dois métodos.
      - O usuário é mostrado o Authenticator e telefone por padrão.
      - O usuário pode optar por registrar o email em vez do aplicativo authenticator ou no telefone.

Este fluxograma descreve quais métodos são mostrados a um usuário quando interrompida para registrar durante o logon:

![Fluxograma de informações de segurança combinada](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se você tiver a autenticação multifator e SSPR habilitado, é recomendável que você imponha o registro da autenticação multifator.

Se a política SSPR exige que os usuários, revise suas informações de segurança em intervalos regulares, os usuários são interrompidos durante a entrada e mostrados todos os seus métodos registrados. Se ele é atualizado, ou eles podem fazer alterações, caso eles precisem, eles podem confirmar as informações atuais.

### <a name="manage-mode"></a>Gerenciar o modo

Os usuários podem acessar gerenciar modo acessando [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) ou selecionando **informações de segurança** de meu perfil. A partir daí, os usuários podem adicionar métodos, excluir ou alterar os métodos existentes, alterar o método padrão e muito mais.

## <a name="key-usage-scenarios"></a>Principais cenários de uso

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante a entrada

Um administrador tem o registro forçado.

Um usuário não tiver configurado todas as informações de segurança necessários e vai para o portal do Azure. Depois de inserir o nome de usuário e senha, o usuário é solicitado a configurar as informações de segurança. O usuário, em seguida, siga as etapas mostradas no Assistente para configurar as informações de segurança necessárias. Se as configurações permitirem, o usuário pode optar por configurar os métodos diferentes daqueles mostradas por padrão. Depois de concluir o assistente, os usuários examinar os métodos que eles configuram e seu método padrão para a autenticação multifator. Para concluir o processo de instalação, o usuário confirma as informações e continua para o portal do Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança do meu perfil

Um administrador não tem imposta de registro.

Um usuário que ainda não tiver configurado todas as informações de segurança necessário vai para [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). O usuário seleciona **informações de segurança** no painel esquerdo. A partir daí, o usuário opta por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue as etapas para configurar esse método. Quando terminar, o usuário verá o método que foi configurado apenas na página de informações de segurança.

### <a name="delete-security-info-from-my-profile"></a>Excluir informações de segurança do meu perfil

Um usuário que tenha configurado anteriormente pelo menos um método navegar para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O usuário optar por excluir um dos métodos registrados anteriormente. Quando terminar, o usuário não vê esse método na página de informações de segurança.

### <a name="change-the-default-method-from-my-profile"></a>Alterar o método padrão de meu perfil

Um usuário que tenha configurado anteriormente pelo menos um método que pode ser usado para a autenticação multifator navegar para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O usuário altera o método padrão atual para um método diferente do padrão. Quando terminar, o usuário verá o novo método padrão na página de informações de segurança.

## <a name="next-steps"></a>Próximas etapas

[Habilite o registro combinado no seu locatário](howto-registration-mfa-sspr-combined.md)

[Uso do SSPR e MFA e relatórios de insights](howto-authentication-methods-usage-insights.md)

[Métodos disponíveis para a autenticação multifator e SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a autenticação multifator do Azure](howto-mfa-getstarted.md)
