---
title: Habilitar a redefinição de senha por autoatendimento do Microsoft Azure Active Directory
description: Neste tutorial, você aprenderá a habilitar a redefinição de senha por autoatendimento do Microsoft Azure Active Directory para um grupo de usuários e testar o processo de redefinição de senha.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 924ff6091e7eb6633d36f75fd6d53745139454d7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741058"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Tutorial: Permitir que os usuários desbloqueiem sua conta ou redefinam senhas usando a redefinição de senha por autoatendimento do Microsoft Azure Active Directory

A SSPR (redefinição de senha por autoatendimento) do Microsoft Azure AD (Active Directory) dá aos usuários a capacidade de alterar ou redefinir a senha, sem envolvimento do administrador ou do suporte técnico. Se a conta de um usuário estiver bloqueada ou se ele esquecer a senha, ele poderá seguir os avisos para desbloquear a si mesmo e voltar ao trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo.

> [!IMPORTANT]
> Este tutorial mostra a um administrador como habilitar a redefinição de senha por autoatendimento. Se você for um usuário final já registrado para redefinição de senha por autoatendimento e precisar voltar à sua conta, vá para https://aka.ms/sspr.
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Habilitar a redefinição de senha por autoatendimento para um grupo de usuários do Azure AD
> * Configurar métodos de autenticação e opções de registro
> * Testar o processo SSPR como um usuário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário do Azure AD em funcionamento com pelo menos uma licença de avaliação ou Gratuita do Azure AD habilitada. Na camada Gratuita, o SSPR funciona apenas para usuários de nuvem no Azure AD.
    * Para os tutoriais posteriores desta série, uma licença Premium P1 ou de avaliação do Azure AD é necessária para o write-back de senha local.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de *Administrador Global*.
* Um usuário que não seja administrador com uma senha que você conheça, como *testuser*. Você testa a experiência de SSPR do usuário final usando essa conta neste tutorial.
    * Se você precisar criar um usuário, confira [Início rápido: Adicionar novos usuários ao Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Um grupo do qual o usuário não administrador seja membro, como *SSPR-Test-Group*. Você habilita a SSPR para esse grupo neste tutorial.
    * Se você precisar criar um grupo, confira como [Criar um grupo e adicionar membros no Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

O Azure AD permite que você habilite a SSPR para *Nenhum* usuário, usuários *Selecionados* ou *Todos* os usuários. Com essa capacidade granular, você pode escolher um subconjunto de usuários para testar o processo de registro e o fluxo de trabalho de SSPR. Quando você estiver familiarizado com o processo e puder comunicar os requisitos com um conjunto mais amplo de usuários, poderá selecionar um grupo de usuários para habilitar para SSPR. Ou, você pode habilitar a SSPR para todos no locatário do Azure AD.

> [!NOTE]
>
> No momento, só é possível habilitar um grupo do Azure AD para SSPR usando o portal do Azure. Como parte de uma implantação mais ampla da SSPR, há suporte para grupos aninhados. Verifique se os usuários nos grupos que você escolher têm as licenças apropriadas atribuídas. No momento, não há nenhum processo de validação desses requisitos de licenciamento.

Neste tutorial, você configurará a SSPR para um conjunto de usuários em um grupo de teste. No exemplo a seguir, o grupo *SSPR-Test-Group* é usado. Forneça seu grupo do Azure AD, conforme necessário:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta com permissões de *administrador global*.
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Redefinição de senha** no menu no lado esquerdo.
1. Na página **Propriedades**, na opção *Redefinição de senha por autoatendimento habilitada*, escolha **Selecionar grupo**
1. Procure e selecione um grupo do Azure AD, como *SSPR-Test-Group*, e escolha *Selecionar*.

    [ ![Selecione um grupo no portal do Azure para habilitar a redefinição de senha por autoatendimento](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) ](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Para habilitar a SSPR para os usuários selecionados, selecione **Salvar**.

## <a name="select-authentication-methods-and-registration-options"></a>Selecionar métodos de autenticação e opções de registro

Quando os usuários precisam desbloquear a conta ou redefinir a senha, eles precisam fornecer um método de confirmação adicional. Esse fator de autenticação adicional garante que apenas eventos de SSPR aprovados sejam concluídos. Você pode escolher quais métodos de autenticação permitir, com base nas informações de registro que o usuário fornece.

1. Na página **Métodos de autenticação** do menu no lado esquerdo, defina o **Número de métodos necessários para redefinir** para *1*.

    Para aprimorar a segurança, você pode aumentar o número de métodos de autenticação necessários para a SSPR.

1. Escolha os **Métodos disponíveis para os usuários** que sua organização deseja habilitar. Para este tutorial, marque as caixas para habilitar os seguintes métodos:

    * *Notificação de aplicativo móvel*
    * *Código do aplicativo móvel*
    * *Email*
    * *Celular*

    Os métodos de autenticação adicionais, como *Telefone comercial* ou *Perguntas de segurança*, podem ser habilitados, conforme necessário, para atender às suas necessidades de negócios.

1. Para aplicar os métodos de autenticação, selecione **Salvar**.

Antes que os usuários possam desbloquear sua conta ou redefinir uma senha, eles precisam registrar as informações de contato. Essas informações de contato são usadas para os diferentes métodos de autenticação configurados nas etapas anteriores.

Um administrador pode fornecer manualmente essas informações de contato ou os usuários podem acessar um portal de registro para fornecer as informações por conta própria. Neste tutorial, configure os usuários para que o registro seja solicitado na próxima vez que entrarem.

1. Na página **Registro** do menu no lado esquerdo, selecione *Sim* para **Exigir que os usuários se registrem ao entrar**.
1. É importante que as informações de contato sejam mantidas atualizadas. Se as informações de contato estiverem desatualizadas quando um evento de SSPR for iniciado, talvez o usuário não consiga desbloquear sua conta ou redefinir a senha.

    Defina **Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação** como *180*.
1. Para aplicar as configurações de registro, selecione **Salvar**.

## <a name="configure-notifications-and-customizations"></a>Configurar notificações e personalizações

Para manter os usuários informados sobre a atividade da conta, você pode configurar notificações por email a serem enviadas quando ocorrer um evento de SSPR. Essas notificações podem abranger contas de usuário e contas de administrador comuns. Para contas de administrador, essa notificação fornece uma camada adicional de reconhecimento quando uma senha de conta de administrador privilegiada é redefinida usando SSPR. Todos os administradores globais serão notificados quando a SSPR for usada em uma conta do administrador.

1. Na página **Notificações** do menu no lado esquerdo, configure as seguintes opções:

   * Defina a opção **Notificar os usuários sobre as redefinições de senha** como *Sim*.
   * Defina **Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas** como *Sim*.

1. Para aplicar as preferências de notificação, selecione **Salvar**.

Se os usuários precisarem de ajuda adicional com o processo de SSPR, você poderá personalizar o link para "Entre em contato com seu administrador". Esse link é usado no processo de registro de SSPR e quando um usuário desbloqueia sua conta ou redefine a senha. Para garantir que os usuários obtenham o suporte necessário, é altamente recomendável fornecer um email ou uma URL de assistência técnica personalizados.

1. Na página **Personalização** do menu no lado esquerdo, defina *Personalizar link de assistência técnica* como **Sim**.
1. No campo **Email ou URL de assistência técnica personalizados**, forneça o endereço de email ou a URL da página da Web em que os usuários possam obter mais ajuda de sua organização, como *`https://support.contoso.com/`*
1. Para aplicar o link personalizado, selecione **Salvar**.

## <a name="test-self-service-password-reset"></a>Testar redefinição de senha de autoatendimento

Com a SSPR habilitada e configurada, teste o processo de SSPR com um usuário que faça parte do grupo selecionado na seção anterior, como *Test-SSPR-Group*. No exemplo a seguir, é usada a conta *testuser*. Forneça uma conta de usuário própria que faça parte do grupo que você habilitou para SSPR na primeira seção deste tutorial.

> [!NOTE]
> Ao testar a redefinição de senha por autoatendimento, use uma conta que não seja de administrador. Por padrão, os administradores estão habilitados para obter a redefinição de senha por autoatendimento e deverão usar dois métodos de autenticação para redefinir a senha. Para obter mais informações, confira [Diferenças da política de redefinição de senha de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Para ver o processo de registro manual, abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Os usuários devem ser direcionados para esse portal de registro na próxima vez que entrarem.
1. Entre com um usuário de teste que não seja administrador, como *testuser*, e registre as informações de contato dos seus métodos de autenticação.
1. Uma vez concluído, selecione o botão marcado **Parece bom** e feche a janela do navegador.
1. Abra uma nova janela do navegador no modo InPrivate ou anônimo e navegue até [https://aka.ms/sspr](https://aka.ms/sspr).
1. Insira as informações da conta dos usuários de teste que não são administradores, como **testuser**, os caracteres do CAPTCHA e, em seguida, selecione *Avançar*.

    ![Inserir as informações da conta de usuário para redefinir a senha](media/tutorial-enable-sspr/password-reset-page.png)

1. Siga as etapas de verificação para redefinir sua senha. Ao concluir, você deverá receber uma notificação por email de que sua senha foi redefinida.

## <a name="clean-up-resources"></a>Limpar os recursos

Em um tutorial a seguir nesta série, você configurará o write-back de senha. Esse recurso grava as alterações de senha da SSPR do Azure AD de volta em um ambiente do AD local. Se você quiser continuar com esta série de tutoriais para configurar o write-back de senha, não desabilite a SSPR agora.

Se não desejar mais usar a funcionalidade de SSPR configurada como parte deste tutorial, defina o status de SSPR como **Nenhum** usando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Redefinição de senha** no menu no lado esquerdo.
1. Na página **Propriedades**, na opção *Redefinição de senha por autoatendimento habilitada*, escolha **Nenhum**.
1. Para aplicar a alteração de SSPR, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou a redefinição de senha por autoatendimento do Azure AD para um grupo de usuários selecionados. Você aprendeu a:

> [!div class="checklist"]
> * Habilitar a redefinição de senha por autoatendimento para um grupo de usuários do Azure AD
> * Configurar métodos de autenticação e opções de registro
> * Testar o processo SSPR como um usuário

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Azure AD](./tutorial-enable-azure-mfa.md)
