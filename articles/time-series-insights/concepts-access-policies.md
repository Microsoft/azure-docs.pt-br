---
title: Configurar a segurança para conceder acesso a dados-Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar a segurança, as permissões e o gerenciamento de políticas de acesso a dados em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780851"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso de Azure Time Series Insights.

## <a name="sign-in-to-azure-time-series-insights"></a>Entrar no Azure Time Series Insights

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Localize seu ambiente de Azure Time Series Insights digitando `Time Series Insights environments` na caixa de **pesquisa** . Selecione `Time Series Insights environments` nos resultados da pesquisa.
1. Selecione seu ambiente de Azure Time Series Insights na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Adicionar**.

    [![Selecionar e adicionar uma política de acesso a dados](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Escolha **Selecionar usuário**. Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Selecione **selecionar** para confirmar a seleção.

    [![Selecione um usuário para adicionar](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Confirmar a função selecionada](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecione **OK** na página **Selecionar Função do Usuário**.

    [![Selecione OK na página Selecionar função de usuário](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Verifique se a página **Políticas de Acesso a Dados** lista os usuários e suas funções.

    [![Verificar os usuários e funções corretos](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornecer acesso de convidado de outro locatário do Azure AD

A `Guest` função não é uma função de gerenciamento. É um termo usado para uma conta que é convidada de um locatário para outro. Depois que a conta de convidado é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado a ela, como qualquer outra conta. Você pode conceder acesso de gerenciamento a um ambiente de Azure Time Series Insights usando a folha de controle de acesso (IAM). Ou pode permitir acesso aos dados no ambiente por meio da folha Políticas de Acesso a Dados. Para obter mais informações sobre o acesso de convidado de locatário do Azure AD (Azure Active Directory), leia [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](../active-directory/external-identities/add-users-administrator.md).

Siga estas etapas para conceder acesso de convidado a um ambiente de Azure Time Series Insights para um usuário do Azure AD de outro locatário.

1. Vá para portal do Azure, clique em  **Azure Active Directory**, role para baixo na guia **visão geral** e selecione **usuário convidado**.

    [![Selecionar políticas de acesso a dados e + convidar](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Insira o endereço de email do usuário que deseja convidar. Este endereço de email deve estar associado com o Azure AD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    [![Insira o endereço de email para localizar o usuário selecionado](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Procure a bolha de confirmação que aparece na tela. Você também pode clicar em **notificações** para confirmar se o usuário convidado foi adicionado.

    [![Procure a bolha de confirmação para aparecer](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Volte para o ambiente de Time Series Insights para adicionar o usuário convidado recém-criado. Clique em **políticas de acesso a dados** , conforme descrito em **conceder acesso a dados**. **Selecione usuário**. Pesquise o endereço de email do usuário convidado para localizar o usuário que deseja adicionar. Em seguida, **selecione** para confirmar a seleção.

    [![Selecionar o usuário e confirmar a seleção](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Confirme a opção de função](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecione **OK** na página **Selecionar Função do Usuário**.

1. Confirme se a página **Políticas de Acesso a Dados** lista o usuário convidado e as funções de cada usuário convidado.

    [![Verificar se os usuários e as funções estão atribuídos corretamente](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Agora, o usuário convidado receberá um email de convite no endereço de email especificado acima. O usuário **convidado selecionará começar a** confirmar sua aceitação e conectar-se à nuvem do Azure.

    [![Convidado seleciona introdução a aceitar](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Depois de **selecionar introdução**, o usuário convidado receberá uma caixa de permissões associada à organização do administrador. Ao conceder permissão selecionando **aceitar**, elas serão conectadas.

    [![Permissões e aceitações de resenhas de convidado](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. O administrador [compartilha a URL do ambiente](time-series-insights-parameterized-urls.md) com seu convidado.

1. Depois que o usuário convidado estiver conectado ao endereço de email que você usou para convidá-los e aceitar o convite, eles serão direcionados para portal do Azure.

1. O convidado agora pode acessar o ambiente compartilhado usando a URL do ambiente fornecida pelo administrador. Eles podem inserir essa URL em seu navegador da Web para acesso imediato.

1. O locatário do administrador será exibido para o usuário convidado depois de selecionar o ícone de perfil no canto superior direito do explorador da série temporal.

    [![Seleção de avatar em insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Depois que o usuário convidado selecionar o locatário do administrador, ele terá a capacidade de selecionar o ambiente de Azure Time Series Insights compartilhado.

    Agora, eles têm todos os recursos associados à função com a qual você os forneceu na **etapa 5**.

    [![O usuário convidado seleciona seu locatário do Azure na lista suspensa](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Leia [autenticação e autorização](time-series-insights-authentication-and-authorization.md) para Azure Active Directory etapas de registro do aplicativo.

* Exiba [seu ambiente no Azure Time Series insights Explorer](./concepts-ux-panels.md).
