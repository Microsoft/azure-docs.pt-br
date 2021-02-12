---
title: Acesso condicional – exigir MFA para administradores-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir que os administradores executem a autenticação multifator
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57826fcff03e79d5617c7eb69aac7d535d3c86f7
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915701"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Acesso condicional: exigir MFA para administradores

Contas que são atribuídas direitos administrativos são direcionadas por invasores. Exigir a autenticação multifator (MFA) nessas contas é uma maneira fácil de reduzir o risco de comprometimento dessas contas.

A Microsoft recomenda que você exija MFA nas seguintes funções no mínimo:

* Administrador de Autenticação
* Administrador de cobrança
* Administrador de acesso condicional
* Administradores do Exchange
* Administrador global
* Administrador de assistência técnica
* Administrador de senha
* Administrador de função com privilégios
* Administrador de segurança
* Administrador do SharePoint
* Administrador de usuários

As organizações podem optar por incluir ou excluir funções como se encontram adequadas.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de Acesso Condicional são ferramentas avançadas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **interrupção** para impedir o bloqueio de conta em todo o locatário. No cenário improvável de todos os administradores serem bloqueados de seu locatário, sua conta administrativa de acesso de emergência poderá ser usada para fazer logon no locatário. Siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [Gerenciar contas de acesso de emergência no Azure AD](../roles/security-emergency-access.md).
* **Contas de serviço** e **entidades de serviço**, como a conta de sincronização do Azure AD Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, elas são usadas por serviços de back-end que permitem acesso programático a aplicativos, mas também são usadas para entrar em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, pois a MFA não pode ser concluída programaticamente. As chamadas feitas por entidades de serviço não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver essas contas em uso em scripts ou código, considere substituí-las por [identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas executem a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **incluir**, selecione **funções de diretório (versão prévia)** e escolha as seguintes funções no mínimo:
      * Administrador de Autenticação
      * Administrador de cobrança
      * Administrador de acesso condicional
      * Administradores do Exchange
      * Administrador global
      * Administrador de assistência técnica
      * Administrador de senha
      * Administrador de segurança
      * Administrador do SharePoint
      * Administrador de usuários
   
      > [!WARNING]
      > As políticas de acesso condicional não dão suporte a usuários atribuídos a uma função de diretório [com escopo para uma unidade administrativa](../roles/admin-units-assign-roles.md) ou funções de diretório com escopo definido diretamente para um objeto, como por meio de [funções personalizadas](../roles/custom-create.md).

   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **Aplicativos ou ações de nuvem** > **Incluir**, selecione **Todos os aplicativos de nuvem** e selecione **Concluído**.
1. Em **condições**  >  **aplicativos cliente**, mude **Configurar** para **Sim** e, em **Selecione os aplicativos cliente para os quais essa política será aplicada** , deixe todos os padrões selecionados e selecione **concluído**.
1. Em **Controles de acesso** > **Conceder**, selecione **Conceder acesso**, **Exigir autenticação multifator** e selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
