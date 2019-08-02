---
title: Guia de início rápido - bloquear acesso quando um risco de sessão é detectado com acesso condicional do Active Directory do Azure | Microsoft Docs
description: Neste início rápido, você aprenderá como você pode configurar uma política de acesso condicional do Azure Active Directory (Azure AD) para bloquear entradas com base em riscos de sessão.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdbdd0253478200d39501444ae649b87b77e65a4
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509041"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Início Rápido: bloquear o acesso quando um risco de sessão é detectado com acesso condicional do Azure Active Directory  

Para manter o ambiente protegido, convém bloquear a conexão de usuários suspeitos. [Proteção de Identidade do Azure Active Directory (Azure AD)](../active-directory-identityprotection.md) analiza cada login e calcula a probabilidade de uma tentativa de conexão não ter sido realizada pelo proprietário legítimo de uma conta de usuário. A probabilidade (baixa, média, alta) é indicada na forma de um valor calculado chamado [níveis de risco de entrada](conditions.md#sign-in-risk). Definindo a condição de risco de entrada, você pode configurar uma política de acesso condicional para responder aos níveis de risco de entrada específico.

Este início rápido mostra como configurar uma [política de Acesso Condicional](../active-directory-conditional-access-azure-portal.md) que bloqueia uma entrada quando um nível de risco de entrada configurado foi detectado.

![Criar política](./media/app-sign-in-risk/1000.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, você precisa de:

- **Acesso a uma edição do Azure Active Directory Premium P2** - enquanto o Acesso Condicional é um recurso do Azure Active Directory Premium P1, você precisa de uma edição de P2 pois o cenário neste início rápido requer a proteção de identidade.
- **Proteção de identidade** -o cenário neste início rápido requer a proteção de identidade a ser habilitado. Se você não sabe como habilitar a proteção de identidade, confira [Habilitando o Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Tor Browser** - o [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) foi projetado para ajudá-lo a preservar sua privacidade online. Identity Protection detecta uma entrada de um Tor Browser como entradas de endereços IP anônimos, que tem um nível de risco médio. Para saber mais, veja [Eventos de risco do Azure Active Directory](../reports-monitoring/concept-risk-events.md).  
- **Uma conta de teste chamada Alain Charon** – caso não saiba como criar uma conta de teste, consulte [Adicionar usuários baseados em nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Teste seu logon

O objetivo desta etapa é certificar-se de que sua conta de teste pode acessar seu locatário usando o Tor Browser.

**Para testar seu logon:**

1. Entre no[portal do Azure](https://portal.azure.com) como **Alain Charon**.
1. Saia.

## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional

O cenário neste início rápido usa uma entrada de um Tor Browser para gerar um evento de risco detectado **entradas de endereços IP anônimos**. O nível de risco desse evento de risco é médio. Para responder a esse evento de risco, defina a condição de risco de entrada como média. Em um ambiente de produção, é necessário definir a condição de risco de entrada como alta ou média e alta.

Esta seção mostra como criar a política de acesso condicional necessária. Em sua política, defina:

| Configuração | Valor |
| --- | --- |
| Usuários e grupos | Alain Charon  |
| Aplicativos na nuvem | Todos os aplicativos em nuvem |
| Risco de entrada | Média |
| Concessão | Acesso bloqueado |

![Criar política](./media/app-sign-in-risk/130.png)

**Para configurar sua política de acesso condicional:**

1. Faça login no [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.
1. No portal do Azure, na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na página **Azure Active Directory**, na seção **Segurança**, clique em **Acesso Condicional**.

   ![Acesso Condicional](./media/app-sign-in-risk/03.png)

1. Na página **Acesso Condicional**, na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![NOME](./media/app-sign-in-risk/108.png)

1. Na página **Novo**, na caixa de texto **nome**, digite **bloquear o acesso de nível de risco médio**.

   ![NOME](./media/app-sign-in-risk/104.png)

1. Na seção **Atribuição**, clique em **Usuários e Grupos**.

   ![Usuários e grupos](./media/app-sign-in-risk/06.png)

1. Na página **Usuários e grupos**:

   ![Acesso Condicional](./media/app-sign-in-risk/107.png)

   1. Clique em **Selecionar usuários e grupos** e selecione **Usuários e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **Selecionar**, selecione **Alain Charon** e, em seguida, clique em **Selecionar**.
   1. Na página **Usuários e grupos**, clique em **Concluído**.
1. Clique em **Aplicativos de nuvem**.

   ![Aplicativos na nuvem](./media/app-sign-in-risk/08.png)

1. Na página de **aplicativos de nuvem**:

   ![Acesso Condicional](./media/app-sign-in-risk/109.png)

   1. Clique em **todos os aplicativos em nuvem**.
   1. Clique em **Concluído**.
1. Clique em **Condições**.

   ![Controles de acesso](./media/app-sign-in-risk/19.png)

1. Na página **Condições**:

   ![Nível de risco de entrada](./media/app-sign-in-risk/21.png)

   1. Clique em **Risco de entrada**.
   1. Em **Configurar**, clique em **Sim**.
   1. Como nível de risco de entrada, selecione **Médio**.
   1. Clique em **Selecionar**.
   1. Na página **Condições**, clique em **Concluído**.
1. Na seção **Controles de acesso**, clique em **Conceder**.

   ![Controles de acesso](./media/app-sign-in-risk/10.png)

1. Na página **Grant**:

   ![Acesso Condicional](./media/app-sign-in-risk/105.png)

   1. Seleione **Bloquear acesso**.
   1. Clique em **Selecionar**.
1. Na seção **Habilitar política**, clique em **Ativar**.

   ![Habilitar política](./media/app-sign-in-risk/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou a política de acesso condicional, provavelmente deseja saber se ela funciona conforme o esperado. Como primeira etapa, use a **ferramenta de política “What If”** do acesso condicional para simular uma entrada de seu usuário de teste. A simulação calcula o impacto que esse logon tem em suas políticas e gera um relatório de simulação.  

Quando você executa o **e se a ferramenta de política** para este cenário, o **bloquear o acesso de nível de risco médio** deve ser listado na **políticas que se aplicam**.

![Usuário](./media/app-sign-in-risk/117.png)

**Para avaliar sua política de acesso condicional:**

1. Na página [Acesso Condicional – Políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), no menu superior, clique em **What If**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Clique em **usuário**, selecione **Alan Charon** sobre o **usuários** página e, em seguida, clique em **selecione**.

   ![Usuário](./media/app-sign-in-risk/116.png)

1. Em **Logue risco**, selecione **Médio**.

   ![Usuário](./media/app-sign-in-risk/119.png)

1. Clique em **What If**.

## <a name="test-your-conditional-access-policy"></a>Teste sua política de acesso condicional

Na seção anterior, você aprendeu como avaliar uma entrada simulada. Além de uma simulação, você também deve testar sua política de acesso condicional para garantir que ela funcione conforme o esperado.

Para testar sua política, tente entrar em sua [portal do Azure](https://portal.azure.com) como **Alan Charon** usando o Tor Browser. Tentativa de logon deve ser bloqueada por sua política de acesso condicional.

![Autenticação multifator](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste, o Tor Browser e a política de acesso condicional:

- Caso não saiba como excluir um usuário do Azure AD, consulte [Excluir usuários do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para excluir sua política, selecione-a e clique em **Excluir** na barra de ferramentas de acesso rápido.

   ![Autenticação multifator](./media/app-sign-in-risk/33.png)

- Para obter instruções remover o Tor Browser, consulte [Desinstalando](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exigir termos de uso para serem aceitos](require-tou.md)
> [exigir MFA para aplicativos específicos](app-based-mfa.md)
