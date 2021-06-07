---
title: Descubra quando um usuário específico poderá acessar um aplicativo
description: Como saber quando um usuário extremamente importante é capaz de acessar um aplicativo que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 890c4a8a5aec3b15b150908c64bb114bd85a61a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256755"
---
# <a name="check-the-status-of-user-provisioning"></a>Verificar o status do provisionamento do usuário

O serviço de provisionamento do Azure AD executa um ciclo de provisionamento inicial no sistema de origem e no sistema de destino, seguido por ciclos incrementais periódicos. Ao configurar o provisionamento para um aplicativo, você pode verificar o status atual do serviço de provisionamento e ver quando um usuário poderá acessar um aplicativo.

## <a name="view-the-provisioning-progress-bar"></a>Exibir a barra de progresso do provisionamento

 Na página de **provisionamento** de um aplicativo, você pode exibir o status do serviço de provisionamento do Azure AD. A seção **status atual** na parte inferior da página mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode ver o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas.

Quando você configura o provisionamento automático pela primeira vez, a seção **status atual** na parte inferior da página mostra o status do ciclo de provisionamento inicial. Esta seção é atualizada toda vez que um ciclo incremental é executado. Os seguintes detalhes são mostrados:
- O tipo de ciclo de provisionamento (inicial ou incremental) que está em execução no momento ou foi concluído pela última vez.
- Uma **barra de progresso** que mostra a porcentagem do ciclo de provisionamento que foi concluída. A porcentagem reflete a contagem de páginas provisionada. Observe que cada página pode conter vários usuários ou grupos, portanto, o percentual não se correlaciona diretamente com o número de usuários, grupos ou funções provisionados.
- Um botão de **atualização** que você pode usar para manter a exibição atualizada.
- O número de **usuários** e **grupos** no armazenamento de dados do conector. A contagem aumenta sempre que um objeto é adicionado ao escopo do provisionamento. A contagem não será desativada se um usuário for excluído de forma reversível ou excluído com hardware, pois isso não remove o objeto do armazenamento de dados do conector. A contagem será recalculada na primeira sincronização depois que o CDS for [redefinido](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) 
- Um link **Exibir logs de auditoria** , que abre os logs de provisionamento do Azure ad para obter detalhes sobre todas as operações executadas pelo serviço de provisionamento de usuário, incluindo o status de provisionamento para usuários individuais (consulte a seção [usar logs de provisionamento](#use-provisioning-logs-to-check-a-users-provisioning-status) abaixo).

Após a conclusão de um ciclo de provisionamento, a seção **estatísticas até a data** mostra os números cumulativos de usuários e grupos que foram provisionados até a data, junto com a data de conclusão e a duração do último ciclo. A **ID da atividade** identifica exclusivamente o ciclo de provisionamento mais recente. A **ID do trabalho** é um identificador exclusivo para o trabalho de provisionamento e é específica para o aplicativo em seu locatário.

O progresso do provisionamento pode ser exibido na portal do Azure, na guia **&gt; &gt; \[ \] &gt; provisionamento de nome de aplicativo do Azure Active Directory Enterprise apps** .

![Barra de progresso da página de provisionamento](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Usar logs de provisionamento para verificar o status de provisionamento de um usuário

Para ver o status de provisionamento de um usuário selecionado, consulte os [logs de provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) no Azure AD. Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos logs de provisionamento do Azure AD. Isso inclui todas as operações de leitura e gravação feitas nos sistemas de origem e de destino, bem como os dados de usuário que foram lidos ou gravados durante cada operação.

Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **logs de provisionamento (versão prévia)** na seção **atividade** . Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Provisionando logs (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os logs de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo:

* Consultando o Azure AD para usuários atribuídos que estão no escopo de provisionamento
* Consultando o aplicativo de destino para a existência desses usuários
* Comparando objetos de usuário entre o sistema
* Adicionar, atualizar ou desabilitar a conta de usuário no sistema de destino com base na comparação

Para obter mais informações sobre como ler os logs de provisionamento no portal do Azure, consulte o guia de [relatórios de provisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo levará para provisionar os usuários?
Ao usar o provisionamento automático de usuário com um aplicativo, o Azure AD provisiona e atualiza automaticamente as contas de usuário em um aplicativo com base em coisas como [atribuição de usuário e grupo](../manage-apps/assign-user-or-group-access-portal.md) em um intervalo de tempo agendado regularmente, normalmente a cada 40 minutos.

O tempo necessário para que um determinado usuário seja provisionado depende principalmente de se o seu trabalho de provisionamento está executando um ciclo inicial ou um ciclo incremental.

- Para o **ciclo inicial**, o tempo de trabalho depende de muitos fatores, incluindo o número de usuários e grupos no escopo para provisionamento e o número total de usuários e grupo no sistema de origem. A primeira sincronização entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. Uma lista abrangente de fatores que afetam o desempenho inicial do ciclo é resumida mais adiante nesta seção.

- Para **ciclos incrementais** após o ciclo inicial, os tempos de trabalho tendem a ser mais rápidos (por exemplo, em 10 minutos), pois o serviço de provisionamento armazena as marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes. A hora do trabalho depende do número de alterações detectadas no ciclo de provisionamento. Se houver menos de 5.000 alterações de associação de usuário ou de grupo, o trabalho poderá ser concluído em um único ciclo de provisionamento incremental. 

A tabela a seguir resume os tempos de sincronização para cenários comuns de provisionamento. Nesses cenários, o sistema de origem é o Azure AD e o sistema de destino é um aplicativo SaaS. Os tempos de sincronização derivam de uma análise estatística de trabalhos de sincronização para os aplicativos SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configuração de escopo | Usuários, grupos e membros no escopo | Tempo de ciclo inicial | Tempo de ciclo incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar somente usuários e grupos atribuídos |  Menos de 1.000 |  Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |  1.000 a 10.000 | 142 a 708 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |   10.000 a 100.000 | 1.170 a 2.340 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  Menos de 1.000 | Menos de 30 minutos  | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  1.000 a 10.000 | Menos de 30 minutos a 120 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  10.000 a 100.000  | 713 minutos a 1.425 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD|  Menos de 1.000  | Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD | 1.000 a 10.000  | 43 a 86 minutos | Menos de 30 minutos |

Para a sincronização de configuração, **somente usuários e grupos atribuídos**, você pode usar as fórmulas a seguir para determinar os tempos de **ciclo inicial** mínimo e máximo aproximado:

- Mínimo de minutos = 0, 1 x [número de usuários atribuídos, grupos e membros do grupo]
- Máximo de minutos = 0, 8 x [número de usuários atribuídos, grupos e membros do grupo]

Resumo dos fatores que influenciam o tempo necessário para concluir um **ciclo inicial**:

- O número total de usuários e grupos no escopo para provisionamento.

- O número total de usuários, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os usuários no escopo para provisionamento são correspondidos aos usuários existentes no aplicativo de destino ou precisam ser criados pela primeira vez. Os trabalhos de sincronização para os quais todos os usuários são criados pela primeira vez levam aproximadamente *duas vezes mais* tempo que os trabalhos de sincronização para os quais todos os usuários têm correspondência com os usuários existentes.

- Número de erros nos [logs de provisionamento](check-status-user-account-provisioning.md). O desempenho será mais lento se houver muitos erros e o serviço de provisionamento tiver entrado em estado de quarentena. 

- Limites de taxa de solicitação implementados pelo sistema de destino. Alguns sistemas de destino implementam limites de taxa de solicitação e limitação, o que pode afetar o desempenho durante grandes operações de sincronização. Sob essas condições, um aplicativo que recebe muitas solicitações de forma muito rápida pode reduzir sua taxa de resposta ou fechar a conexão. Para melhorar o desempenho, o conector precisa fazer ajustes para não enviar as solicitações de aplicativo mais rápido do que o aplicativo pode processá-las. Os conectores de provisionamento criados pela Microsoft fazem esse ajuste. 

- O número e os tamanhos de grupos atribuídos. A sincronização de grupos atribuídos demora mais do que a sincronização de usuários. O número e os tamanhos dos grupos atribuídos afetam o desempenho. Se um aplicativo tiver [mapeamentos habilitados para sincronização de objeto de grupo](customize-application-attributes.md#editing-group-attribute-mappings), propriedades de grupo como nomes de grupo e associações serão sincronizadas, além dos usuários. Essas sincronizações adicionais tomarão mais tempo do que apenas sincronizar os objetos de usuário.

- Se o desempenho se tornar um problema e você estiver tentando provisionar a maioria dos usuários e grupos em seu locatário, use filtros de escopo. Filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Active Directory do Azure](user-provisioning.md)