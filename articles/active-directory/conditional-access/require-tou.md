---
title: Início rápido - exigem termos de uso para ser aceito antes de acessar a aplicativos de nuvem que são protegidos pelo Acesso Condicional do Azure Active Directory | Microsoft Docs
description: Neste início rápido, você aprenderá como exigir que os termos de uso sejam aceitos antes de ter acesso aos aplicativos de nuvem selecionados pelo Acesso Condicional do Azure Active Directory.
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
ms.openlocfilehash: ba684209b497792cd2f520f6b530168959e62d7f
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506921"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Início Rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem

Antes de acessar determinados aplicativos de nuvem em seu ambiente, talvez você queira obter o consentimento dos usuários na forma de aceitar os termos de uso (ToU). O Acesso Condicional do Azure AD (Active Directory do Azure) oferece:

- Um método simples para configurar os termos de uso
- A opção de exigir a aceitar os termos de uso por meio de uma política de Acesso Condicional  

Este início rápido mostra como configurar uma [política de Acesso Condicional do Azure AD](../active-directory-conditional-access-azure-portal.md) que requer um termo de uso ser aceito para um aplicativo de nuvem selecionado em seu ambiente.

![Criar política](./media/require-tou/5555.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste início rápido, você precisa de:

- **Acesso a uma edição do Azure AD Premium** – o Acesso Condicional ao Azure AD é um recurso do Azure AD Premium.
- **Uma conta de teste chamada Isabella Simonsen** – caso não saiba como criar uma conta de teste, consulte [Adicionar usuários baseados em nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Teste seu logon

O objetivo desta etapa é obter uma impressão da experiência de logon sem uma política de Acesso Condicional.

**Para testar seu logon:**

1. Entre no [Portal do Azure](https://portal.azure.com/) como Isabella Simonsen.
1. Saia.

## <a name="create-your-terms-of-use"></a>Criar seus termos de uso

Esta seção fornece as etapas para criar um exemplo de termos de uso. Quando você cria um termos de uso, você seleciona um valor para **impor com modelos de política de Acesso Condicional**. Selecionando **política personalizada** abre a caixa de diálogo para criar uma nova política de Acesso Condicional, assim que seus termos de uso foi criado.

**Para criar seus termos de uso:**

1. No Microsoft Word, crie um novo documento.
1. Digite **meu termos de uso**e, em seguida, salve o documento no seu computador como **mytou.pdf**.
1. Faça login no [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. No portal do Azure, na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na página **Azure Active Directory**, na seção **Segurança**, clique em **Acesso Condicional**.

   ![Acesso Condicional](./media/require-tou/03.png)

1. Na seção **Gerenciar**, clique em **Termos de uso**.

   ![Termos de uso](./media/require-tou/04.png)

1. No menu na parte superior, clique em **Novos termos**.

   ![Termos de uso](./media/require-tou/05.png)

1. Na página **Novos termos de uso**:

   ![Termos de uso](./media/require-tou/112.png)

   1. Na caixa de texto **Nome**, digite **Meus Termos de uso**.
   1. Na caixa de texto **Exibir nome**, digite **Meus Termos de uso**.
   1. Carregue seus termos de usar o arquivo de PDF.
   1. Como **Linguagem**, selecione **Inglês**.
   1. Para **Exigir que os usuários expandam os Termos de uso**, selecione **Ativado**.
   1. Para **Impor com modelos da política de Acesso Condicional**, selecione **Políticas personalizadas**.
   1. Clique em **Criar**.

## <a name="create-your-conditional-access-policy"></a>Criar sua política de Acesso Condicional

Esta seção mostra como criar a política de Acesso Condicional necessária. O cenário deste início rápido usa:

- O portal do Azure como espaço reservado para um aplicativo de nuvem que requer seus termos de uso serem aceitos. 
- Seu usuário de exemplo para testar a política de Acesso Condicional.  

Em sua política, defina:

| Configuração | Valor |
| --- | --- |
| Usuários e grupos | Isabella Simonsen |
| Aplicativos na nuvem | Gerenciamento do Microsoft Azure |
| Conceder acesso | Meus termos de uso |

![Criar política](./media/require-tou/1234.png)

**Para configurar sua política de Acesso Condicional:**

1. Na página **Novo**, na caixa de texto **Nome**, digite **Exige Termos de uso para Isabella**.

   ![NOME](./media/require-tou/71.png)

1. Na seção **Atribuição**, clique em **Usuários e Grupos**.

   ![Usuários e grupos](./media/require-tou/06.png)

1. Na página **Usuários e grupos**:

   ![Usuários e grupos](./media/require-tou/24.png)

   1. Clique em **Selecionar usuários e grupos** e selecione **Usuários e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **Selecionar**, selecione **Isabella Simonsen** e depois clique em **Selecionar**.
   1. Na página **Usuários e grupos**, clique em **Concluído**.
1. Clique em **Aplicativos de nuvem**.

   ![Aplicativos na nuvem](./media/require-tou/08.png)

1. Na página de **aplicativos de nuvem**:

   ![Selecionar aplicativos de nuvem](./media/require-tou/26.png)

   1. Clique em **Selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **Selecionar**, selecione **Microsoft Azure** e clique em **Selecionar**.
   1. Na página **Aplicativos de nuvem**, clique em **Concluído**.
1. Na seção **Controles de acesso**, clique em **Conceder**.

   ![Controles de acesso](./media/require-tou/10.png)

1. Na página **Grant**:

   ![Concessão](./media/require-tou/111.png)

   1. Selecione **Conceder acesso**.
   1. Selecione **Meus Termos de uso**.
   1. Clique em **Selecionar**.
1. Na seção **Habilitar política**, clique em **Ativar**.

   ![Habilitar política](./media/require-tou/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou a política de Acesso Condicional, provavelmente deseja saber se ela funciona conforme o esperado. Como primeira etapa, use a ferramenta de política What If do Acesso Condicional para simular uma entrada de seu usuário de teste. A simulação calcula o impacto que esse logon tem em suas políticas e gera um relatório de simulação.  

Para inicializar a ferramenta de avaliação de política **What If**, defina:

- **Isabella Simonsen** como usuário
- **Gerenciamento do Microsoft Azure** como aplicativo de nuvem

Ao clicar em **What If**, é criado um relatório de simulação que mostra:

- **Exigir termos de uso para Isabella** sob **políticas que se aplicam**
- **Meus Termos de uso** como **Conceder Controles**.

![Ferramenta de política “What If”](./media/require-tou/79.png)

**Para avaliar sua política de acesso condicional:**

1. Na página [Acesso Condicional – Políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), no menu superior, clique em **What If**.  

   ![What If](./media/require-tou/14.png)

1. Clique **Usuários**, selecione **Isabella Simonsen** e depois clique em **Selecionar**.

   ![Usuário](./media/require-tou/15.png)

1. Para selecionar um aplicativo de nuvem:

   ![Aplicativos na nuvem](./media/require-tou/16.png)

   1. Clique em **Aplicativos de nuvem**.
   1. Na **Página de aplicativos de nuvem**, clique em **Selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **Selecionar**, selecione **Microsoft Azure** e clique em **Selecionar**.
   1. Na página de aplicativos de nuvem, clique em **Concluído**.
1. Clique em **What If**.

## <a name="test-your-conditional-access-policy"></a>Teste sua política de acesso condicional

Na seção anterior, você aprendeu como avaliar uma entrada simulada. Além de uma simulação, você também deve testar sua política de acesso condicional para garantir que ela funcione conforme o esperado.

Para testar sua política, tente entrar no seu [portal do Azure](https://portal.azure.com) usando sua conta de teste **Isabella Simonsen**. Você deve ver uma caixa de diálogo que exige que você aceite os termos de uso.

![Termos de uso](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste e a política de acesso condicional:

- Caso não saiba como excluir um usuário do Azure AD, consulte [Excluir usuários do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para excluir sua política, selecione-a e clique em **Excluir** na barra de ferramentas de acesso rápido.

    ![Autenticação multifator](./media/require-tou/33.png)

- Para excluir os termos de uso, selecione-o e, em seguida, clique em **excluir termos** na barra de ferramentas na parte superior.

    ![Autenticação multifator](./media/require-tou/29.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exige MFA para aplicativos específicos](app-based-mfa.md)
> [Bloquear acesso quando é detectado um risco da sessão](app-sign-in-risk.md)
