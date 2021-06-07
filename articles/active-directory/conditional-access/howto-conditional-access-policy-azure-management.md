---
title: Acesso Condicional – exigir MFA para gerenciamento do Azure – Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir a autenticação multifator para grupos de gerenciamento do Azure
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
ms.openlocfilehash: 2e2b6b3e9a6bdead4e4da7f1a829698d86cfbf52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366166"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Acesso condicional: Exigir MFA para gerenciamento do Azure

As organizações usam uma variedade de serviços do Azure e os gerenciam usando ferramentas baseadas no Azure Resource Manager, como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Essas ferramentas podem fornecer acesso altamente privilegiado a recursos, que podem alterar configurações de toda a assinatura, bem como configurações de serviço e cobrança de assinatura. Para proteger esses recursos privilegiados, a Microsoft recomenda exigir a autenticação multifator para que qualquer usuário acesse esses recursos.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de Acesso Condicional são ferramentas avançadas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **interrupção** para impedir o bloqueio de conta em todo o locatário. No cenário improvável de todos os administradores serem bloqueados de seu locatário, sua conta administrativa de acesso de emergência poderá ser usada para fazer logon no locatário. Siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [Gerenciar contas de acesso de emergência no Azure AD](../roles/security-emergency-access.md).
* **Contas de serviço** e **entidades de serviço**, como a conta de sincronização do Azure AD Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, elas são usadas por serviços de back-end que permitem acesso programático a aplicativos, mas também são usadas para entrar em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, pois a MFA não pode ser concluída programaticamente. As chamadas feitas por entidades de serviço não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver essas contas em uso em scripts ou código, considere substituí-las por [identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que quem tiver acesso ao aplicativo [Microsoft Azure Management](concept-conditional-access-cloud-apps.md#microsoft-azure-management) execute a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador da segurança ou administrador de Acesso Condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **Aplicativos de nuvem ou ações** > **Incluir**, selecione **Selecionar aplicativos**, escolha **Microsoft Azure Management**, **Selecionar**, **Concluído**.
1. Em **condições**  >  **aplicativos de cliente (versão prévia)**, em **selecionar os aplicativos cliente para os quais essa política será aplicada** , deixe todos os padrões selecionados e selecione **concluído**.
1. Em **Controles de acesso** > **Conceder**, selecione **Conceder acesso**, **Exigir autenticação multifator** e selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
