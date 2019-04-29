---
title: Administração de serviço do Azure Search no portal – Azure Search
description: Gerencie um serviço do Azure Search, um serviço de pesquisa hospedado na nuvem do Microsoft Azure, usando o portal do Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565422"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração do serviço do Azure Search no portal do Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

O Azure Search é um serviço de pesquisa baseado em nuvem, totalmente gerenciado usado para criar uma experiência avançada de pesquisa em aplicativos personalizados. Este artigo aborda as tarefas de administração de serviços que você pode executar no [Portal do Azure](https://portal.azure.com) para um serviço de pesquisa já provisionado. A administração de serviços é leve por design, limitada às seguintes tarefas:

> [!div class="checklist"]
> * Gerencie o acesso às *chaves-api* usadas para acesso de leitura ou gravação ao seu serviço.
> * Ajuste a capacidade de serviço, alterando a alocação de partições e réplicas.
> * Monitorar o uso de recursos, em relação aos limites máximos da sua camada de serviço.

Observe que *atualização* não está listado como uma tarefa administrativa. Já que os recursos são alocados quando o serviço for fornecido, a mudança para uma camada diferente exige um novo serviço. Veja [Criar um serviço do Azure Search](search-create-service-portal.md) para obter detalhes.

> [!Tip]
> Procurando ajuda sobre como analisar o desempenho de tráfego ou consulta de pesquisa? Você pode monitorar o volume de consultas, quais termos as pessoas pesquisam e como os resultados da pesquisa são bem-sucedidos na orientação de clientes para documentos específicos em seu índice. Para obter mais informações, consulte [Pesquisar análise de tráfego para a pesquisa do Azure](search-traffic-analytics.md), [Monitorar as métricas de uso e consulta](search-monitor-usage.md) e [Desempenho e otimização](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
O provisionamento ou encerramento do serviço em si podem ser feitos por um administrador ou coadministrador de assinatura do Azure.

Em um serviço, qualquer pessoa com acesso à URL do serviço e uma chave de API de administração tem acesso de leitura-gravação para o serviço. Acesso de leitura-gravação fornece a capacidade de adicionar, excluir ou modificar objetos de servidor, incluindo chaves de API, índices, indexadores, fontes de dados, agendas e atribuições de função, conforme implementados por meio de [funções definidas pelo RBAC](search-security-rbac.md).

Todas as interações do usuário com o Azure Search se enquadram em um destes modos: acesso de leitura e gravação para o serviço (direitos de administrador) ou acesso somente de leitura ao serviço (direitos de consulta). Para obter mais informações, consulte [Gerenciar as chaves de api](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Log e informações do sistema
O Azure Search não expõe os arquivos de log para um serviço individual por meio do portal ou interfaces programáticas. Na camada Básica e acima, a Microsoft monitora todos os serviços do Azure Search para disponibilidade de 99,9% por contratos de nível de serviço (SLA). Se o serviço estiver lento ou a taxa de transferência de solicitação estiver abaixo dos limites de SLA, as equipes de suporte analisam os arquivos de log disponíveis para resolver o problema.

Em termos de informações gerais sobre o serviço, você pode obter informações das seguintes maneiras:

* No portal, no painel de serviço, por meio de notificações, propriedades e mensagens de status.
* Usando o [PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) para [obter propriedades do serviço](https://docs.microsoft.com/rest/api/searchmanagement/services), ou status no uso de recursos do índice.
* Por meio de [análise de tráfego de pesquisa](search-traffic-analytics.md), conforme observado anteriormente.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorar o uso de recursos
No painel, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e a algumas métricas que você pode obter ao consultar o serviço. No painel do serviço, na seção Uso, é possível determinar rapidamente se os níveis de recurso da partição estão adequados para o seu aplicativo. Você pode provisionar recursos externos, como o monitoramento do Azure, se você deseja capturar e manter os eventos registrados em log. Para obter mais informações, consulte [de monitoramento do Azure Search](search-monitor-usage.md).

Usando a API REST do Serviço Search, você pode obter uma contagem dos documentos e índices: 

* [Obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contar documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastre e interrupções de serviço

Embora possamos recuperar seus dados, o Azure Search não fornece failover instantâneo do serviço se há uma interrupção no cluster ou no nível do datacenter. Se um cluster falhar no datacenter, a equipe de operações detectará e trabalhará para restaurar o serviço. Você experimentará o tempo de inatividade durante a restauração do serviço, mas poderá solicitar créditos de serviço para compensar a indisponibilidade do serviço de acordo com o [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se o serviço contínuo for necessário em caso de falhas catastróficas fora do controle da Microsoft, você poderá [provisionar um serviço adicional](search-create-service-portal.md) em outra região e implementar uma estratégia de replicação geográfica para garantir que os índices seja totalmente redundantes em todos os serviços.

Os clientes que usam [indexadores](search-indexer-overview.md) para popular e atualizar índices podem lidar com a recuperação de desastre por meio de indexadores específicos à geografia utilizando a mesma fonte de dados. Dois serviços em diferentes regiões, cada um executando um indexador, poderiam indexar a mesma fonte de dados para obter a redundância geográfica. Se você estiver indexando fontes de dados que também são com redundância geográfica, lembre-se de que a indexadores do Azure Search só podem executar a indexação incremental de réplicas primárias. Em um evento de failover, verifique se você apontou novamente o indexador para a nova réplica primária. 

Se você não usar indexadores, você usará o código do aplicativo para enviar objetos e dados por push para diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [Desempenho e otimização no Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Backup e restauração

Como o Azure Search não é uma solução de armazenamento de dados primário, não fornecemos um mecanismo formal de backup e restauração de autoatendimento. O código do aplicativo usado para criar e popular um índice é a opção de restauração de fato, caso você exclua um índice por engano. 

Para recompilar um índice, exclua-o (supondo que ele exista), recrie o índice no serviço e recarregue-o recuperando dados do armazenamento de dados primário.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Expandir ou reduzir
Todo serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se você se inscreveu um [camada que fornece recursos dedicados](search-limits-quotas-capacity.md), clique no bloco **ESCALA** no painel de serviço para ajustar o uso de recursos.

Quando você adiciona capacidade por meio de qualquer recurso, o serviço as utiliza automaticamente. Não será necessária nenhuma outra medida de sua parte, mas haverá um breve atraso antes que ocorra o impacto do novo serviço. Pode levar 15 minutos ou mais para provisionar recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
O aumento de QPS (consultas por segundo) ou o alcance da alta disponibilidade são feitos adicionando réplicas. Cada réplica tem uma cópia de um índice, de modo que adicionar mais uma réplica se traduz em mais um índice que pode ser usado para atender às solicitações de consulta. Um mínimo de 3 réplicas são necessários para alta disponibilidade (consulte [Planejamento de capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa com mais réplicas é capaz de balancear a carga das solicitações de consulta em um número maior de índices. Em um determinado nível de volume de consultas, a produtividade das consultas será maior quando houver mais cópias do índice disponíveis para atender à solicitação. Caso esteja ocorrendo latência nas consultas, você pode esperar um impacto positivo quando as réplicas adicionais entrarem em atividade.

Embora a produtividade das consultas aumente conforme você adiciona réplicas, ela não necessariamente dobra ou triplica conforme você adiciona réplicas ao seu serviço. Todos os aplicativos de pesquisa estão sujeitos a fatores externos que podem afetar o desempenho das consultas. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta das consultas.

### <a name="add-partitions"></a>Adicionar partições
A maioria dos aplicativos de serviço possui uma necessidade integrada de mais réplicas em vez de partições. Nos casos em que seja necessária uma contagem maior de documentos, é possível adicionar partições se você se inscreveu no serviço Standard. A camada Básica não fornece partições adicionais.

Na camada Standard, as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Isso é um artefato de fragmentação. Um índice é criado em 12 fragmentos, que podem todos ser armazenados em 1 partição ou divididos igualmente em 2, 3, 4, 6 ou 12 partições (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas
Após períodos de volumes de consulta altos, você pode usar o controle deslizante para reduzir as réplicas depois que as cargas de consulta de pesquisa forem normalizadas (por exemplo, após o término das vendas de feriados). Não são necessárias mais medidas de sua parte. Reduzir o número de réplicas libera máquinas virtuais no datacenter. Suas operações de consulta e ingestão de dados passarão a ser executadas em menos VMs do que antes. O requisito mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover partições
Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode precisar tomar algumas medidas se estiver usando armazenamento a mais que pode ser reduzido. Por exemplo, se sua solução estiver usando três partições, o downsizing de uma ou duas partições gerará um erro se o novo espaço de armazenamento for menor que o necessário para hospedar seu índice. Como é de se esperar, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

Não há um método de detecção que informe quais fragmentos de índices estão armazenados em quais partições. Cada partição fornece cerca de 25 GB de armazenamento, de modo que você precisará reduzir o armazenamento para um tamanho que possa ser acomodado pelo número de partições que possui. Caso queira reverter para uma partição, os 12 fragmentos precisarão se encaixar.

Para se planejar para o futuro, talvez você queira conferir o armazenamento (usando [Obter Estatísticas do Índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver o quanto você realmente usou. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Melhores práticas para escala e implantação
Este vídeo de 30 minutos examina as práticas recomendadas para cenários de implantação avançados, incluindo cargas de trabalho distribuídas geograficamente. Você também pode consultar [Desempenho e otimização no Azure Search](search-performance-optimization.md) para páginas de ajuda que abrangem os mesmos pontos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Próximas etapas
Depois de compreender os conceitos por trás de administração do serviço, considere o uso do [PowerShell](search-manage-powershell.md) para automatizar tarefas.

Também é recomendável examinar o [artigo de desempenho e otimização](search-performance-optimization.md).

Outra recomendação é assistir ao vídeo mencionado na seção anterior. Ele fornece uma cobertura mais profunda das técnicas mencionadas nesta seção.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



