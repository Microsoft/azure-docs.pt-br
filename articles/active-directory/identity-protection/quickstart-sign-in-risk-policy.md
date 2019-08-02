---
title: Início rápido - Bloquear acesso quando um risco de sessão é detectado com o Azure Active Directory Identity Protection | Microsoft Docs
description: Neste início rápido, você aprenderá como configurar uma política de Acesso Condicional de risco de entrada do Azure AD (Azure Active Directory) Identity Protection para bloquear entradas com base em riscos de sessão.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb1e29735a860f5dc3b6ce8996af9fcd4962871
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335309"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Início Rápido: Bloquear o acesso quando um risco de sessão for detectado com o Azure Active Directory Identity Protection  

Para manter o ambiente protegido, convém bloquear a conexão de usuários suspeitos. O Azure AD (Azure Active Directory) Identity Protection analisa cada entrada e calcula a probabilidade de uma tentativa de entrada não ter sido realizada pelo proprietário legítimo de uma conta de usuário. A probabilidade (baixa, média, alta) é indicada na forma de um valor calculado chamado níveis de risco de entrada. Ao definir a condição de risco de entrada, é possível configurar uma política de Acesso Condicional de risco de entrada para responder a níveis de risco de entrada específicos. 

Este início rápido mostra como configurar uma política de Acesso Condicional de risco de entrada que bloqueia uma entrada quando um nível de risco médio e superior foi detectado. 

![Criar política](./media/quickstart-sign-in-risk-policy/1004.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir o cenário deste tutorial, você precisa de:

- **Acesso a uma edição do Azure AD Premium P2**  - O Azure AD Identity Protection é um recurso do Azure AD Premium P2. 
- **Proteção de identidade** -o cenário neste início rápido requer a proteção de identidade a ser habilitado. Se você não sabe como habilitar a proteção de identidade, confira [Habilitando o Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Tor Browser** - o [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) foi projetado para ajudá-lo a preservar sua privacidade online. Identity Protection detecta uma entrada de um Tor Browser como **entradas de endereços IP anônimos**, que tem um nível de risco médio. Para saber mais, veja [Eventos de risco do Azure Active Directory](../reports-monitoring/concept-risk-events.md).  
- **Uma conta de teste chamada Alain Charon** - Se você não souber como criar uma conta de teste, consulte [Adicionar um novo usuário](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Teste seu logon 

O objetivo desta etapa é certificar-se de que sua conta de teste pode acessar seu locatário usando o Tor Browser.

**Para testar seu logon:**

1. Entre no[portal do Azure](https://portal.azure.com) como **Alain Charon**.
2. Saia. 

## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional 

O cenário neste início rápido usa uma entrada de um Tor Browser para gerar um evento de risco detectado **entradas de endereços IP anônimos**. O nível de risco desse evento de risco é médio. Para responder a esse evento de risco, defina a condição de risco de entrada como média. 

Esta seção mostra como criar a política de Acesso Condicional de risco de entrada necessária. Em sua política, defina:

|Configuração |Valor|
|---     | --- |
| Usuários  | Alain Charon  |
| Condições | Risco de entrada, médio e acima |
| Controles | Acesso bloqueado |

![Criar política](./media/quickstart-sign-in-risk-policy/201.png)

**Para configurar sua política de acesso condicional:**

1. Entre no seu [portal do Azure](https://portal.azure.com) como administrador global.
2. Vá para a [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Na página **Azure AD Identity Protection**, na seção **Configurar**, clique em **Política de entrada de risco**.
4. Na página de política, na seção **Atribuições**, clique em **Usuários**.
5. Na página **Usuários**, clique em **Selecionar usuários**.
6. Na página **Selecionar usuários**, selecione **Alain Charon** e, em seguida, clique em **Selecionar**.
7. Na página **Usuários**, clique em **Concluído**. 
8. Na página de política, na seção **Atribuições**, clique em **Condições**.
9. Na página **Condições**, clique em **Risco de entrada**.
10. Na página **Risco de entrada**, selecione **Médio e acima** e, em seguida, clique em **Selecionar**. 
11. Na página **Condições**, clique em **Concluído**.
12. Na página de política, na seção **Controles**, clique em **Acesso**.
13. Na página **Acesso**, clique em **Permitir acesso**, selecione **Requer autenticação multifator** e, em seguida, clique em **Selecionar**.
14. Na página de política, clique em **Salvar**.  

## <a name="test-your-conditional-access-policy"></a>Teste sua política de acesso condicional

Para testar sua política, tente entrar em sua [portal do Azure](https://portal.azure.com) como **Alan Charon** usando o Tor Browser. Tentativa de logon deve ser bloqueada por sua política de acesso condicional.

![Autenticação multifator](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste, o Navegador Tor e desabilite a política de Acesso Condicional de risco de entrada:

- Se você não souber excluir um usuário do Azure AD, consulte [Como adicionar ou excluir usuários](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para obter instruções remover o Tor Browser, consulte [Desinstalando](https://tb-manual.torproject.org/uninstalling/).
