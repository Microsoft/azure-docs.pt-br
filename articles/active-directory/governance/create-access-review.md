---
title: Criar uma revisão de acesso de grupos & aplicativos-Azure AD
description: Saiba como criar uma revisão de acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12eb95a7840bdbb902701fc644eee30ffe9900f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778505"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicativos nas revisões de acesso do Azure AD

Acesso a grupos e aplicativos para funcionários e visitantes muda ao longo do tempo. Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar revisões de acesso para membros do grupo ou usuários atribuídos a um aplicativo. Se você precisar revisar rotineiramente o acesso, você também pode criar as revisões de acesso recorrentes. Para obter mais informações sobre esses cenários, consulte [Gerenciar acesso de usuário](manage-user-access-with-access-reviews.md) e [Gerenciar acesso de convidado](manage-guest-access-with-access-reviews.md).

Você pode assistir a um vídeo rápido falando sobre como habilitar as revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Este artigo descreve como criar uma ou mais revisões de acesso para membros do grupo ou acesso ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de usuário

Para obter mais informações, veja [Requisitos de licença](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais revisões de acesso

1. Entre no portal do Azure e abra a [página governança de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. No menu à esquerda, clique em **revisões de acesso**.

3. Clique em **Nova análise de acesso** para criar uma nova revisão de acesso.

    ![Painel de revisões de acesso no controle de identidade](./media/create-access-review/access-reviews.png)

4. Na **etapa 1: selecione o que deve ser revisado** selecione qual recurso você gostaria de examinar.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/create-access-review/select-what-review.png)

5. Se você selecionou **Teams + groups** na etapa 1, terá duas opções na etapa 2
   - **Todos os grupos de Microsoft 365 com usuários convidados.** Selecione esta opção se desejar criar revisões recorrentes em todos os seus usuários convidados em todas as suas equipes e grupos de M365 da Microsoft em sua organização. Você pode optar por excluir determinados grupos clicando em ' selecionar grupo (s) para exclusão '.
   - **Selecione equipes + grupos.** Selecione esta opção se desejar especificar um conjunto finito de equipes e/ou grupos a serem examinados. Depois de clicar nessa opção, você verá uma lista de grupos à direita da qual escolher.

     ![Equipes e grupos](./media/create-access-review/teams-groups.png)

     ![Equipes e grupos escolhidos na interface do usuário](./media/create-access-review/teams-groups-detailed.png)

6. Se você selecionou **aplicativos** na etapa 1, poderá selecionar um ou mais aplicativos na etapa 2.

    >[!NOTE]
    > A seleção de vários grupos e/ou aplicativos resultará em várias revisões de acesso criadas. Por exemplo, se você selecionar 5 grupos para revisão, isso resultará em 5 revisões de acesso separadas

   ![A interface exibida se você escolher aplicativos em vez de grupos](./media/create-access-review/select-application-detailed.png)

7. Em seguida, na etapa 3, você pode selecionar um escopo para a revisão. Suas opções são
   - **Somente usuários convidados.** Selecionar essa opção limita a revisão de acesso apenas aos usuários convidados do Azure AD B2B em seu diretório.
   - **Mundo.** A seleção dessa opção abrange a revisão de acesso a todos os objetos de usuário associados ao recurso.

    >[!NOTE]
    > Se você selecionou todos os grupos de Microsoft 365 com usuários convidados na etapa 2, sua única opção é examinar os usuários convidados na etapa 3

8. Clique em Avançar: revisões
9. Na seção **selecionar revisores** , selecione uma ou mais pessoas para executar as revisões de acesso. Você pode escolher:
    - **Proprietário (s) do grupo** (disponível somente ao executar uma revisão em uma equipe ou grupo)
    - **Usuário (s) ou grupos (s) selecionados**
    - **Os usuários revisam o próprio acesso**
    - **Apresentação Gerentes de usuários.**
    Se você escolher **gerentes de usuários** ou **proprietários de grupo**  , também terá a opção de especificar um revisor de fallback. Os revisores de fallback são solicitados a fazer uma análise quando o usuário não tem nenhum gerente especificado no diretório ou o grupo não tem um proprietário.

    ![nova revisão de acesso](./media/create-access-review/new-access-review.png)

10. Na seção **especificar recorrência da revisão** , você pode especificar uma frequência como **semanal, mensal, trimestral, semianual, anualmente**. Em seguida, você especifica uma **duração**, que define por quanto tempo uma análise será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas. Talvez você queira reduzir a duração para garantir que a entrada dos revisores seja aplicada anteriormente. Em seguida, você pode selecionar uma **data de início** e uma data de **término**.

    ![Escolha a frequência com que a revisão deve ocorrer](./media/create-access-review/frequency.png)

11. Clique no botão **próximo: configurações** na parte inferior da página
12. Nas **configurações de conclusão** , você pode especificar o que acontece após a conclusão da revisão

    ![Criar uma revisão de acesso-após as configurações de conclusão](./media/create-access-review/upon-completion-settings-new.png)

Se você quiser remover automaticamente o acesso para usuários negados, defina auto aplicar resultados para recurso a ser habilitado. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para Desabilitar.
Use a lista se os revisores não responderem para especificar o que acontece para os usuários que não são revisados pelo revisor no período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

- **Nenhuma alteração** - deixar o acesso do usuário inalterado
- **Remover o acesso** - remover o acesso do usuário
- **Aprovar o acesso** - aprovar o acesso do usuário
- **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

    ![Nas opções de configurações de conclusão](./media/create-access-review/upon-completion-settings-new.png)

Use a ação a ser aplicada em usuários **convidados** negados para especificar o que acontece com os usuários convidados se eles forem negados.
- Remover a associação do usuário do recurso removerá o acesso do usuário negado ao grupo ou aplicativo que está sendo revisado. eles ainda poderão entrar no locatário.
- Bloquear o logon do usuário por 30 dias e, em seguida, remover o usuário do locatário bloqueará a entrada de usuários negados no locatário, independentemente de terem acesso a outros recursos. Se houvesse um erro ou se um administrador decidir reabilitar o acesso de um, ele poderá fazer isso dentro de 30 dias depois que o usuário tiver sido desabilitado. Se não houver nenhuma ação executada nos usuários desabilitados, eles serão excluídos do locatário.

Para saber mais sobre as práticas recomendadas para remover usuários convidados que não têm mais acesso aos recursos em sua organização, leia o artigo intitulado [Use Azure ad Identity Governance para revisar e remover usuários externos que não têm mais acesso a recursos.](access-reviews-external-users.md)

   >[!NOTE]
   >A ação a ser aplicada em usuários convidados negados não é configurável em revisões com escopo para mais de usuários convidados. Ele também não é configurável para revisões de **todos os grupos de M365 com usuários convidados.** Quando não configurável, a opção padrão de remover a associação do usuário do recurso é usada em usuários negados.

13. Na tela **habilitar, os auxiliares de decisão** escolhem se deseja que o revisor receba recomendações durante o processo de revisão.

    ![Habilitar opções de auxiliares de decisão](./media/create-access-review/helpers.png)

14. Na seção **Configurações avançadas** , você pode escolher o seguinte
    - Defina a **justificativa necessária** para **habilitar** o para exigir que o revisor forneça um motivo para aprovação.
    - Defina **notificações por email** para **habilitar** o para que o Azure ad envie notificações por email aos revisores quando uma revisão de acesso for iniciada e aos administradores quando uma análise for concluída.
    - Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise. Esses lembretes serão autodirecionados pela duração da revisão.
    - O conteúdo do email enviado aos revisores é gerado automaticamente com base nos detalhes da revisão, como nome da revisão, nome do recurso, data de vencimento, etc. Se você precisar de uma maneira de comunicar informações adicionais, como instruções adicionais ou informações de contato, poderá especificar esses detalhes na seção **conteúdo adicional para o email do revisor** . As informações inseridas são incluídas nos emails de convite e lembrete enviados aos revisores atribuídos. A seção realçada na imagem abaixo mostra onde essas informações são exibidas.


      ![conteúdo adicional para o revisor](./media/create-access-review/additional-content-reviewer.png)

15. Clique em **Avançar: revisar + criar** para ir para a próxima página
16. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.
17. Examine as informações e selecione **criar**

       ![criar tela de revisão](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso e seu status](./media/create-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar a eles as instruções de como [revisar o acesso a grupos ou aplicativos](perform-access-review.md). Se sua análise for para convidados para revisar seu próprio acesso, mostre-lhes as instruções de como [examinar o acesso a grupos ou aplicativos](review-your-access.md).

Se você tiver atribuído convidados como revisores e eles não tiverem aceitado o convite, eles não receberão um email das revisões de acesso, pois eles devem primeiro aceitar o convite antes da revisão.

## <a name="access-review-status-table"></a>Tabela de status de análise de acesso

| Status | Definição |
|--------|------------|
|NotStarted | A revisão foi criada, a descoberta de usuário está aguardando para ser iniciada. |
|Inicializando   | A descoberta de usuário está em andamento para identificar todos os usuários que fazem parte da revisão. |
|Iniciando | A revisão está sendo iniciada. Se as notificações por email estiverem habilitadas, os emails serão enviados aos revisores. |
|InProgress | A revisão foi iniciada. Se as notificações por email forem habilitadas, os emails serão enviados aos revisores. Os revisores podem enviar decisões até a data de vencimento. |
|Finaliza | A revisão está sendo concluída e os emails estão sendo enviados para o proprietário da revisão. |
|Revisão automática | A revisão está em um estágio de revisão do sistema. O sistema está gravando decisões para usuários que não foram revisados com base em recomendações ou decisões pré-configuradas. |
|Revisado automaticamente | As decisões foram registradas pelo sistema para todos os usuários que não foram revisados. A revisão está pronta para continuar a **aplicação** se a aplicação automática estiver habilitada. |
|Solicita | Não haverá nenhuma alteração no acesso para usuários que foram aprovados. |
|Aplicado | Os usuários negados, se houver algum, foram removidos do recurso ou diretório. |
|Com falha | A revisão não pôde ser executada. Esse erro pode estar relacionado à exclusão do locatário, uma alteração nas licenças ou outras alterações internas do locatário. |

## <a name="create-reviews-via-apis"></a>Criar revisões via APIs

Você também pode criar as revisões de acesso usando as APIs. O que você faz para gerenciar as revisões de acesso dos grupos e usuários do aplicativo no portal do Azure também pode ser feito usando as APIs do Microsoft Graph. Para obter mais informações, consulte a [referência da API de revisões de acesso do Azure ad](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter um exemplo de código, consulte o [exemplo de recuperação de revisões de acesso do Azure ad via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Examinar o acesso a grupos ou aplicativos](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)