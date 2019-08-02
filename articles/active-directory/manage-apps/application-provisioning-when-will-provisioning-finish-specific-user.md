---
title: Descubra quando um usuário específico será capaz de acessar um aplicativo | Microsoft Docs
description: Como saber quando um usuário extremamente importante é capaz de acessar um aplicativo que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310024"
---
# <a name="check-the-status-of-user-provisioning"></a>Verificar o status de provisionamento do usuário

O serviço de provisionamento do AD do Azure executa um ciclo de provisionamento inicial contra o sistema de origem e o sistema de destino, seguido de ciclos incrementais periódicos. Quando você configura o provisionamento para um aplicativo, você pode verificar o status atual do serviço de provisionamento e ver quando um usuário será capaz de acessar um aplicativo.

## <a name="view-the-provisioning-progress-bar-preview"></a>Exibir o provisionamento (visualização) da barra de progresso

 Sobre o **provisionamento** página para um aplicativo, você pode exibir o status do serviço de provisionamento do Azure AD. O **Status atual** seção na parte inferior da página mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode observar o progresso do ciclo de, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas.

Quando você primeiro configurar o provisionamento automático, o **Status atual** seção na parte inferior da página mostra o status do ciclo do provisionamento inicial. Esta seção atualiza cada vez que um ciclo incremental é executado. Os seguintes detalhes são mostrados:
- O tipo de provisionamento ciclo (inicial ou incremental) que está sendo executado ou foi concluído por último.
- Um **barra de progresso** que mostre a porcentagem do ciclo de provisionamento que foi concluída. A porcentagem reflete a contagem de páginas provisionado. Observe que cada página pode conter vários usuários ou grupos, portanto, a porcentagem não correlacione diretamente para o número de usuários, grupos ou funções provisionadas.
- Um **Refresh** botão, você pode usar para manter o modo de exibição atualizado.
- O número de **os usuários** e **grupos** provisionado e o número de funções criadas. Durante o ciclo inicial, o **usuários** número contagens para cima em 1, quando um usuário é criado ou atualizado, e ele contagem regressiva em 1 quando um usuário é excluído. Durante um ciclo incremental, as atualizações do usuário não afetam a **usuários** contagem; o número é alterado somente quando os usuários são criados ou excluídos.
- Um **Exibir Logs de auditoria** vincular, que abre a auditoria do AD do Azure registra detalhes sobre todas as operações executadas pelo serviço de provisionamento de usuário, incluindo o provisionamento de status para usuários individuais (consulte o [delogsdeauditoriadouso](#use-audit-logs-to-check-a-users-provisioning-status)seção abaixo).

Depois que um ciclo de provisionamento for concluído, o **a data das estatísticas** seção mostra os números cumulativos de usuários e grupos que foram provisionados como data, juntamente com a data de conclusão e a duração do último ciclo. O **ID da atividade** identifica exclusivamente o ciclo de provisionamento mais recente. O **ID do trabalho** é um identificador exclusivo para o trabalho de provisionamento e é específico para o aplicativo em seu locatário.

O progresso do provisionamento podem ser exibidos no portal do Azure, nos **Azure Active Directory &gt; aplicativos empresariais &gt; \[nome do aplicativo\] &gt; provisionamento** guia.

![Provisionamento de barra de progresso de página](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Usar logs de auditoria para verificar o status de provisionamento do usuário

Para ver o status de provisionamento para um usuário selecionado, consulte os logs de auditoria no Azure AD. Todas as operações executadas pelo usuário serviço de provisionamento são registradas no Azure AD logs de auditoria. Isso inclui todas as operações feitas nos sistemas de origem e destino e os dados do usuário que foi lido ou gravados durante cada operação de leitura e gravação.

Os logs de auditoria de provisionamento podem ser acessados no portal do Azure na **Azure Active Directory &gt; aplicativos empresariais &gt; \[nome do aplicativo\] &gt; Logs de auditoria** guia. Filtre os logs na categoria de **provisionamento de conta**, para ver apenas os eventos de provisionamento para aquele aplicativo. Você pode procurar por usuários com base na "ID correspondente" que foi configurado para eles nos mapeamentos de atributo. 

Por exemplo, se você configurou o "nome UPN" ou "endereço de email" como o atributo correspondente no lado do Azure AD e o usuário não sendo provisionado tem um valor de "audrey@contoso.com", em seguida, pesquise os logs de auditoria para "audrey@contoso.com" e reveja as entradas retornadas.

Os logs de auditoria de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo:

* Consultando o Azure AD para usuários atribuídos que estão no escopo de provisionamento
* Consultando o aplicativo de destino para a existência desses usuários
* Comparando objetos de usuário entre o sistema
* Adicionar, atualizar ou desabilitar a conta de usuário no sistema de destino com base na comparação

Para obter mais informações sobre como ler os logs de auditoria no portal do Azure, consulte a [guia de relatório de provisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo levará para provisionar os usuários?
Ao usar o provisionamento automático de usuário com um aplicativo, o Azure AD automaticamente provisiona e atualiza as contas de usuário em um aplicativo com base em coisas como [atribuição de usuário e grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) em agendado regularmente intervalo de tempo, normalmente cada 10 minutos.

O tempo necessário para um determinado usuário seja provisionado depende principalmente se o trabalho de provisionamento está executando uma sincronização inicial ou uma sincronização incremental.

- Para **inicial sincronizações**, o tempo de trabalho depende de vários fatores, incluindo o número de usuários e grupos no escopo para provisionamento e o número total de usuários e grupos no sistema de origem. A primeira sincronização entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. Confira posteriormente nesta seção uma lista abrangente dos fatores que afetam o desempenho da sincronização inicial.

- Para **sincronizações incrementais** após a sincronização inicial, os horários de trabalho tendem a ser mais rápida (por exemplo, dentro de 10 minutos), como o serviço de provisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes. O tempo de trabalho depende do número de alterações detectadas nesse ciclo de provisionamento. Se houver menos de 5.000 usuários ou alterações de associação de grupo, o trabalho pode terminar dentro de um único ciclo de provisionamento incremental. 

A tabela a seguir resume os tempos de sincronização para cenários comuns de provisionamento. Nesses cenários, o sistema de origem é o Azure AD e o sistema de destino é um aplicativo SaaS. Os tempos de sincronização são derivados de uma análise estatística dos trabalhos de sincronização para os aplicativos de SaaS G Suite, no local de trabalho, Salesforce e ServiceNow.


| Configuração de escopo | Usuários, grupos e membros no escopo | Tempo de sincronização inicial | Tempo de sincronização incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar somente usuários e grupos atribuídos |  Menos de 1.000 |  Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |  1\.000 a 10.000 | 142 a 708 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |   10.000 a 100.000 | 1\.170 a 2.340 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  Menos de 1.000 | Menos de 30 minutos  | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  1\.000 a 10.000 | Menos de 30 minutos a 120 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  10.000 a 100.000  | 713 minutos a 1.425 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD|  Menos de 1.000  | Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD | 1\.000 a 10.000  | 43 a 86 minutos | Menos de 30 minutos |


Para a configuração **Sincronizar apenas usuários e grupos atribuídos**, use as fórmulas a seguir para determinar os valores mínimo e máximo aproximados esperados dos tempos de **sincronização inicial**:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo de fatores que influenciam o tempo necessário para concluir uma **sincronização inicial**:

- O número total de usuários e grupos no escopo para provisionamento.

- O número total de usuários, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os usuários no escopo para provisionamento são correspondidos para os usuários existentes no aplicativo de destino ou precisam ser criadas pela primeira vez. Trabalhos de sincronização para o qual todos os usuários são criados pela primeira vez levar cerca *duas vezes desde* como sincronizar trabalhos para o qual todos os usuários são correspondidos para os usuários existentes.

- Número de erros nos [logs de auditoria](check-status-user-account-provisioning.md). O desempenho será mais lento se houver muitos erros e o serviço de provisionamento tiver entrado em estado de quarentena.    

- Limites de taxa de solicitação implementados pelo sistema de destino. Alguns sistemas de destino implementam limites de taxa de solicitação e a limitação, que pode afetar o desempenho durante grandes operações de sincronização. Sob essas condições, um aplicativo que recebe muitas solicitações de forma muito rápida pode reduzir sua taxa de resposta ou fechar a conexão. Para melhorar o desempenho, o conector precisa fazer ajustes para não enviar as solicitações de aplicativo mais rápido do que o aplicativo pode processá-las. Os conectores de provisionamento criados pela Microsoft fazem esse ajuste. 

- O número e os tamanhos de grupos atribuídos. A sincronização de grupos atribuídos demora mais do que a sincronização de usuários. O número e os tamanhos dos grupos atribuídos afetam o desempenho. Se um aplicativo tiver [mapeamentos habilitados para sincronização de objeto de grupo](customize-application-attributes.md#editing-group-attribute-mappings), propriedades de grupo como nomes de grupo e associações serão sincronizadas, além dos usuários. Essas sincronizações adicionais tomarão mais tempo do que apenas sincronizar os objetos de usuário.

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
