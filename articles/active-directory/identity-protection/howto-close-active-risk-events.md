---
title: Como fechar eventos de risco ativos no Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba mais sobre as opções para fechar eventos de risco ativos.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5e24c12b72852ee7009533c8dc24d231fe636f2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333988"
---
# <a name="how-to-close-active-risk-events"></a>Como: Fechar eventos de risco ativos

Com [eventos de risco](../reports-monitoring/concept-risk-events.md), o Azure Active Directory detecta indicadores para contas de usuário potencialmente comprometidas. Como administrador, você deseja fechar todos os eventos de risco de modo que os usuários afetados não estejam mais em risco.

Este artigo apresenta uma visão geral das opções adicionais que você tem para fechar eventos de risco ativos.

## <a name="options-to-close-risk-events"></a>Opções para fechar eventos de risco 

O status de um evento de risco é **ativo** ou **fechado**. Todos os eventos de risco ativos contribuem para o cálculo de um nível de risco do usuário chamado valor. O nível de risco do usuário é um indicador (baixo, médio, alto) para a probabilidade de que uma conta tenha sido comprometida. 

Para fechar eventos de risco ativos, você tem as seguintes opções:

- Exigir redefinição de senha com uma política de risco do usuário
- Redefinição de senha manual
- Ignorar todos os eventos de risco 
- Fechar eventos de risco individuais manualmente

## <a name="require-password-reset-with-a-user-risk-policy"></a>Exigir redefinição de senha com uma política de risco do usuário

Ao configurar a [política de acesso condicional de risco do usuário](howto-user-risk-policy.md), você poderá exigir uma alteração de senha se um nível de risco do usuário especificado tiver sido detectado automaticamente. 

![Redefinir Senha](./media/howto-close-active-risk-events/13.png)

Uma redefinição de senha fecha todos os eventos de riscos ativos do usuário relacionado e coloca a identidade de volta em um estado seguro. Usar uma política de risco do usuário é o método preferencial para fechar eventos de risco ativos, pois esse método é automatizado. Não há interação necessária entre o usuário afetado e o suporte técnico ou um administrador.

No entanto, usar uma política de risco do usuário nem sempre é aplicável. Isso se aplica, por exemplo, a:

- Usuários que não tenham sido registrados para a MFA (Autenticação Multifator).
- Usuários com eventos de risco ativos que foram excluídos.
- Uma investigação revela que um evento de risco relatado foi executado pelo usuário legítimo.

## <a name="manual-password-reset"></a>Redefinição de senha manual

Se exigir uma redefinição de senha usando uma política de risco de usuário não for uma opção, você poderá fechar todos os eventos de risco para um usuário com uma redefinição de senha manual.

![Redefinir Senha](./media/howto-close-active-risk-events/04.png)

A caixa de diálogo correspondente fornece dois métodos diferentes para redefinir uma senha:

![Redefinir Senha](./media/howto-close-active-risk-events/05.png)

**Gerar uma senha temporária** – ao gerar uma senha temporária, você pode imediatamente deixar uma identidade em um estado seguro novamente. Esse método requer interação com os usuários afetados, pois eles precisarão saber qual é a senha temporária. Você pode, por exemplo, enviar a nova senha temporária para um endereço de email alternativo para o usuário ou para o gerente dele. Como a senha é temporária, o usuário é solicitado a alterá-la na próxima vez que entrar.

**Exigir que o usuário redefina a senha** – exigir que os usuários redefinam senhas permite a autorrecuperação sem entrar em contato com o suporte técnico nem com o administrador. Como no caso de uma política de risco do usuário, esse método só se aplica a usuários registrados para MFA. Para usuários não registrados para MFA, essa opção não está disponível.

## <a name="dismiss-all-risk-events"></a>Ignorar todos os eventos de risco

Se uma redefinição de senha não for uma opção, você também poderá ignorar todos os eventos de risco. 

![Redefinir Senha](./media/howto-close-active-risk-events/03.png)

Quando você clica em **Descartar todos os eventos**, todos os eventos são fechados e o usuário afetado não está mais em risco. No entanto, como esse método não tem um impacto sobre a senha existente, ele não deixa a identidade relacionada em um estado seguro novamente. O caso de uso preferencial para esse método é um usuário excluído com eventos de risco ativos. 

## <a name="close-individual-risk-events-manually"></a>Fechar eventos de risco individuais manualmente

Você pode fechar eventos de risco individuais manualmente. Ao fechar eventos de risco manualmente, você pode diminuir o nível de risco do usuário. Normalmente, os eventos de risco são fechados manualmente em resposta a uma investigação relacionada. Por exemplo, quando conversar com um usuário revela que um evento de risco ativo não é mais necessário. 
 
Ao fechar manualmente os eventos de risco, você pode optar por executar uma das ações a seguir para alterar o status de um evento de risco:

![Ações](./media/howto-close-active-risk-events/06.png)

- **Resolver** - Se, após investigar um evento de risco, você tomou uma ação de correção apropriada fora do Identity Protection e acredita que o evento de risco deve ser considerado fechado, marque o evento como Resolvido. Eventos resolvidos definirão o status do evento de risco como Fechado e ele não contribuirá com o risco do usuário.
- **Marcar como falso positivo** - Em alguns casos, você pode investigar um evento de risco e descobrir que ele foi sinalizado incorretamente como uma situação arriscada. Você pode ajudar a reduzir o número de tais ocorrências marcando o evento de risco como falso positivo. Isso ajudará os algoritmos de aprendizado de máquina a melhorarem a classificação de eventos semelhantes no futuro. O status de eventos falso positivos é Fechado, e eles não contribuirão mais para o risco do usuário.
- **Ignorar** - Se você não executou nenhuma ação de correção, mas deseja remover o evento de risco da lista ativa, marque um evento de risco como Ignorar e o status do evento será Fechado. Eventos ignorados não contribuem com o risco do usuário. Essa opção deve ser usada somente em circunstâncias incomuns.
- **Reativar** – Eventos de risco fechados manualmente (escolhendo Resolver, Falso positivo ou Ignorar) podem ser reativados, definindo o status do evento novamente como Ativo. Eventos de risco reativados contribuem no cálculo de nível de risco do usuário. Eventos de risco fechados por meio de correção (como redefinir uma senha de segurança) não podem ser reativados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview.md).
