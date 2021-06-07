---
title: Criando uma política de acesso condicional-Azure Active Directory
description: Quais são todas as opções disponíveis para criar uma política de acesso condicional e o que elas significam?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35d2bf33b4a22c14abfb61a87a3697b05188ed31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579086"
---
# <a name="building-a-conditional-access-policy"></a>Criando uma política de acesso condicional

Conforme explicado no artigo [o que é acesso condicional](overview.md), uma política de acesso condicional é uma instrução if-then, de **atribuições** e **controles de acesso**. Uma política de acesso condicional reúne sinais, para tomar decisões e impor políticas organizacionais.

Como uma organização cria essas políticas? O que é necessário? Como eles são aplicados?

![Acesso condicional (sinais + decisões + imposição = políticas)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

Várias políticas de acesso condicional podem se aplicar a um usuário individual a qualquer momento. Nesse caso, todas as políticas que se aplicam devem ser atendidas. Por exemplo, se uma política exigir a MFA (autenticação multifator) e outra exigir um dispositivo em conformidade, você deverá concluir a MFA e usar um dispositivo compatível. Todas as atribuições são avaliadas com **AND** lógicos. Se você tiver mais de uma atribuição configurada, todas as atribuições deverão ser atendidas para disparar uma política.

Todas as políticas são impostas em duas fases:

- Fase 1: coletar detalhes da sessão 
   - Colete detalhes da sessão, como o local de rede e a identidade do dispositivo que serão necessários para a avaliação da política. 
   - A fase 1 da avaliação de política ocorre para políticas e políticas habilitadas no [modo somente de relatório](concept-conditional-access-report-only.md).
- Fase 2: imposição 
   - Use os detalhes da sessão coletados na fase 1 para identificar os requisitos que não foram atendidos. 
   - Se houver uma política configurada para bloquear o acesso, com o controle de concessão de bloqueio, a imposição será interrompida aqui e o usuário será bloqueado. 
   - O usuário será solicitado a concluir os requisitos adicionais de controle de concessão que não foram atendidos durante a fase 1 na seguinte ordem, até que a política seja satisfeita:  
      - Autenticação multifator 
      - Aplicativo cliente aprovado/política de proteção de aplicativo 
      - Dispositivo gerenciado (ingresso em conformidade ou Azure AD híbrido) 
      - Termos de uso 
      - Controles personalizados  
   - Depois que todos os controles de concessão forem satisfeitos, aplique os controles de sessão (aplicativo imposto, Microsoft Cloud App Security e tempo de vida do token) 
   - A fase 2 da avaliação de política ocorre para todas as políticas habilitadas. 

## <a name="assignments"></a>Atribuições

A parte de atribuições controla quem, e e onde a política de acesso condicional.

### <a name="users-and-groups"></a>Usuários e grupos

[Usuários e grupos](concept-conditional-access-users-groups.md) atribuem quem a política incluirá ou excluirá. Essa atribuição pode incluir todos os usuários, grupos específicos de usuários, funções de diretório ou usuários convidados externos. 

### <a name="cloud-apps-or-actions"></a>Aplicativos na nuvem ou ações

Os [aplicativos de nuvem ou as ações](concept-conditional-access-cloud-apps.md) podem incluir ou excluir aplicativos de nuvem ou ações do usuário que estarão sujeitas à política.

### <a name="conditions"></a>Condições

Uma política pode conter várias [condições](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Risco de entrada

Para organizações com [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md), as detecções de risco geradas podem influenciar suas políticas de acesso condicional.

#### <a name="device-platforms"></a>Plataformas de dispositivo

As organizações com várias plataformas de sistema operacional de dispositivos podem querer impor políticas específicas em diferentes plataformas. 

As informações usadas para calcular a plataforma de dispositivo vêm de fontes não verificadas, como cadeias de caracteres de agente do usuário que podem ser alteradas.

#### <a name="locations"></a>Locais

Os dados de local são fornecidos por dados de localização geográfica de IP. Os administradores podem optar por definir locais e optar por marcá-los como confiáveis para os locais de rede de sua organização.

#### <a name="client-apps"></a>Aplicativos cliente

Por padrão, as políticas de acesso condicional se aplicam a aplicativos de navegador, aplicativos móveis e clientes de área de trabalho que dão suporte à autenticação moderna. 

Essa condição de atribuição permite que as políticas de acesso condicional destinam-se a aplicativos cliente específicos que não usam autenticação moderna. Esses aplicativos incluem clientes do Exchange ActiveSync, aplicativos mais antigos do Office que não usam autenticação moderna e protocolos de email como IMAP, MAPI, POP e SMTP.

#### <a name="device-state"></a>Estado do dispositivo

Esse controle é usado para excluir dispositivos que são ingressados no Azure AD híbrido ou marcados como em conformidade no Intune. Essa exclusão pode ser feita para bloquear dispositivos não gerenciados. 

## <a name="access-controls"></a>Controles de acesso

A parte controles de acesso da política de acesso condicional controla como uma política é imposta.

### <a name="grant"></a>Conceder

[Grant](concept-conditional-access-grant.md) fornece aos administradores um meio de imposição de política onde eles podem bloquear ou conceder acesso.

#### <a name="block-access"></a>Acesso bloqueado

Bloquear acesso faz exatamente isso, ele bloqueará o acesso nas atribuições especificadas. O controle de bloco é poderoso e deve ser atraente com o conhecimento apropriado.

#### <a name="grant-access"></a>Conceder acesso

O controle Grant pode disparar a imposição de um ou mais controles. 

- Exigir autenticação multifator (autenticação multifator do Azure AD)
- Exigir que o dispositivo seja marcado como compatível (Intune)
- Exigir um dispositivo ingressado no Azure AD Híbrido
- Exigir um aplicativo cliente aprovado
- Requer política de proteção do aplicativo
- Exigir alteração de senha
- Requerer termos de uso

Os administradores podem optar por exigir um dos controles anteriores ou todos os controles selecionados usando as opções a seguir. O padrão para vários controles é exigir todos.

- Exigir todos os controles selecionados (controle e controle)
- Exigir um dos controles selecionados (controle ou controle)

### <a name="session"></a>Session

[Controles de sessão](concept-conditional-access-session.md) podem limitar a experiência 

- Usar restrições de aplicativo impostas
   - Atualmente, funciona somente com o Exchange Online e o SharePoint Online.
      - Passa informações do dispositivo para permitir o controle da experiência que concede acesso completo ou limitado.
- Usar o Controle de Aplicativos de Acesso Condicional
   - Usa sinais de Microsoft Cloud App Security para fazer coisas como: 
      - Bloquear download, recortar, copiar e imprimir documentos confidenciais.
      - Monitore o comportamento de sessão arriscada.
      - Exigir rotulagem de arquivos confidenciais.
- Frequência de entrada
   - Capacidade de alterar a frequência de entrada padrão para autenticação moderna.
- Sessão persistente do navegador
   - Permite que os usuários permaneçam conectados após fechar e reabrir a janela do navegador.

## <a name="simple-policies"></a>Políticas simples

Uma política de acesso condicional deve conter, no mínimo, o seguinte para ser aplicado:

- **Nome** da política.
- **Atribuições**
   - **Usuários e/ou grupos** aos quais aplicar a política.
   - **Aplicativos de nuvem ou ações** às quais aplicar a política.
- **Controles de acesso**
   - Controles **Grant** ou **Block**

![Política de acesso condicional em branco](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

O artigo [políticas de acesso condicional comum](concept-conditional-access-policy-common.md) inclui algumas políticas que achamos que seriam úteis para a maioria das organizações.

## <a name="next-steps"></a>Próximas etapas

[Criar uma política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)

[Planejar uma implantação da Autenticação Multifator do Azure AD baseada em nuvem](../authentication/howto-mfa-getstarted.md)

[Gerenciando a conformidade do dispositivo com o Intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security e acesso condicional](/cloud-app-security/proxy-intro-aad)