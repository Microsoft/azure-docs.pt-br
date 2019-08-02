---
title: Criar uma revisão de acesso de grupos ou aplicativos-Azure Active Directory | Microsoft Docs
description: Saiba como criar uma revisão de acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 230fb40c8e3a100d2fdfa0af6b40c93c3e5b47d2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499733"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos ou aplicativos em revisões de acesso do Azure AD

Acesso a grupos e aplicativos para funcionários e visitantes muda ao longo do tempo. Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar revisões de acesso para membros do grupo ou usuários atribuídos a um aplicativo. Se você precisar revisar rotineiramente o acesso, você também pode criar as revisões de acesso recorrentes. Para obter mais informações sobre esses cenários, consulte [Gerenciar acesso de usuário](manage-user-access-with-access-reviews.md) e [Gerenciar acesso de convidado](manage-guest-access-with-access-reviews.md).

Este artigo descreve como criar uma ou mais revisões de acesso para membros do grupo ou acesso ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de usuário

Para obter mais informações, consulte [quais usuários devem ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais revisões de acesso

1. Entre no portal do Azure e abra a [página governança de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu à esquerda, clique em **revisões de acesso**.

1. Clique em **Nova análise de acesso** para criar uma nova revisão de acesso.

    ![Painel de revisões de acesso no controle de identidade](./media/create-access-review/access-reviews.png)

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/create-access-review/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Criar uma revisão de acesso - Data inicial e final](./media/create-access-review/start-end-dates.png)

1. Para fazer com que a revisão de acesso seja recorrente, altere a configuração de **frequência** de **uma vez** para **semanal**, **mensal**, **trimestral** ou **anual**. Use o controle deslizante **duração** ou a caixa de texto para definir o número de dias que cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, outro administrador de usuário ou outro administrador global pode interromper a série após a criação alterando a data em **configurações**, de modo que ela termine nessa data.

1. Na seção **usuários** , especifique os usuários aos quais a revisão de acesso se aplica. As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo. Você pode detalhar ainda mais o escopo da análise de acesso para examinar apenas os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de examinar todos os usuários que são membros ou que têm acesso ao aplicativo.

    ![Criar uma revisão de acesso - Usuários](./media/create-access-review/users.png)

1. Na seção **grupo** , selecione um ou mais grupos dos quais você gostaria de examinar a associação.

    > [!NOTE]
    > A seleção de mais de um grupo criará várias revisões de acesso. Por exemplo, a seleção de cinco grupos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar grupo](./media/create-access-review/select-group.png)

1. Na seção **aplicativos** (se você selecionou **atribuído a um aplicativo** na etapa 8), selecione os aplicativos para os quais deseja revisar o acesso.

    > [!NOTE]
    > A seleção de mais de um aplicativo criará várias revisões de acesso. Por exemplo, a seleção de cinco aplicativos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar aplicativo](./media/create-access-review/select-application.png)

1. Na seção **Revisores**, selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos. Se o recurso for um grupo, você pode pedir que a revisão seja realizada pelos proprietários de grupo. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

    ![Criar uma revisão de acesso - Revisores](./media/create-access-review/reviewers.png)

1. Na seção **Programas**, selecione o programa que você deseja usar. O **Programa Padrão** sempre está presente.

    ![Criar uma revisão de acesso - Programas](./media/create-access-review/programs.png)

    É possível simplificar a maneira de rastrear e coletar revisões de acesso para finalidades diferentes, organizando-as em programas. Cada análise de acesso pode ser vinculada a um programa. Em seguida, ao preparar relatórios para um auditor, você poderá concentrar-se nas revisões de acesso no escopo de uma iniciativa específica. Os resultados de análise de programas e acesso são visíveis para os usuários no administrador global, administrador de usuários, administrador de segurança ou função de leitor de segurança.

    Para ver uma lista de programas, vá para a página revisões de acesso e selecione **programas**. Se você estiver em uma função de administrador global ou de administrador de usuários, poderá criar programas adicionais. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios. Se você não precisar mais de um programa e se ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Criar uma revisão de acesso-após as configurações de conclusão](./media/create-access-review/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **Resultados de aplicação automática ao recurso** para **Habilitar**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

1. Use a lista **Se o revisor não responder** para especificar o que acontece para usuários que não foram examinados pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

    ![Criar uma revisão de acesso – configurações avançadas](./media/create-access-review/advanced-settings.png)

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise.

    Por padrão, o Azure AD enviará automaticamente um lembrete após passar a metade do tempo para o prazo final para revisores que ainda não responderam.

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso e seu status](./media/create-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar a eles as instruções de como revisar o [acesso a grupos ou aplicativos](perform-access-review.md). Se sua análise for para convidados para revisar seu próprio acesso, mostre-lhes as instruções de como [examinar o acesso a grupos ou aplicativos](review-your-access.md).

Se você tiver atribuído convidados como revisores e eles não tiverem aceitado o convite, eles não receberão um email das revisões de acesso, pois eles devem primeiro aceitar o convite antes da revisão.

## <a name="create-reviews-via-apis"></a>Criar revisões via APIs

Você também pode criar as revisões de acesso usando as APIs. O que você faz para gerenciar as revisões de acesso dos grupos e usuários do aplicativo no portal do Azure também pode ser feito usando as APIs do Microsoft Graph. Para obter mais informações, consulte a [referência da API de revisões de acesso do Azure ad](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter um exemplo de código, consulte o [exemplo de recuperação de revisões de acesso do Azure ad via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Examinar o acesso a grupos ou aplicativos](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
