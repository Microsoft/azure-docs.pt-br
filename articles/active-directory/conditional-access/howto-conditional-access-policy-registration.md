---
title: Acesso Condicional – informações de segurança combinadas – Azure Active Directory
description: Crie uma política de Acesso Condicional personalizada para o registro de informações de segurança
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559495"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso Condicional: proteger o registro de informações de segurança

A proteção de quando e como os usuários se registram para a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento é possível com ações do usuário em uma política de acesso condicional. Esse recurso está disponível para organizações que habilitaram o [registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade permite que as organizações tratem o processo de registro como qualquer aplicativo em uma política de acesso condicional e usem todo o poder do acesso condicional para proteger a experiência. 

Algumas organizações no passado podem ter usado o local de rede confiável ou a conformidade do dispositivo como um meio de proteger a experiência de registro. Com a adição de [passagem de acesso temporário](../authentication/howto-authentication-temporary-access-pass.md) no Azure AD, os administradores podem provisionar credenciais de tempo limitado aos usuários que permitem que eles se registrem de qualquer dispositivo ou local. Credenciais de passagem de acesso temporário atendem aos requisitos de acesso condicional para autenticação multifator.

## <a name="create-a-policy-to-secure-registration"></a>Criar uma política para proteger o registro

A política a seguir se aplica aos usuários selecionados, que tentam se registrar usando a experiência de registro combinada. A política requer que os usuários executem a autenticação multifator ou usem credenciais de passagem de acesso temporário.

1. No **Portal do Azure**, navegue até **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. No campo Nome, digite um nome para esta política. Por exemplo, o **registro de informações de segurança combinadas com TAP**.
1. Em **Atribuições** na opção **Usuários e grupos**, selecione os usuários e grupos aos quais você deseja aplicar esta política.
   1. Em **Incluir**, selecione **Todos os usuários**.

      > [!WARNING]
      > Os usuários devem estar habilitados para o [registro combinado](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Em **excluir**.
      1. Selecione **todos os usuários convidados e externos**.
      
         > [!NOTE]
         > A passagem de acesso temporário não funciona para usuários convidados.

      1. Selecione **usuários e grupos** e escolha o acesso de emergência ou as contas de vidro de sua organização. 
1. Em **Aplicativos de nuvem ou ações**, selecione **Ações do usuário** e marque **Registrar informações de segurança**.
1. Em **Controles de acesso** > **Conceder**.
   1. Selecione **Conceder acesso**.
   1. Selecione **Exigir autenticação multifator**.
   1. Clique em **Selecionar**.
1. Defina **Habilitar política** como **Ativo**.
1. Em seguida, selecione **Criar**.

Agora, os administradores terão que emitir credenciais de passagem de acesso temporário para novos usuários para que eles possam atender aos requisitos de autenticação multifator para se registrar. As etapas para realizar essa tarefa são encontradas na seção [criar uma passagem de acesso temporária no portal do Azure ad](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal).

As organizações podem optar por exigir outros controles Grant, além de ou no lugar de **exigir autenticação multifator** na etapa 6B. Ao selecionar vários controles, certifique-se de selecionar o botão de opção apropriado para exigir **todos** ou **um** dos controles selecionados ao fazer essa alteração.

### <a name="guest-user-registration"></a>Registro de usuário convidado

Para [usuários convidados](../external-identities/what-is-b2b.md) que precisam se registrar para autenticação multifator no seu diretório, você pode optar por bloquear o registro fora dos [locais de rede confiáveis](concept-conditional-access-conditions.md#locations) usando o guia a seguir.

1. No **Portal do Azure**, navegue até **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. No campo Nome, digite um nome para esta política. Por exemplo, **Registro combinado de informações de segurança em redes confiáveis**.
1. Em **Atribuições** na opção **Usuários e grupos**, selecione os usuários e grupos aos quais você deseja aplicar esta política.
   1. Em **incluir**, selecione **todos os usuários convidados e externos**.
1. Em **Aplicativos de nuvem ou ações**, selecione **Ações do usuário** e marque **Registrar informações de segurança**.
1. Em **Condições** > **Locais**.
   1. Configure **Sim**.
   1. Inclua **Qualquer localização**.
   1. Exclua **Todos os locais confiáveis**.
   1. Selecione **Concluído** na folha Locais.
   1. Selecione **Concluído** na folha Condições.
1. Em **Controles de acesso** > **Conceder**.
   1. Seleione **Bloquear acesso**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Habilitar política** como **Ativo**.
1. Em seguida, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
