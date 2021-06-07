---
title: Habilitar a redefinição de senha por autoatendimento do Microsoft Azure Active Directory
description: Neste tutorial, você aprenderá a habilitar a redefinição de senha por autoatendimento do Microsoft Azure Active Directory para um grupo de usuários e testar o processo de redefinição de senha.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39eec4fb6e9907b36908a87c09aceabd0dd1a678
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075159"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Tutorial: Permitir que os usuários desbloqueiem sua conta ou redefinam senhas usando a redefinição de senha por autoatendimento do Microsoft Azure Active Directory

A SSPR (redefinição de senha por autoatendimento) do Microsoft Azure AD (Active Directory) dá aos usuários a capacidade de alterar ou redefinir a senha, sem envolvimento do administrador ou do suporte técnico. Se a conta de um usuário for bloqueada pelo Azure AD ou se ele esquecer a senha, ele poderá seguir os avisos para desbloquear a si mesmo e voltar ao trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo. Recomendamos este vídeo sobre [como habilitar e configurar o SSPR no Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ). Também temos um vídeo para os administradores de TI sobre [como resolver as seis mensagens de erro mais comuns do usuário final com a SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> Este tutorial mostra a um administrador como habilitar a redefinição de senha por autoatendimento. Se você for um usuário final já registrado para redefinição de senha por autoatendimento e precisar voltar à sua conta, vá para a página [Redefinição de senha do Microsoft Online](https://passwordreset.microsoftonline.com/).
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Habilitar a redefinição de senha por autoatendimento para um grupo de usuários do Azure AD
> * Configurar métodos de autenticação e opções de registro
> * Testar o processo SSPR como um usuário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário do Azure AD em funcionamento com pelo menos uma licença de avaliação ou gratuita do Azure AD habilitada. Na camada Gratuita, o SSPR funciona apenas para usuários de nuvem no Azure AD.
    * Para os tutoriais posteriores desta série, uma licença Premium P1 ou de avaliação do Azure AD é necessária para o write-back de senha local.
    * Se necessário, [crie uma conta do Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de *Administrador Global*.
* Um usuário que não seja administrador com uma senha que você conheça, como *testuser*. Você testará a experiência de SSPR do usuário final usando essa conta neste tutorial.
    * Se você precisar criar um usuário, confira [Início rápido: Adicionar novos usuários ao Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Um grupo do qual o usuário não administrador seja membro, como *SSPR-Test-Group*. Você habilitará a SSPR para esse grupo neste tutorial.
    * Se você precisar criar um grupo, confira [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

O Azure AD permite que você habilite a SSPR para *Nenhum* usuário, usuários *Selecionados* ou *Todos* os usuários. Com essa capacidade granular, você pode escolher um subconjunto de usuários para testar o processo de registro e o fluxo de trabalho de SSPR. Quando você estiver familiarizado com o processo e puder comunicar os requisitos com um conjunto mais amplo de usuários, poderá selecionar um grupo de usuários para habilitar para SSPR. Ou, você pode habilitar a SSPR para todos no locatário do Azure AD.

> [!NOTE]
> No momento, você só pode habilitar um grupo do Azure AD para SSPR usando o portal do Azure. Como parte de uma implantação mais ampla da SSPR, o Azure AD dá suporte para grupos aninhados. Verifique se os usuários nos grupos que você escolher têm as licenças apropriadas atribuídas. No momento, não há nenhum processo de validação desses requisitos de licenciamento.

Neste tutorial, configure a SSPR para um conjunto de usuários em um grupo de teste. Use o *SSPR-Test-Group* e forneça seu próprio grupo do Azure AD, conforme necessário:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta com permissões de *administrador global*.
1. Pesquise e selecione **Azure Active Directory**. Em seguida, escolha **Redefinição de senha** no menu no lado esquerdo.
1. Na página **Propriedades**, na opção *Redefinição de senha por autoatendimento habilitada*, escolha **Selecionar grupo**
1. Procure e selecione um grupo do Azure AD, como *SSPR-Test-Group* e escolha *Selecionar*.

    [![Selecione um grupo no portal do Azure para habilitar a redefinição de senha por autoatendimento](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Para habilitar a SSPR para os usuários selecionados, selecione **Salvar**.

## <a name="select-authentication-methods-and-registration-options"></a>Selecionar métodos de autenticação e opções de registro

Quando os usuários precisam desbloquear a conta ou redefinir a senha, eles precisam fornecer um método de confirmação adicional. Esse fator de autenticação extra garante que o Azure AD concluiu apenas eventos SSPR aprovados. Você pode escolher quais métodos de autenticação permitir, com base nas informações de registro que o usuário fornece.

1. Na página **Métodos de autenticação** do menu no lado esquerdo, defina o **Número de métodos necessários para redefinir** para *1*.

    Para aprimorar a segurança, você pode aumentar o número de métodos de autenticação necessários para a SSPR.

1. Escolha os **Métodos disponíveis para os usuários** que sua organização deseja habilitar. Para este tutorial, marque as caixas para habilitar os seguintes métodos:

    * *Notificação de aplicativo móvel*
    * *Código do aplicativo móvel*
    * *Email*
    * *Celular*

    É possível habilitar métodos de autenticação adicionais, como *Telefone comercial* ou *Perguntas de segurança*, conforme necessário, para atender às suas necessidades empresariais.

1. Para aplicar os métodos de autenticação, selecione **Salvar**.

Antes que os usuários possam desbloquear sua conta ou redefinir uma senha, eles precisam registrar as informações de contato. O Azure AD usa essas informações de contato para os diferentes métodos de autenticação configurados nas etapas anteriores.

Um administrador pode fornecer manualmente essas informações de contato ou os usuários podem acessar um portal de registro para fornecer as informações por conta própria. Neste tutorial, configure o Azure AD para solicitar aos usuários o registro na próxima vez que entrarem.

1. Na página **Registro** do menu no lado esquerdo, selecione *Sim* para **Exigir que os usuários se registrem ao entrar**.
1. Defina **Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação** como *180*.

    É importante manter as informações de contato atualizadas. Se as informações de contato estiverem desatualizadas quando um evento de SSPR for iniciado, talvez o usuário não consiga desbloquear a conta ou redefinir a senha.

1. Para aplicar as configurações de registro, selecione **Salvar**.

## <a name="set-up-notifications-and-customizations"></a>Configurar notificações e personalizações

Para manter os usuários informados sobre a atividade da conta, você pode configurar o Azure AD para enviar notificações por email quando ocorrer um evento de SSPR. Essas notificações podem abranger contas de usuário e contas de administrador comuns. Para contas de administrador, essa notificação fornece uma camada adicional de reconhecimento quando uma senha de conta de administrador com privilégios é redefinida usando a SSPR. O Azure AD notificará todos os administradores globais quando alguém usar a SSPR em uma conta do administrador.

1. No menu no lado esquerdo da página **Notificações**, configure as seguintes opções:

   * Defina a opção **Notificar os usuários sobre as redefinições de senha** como *Sim*.
   * Defina **Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas** como *Sim*.

1. Para aplicar as preferências de notificação, selecione **Salvar**.

Se os usuários precisarem de ajuda adicional com o processo de SSPR, você poderá personalizar o link para "Entre em contato com seu administrador". O usuário pode selecionar esse link no processo de registro de SSPR e quando desbloquear conta ou redefinir a senha. Para garantir que os usuários obtenham o suporte necessário, é altamente recomendável fornecer um email ou uma URL de assistência técnica personalizada.

1. Na página **Personalização** do menu no lado esquerdo, defina **Personalizar link de assistência técnica** como *Sim*.
1. No campo **Email ou URL de assistência técnica personalizado**, forneça o endereço de email ou a URL da página da Web em que os usuários podem obter mais ajuda de sua organização, como *https:\//support.contoso.com/*
1. Para aplicar o link personalizado, selecione **Salvar**.

## <a name="test-self-service-password-reset"></a>Testar redefinição de senha de autoatendimento

Com a SSPR habilitada e configurada, teste o processo de SSPR com um usuário que faça parte do grupo selecionado na seção anterior, como *Test-SSPR-Group*. O exemplo a seguir usa a conta *testuser*. Forneça sua própria conta de usuário. Ela deve fazer parte do grupo que você habilitou para SSPR na primeira seção deste tutorial.

> [!NOTE]
> Ao testar a redefinição de senha por autoatendimento, use uma conta que não seja de administrador. Por padrão, o Azure AD habilita a redefinição de senha por autoatendimento para administradores. Eles devem usar dois métodos de autenticação para redefinir a própria senha. Para obter mais informações, confira [Diferenças da política de redefinição de senha de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Para ver o processo de registro manual, abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até *https:\//aka.ms/ssprsetup*. O Azure AD direcionará os usuários para este portal de registro quando eles entrarem na próxima vez.
1. Entre com um usuário de teste que não seja administrador, como *testuser*, e registre as informações de contato dos seus métodos de autenticação.
1. Uma vez concluído, selecione o botão marcado **Parece bom** e feche a janela do navegador.
1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até *https:\//aka.ms/sspr*.
1. Insira as informações da conta dos usuários de teste que não são administradores, como **testuser**, os caracteres do CAPTCHA e, em seguida, selecione *Avançar*.

    ![Inserir as informações da conta de usuário para redefinir a senha](media/tutorial-enable-sspr/password-reset-page.png)

1. Siga as etapas de verificação para redefinir sua senha. Quando terminar, você receberá uma notificação por email informando que sua senha foi redefinida.

## <a name="clean-up-resources"></a>Limpar os recursos

Em um tutorial posterior desta série, você vai configurar o write-back de senha. Esse recurso grava as alterações de senha da SSPR do Azure AD de volta em um ambiente do AD local. Se você quiser continuar com esta série de tutoriais para configurar o write-back de senha, não desabilite a SSPR agora.

Se não desejar mais usar a funcionalidade de SSPR configurada como parte deste tutorial, defina o status de SSPR como **Nenhum** usando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise e selecione **Azure Active Directory**. Em seguida, escolha **Redefinição de senha** no menu no lado esquerdo.
1. Na página **Propriedades**, na opção *Redefinição de senha por autoatendimento habilitada*, escolha **Nenhum**.
1. Para aplicar a alteração de SSPR, selecione **Salvar**.

## <a name="faqs"></a>Perguntas frequentes

Esta seção explica perguntas comuns de administradores e usuários finais que experimentam o SSPR:

- Por que usuários federados esperam até dois minutos após verem **Sua senha foi redefinida** até que possam usar senhas sincronizadas do local?

  Para usuários federados cujas senhas são sincronizadas, a origem da autoridade das senhas é local. Como resultado, o SSPR atualiza apenas as senhas locais. A sincronização de hash de senha com o Azure AD está agendada para ocorrer a cada dois minutos.

- Quando um usuário recém-criado que foi pré-populado com os dados do SSPR, como telefone e email, visita a página de registro do SSPR, **Não perca o acesso à sua conta!** aparece como título da página. Por que outros usuários que têm dados do SSPR pré-populados não veem a mensagem?

  Um usuário que vê **Não perca o acesso à sua conta!** é membro de grupos de registro de SSPR/combinado que estão configurados para o locatário. Usuários que não veem **Não perca o acesso à sua conta!** não fazem parte dos grupos de registro de SSPR/combinado.

- Quando alguns usuários passam pelo processo de SSPR e redefinem sua senha, por que eles não veem o indicador de força de senha?

  Os usuários que não veem a força de senha fraca/forte têm o write-back de senha sincronizado habilitado. Como o SSPR não pode determinar a política de senha do ambiente local do cliente, ele não pode validar a força ou a fraqueza da senha. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou a redefinição de senha por autoatendimento do Azure AD para um grupo de usuários selecionados. Você aprendeu a:

> [!div class="checklist"]
> * Habilitar a redefinição de senha por autoatendimento para um grupo de usuários do Azure AD
> * Configurar métodos de autenticação e opções de registro
> * Testar o processo SSPR como um usuário

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Azure AD](./tutorial-enable-azure-mfa.md)
