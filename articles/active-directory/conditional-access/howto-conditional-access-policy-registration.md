---
title: Acesso Condicional – informações de segurança combinadas – Azure Active Directory
description: Crie uma política de Acesso Condicional personalizada para o registro de informações de segurança
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e99f7466bd3b7ed5517157ca3fa45e7c3241217
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599755"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso Condicional: proteger o registro de informações de segurança

A proteção de quando e como os usuários se registram para a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento agora é possível com as ações do usuário na política de acesso condicional. Essa versão prévia do recurso está disponível para organizações que habilitaram a [versão prévia de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações em que desejam usar condições como local de rede confiável para restringir o acesso ao registro para a autenticação multifator do Azure AD e a SSPR (redefinição de senha de autoatendimento). Para obter mais informações sobre as condições utilizáveis, consulte o artigo [Acesso Condicional: Condições](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados que tentam se registrar usando a experiência de registro combinado; ela bloqueia o acesso, a menos que eles estejam se conectando de um local marcado como rede confiável.

1. No **Portal do Azure**, navegue até **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. No campo Nome, digite um nome para esta política. Por exemplo, **Registro combinado de informações de segurança em redes confiáveis**.
1. Em **Atribuições** na opção **Usuários e grupos**, selecione os usuários e grupos aos quais você deseja aplicar esta política.

   > [!WARNING]
   > Os usuários devem estar habilitados para o [registro combinado](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações**, selecione **Ações do usuário** e marque **Registrar informações de segurança**.
1. Em **Condições** > **Locais**.
   1. Configure **Sim**.
   1. Inclua **Qualquer localização**.
   1. Exclua **Todos os locais confiáveis**.
   1. Selecione **Concluído** na folha Locais.
   1. Selecione **Concluído** na folha Condições.
1. Em **Condições** > **Aplicativos clientes (versão prévia)** , defina **Configurar** como **Sim** e selecione **Concluído**.
1. Em **Controles de acesso** > **Conceder**.
   1. Seleione **Bloquear acesso**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Habilitar política** como **Ativo**.
1. Em seguida, selecione **Salvar**.

Na etapa 6 desta política, as organizações têm opções que podem escolher. A política acima requer o registro de um local de rede confiável. As organizações podem optar por utilizar qualquer condição disponível no lugar de **Locais**. Lembre-se de que essa política é uma política de bloqueio para que tudo incluído seja bloqueado e o que não corresponder à inclusão seja permitido. 

Alguns podem optar por usar o estado do dispositivo em vez do local na etapa 6 acima:

6. Em **Condições** > **Estado do dispositivo (versão prévia)** .
   1. Configure **Sim**.
   1. Inclua **Todos os estados de dispositivo**.
   1. Exclua **Dispositivo ingressado no Azure AD Híbrido** e/ou **Dispositivo marcado como em conformidade**
   1. Selecione **Concluído** na folha Locais.
   1. Selecione **Concluído** na folha Condições.

> [!WARNING]
> Se você usar o estado do dispositivo como uma condição em sua política, isso poderá afetar os usuários convidados no diretório. [Modo somente relatório](concept-conditional-access-report-only.md) pode ajudar a determinar o impacto das decisões de política.
> Observe que o modo somente de relatório não é aplicável para políticas de acesso condicional com o escopo "ações do usuário".

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
