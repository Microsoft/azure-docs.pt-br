---
title: Criar uma revisão de acesso das funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções do Azure AD no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 4/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aba8d9de5e068cd98675f67cb26b0eac8d1ad6d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552811"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções do Azure AD no Privileged Identity Management

Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar regularmente o acesso. Use o Azure AD PIM (Privileged Identity Management) para criar revisões de acesso para funções privilegiadas do Azure AD. Também é possível configurar revisões de acesso recorrentes que ocorrem de modo automático.

Este artigo descreve como criar uma ou mais revisões de acesso para funções privilegiadas do Azure AD.

## <a name="prerequisite-license"></a>Licença de pré-requisito

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. Para saber mais sobre as licenças do PIM, veja [Requisitos de licença para usar o Privileged Identity Management](subscription-requirements.md).

> [!Note]
>  No momento, você pode fazer o escopo de uma revisão de acesso às entidades de serviço com acesso ao Azure AD e às funções de recurso do Azure (versão prévia) com uma edição do Azure Active Directory Premium P2 ativa em seu locatário. O modelo de licenciamento para entidades de serviço será finalizado para disponibilidade geral desse recurso e licenças adicionais poderão ser necessárias.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador de Função com Privilégios](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Abrir revisões de acesso

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função Administrador de funções com privilégios.

1. Selecione **Governança de Identidade**.
 
1. Selecione **funções do Azure AD** em **Azure AD Privileged Identity Management**.
 
1. Selecione **funções do Azure AD** novamente em **Gerenciar**.

1. Em "Gerenciar", selecione **Revisões de acesso** e **Novo**.

    ![Funções do Azure AD – Lista de revisões de acesso mostrando o status de todas as revisões](./media/pim-how-to-start-security-review/access-reviews.png)

Clique em **Novo** para criar uma revisão de acesso.

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/pim-how-to-start-security-review/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Data de início, frequência, duração, fim, número de vezes e data de término](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Para fazer com que a revisão de acesso seja recorrente, altere a configuração **Frequência** de **Uma vez** para **Semanal**, **Mensal**, **Trimestral**, **Anual** ou **Semestral**. Use o controle deslizante **Duração** ou caixa de texto para definir por quantos dias cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, outro usuário administrador ou outro administrador global pode interromper a série após a criação, alterando a data em **Configurações** para que ela encerre nessa data.

1. Na seção **Escopo dos Usuários**, selecione o escopo da revisão. Para revisar usuários e grupos com acesso à função do Azure AD, selecione **Usuários e Grupos** ou **(Versão Prévia) Entidades de Serviço** para revisar as contas de computador com acesso à função do Azure AD.

    ![Escopo dos usuários dos quais examinar a associação](./media/pim-how-to-start-security-review/users.png)

1. Em **Analisar associação de função**, selecione as funções privilegiadas do Azure AD a serem revisadas. 

    > [!NOTE]
    > - As funções selecionadas aqui incluem [funções permanentes e qualificadas](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - A seleção de mais de uma função criará várias revisões de acesso. Por exemplo, a seleção de cinco funções criará cinco revisões de acesso separadas.
    > - Para funções com grupos atribuídos, o acesso de cada grupo associado à função em revisão será revisado como parte da revisão de acesso.
    Se você estiver criando uma revisão de acesso de **funções do Azure AD**, a imagem a seguir mostra um exemplo da lista Examinar associação.

    ![Examine o painel de associação listando as funções do Azure AD que você pode selecionar](./media/pim-how-to-start-security-review/review-membership.png)

    Se você estiver criando uma revisão de acesso de **funções de recurso do Azure**, a imagem a seguir mostra um exemplo da lista Examinar associação.

    ![Examine o painel de associação listando as funções de recurso do Azure que você pode selecionar](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. Na seção **Revisores**, selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos.

    ![Lista de revisores de usuários ou membros selecionados (próprio)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Usuários selecionados**: use esta opção para designar um usuário específico para concluir a revisão. Essa opção está disponível independentemente do escopo da revisão, e os revisores selecionados podem revisar usuários, grupos e entidades de serviço. 
    - **Membros (próprio)** – Use essa opção para fazer com que os usuários examinem suas próprias atribuições de função. Os grupos atribuídos à função não serão parte da revisão quando essa opção estiver selecionada. Essa opção só estará disponível se a revisão estiver no escopo de **Usuários e Grupos**.
    - **Gerenciador**: use essa opção para fazer com que o gerenciador do usuário revise a atribuição de função dele. Essa opção só estará disponível se a revisão estiver no escopo de **Usuários e Grupos**. Após selecionar o Gerenciador, você também terá a opção de especificar um revisor de fallback. Os revisores de fallback são solicitados a fazer uma revisão quando o usuário não tem gerenciadores especificados no diretório. Os grupos atribuídos à função serão revisados pelo revisor de fallback se um estiver selecionado. 

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Após configurações de conclusão para aplicação automática e Se o revisor não responder](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **Resultados de aplicação automática ao recurso** para **Habilitar**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

1. Use a lista **Se o revisor não responder** para especificar o que acontece para usuários que não foram examinados pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

    ![Configurações avançadas para mostrar recomendações, requer motivo sob aprovação, notificações por email e lembretes](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise.
1. O conteúdo do email enviado aos revisores é gerado automaticamente com base nos detalhes, como o nome da revisão, o nome do recurso, a data de conclusão etc. Se você precisar comunicar informações adicionais, como instruções adicionais ou informações de contato, especifique esses detalhes na seção **Conteúdo adicional para o email do revisor**, que será incluída nos emails de convite e lembrete enviado aos revisores atribuídos. A seção destacada abaixo é onde essas informações serão exibidas.

    ![Conteúdo do email enviado aos revisores com destaques](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na lista com um indicador de status.

![Lista de revisões de acesso mostrando o status das revisões iniciadas](./media/pim-how-to-start-security-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar a eles as instruções de como [revisar o acesso das funções do Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

É possível acompanhar o progresso à medida que os revisores concluírem as revisões na página **Visão geral** da revisão de acesso. Nenhum direito de acesso será alterado no diretório até que a [revisão seja concluída](pim-how-to-complete-review.md).

![Página de visão geral das revisões de acesso mostrando os detalhes da revisão](./media/pim-how-to-start-security-review/access-review-overview.png)

Se essa for uma revisão única, depois que o período de revisão de acesso terminar ou o administrador interromper a revisão de acesso, execute as etapas em [Concluir uma revisão de acesso das funções do Azure AD](pim-how-to-complete-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de revisões de acesso, navegue até a revisão de acesso. Você localizará as próximas ocorrências em "Revisões agendadas" e editará a data de término ou adicionará/removerá os revisores adequadamente.

Com base nas seleções em **Após configurações de conclusão**, a aplicação automática será executada após a data de término da revisão ou quando a revisão for interrompida manualmente. O status da revisão será alterado de **Concluído** para estados intermediários, como **Aplicando** e, finalmente, para estado **Aplicado**. Você deve esperar que os usuários negados (caso haja algum) sejam removidos das funções em alguns minutos.

## <a name="next-steps"></a>Próximas etapas

- [Revisar o acesso às funções do Azure AD](pim-how-to-perform-security-review.md)
- [Concluir uma revisão de acesso das funções do Azure AD](pim-how-to-complete-review.md)
- [Criar uma revisão de acesso das funções do Azure](pim-resource-roles-start-access-review.md)
