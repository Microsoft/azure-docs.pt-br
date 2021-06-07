---
title: Habilitar a Autenticação Multifator do Azure AD
description: Neste tutorial, você aprenderá a habilitar a Autenticação Multifator do Azure AD para um grupo de usuários e testar o prompt do fator secundário durante um evento de entrada.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e8bf2ccbf7a53563013c7ba653a6f6e8905337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881303"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Tutorial: Proteger eventos de entrada do usuário com a Autenticação Multifator do Azure AD

A MFA (autenticação multifator) é um processo em que um usuário é solicitado durante um evento de entrada para formas adicionais de identificação. Esse prompt pode ser o de inserir um código no celular ou fornecer uma digitalização de impressão digital. Quando você precisar de uma segunda forma de autenticação, a segurança será aprimorada, pois esse fator adicional não será algo fácil de ser obtido ou duplicado por um invasor.

As políticas de Acesso Condicional e Autenticação Multifator do Azure AD oferecem a flexibilidade para habilitar a MFA para usuários durante eventos de entrada específicos. Veja aqui um vídeo sobre [Como configurar e aplicar a autenticação multifator no locatário](https://www.youtube.com/watch?v=qNndxl7gqVM) (**recomendado**)

> [!IMPORTANT]
> Este tutorial mostra a um administrador como habilitar a Autenticação Multifator do Azure AD.
>
> Se a sua equipe de TI não tiver habilitado a capacidade de usar a Autenticação Multifator do Azure AD ou se você tiver problemas durante a entrada, entre em contato com o suporte técnico para obter assistência adicional.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma política de Acesso Condicional para habilitar a Autenticação Multifator do Azure AD para um grupo de usuários
> * Configurar as condições de política que solicitam MFA
> * Testar o processo de MFA como um usuário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário do Azure AD em funcionamento com pelo menos uma licença de avaliação ou Premium P1 do Azure AD habilitada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de *administrador global*.
* Um usuário que não seja administrador com uma senha que você conheça, como *testuser*. Você testa a experiência de Autenticação Multifator do Azure AD usando essa conta neste tutorial.
    * Se você precisar criar um usuário, confira [Início rápido: Adicionar novos usuários ao Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Um grupo do qual o usuário não administrador seja membro, como *MFA-Test-Group*. Você habilita a Autenticação Multifator do Azure AD para este grupo neste tutorial.
    * Se você precisar criar um grupo, confira como [Criar um grupo e adicionar membros no Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

A maneira recomendada de habilitar e usar a Autenticação Multifator do Azure AD é com políticas de Acesso Condicional. O Acesso Condicional permite criar e definir políticas que reagem à entrada de eventos e solicitam ações adicionais antes que um usuário tenha acesso a um aplicativo ou serviço.

![Diagrama de visão geral de como o Acesso Condicional funciona para proteger o processo de entrada](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

As políticas de Acesso Condicional podem ser granulares e específicas, com o objetivo de capacitar os usuários a serem produtivos onde e quando quer que seja, mas também proteger sua organização. Neste tutorial, vamos criar uma política básica de Acesso Condicional para solicitar a MFA quando um usuário entrar no portal do Azure. Em um tutorial posterior desta série, você configurará a Autenticação Multifator do Azure AD usando uma política de Acesso Condicional com base em risco.

Primeiro, crie uma política de Acesso Condicional e atribua seu grupo de usuários de teste da seguinte maneira:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta com permissões de *administrador global*.
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Segurança** no menu no lado esquerdo.
1. Selecione **Acesso Condicional** e, em seguida, escolha **+ Nova política**.
1. Insira um nome para a política, como *Piloto de MFA* .
1. Em **Atribuições**, escolha **Usuários e grupos** e, em seguida, o botão de opção **Selecionar usuários e grupos**.
1. Marque a caixa **Usuários e grupos** e **Selecionar** para procurar os usuários e grupos disponíveis do Azure AD.
1. Procure e selecione um grupo do Azure AD, como *MFA-Test-Group*, e escolha **Selecionar**.

    [ ![Selecione o seu grupo do Azure AD para usar com a política de Acesso Condicional](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Para aplicar a política de Acesso Condicional ao grupo, selecione **Concluído**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Configurar as condições para a autenticação multifator

Com a política de Acesso Condicional criada e um grupo de teste de usuários atribuído, agora defina os aplicativos ou ações na nuvem que acionam a política. Esses aplicativos ou ações na nuvem são os cenários que você decide que requerem processamento adicional, como solicitar MFA. Por exemplo, você pode decidir que o acesso a um aplicativo financeiro ou o uso de ferramentas de gerenciamento requer um prompt de verificação adicional.

Neste tutorial, configure a política de Acesso Condicional para exigir MFA quando um usuário entrar no portal do Azure.

1. Selecione **Aplicativos na nuvem ou ações**. Você pode optar por aplicar a política de acesso condicional a *Todos os aplicativos em nuvem* ou *Selecionar aplicativos*. Para fornecer flexibilidade, você também pode excluir certos aplicativos da política.

    Neste tutorial, na página *Incluir*, escolha o botão de opção **Selecionar aplicativos**.

1. Escolha **Selecionar** e procure a lista de eventos de entrada disponíveis que podem ser usados.

    Neste tutorial, escolha **Gerenciamento do Microsoft Azure** para que a política se aplique aos eventos de entrada no portal do Azure.

1. Para aplicar os aplicativos selecionados, escolha **Selecionar** e **Concluído**.

    ![Selecione o aplicativo Gerenciamento do Microsoft Azure para incluir na política de Acesso Condicional](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Os controles de acesso permitem definir os requisitos para a concessão de acesso ao usuário, como a necessidade de um aplicativo cliente aprovado ou o uso de um dispositivo em que o Azure AD Híbrido ingressou. Neste tutorial, configure os controles de acesso para exigir MFA durante um evento de entrada para o portal do Azure.

1. Em *Controles de acesso*, escolha **Conceder** e, em seguida, verifique se o botão de opção **Conceder acesso** está selecionado.
1. Marque a caixa de seleção **Exigir autenticação multifator** e escolha **Selecionar**.

As políticas de acesso condicional poderão ser definidas como *Somente relatório* se você quiser ver como a configuração afetaria os usuários ou *Desativada* se não desejar a política de uso no momento. Como um grupo de usuários de teste foi direcionado para este tutorial, vamos habilitar a política e testar a Autenticação Multifator do Azure AD.

1. Defina a alternância *Habilitar política* para **Ativado**.
1. Para aplicar a política de Acesso Condicional selecione **Criar**.

## <a name="test-azure-ad-multi-factor-authentication"></a>Testar a Autenticação Multifator do Azure AD

Vamos ver sua política de Acesso Condicional e a Autenticação Multifator do Azure AD em ação. Primeiro, entre em um recurso que não requer MFA, da seguinte maneira:

1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Entre com seu usuário de teste não administrador, como *testuser*. Não há nenhum prompt para você concluir a MFA.
1. Feche a janela do navegador.

Agora, entre no portal do Azure. Como o portal do Azure foi configurado na política de Acesso Condicional para exigir verificação adicional, você recebe um prompt de Autenticação Multifator do Azure AD.

1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até [https://portal.azure.com](https://portal.azure.com).
1. Entre com seu usuário de teste não administrador, como *testuser*. Você deve se registrar e usar a Autenticação Multifator do Azure AD. Siga os prompts para concluir o processo e verifique se você se inscreveu com êxito no portal do Azure.

    ![Siga os prompts do navegador e, em seguida, no prompt de autenticação multifator registrado, faça login](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Feche a janela do navegador.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser mais usar a política de Acesso Condicional para habilitar a Autenticação Multifator do Azure AD configurada como parte deste tutorial, exclua a política seguindo as etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Segurança** no menu no lado esquerdo.
1. Selecione **Acesso condicional** e escolha a política que você criou, como *Pilot de MFA*
1. Escolha **Excluir**, em seguida, confirme que deseja excluir a política.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou a Autenticação Multifator do Azure AD usando políticas de Acesso Condicional para um grupo selecionado de usuários. Você aprendeu a:

> [!div class="checklist"]
> * Criar uma política de Acesso Condicional para habilitar a Autenticação Multifator do Azure AD para um grupo de usuários do Azure AD
> * Configurar as condições de política que solicitam MFA
> * Testar o processo de MFA como um usuário

> [!div class="nextstepaction"]
> [Habilitar o write-back de senha na SSPR (redefinição de senha self-service)](./tutorial-enable-sspr-writeback.md)
