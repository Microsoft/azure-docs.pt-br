---
title: "Administração do serviço do Azure Search no portal do Azure"
description: "Gerenciar o Azure Search, um serviço de nuvem hospedado no Microsoft Azure, usando o portal do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/18/2017
ms.author: heidist
ms.openlocfilehash: c293de5b43103c8cbec01f61a26b8b28ac7e9116
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração do serviço do Azure Search no portal do Azure
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

O Azure Search é um serviço de pesquisa baseado em nuvem, totalmente gerenciado usado para criar uma experiência avançada de pesquisa em aplicativos personalizados. Este artigo aborda as tarefas *administração de serviço* que você pode executar no [portal do Azure](https://portal.azure.com) para um serviço de pesquisa que você já tenha provisionado. *Service administration* é leve por design, limitada às seguintes tarefas:

* Gerenciar e proteger o acesso para as *chaves de api* usadas para acesso de leitura ou gravação para o serviço.
* Ajuste a capacidade de serviço, alterando a alocação de partições e réplicas.
* Monitorar o uso de recursos, em relação aos limites máximos da sua camada de serviço.

**Não está no escopo** 

*Gerenciamento de conteúdo* (ou gerenciamento de índice) refere-se a operações, como análise de tráfego de pesquisa para entender o volume de consultas, descobrir quais termos as pessoas pesquisam e como os resultados da pesquisa bem-sucedida são para a orientação dos clientes para documentos específicos no índice. Para ajuda nessa área, veja [Análise de Tráfego de Pesquisa para o Azure Search](search-traffic-analytics.md).

*desempenho de consulta* também está além do escopo deste artigo. Para obter mais informações, confira [Monitorar as métricas de uso e consulta](search-monitor-usage.md) e [Desempenho e otimização](search-performance-optimization.md).

*Atualizar* não é uma tarefa administrativa. Já que os recursos são alocados quando o serviço for fornecido, a mudança para uma camada diferente exige um novo serviço. Veja [Criar um serviço do Azure Search](search-create-service-portal.md) para obter detalhes.

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
O provisionamento ou encerramento do serviço em si podem ser feitos por um administrador ou coadministrador de assinatura do Azure.

Em um serviço, qualquer pessoa com acesso à URL do serviço e uma chave de API de administração tem acesso de leitura-gravação para o serviço. Acesso de leitura-gravação fornece a capacidade de adicionar, excluir ou modificar objetos de servidor, incluindo chaves de API, índices, indexadores, fontes de dados, agendas e atribuições de função, conforme implementados por meio de [funções definidas pelo RBAC](#rbac).

Todas as interações do usuário com o Azure Search se enquadram em um destes modos: acesso de leitura e gravação para o serviço (direitos de administrador) ou acesso somente de leitura ao serviço (direitos de consulta). Para obter mais informações, consulte [Gerenciar as chaves de api](#manage-keys).

<a id="sys-info"></a>

## <a name="set-rbac-roles-for-administrative-access"></a>Definir funções RBAC para acesso administrativo
O Azure fornece um [modelo global de autorização baseado em funções](../active-directory/role-based-access-control-configure.md) para todos os serviços gerenciados por meio do portal ou nas APIs do Gerenciador de Recursos. Funções de Leitor, Colaborador e Proprietário determinam o nível de administração do serviço para usuários, grupos e entidades de segurança do Active Directory, que você atribui a cada função. 

Para o Azure Search, as permissões de RBAC determinam as seguintes tarefas administrativas:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou excluir o serviço ou qualquer objeto no serviço, incluindo chaves de api, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir o status do serviço, incluindo o tamanho de armazenamento e contagens.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e proprietários de serviço possuem associação automática na função Proprietários. |
| Colaborador |Mesmo nível de acesso como Proprietário, menos gerenciamento de funções RBAC. Por exemplo, um Colaborador pode exibir e gerar novamente a `api-key`, mas não pode modificar as associações de função. |
| Leitor |Exibir chaves de consulta e de status do serviço. Os membros dessa função não podem alterar a configuração do serviço, nem exibir chaves admin. |

As funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço de pesquisa, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Para mais informações, consulte "Autorização para gerenciamento versus operações de dados" em [O que é controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md).

<a id="secure-keys"></a>
## <a name="logging-and-system-information"></a>Log e informações do sistema
O Azure Search não expõe os arquivos de log para um serviço individual por meio do portal ou interfaces programáticas. Na camada Básica e acima, a Microsoft monitora todos os serviços do Azure Search para disponibilidade de 99,9% por contratos de nível de serviço (SLA). Se o serviço estiver lento ou a taxa de transferência de solicitação estiver abaixo dos limites de SLA, as equipes de suporte analisam os arquivos de log disponíveis para resolver o problema.

Em termos de informações gerais sobre o serviço, você pode obter informações das seguintes maneiras:

* No portal, no painel de serviço, por meio de notificações, propriedades e mensagens de status.
* Usando o [PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) para [obter propriedades do serviço](https://docs.microsoft.com/rest/api/searchmanagement/services), ou status no uso de recursos do índice.
* Por meio de [análise de tráfego de pesquisa](search-traffic-analytics.md), conforme observado anteriormente.

<a id="manage-keys"></a>

## <a name="manage-api-keys"></a>Gerenciar api-keys
Todas as solicitações para um serviço de pesquisa precisam de uma chave de api gerada especificamente para o seu serviço. Esta chave de API é o único mecanismo para autenticar o acesso ao ponto de extremidade do seu serviço de pesquisa. 

Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. Por meio de [permissões RBAC](#rbac), você pode excluir ou ler as chaves, mas você não pode substituir uma chave por uma senha definida pelo usuário. 

Dois tipos de chaves são usados para acessar seu serviço de pesquisa:

* Admin (válida para qualquer operação de leitura e gravação em relação ao serviço)
* Consulta (válida para operações somente leitura, como consultas em um índice)

Uma chave de api admin é criada quando o serviço é provisionado. Há duas chaves de administração, designadas como *primária* e *secundária* para mantê-las de forma linear, mas na verdade elas são intercambiáveis. Cada serviço tem duas chaves admin para que você possa derrubar uma sem perder o acesso ao seu serviço. Você pode gerar novamente as duas chaves admin, mas não pode aumentar a contagem total de chaves admin. Pode haver no máximo duas chaves admin por serviço de pesquisa.

Chaves de consulta foram criadas para aplicativos cliente que chamam a Pesquisa diretamente. Você pode criar até 50 chaves de consulta. No código do aplicativo, você pode especificar a URL de pesquisa e uma chave de api de consulta para permitir o acesso somente leitura para o serviço. O código do aplicativo também especifica o índice usado pelo seu aplicativo. Juntos, o ponto de extremidade, uma chave de api para acesso somente leitura e um índice de destino definem o nível de acesso e escopo da conexão de seu aplicativo cliente.

Para obter ou gerar chaves de API novamente, abra o painel do serviço. Clique em **CHAVES** para abrir a página de gerenciamento de chaves. Os comandos para gerar novamente ou criar chaves estão no topo da página. Por padrão, somente as chaves admin são criadas. As chaves de api de consulta devem ser criadas manualmente.

 ![][9]

<a id="rbac"></a>

## <a name="secure-api-keys"></a>Proteger api-keys
A chave de segurança é protegida restringindo o acesso por meio do portal ou interfaces do Gerenciador de Recursos (PowerShell ou interface de linha de comando). Conforme observado, os administradores de assinatura podem exibir e gerar novamente todas as chaves de api. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves admin.

1. No painel de serviço, clique no ícone de acesso para abrir a folha de Usuários.
   ![][7]
2. Em Usuários, analise as atribuições de função existentes. Conforme o esperado, os administradores de Assinatura já possuem acesso total ao serviço por meio da função Proprietário.
3. Para analisar mais, clique em **Administradores de assinatura** e, em seguida, expanda a lista de atribuição de função para ver quem tem direitos de coadministração no seu serviço de pesquisa.

Outra maneira de exibir permissões de acesso é clicar em **Funções** na folha de Usuários. Isso exibe funções disponíveis e o número de usuários ou grupos atribuídos a cada função.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorar o uso de recursos
No painel, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e a algumas métricas que você pode obter ao consultar o serviço. No painel do serviço, na seção Uso, é possível determinar rapidamente se os níveis de recurso da partição estão adequados para o seu aplicativo.

Usando a API do serviço de pesquisa, você pode obter uma contagem dos documentos e índices. Há limites associados a essas contagens com base na camada de preços. Para saber mais, confira [Limites de serviço de pesquisa](search-limits-quotas-capacity.md). 

* [Obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contar documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents)

> [!NOTE]
> Os comportamentos de cache podem aumentar um limite temporariamente. Por exemplo, ao usar o serviço compartilhado, você pode ver uma contagem de documentos que ultrapassa o limite de 10.000. O aumento é temporário e será detectado na próxima verificação de aplicação do limite. 
> 
> 

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastre e interrupções de serviço

Embora possamos recuperar seus dados, o Azure Search não fornece failover instantâneo do serviço se há uma interrupção no cluster ou no nível do datacenter. Se um cluster falhar no datacenter, a equipe de operações detectará e trabalhará para restaurar o serviço. Haverá tempo de inatividade durante a restauração do serviço. É possível solicitar créditos de serviço para compensar a indisponibilidade do serviço de acordo com o [SLA (Contrato de Nível de Serviço)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se o serviço contínuo for necessário em caso de falhas catastróficas fora do controle da Microsoft, você poderá [provisionar um serviço adicional](search-create-service-portal.md) em outra região e implementar uma estratégia de replicação geográfica para garantir que os índices seja totalmente redundantes em todos os serviços.

Os clientes que usam [indexadores](search-indexer-overview.md) para popular e atualizar índices podem lidar com a recuperação de desastre por meio de indexadores específicos à geografia utilizando a mesma fonte de dados. Dois serviços em regiões diferentes, cada um executando um indexador, poderiam indexar da mesma fonte de dados para obter redundância geográfica. Se você estiver indexando fontes de dados que também são com redundância geográfica, lembre-se de que a indexadores do Azure Search só podem executar a indexação incremental de réplicas primárias. Em um evento de failover, verifique se você apontou novamente o indexador para a nova réplica primária. 

Se você não usar indexadores, você usará o código do aplicativo para enviar objetos e dados por push para diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [Desempenho e otimização no Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Backup e restauração

Como o Azure Search não é uma solução de armazenamento de dados primário, não fornecemos um mecanismo formal de backup e restauração de autoatendimento. O código do aplicativo usado para criar e popular um índice é a opção de restauração de fato, caso você exclua um índice por engano. 

Para recompilar um índice, exclua-o (supondo que ele exista), recrie o índice no serviço e recarregue-o recuperando dados do armazenamento de dados primário. Como alternativa, você poderá contatar o [atendimento ao cliente]() para recuperar índices, caso haja uma interrupção regional.


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
Após períodos de grandes volumes de consulta, você poderá reduzir réplicas quando as cargas de pesquisa tiverem se normalizado (por exemplo, após as vendas de final de ano).

Para fazer isso, mova o controle deslizante de réplicas para um número mais baixo. Não são necessárias mais medidas de sua parte. Reduzir o número de réplicas libera máquinas virtuais no datacenter. Suas operações de consulta e ingestão de dados passarão a ser executadas em menos VMs do que antes. O limite mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover partições
Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode precisar tomar algumas medidas se estiver usando armazenamento a mais que pode ser reduzido. Por exemplo, se a sua solução estiver usando três partições, fazendo o downsizing de uma ou duas partições gerará um erro se o novo espaço de armazenamento é menor do que o necessário. Como é de se esperar, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

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
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



