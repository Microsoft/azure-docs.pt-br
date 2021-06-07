---
title: Acesso condicional – exigir MFA para todos os usuários-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir que todos os usuários executem a autenticação multifator
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
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994750"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Acesso condicional: exigir MFA para todos os usuários

Como Alex Weinert, o diretório de segurança de identidade na Microsoft, menciona em sua postagem de blog [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Sua senha não importa, mas a MFA faz! Com base em nossos estudos, sua conta tem mais de 99,9% menos provável de ser comprometida se você usar a MFA.

As diretrizes neste artigo ajudarão sua organização a criar uma política de MFA balanceada para seu ambiente.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de Acesso Condicional são ferramentas avançadas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **interrupção** para impedir o bloqueio de conta em todo o locatário. No cenário improvável de todos os administradores serem bloqueados de seu locatário, sua conta administrativa de acesso de emergência poderá ser usada para fazer logon no locatário. Siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [Gerenciar contas de acesso de emergência no Azure AD](../roles/security-emergency-access.md).
* **Contas de serviço** e **entidades de serviço**, como a conta de sincronização do Azure AD Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, elas são usadas por serviços de back-end que permitem acesso programático a aplicativos, mas também são usadas para entrar em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, pois a MFA não pode ser concluída programaticamente. As chamadas feitas por entidades de serviço não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver essas contas em uso em scripts ou código, considere substituí-las por [identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="application-exclusions"></a>Exclusões de aplicativo

As organizações podem ter muitos aplicativos de nuvem em uso. Nem todos esses aplicativos podem exigir segurança igual. Por exemplo, os aplicativos de folha de pagamento e de presença podem exigir MFA, mas a lanchonete provavelmente não. Os administradores podem optar por excluir aplicativos específicos de sua política.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que todos os usuários executem a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**.
   1. Em **incluir**, selecione **todos os usuários**
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **Aplicativos ou ações de nuvem** > **Incluir**, selecione **Todos os aplicativos de nuvem**.
   1. Em **excluir**, selecione qualquer aplicativo que não exija autenticação multifator.
1. Em **condições**  >  **aplicativos de cliente (versão prévia)**, em **selecionar os aplicativos cliente para os quais essa política será aplicada** , deixe todos os padrões selecionados e selecione **concluído**.
1. Em **Controles de acesso** > **Conceder**, selecione **Conceder acesso**, **Exigir autenticação multifator** e selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

### <a name="named-locations"></a>Localizações nomeadas

As organizações podem optar por incorporar locais de rede conhecidos conhecidos como **locais nomeados** a suas políticas de acesso condicional. Esses locais nomeados podem incluir redes IPv4 confiáveis como as de um local principal do Office. Para obter mais informações sobre como configurar locais nomeados, consulte o artigo [qual é a condição de local em Azure Active Directory acesso condicional?](location-condition.md)

Na política de exemplo acima, uma organização pode optar por não exigir a autenticação multifator se estiver acessando um aplicativo de nuvem de sua rede corporativa. Nesse caso, eles podem adicionar a seguinte configuração à política:

1. Em **atribuições**, selecione **condições**  >  **locais**.
   1. Configure **Sim**.
   1. Inclua **Qualquer localização**.
   1. Excluir **Todos os locais confiáveis**.
   1. Selecione **Concluído**.
1. Selecione **Concluído**.
1. **Salve** as alterações de política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
