---
title: 'Tutorial: Autenticação multifator para B2B – Azure Active Directory | Microsoft Docs'
description: Saiba como exigir a MFA (autenticação multifator) quando você usa o Azure AD B2B para colaborar com usuários externos e organizações parceiras.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16a2438133f545c57d1046a0c4db94135f8a426d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113191"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Tutorial: Impor a autenticação multifator para usuários convidados de B2B

Ao colaborar com os usuários convidados de B2B externos, é uma boa ideia proteger seus aplicativos com políticas de MFA (autenticação multifator). Em seguida, os usuários externos precisarão de mais do que apenas um nome de usuário e senha para acessar os recursos. No Azure AD (Azure Active Directory), você pode atingir essa meta com uma política de acesso condicional que requer MFA para o acesso. As políticas de MFA podem ser impostas no nível do locatário, aplicativo ou usuário convidado individual, da mesma maneira que podem ser habilitadas para membros da sua própria organização.

Exemplo:

![Diagrama mostrando um usuário convidado entrando em aplicativos da empresa](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Um administrador ou um funcionário na empresa A convida um usuário convidado para usar uma nuvem ou um aplicativo local que está configurado para exigir MFA para acesso.
2.  O usuário convidado entra com as próprias identidades empresariais, estudantis ou sociais. 
3.  O usuário é solicitado a concluir um desafio de MFA. 
4.  O usuário configura sua MFA com a empresa A e escolhe a opção de MFA. O usuário tem permissão de acesso ao aplicativo.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Teste a experiência de entrada antes da configuração da MFA.
> * Crie uma política de acesso condicional que exige MFA para acesso a um aplicativo de nuvem em seu ambiente. Neste tutorial, vamos usar o aplicativo de gerenciamento do Microsoft Azure para ilustrar o processo.
> * Use a ferramenta What If para simular a entrada da MFA.
> * Teste sua política de acesso condicional.
> * Limpe o usuário de teste e a política.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, você precisa de:

 - **Acesso à edição do Azure AD Premium**, que inclui recursos de política de acesso condicional. Para impor a MFA, você precisa criar uma política de acesso condicional do Azure AD. Observe que as políticas de MFA são sempre impostas em sua organização, independentemente de se o parceiro tem recursos de MFA. Se você configurar a MFA para sua organização, precisará garantir que você tem licenças suficientes do Azure AD Premium para os usuários convidados. 
 - **Uma conta de email externo válido** que você pode adicionar ao seu diretório de locatário como um usuário convidado e usar para entrar. Se você não souber como criar uma conta de convidado, confira [Adicionar um usuário convidado de B2B no portal do Azure](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Criar um usuário convidado de teste no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerenciar**, selecione **Usuários**.
4.  Selecione **Novo usuário convidado**.

    ![Captura de tela mostrando onde selecionar a opção Novo usuário convidado](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  Em **Nome de usuário**, insira o endereço de email do usuário externo. Opcionalmente, inclua uma mensagem de boas-vindas. 

    ![Captura de tela mostrando onde inserir a mensagem de convite do convidado](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Selecione **Convidar** para enviar automaticamente o convite ao usuário convidado. Uma mensagem **Usuário convidado com êxito** será exibida. 
7.  Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testar a experiência de entrada antes da configuração da MFA
1.  Use seu nome de usuário de teste e senha para entrar no [portal do Azure](https://portal.azure.com/).
2.  Observe que você é capaz de acessar o portal do Azure usando apenas suas credenciais de entrada. Não é necessária nenhuma autenticação adicional.
3.  Saia.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Criar uma política de acesso condicional que exige MFA
1.  Entre no [portal do Azure](https://portal.azure.com/) como administrador de segurança ou como administrador de acesso condicional.
2.  No portal do Azure, selecione **Azure Active Directory**. 
3.  Na página do **Azure Active Directory**, na seção **Segurança**, selecione **Acesso condicional**.
4.  Na página **Acesso Condicional**, na barra de ferramentas na parte superior, selecione **Nova política**.
5.  Na página **Novo**, na caixa de texto **Nome**, digite **Exigir MFA para acesso ao portal do B2B**.
6.  Na seção **Atribuições**, selecione **Usuários e grupos**.
7.  Na página **Usuários e grupos**, escolha **Selecionar usuários e grupos** e, em seguida, selecione **Todos os usuários convidados (visualização)** .

    ![Captura de tela mostrando a seleção de todos os usuários convidados](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Selecione **Concluído**.
10. Na página **Novo**, na seção **Atribuições**, selecione **Aplicativos de nuvem**.
11. Na página **Aplicativos de nuvem**, escolha **Selecionar aplicativos** e, em seguida, escolha **Selecionar**.

    ![Captura de tela mostrando a página de aplicativos de nuvem e a opção Selecionar](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Na página **Selecionar**, escolha **Gerenciamento do Microsoft Azure** e escolha **Selecionar**.

    ![Captura de tela mostrando o aplicativo de Gerenciamento do Microsoft Azure selecionado](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Na página **Aplicativos de nuvem**, selecione **Concluído**.
14. Na página **Novo**, na seção **Controles de acesso**, selecione **Conceder**.
15. Na página **Conceder**, escolha **Conceder acesso**, marque a caixa de seleção **Exigir autenticação multifator** e escolha **Selecionar**.

    ![Captura de tela mostrando a opção de autenticação multifator](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. Em **Habilitar política**, selecione **Ativar**.

    ![Captura de tela mostrando a opção da política Habilitar definida como ativada](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Selecione **Criar**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Use a opção What If para simular a entrada

1.  Na página **Acesso condicional – Políticas**, selecione **What If**. 

    ![Captura de tela mostrando onde selecionar a opção What if](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Selecione **Usuário**, escolha seu usuário convidado de teste e escolha **Selecionar**.

    ![Captura de tela mostrando um usuário convidado selecionado](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Selecione **Aplicativos de nuvem**.
4.  Na página **Aplicativos de nuvem**, escolha **Selecionar aplicativos** e, em seguida, clique em **Selecionar**. Na lista de aplicativos, selecione **Gerenciamento do Microsoft Azure** e clique em **Selecionar**. 

    ![Captura de tela mostrando o aplicativo de Gerenciamento do Microsoft Azure selecionado](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Na página **Aplicativos de nuvem**, selecione **Concluído**.
6.  Selecione **What If** e verifique se a nova política aparece nos **Resultados da avaliação** na guia **Políticas que serão aplicadas**.

    ![Captura de tela mostrando onde selecionar a opção What if](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Teste sua política de acesso condicional
1.  Use seu nome de usuário de teste e senha para entrar no [portal do Azure](https://portal.azure.com/).
2.  Você deve ver uma solicitação para métodos de autenticação adicionais. Observe que pode levar algum tempo para que a política entre em vigor.

    ![Captura de tela mostrando a mensagem Mais informações necessárias](media/tutorial-mfa/mfa-required.png)
 
3.  Saia.

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, remova o usuário de teste e a política de acesso condicional de teste.
1.  Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2.  No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerenciar**, selecione **Usuários**.
4.  Selecione o usuário de teste e, em seguida, selecione **Excluir usuário**.
5.  No painel esquerdo, selecione **Azure Active Directory**.
6.  Em **Segurança**, selecione **Acesso Condicional**.
7.  Na lista **Nome da Política**, selecione o menu de contexto (...) da sua política de teste e, em seguida, selecione **Excluir**. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou uma política de acesso condicional que exige que os usuários convidados usem MFA ao entrar em um dos seus aplicativos de nuvem. Para saber mais sobre como adicionar usuários convidados para colaboração, confira [Adicionar usuários de colaboração do Azure Active Directory B2B no portal do Azure](add-users-administrator.md).
