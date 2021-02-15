---
title: Administração de serviço no portal
titleSuffix: Azure Cognitive Search
description: Gerenciar um serviço de Pesquisa Cognitiva do Azure, um serviço de pesquisa de nuvem hospedado no Microsoft Azure, usando o portal do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 84ddc4b427f6dc168c044f34b41e81e3b0ff19e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935034"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administração de serviço do Azure Pesquisa Cognitiva no portal do Azure

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [SDK .NET](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

O Azure Pesquisa Cognitiva é um serviço de pesquisa baseado em nuvem totalmente gerenciado usado para criar uma experiência de pesquisa avançada em aplicativos personalizados. Este artigo aborda as tarefas de administração de serviços que você pode executar no [Portal do Azure](https://portal.azure.com) para um serviço de pesquisa já provisionado. A administração de serviços é leve por design, limitada às seguintes tarefas:

* Verifique o armazenamento usando o link **uso** da página mid.
* Verifique os volumes de consulta e a latência usando o link de **monitoramento** de página intermediária e se as solicitações foram limitadas.
* Gerencie o acesso usando a página **chaves** à esquerda.
* Ajuste a capacidade usando a página **escala** à esquerda.

As mesmas tarefas executadas no portal também podem ser manipuladas programaticamente por meio das [APIs de gerenciamento](/rest/api/searchmanagement/) e do [módulo do PowerShell AZ. Search](search-manage-powershell.md). As tarefas administrativas são totalmente representadas em todo o portal e interfaces programáticas. Não há nenhuma tarefa administrativa específica que esteja disponível em apenas uma modalidade.

O Azure Pesquisa Cognitiva aproveita outros serviços do Azure para monitoramento e gerenciamento mais aprofundados. Por si só, os únicos dados armazenados com um serviço de pesquisa são Content (índices, indexador e definições de fonte de dados e outros objetos). As métricas relatadas para as páginas do portal são extraídas de logs internos em um ciclo de 30 dias sem interrupção. Para a retenção de log controlada pelo usuário e eventos adicionais, você precisará de [Azure monitor](../azure-monitor/index.yml). 

## <a name="fixed-service-properties"></a>Propriedades do serviço fixo

Vários aspectos de um serviço de pesquisa são determinados quando o serviço é provisionado e não podem ser alterados posteriormente:

* Nome do serviço (você não pode renomear um serviço)
* Local do serviço (atualmente, não é possível mover um serviço intacto para outra região)
* Contagem máxima de réplicas e partições (determinado pela camada, básica ou padrão)

Se você começou com o básico com seu máximo de uma partição e agora precisa de mais partições, será necessário [criar um novo serviço](search-create-service-portal.md) em uma camada mais alta e recriar seu conteúdo no novo serviço. 

## <a name="administrator-rights"></a>Direitos de administrador

O provisionamento ou encerramento do serviço em si podem ser feitos por um administrador ou coadministrador de assinatura do Azure.

Em relação ao acesso ao ponto de extremidade, qualquer pessoa com acesso à URL do serviço e uma chave de API tem acesso ao conteúdo. Para obter mais informações sobre chaves, consulte [gerenciar as chaves de API](search-security-api-keys.md).

* Acesso somente leitura ao serviço são direitos de consulta, normalmente concedidos a um aplicativo cliente, fornecendo a ela a URL e uma chave de API de consulta.
* Acesso de leitura/gravação fornece a capacidade de adicionar, excluir ou modificar objetos de servidor, incluindo chaves de API, índices, indexadores, fontes de dados e agendas. Acesso de leitura-gravação é concedido fornecendo a URL, uma chave de API de administração.

Os direitos para os aparelhos de provisionamento de serviço são concedidos por meio de atribuições de função. O Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) do Azure é um sistema de autorização criado em [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o provisionamento de recursos do Azure. 

No contexto do Azure Pesquisa Cognitiva, as [atribuições de função do Azure](search-security-rbac.md) determinarão quem pode executar tarefas, independentemente de estarem usando o [portal](search-manage.md), o [PowerShell](search-manage-powershell.md)ou as [APIs REST de gerenciamento](/rest/api/searchmanagement/search-howto-management-rest-api):

* Criar ou excluir um serviço
* Dimensionar o serviço
* Excluir ou regenerar chaves de API
* Habilitar o log de diagnóstico (criar serviços)
* Habilitar análise de tráfego (criar serviços)

> [!TIP]
> Usando mecanismos do Azure, você pode bloquear uma assinatura ou um recurso para impedir a exclusão acidental ou não autorizada de seu serviço de pesquisa por usuários com direitos de administrador. Para obter mais informações, consulte [Bloquear recursos para evitar a exclusão inesperada](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Log e informações do sistema

Na camada básica e acima, a Microsoft monitora todos os serviços de Pesquisa Cognitiva do Azure para 99,9% de disponibilidade por SLA (contratos de nível de serviço). Se o serviço estiver lento ou a taxa de transferência de solicitação estiver abaixo dos limites de SLA, as equipes de suporte analisam os arquivos de log disponíveis para resolver o problema.

O Azure Pesquisa Cognitiva aproveita [Azure monitor](../azure-monitor/index.yml) para coletar e armazenar a indexação e a atividade de consulta. Um serviço de pesquisa armazena apenas seu conteúdo (índices, definições de indexador, definições de fonte de dados, definições de habilidades, mapas de sinônimos). O cache e as informações registradas são armazenadas fora do serviço, geralmente em uma conta de armazenamento do Azure. Para obter mais informações sobre a indexação de log e as cargas de trabalho de consulta, consulte [coletar e analisar dados de log](search-monitor-logs.md).

Em termos de informações gerais sobre seu serviço, usando apenas os recursos criados no Azure Pesquisa Cognitiva em si, você pode obter informações das seguintes maneiras:

* Usando a página **visão geral** do serviço, por meio de notificações, propriedades e mensagens de status.
* Usando o [PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](/rest/api/searchmanagement/) para [obter as propriedades do serviço](/rest/api/searchmanagement/services). Não há nenhuma nova informação ou operação fornecida na camada programática. As interfaces existem para que você possa escrever scripts.

## <a name="monitor-resource-usage"></a>Monitorar o uso de recursos

No painel, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e a algumas métricas que você pode obter ao consultar o serviço. No painel do serviço, na seção Uso, é possível determinar rapidamente se os níveis de recurso da partição estão adequados para o seu aplicativo. Você pode provisionar recursos externos, como o monitoramento do Azure, se desejar capturar e persistir eventos registrados. Para obter mais informações, consulte [monitorando pesquisa cognitiva do Azure](search-monitor-usage.md).

Usando a API REST do serviço de pesquisa, você pode obter uma contagem de documentos e índices programaticamente: 

* [Obter estatísticas de índice](/rest/api/searchservice/Get-Index-Statistics)
* [Contar documentos](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastre e interrupções de serviço

Embora possamos recuperar seus dados, o Azure Pesquisa Cognitiva não fornece um failover instantâneo do serviço se houver uma interrupção no nível do cluster ou do data center. Se um cluster falhar no datacenter, a equipe de operações detectará e trabalhará para restaurar o serviço. Você experimentará o tempo de inatividade durante a restauração do serviço, mas poderá solicitar créditos de serviço para compensar a indisponibilidade do serviço de acordo com o [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se o serviço contínuo for necessário em caso de falhas catastróficas fora do controle da Microsoft, você poderá [provisionar um serviço adicional](search-create-service-portal.md) em outra região e implementar uma estratégia de replicação geográfica para garantir que os índices seja totalmente redundantes em todos os serviços.

Os clientes que usam [indexadores](search-indexer-overview.md) para popular e atualizar índices podem lidar com a recuperação de desastre por meio de indexadores específicos à geografia utilizando a mesma fonte de dados. Dois serviços em diferentes regiões, cada um executando um indexador, poderiam indexar a mesma fonte de dados para obter a redundância geográfica. Se você estiver indexando de fontes de dados que também são com redundância geográfica, lembre-se de que os indexadores do Azure Pesquisa Cognitiva só podem executar a indexação incremental (mesclar atualizações de documentos novos, modificados ou excluídos) de réplicas primárias. Em um evento de failover, verifique se você apontou novamente o indexador para a nova réplica primária. 

Se você não usar indexadores, você usará o código do aplicativo para enviar objetos e dados por push para diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [desempenho e otimização no Azure pesquisa cognitiva](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Backup e restauração

Como o Azure Pesquisa Cognitiva não é uma solução de armazenamento de dados principal, não fornecemos um mecanismo formal para backup e restauração de autoatendimento. No entanto, você pode usar o código de exemplo **index-backup-restore** neste [repositório de exemplo do Azure pesquisa cognitiva .net](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup da definição de índice e do instantâneo para uma série de arquivos JSON e, em seguida, usar esses arquivos para restaurar o índice, se necessário. Essa ferramenta também pode mover índices entre as camadas de serviço.

Caso contrário, o código do aplicativo usado para criar e popular um índice é a opção de restauração de fato se você excluir um índice por engano. Para recompilar um índice, exclua-o (supondo que ele exista), recrie o índice no serviço e recarregue-o recuperando dados do armazenamento de dados primário.

## <a name="scale-up-or-down"></a>Expandir ou reduzir

Todo serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se você se inscreveu em uma [camada que dá suporte a mais capacidade](search-limits-quotas-capacity.md), clique em **escala** no painel de navegação esquerdo para ajustar o uso do recurso.

Quando você adiciona capacidade por meio de qualquer recurso, o serviço as utiliza automaticamente. Não será necessária nenhuma outra medida de sua parte, mas haverá um breve atraso antes que ocorra o impacto do novo serviço. Pode levar 15 minutos ou mais para provisionar recursos adicionais.

### <a name="add-replicas"></a>Adicionar réplicas

O aumento de QPS (consultas por segundo) ou o alcance da alta disponibilidade são feitos adicionando réplicas. Cada réplica tem uma cópia de um índice, de modo que adicionar mais uma réplica se traduz em mais um índice que pode ser usado para atender às solicitações de consulta. Um mínimo de 3 réplicas é necessário para alta disponibilidade (consulte [ajustar a capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa com mais réplicas é capaz de balancear a carga das solicitações de consulta em um número maior de índices. Em um determinado nível de volume de consultas, a produtividade das consultas será maior quando houver mais cópias do índice disponíveis para atender à solicitação. Caso esteja ocorrendo latência nas consultas, você pode esperar um impacto positivo quando as réplicas adicionais entrarem em atividade.

Embora a produtividade das consultas aumente conforme você adiciona réplicas, ela não necessariamente dobra ou triplica conforme você adiciona réplicas ao seu serviço. Todos os aplicativos de pesquisa estão sujeitos a fatores externos que podem afetar o desempenho das consultas. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta das consultas.

### <a name="add-partitions"></a>Adicionar partições

É mais comum adicionar réplicas, mas quando o armazenamento é restrito, você pode adicionar partições para obter mais capacidade. A camada na qual você provisionou o serviço determina se as partições podem ser adicionadas. A camada básica está bloqueada em uma partição. As camadas Standard e superior dão suporte a partições adicionais.

As partições são adicionadas em divisores de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Isso é um artefato de fragmentação. Um índice é criado em 12 fragmentos, que podem todos ser armazenados em 1 partição ou divididos igualmente em 2, 3, 4, 6 ou 12 partições (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas

Após períodos de volumes de consulta altos, você pode usar o controle deslizante para reduzir as réplicas depois que as cargas de consulta de pesquisa forem normalizadas (por exemplo, após o término das vendas de feriados). Não são necessárias mais medidas de sua parte. Reduzir o número de réplicas libera máquinas virtuais no datacenter. Suas operações de consulta e ingestão de dados passarão a ser executadas em menos VMs do que antes. O requisito mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover partições

Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode precisar tomar algumas medidas se estiver usando armazenamento a mais que pode ser reduzido. Por exemplo, se sua solução estiver usando três partições, o downsizing de uma ou duas partições gerará um erro se o novo espaço de armazenamento for menor que o necessário para hospedar seu índice. Como é de se esperar, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

Não há um método de detecção que informe quais fragmentos de índices estão armazenados em quais partições. Cada partição fornece cerca de 25 GB de armazenamento, de modo que você precisará reduzir o armazenamento para um tamanho que possa ser acomodado pelo número de partições que possui. Caso queira reverter para uma partição, os 12 fragmentos precisarão se encaixar.

Para se planejar para o futuro, talvez você queira conferir o armazenamento (usando [Obter Estatísticas do Índice](/rest/api/searchservice/Get-Index-Statistics)) para ver o quanto você realmente usou. 

## <a name="next-steps"></a>Próximas etapas

* Automatizar com o [PowerShell](search-manage-powershell.md)

* Examinar as técnicas [de desempenho e otimização](search-performance-optimization.md)

* Examine os [recursos de segurança](search-security-overview.md) para proteger o conteúdo e as operações

* Habilitar o [log de diagnóstico](search-monitor-logs.md) para monitorar cargas de trabalho de consulta e indexação