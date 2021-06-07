---
title: Proteção de entrada do usuário baseada em risco no Azure Active Directory
description: Neste tutorial, você aprenderá a habilitar o Azure Identity Protection para proteger usuários quando o comportamento de entrada suspeita foi detectado na conta deles.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741331"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Tutorial: Usar detecções de risco para entradas de usuário para disparar a Autenticação Multifator do Azure AD ou alterações de senha

Para proteger seus usuários, você pode configurar políticas baseadas em risco no Azure AD (Azure Active Directory) que respondem automaticamente a comportamentos suspeitos. As políticas do Azure AD Identity Protection podem bloquear automaticamente uma tentativa de entrada ou exigir ação adicional, como exigir uma alteração de senha ou solicitar a Autenticação Multifator do Azure AD. Essas políticas funcionam com as políticas de Acesso Condicional do Azure AD existentes como uma camada extra de proteção para a sua organização. Talvez os usuários nunca disparem um comportamento suspeito em uma dessas políticas, mas sua organização estará protegida se for realizada uma tentativa de comprometer sua segurança.

> [!IMPORTANT]
> Este tutorial mostra a um administrador como habilitar a Autenticação Multifator do Azure AD baseada em risco.
>
> Se a sua equipe de TI não tiver habilitado a capacidade de usar a Autenticação Multifator do Azure AD ou se você tiver problemas durante a entrada, entre em contato com o suporte técnico para obter assistência adicional.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Noções básicas sobre as políticas disponíveis para o Azure AD Identity Protection
> * Habilitar o registro da Autenticação Multifator do Azure AD
> * Habilitar alterações de senha com base em risco
> * Habilitar Autenticação Multifator baseada em risco
> * Testar políticas baseadas em risco para tentativas de entrada do usuário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário do Azure AD em funcionamento com pelo menos uma licença de avaliação ou Premium P2 do Azure AD habilitada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de *Administrador Global*.
* Azure AD configurado para redefinição de senha por autoatendimento e Autenticação Multifator do Azure AD
    * Se necessário, [conclua o tutorial para habilitar o SSPR do Azure AD](tutorial-enable-sspr.md).
    * Se necessário, [conclua o tutorial para habilitar a Autenticação Multifator do Azure AD](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Visão geral do Azure AD Identity Protection

Todos os dias, a Microsoft coleta e analisa trilhões de sinais anônimos como parte das tentativas de entrada do usuário. Esses sinais ajudam a criar padrões de bom comportamento de entrada do usuário e a identificar possíveis tentativas de entrada suspeita. O Azure AD Identity Protection poderá analisar as tentativas de entrada do usuário e executar uma ação adicional se houver um comportamento suspeito:

Algumas das seguintes ações podem disparar detecção de risco do Azure AD Identity Protection:

* Usuários com credenciais vazadas.
* Entradas de endereços de IP anônimos.
* Viagem impossível a locais atípicos.
* Entradas de dispositivos infectados.
* Entradas de endereços de IP com atividade suspeita.
* Entradas de locais desconhecidos.

As três políticas a seguir estão disponíveis no Azure AD Identity Protection para proteger os usuários e responder a atividades suspeitas. Você pode optar por ativar ou desativar a imposição de política, selecionar usuários ou grupos aos quais a política será aplicada e decidir se deseja bloquear o acesso na entrada ou solicitar ação adicional.

* Política de risco do usuário
    * Identifica contas de usuário que podem ter credenciais comprometidas e reponde a elas. Pode solicitar que o usuário crie uma senha.
* A política de risco de entrada
    * Identifica tentativas de entrada suspeitas e responde a elas. Pode solicitar que o usuário forneça outras formas de verificação usando a Autenticação Multifator do Azure AD.
* Política de registro de MFA
    * Verifica se os usuários estão registrados na Autenticação Multifator do Azure AD. Se uma política de risco de entrada solicitar a MFA, o usuário já precisará estar registrado na Autenticação Multifator do Azure AD.

Ao habilitar uma política de risco do usuário ou de entrada, também você pode escolher o limite para o nível do risco – *baixo e superior*, *médio e superior* ou *alto*. Com essa flexibilidade, é possível decidir o nível de agressividade que você deseja ter para impor controles para eventos de entrada suspeitos.

Para obter mais informações sobre o Azure AD Identity Protection, confira [O que é o Azure AD Identity Protection?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Habilitar a política de registro com MFA

O Azure AD Identity Protection inclui uma política padrão que pode ajudar a registrar os usuários na Autenticação Multifator do Azure AD. Se você usar políticas adicionais para proteger eventos de entrada, será necessário que os usuários já tenham se registrado na MFA. Quando você habilita essa política, ela não exige que os usuários executem a MFA em cada evento de entrada. A política verifica apenas o status de registro de um usuário e solicita que ele se registre previamente se necessário.

É recomendável habilitar a política de registro com MFA para os usuários que devem ser habilitados para políticas do Azure AD Identity Protection. Para habilitar essa política, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Pesquise e selecione **Azure Active Directory**, selecione **Segurança** e, em seguida, no título do menu *Proteger*, escolha **Identity Protection**.
1. Selecione a **Política de registro com MFA** no menu esquerdo.
1. Por padrão, a política se aplica a *Todos os usuários*. Se desejado, selecione **Atribuições** e escolha os usuários ou grupos aos quais aplicar a política.
1. Em *Controles*, selecione **Acesso**. Verifique se a opção *Exigir registro da MFA do Azure AD* está selecionada e, em seguida, escolha **Selecionar**.
1. Defina **Impor Política** como *Ativado* e selecione **Salvar**.

    ![Captura de tela de como exigir que os usuários se registrem no MFA no portal do Azure](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Habilitar a política de risco do usuário para alteração de senha

A Microsoft trabalha com pesquisadores, autoridades, várias equipes de segurança da Microsoft e outras fontes confiáveis para localizar os pares de nome de usuário e senha. Quando um desses pares corresponde a uma conta em seu ambiente, uma alteração de senha baseada em risco pode ser solicitada. Essa política e ação exigem que o usuário atualize a senha dele antes de poder entrar para verificar se as credenciais expostas anteriormente não funcionam mais.

Para habilitar essa política, conclua as seguintes etapas:

1. Selecione a **Política de risco do usuário** no menu esquerdo.
1. Por padrão, a política se aplica a *Todos os usuários*. Se desejado, selecione **Atribuições** e escolha os usuários ou grupos aos quais aplicar a política.
1. Em *Condições*, escolha **Selecionar as condições > Selecionar um nível de risco** e escolha *Médio e acima*.
1. Escolha **Selecionar** e **Concluído**.
1. Em *Acessar*, selecione **Acessar**. Verifique se as opções **Permitir acesso** e *Exigir alteração de senha* estão selecionadas e, em seguida, escolha **Selecionar**.
1. Defina **Impor Política** como *Ativado* e selecione **Salvar**.

    ![Captura de tela de como habilitar a política de risco do usuário no portal do Azure](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Habilitar política de risco de entrada para MFA

A maioria dos usuários tem um comportamento normal que pode ser acompanhado. Quando eles não se enquadram nessa norma, talvez seja arriscado permitir que eles se conectem com êxito. Em vez disso, talvez seja interessante bloquear esse usuário ou pedir que ele execute uma autenticação multifator. Se o usuário concluir com êxito o desafio da MFA, você poderá considerar uma tentativa de entrada válida e permitir acesso ao aplicativo ou ao serviço.

Para habilitar essa política, conclua as seguintes etapas:

1. Selecione a **Política de risco de entrada** no menu esquerdo.
1. Por padrão, a política se aplica a *Todos os usuários*. Se desejado, selecione **Atribuições** e escolha os usuários ou grupos aos quais aplicar a política.
1. Em *Condições*, escolha **Selecionar as condições > Selecionar um nível de risco** e escolha *Médio e acima*.
1. Escolha **Selecionar** e **Concluído**.
1. Em *Acesso*, escolha **Selecionar um controle**. Verifique se as opções **Permitir acesso** e *Exigir autenticação multifator* estão selecionadas e, em seguida, escolha **Selecionar**.
1. Defina **Impor Política** como *Ativado* e selecione **Salvar**.

    ![Captura de tela de como habilitar a política de risco de entrada no portal do Azure](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Testar eventos de entrada suspeita

A maioria dos eventos de entrada do usuário não disparará as políticas baseadas em risco configuradas nas etapas anteriores. Um usuário talvez nunca veja uma solicitação de MFA adicional ou redefina a senha dele. Se as credenciais dele permanecerem seguras e o comportamento for consistente, os eventos de entrada serão bem-sucedidos.

Para testar as políticas do Azure AD Identity Protection criadas nas etapas anteriores, você precisa de uma forma de simular o comportamento suspeito ou eventuais ataques. As etapas para realizar esses testes variam com base na política do Azure AD Identity Protection que você deseja validar. Para obter mais informações sobre cenários e etapas, confira [Simular detecções de risco no Azure AD Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você tiver concluído os testes e não desejar mais ter as políticas baseadas em risco habilitadas, volte para cada política que deseja desabilitar e defina **Impor política** como *Desativado*.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou políticas de usuário baseadas em risco para o Azure AD Identity Protection. Você aprendeu a:

> [!div class="checklist"]
> * Noções básicas sobre as políticas disponíveis para o Azure AD Identity Protection
> * Habilitar o registro da Autenticação Multifator do Azure AD
> * Habilitar alterações de senha com base em risco
> * Habilitar Autenticação Multifator baseada em risco
> * Testar políticas baseadas em risco para tentativas de entrada do usuário

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
