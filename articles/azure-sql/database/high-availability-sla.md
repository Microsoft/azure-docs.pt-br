---
title: Alta disponibilidade
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Conheça o banco de dados SQL do Azure e recursos e recursos de alta disponibilidade do serviço SQL Instância Gerenciada
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: a14f8e0ba3ae5cca75cf6518320023703a6d1700
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626377"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Alta disponibilidade para o banco de dados SQL do Azure e o SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O objetivo da arquitetura de alta disponibilidade no banco de dados SQL do Azure e no SQL Instância Gerenciada é garantir que seu banco de dados esteja em execução no mínimo 99,99% do tempo (para obter mais informações sobre SLA específico para diferentes camadas, consulte [SLA para o banco de dados SQL do Azure e o sql instância gerenciada](https://azure.microsoft.com/support/legal/sla/sql-database/)), sem se preocupar com o impacto de operações de manutenção e interrupções. O Azure manipula automaticamente tarefas de manutenção críticas, como aplicação de patches, backups, atualizações de SQL do Windows e do Azure, bem como eventos não planejados, como hardware subjacente, software ou falhas de rede.  Quando o banco de dados subjacente no banco de dados SQL do Azure for corrigido ou passar por failover, o tempo de inatividade não será perceptível se você [empregar a lógica de repetição](develop-overview.md#resiliency) em seu aplicativo. O banco de dados SQL e o SQL Instância Gerenciada podem ser recuperados rapidamente mesmo nas circunstâncias mais críticas, garantindo que seus dados estejam sempre disponíveis.

A solução de alta disponibilidade foi projetada para garantir que os dados confirmados nunca sejam perdidos devido a falhas, que as operações de manutenção não afetam sua carga de trabalho e que o banco de dados não será um ponto único de falha em sua arquitetura de software. Não há nenhuma janela de manutenção ou tempo de inatividade que deva exigir que você pare a carga de trabalho enquanto o banco de dados é atualizado ou está em manutenção.

Há dois modelos arquitetônicos de alta disponibilidade:

- **Modelo de disponibilidade padrão** baseado em uma separação de computação e armazenamento.  Ele se baseia na alta disponibilidade e na confiabilidade da camada de armazenamento remoto. Essa arquitetura destina-se a aplicativos de negócios orientados a orçamento que podem tolerar alguma degradação de desempenho durante atividades de manutenção.
- **Modelo de disponibilidade Premium** baseado em um cluster de processos do mecanismo de banco de dados. Ele depende do fato de que sempre há um quorum de nós do mecanismo de banco de dados disponíveis. Essa arquitetura destina-se a aplicativos de missão crítica com alto desempenho de e/s, alta taxa de transações e garante o impacto mínimo no desempenho da carga de trabalho durante atividades de manutenção.

O banco de dados SQL e o SQL Instância Gerenciada são executados na versão estável mais recente do mecanismo de banco de dados do SQL Server e no sistema operacional Windows, e a maioria dos usuários não observa que as atualizações são executadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Disponibilidade redundante local da camada de serviço Basic, Standard e Uso Geral

As camadas de serviço básico, Standard e Uso Geral aproveitam a arquitetura de disponibilidade padrão para computação sem servidor e provisionada. A figura a seguir mostra quatro nós diferentes com a computação separada e as camadas de armazenamento.

![Separação de computação e armazenamento](./media/high-availability-sla/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação sem monitoração de estado que executa o `sqlservr.exe` processo e contém somente dados transitórios e armazenados em cache, como o tempdb, os bancos de dado modelo no SSD anexado e o cache de planos, o pool de buffers e o pool columnstore na memória. Esse nó sem estado é operado pelo Service Fabric do Azure que inicializa `sqlservr.exe` , controla a integridade do nó e executa o failover para outro nó, se necessário.
- Uma camada de dados com monitoração de estado com os arquivos de banco (. MDF/. ldf) armazenados no armazenamento de BLOBs do Azure. O armazenamento de BLOBs do Azure tem recursos internos de redundância e disponibilidade de dados. Ele garante que todos os registros no arquivo de log ou na página do arquivo de dados serão preservados mesmo se o `sqlservr.exe` processo falhar.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado ou uma falha for detectada, o Azure Service Fabric moverá o processo sem estado `sqlservr.exe` para outro nó de computação sem estado com capacidade livre suficiente. Os dados no armazenamento de BLOBs do Azure não são afetados pela movimentação e os arquivos de dados/log são anexados ao processo inicializado recentemente `sqlservr.exe` . Esse processo garante a disponibilidade de 99,99%, mas uma carga de trabalho pesada pode enfrentar alguma degradação de desempenho durante a transição, uma vez que o novo `sqlservr.exe` processo começa com o cache frio.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Disponibilidade com redundância de zona de camada de serviço Uso Geral (visualização)

A configuração com redundância de zona para a camada de serviço de uso geral é oferecida para computação sem servidor e provisionada. Essa configuração utiliza [zonas de disponibilidade do Azure](../../availability-zones/az-overview.md)   para replicar bancos de dados em vários locais físicos em uma região do Azure.Ao selecionar a redundância de zona, você pode tornar seus novos e existentes serverlesss e provisionado bancos de dados individuais de uso geral e pools elásticos resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração da lógica do aplicativo.

A configuração com redundância de zona para a camada de uso geral tem duas camadas:  

- Uma camada de dados com monitoração de estado com os arquivos de banco (. MDF/. ldf) armazenados em ZRS (armazenamento com redundância de zona). Usando o [ZRS](../../storage/common/storage-redundancy.md) , os arquivos de dados e de log são copiados de forma síncrona em três zonas de disponibilidade do Azure fisicamente isoladas.
- Uma camada de computação sem monitoração de estado que executa o processo de sqlservr.exe e contém somente dados transitórios e em cache, como TempDB, bancos de dado de modelo no SSD anexado e cache de planos, pool de buffers e pool columnstore na memória. Esse nó sem estado é operado pelo Service Fabric do Azure que inicializa sqlservr.exe, controla a integridade do nó e executa o failover para outro nó, se necessário. Para bancos de dados de uso geral com e sem servidor com redundância de zona, os nós com capacidade de reposição estão prontamente disponíveis em outros Zonas de Disponibilidade para failover.

A versão com redundância de zona da arquitetura de alta disponibilidade para a camada de serviço de uso geral é ilustrada pelo seguinte diagrama:

![Configuração com redundância de zona para fins gerais](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> A configuração com redundância de zona só estará disponível quando o hardware de computação Gen5 estiver selecionado. Este recurso não está disponível no SQL Instância Gerenciada. A configuração com redundância de zona para camada de uso geral sem servidor e provisionada só está disponível nas seguintes regiões: leste dos EUA, leste dos EUA 2, oeste dos EUA 2, Europa Setentrional, Europa Ocidental, Sudeste Asiático, leste da Austrália, leste do Japão, Sul do Reino Unido e França central.

> [!NOTE]
> Uso Geral bancos de dados com um tamanho de 80 VCORE podem apresentar degradação de desempenho com configuração com redundância de zona. Além disso, as operações como backup, restauração, cópia de banco de dados, configuração de relações de DR geográfica e downgrade de um banco de dados com redundância de zona de Comercialmente Crítico para Uso Geral podem apresentar um desempenho mais lento para qualquer um de mais de 1 TB. Consulte nossa [documentação de latência sobre como dimensionar um banco de dados](single-database-scale.md) para obter mais informações.
> 
> [!NOTE]
> A visualização não é coberta pela instância reservada

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Disponibilidade com redundância local da camada de serviço Premium e Comercialmente Crítico

As camadas de serviço Premium e Comercialmente Crítico aproveitam o modelo de disponibilidade Premium, que integra os recursos de computação ( `sqlservr.exe` processo) e o armazenamento (SSD anexado localmente) em um único nó. A alta disponibilidade é obtida com a replicação da computação e do armazenamento para nós adicionais, criando um cluster de três a quatro nós.

![Cluster de nós de mecanismo de banco de dados](./media/high-availability-sla/business-critical-service-tier.png)

Os arquivos de banco de dados subjacentes (. MDF/. ldf) são colocados no armazenamento SSD anexado para fornecer uma e/s de latência muito baixa para sua carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante à SQL Server [grupos de disponibilidade Always on](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária que é acessível para cargas de trabalho de leitura/gravação do cliente e até três réplicas secundárias (computação e armazenamento) que contêm cópias de dados. O nó primário envia constantemente as alterações para os nós secundários e garante que os dados sejam sincronizados com pelo menos uma réplica secundária antes de confirmar cada transação. Esse processo garante que, se o nó primário falhar por algum motivo, sempre haverá um nó totalmente sincronizado para o failover. O failover é iniciado pelo Service Fabric do Azure. Depois que a réplica secundária se tornar o novo nó primário, outra réplica secundária será criada para garantir que o cluster tenha nós suficientes (conjunto de quorum). Depois que o failover for concluído, as conexões SQL do Azure serão redirecionadas automaticamente para o novo nó primário.

Como um benefício extra, o modelo de disponibilidade Premium inclui a capacidade de redirecionar conexões SQL do Azure somente leitura para uma das réplicas secundárias. Esse recurso é chamado [de expansão de leitura](read-scale-out.md). Ele fornece uma capacidade de computação adicional de 100% sem custo adicional para operações somente leitura fora do carregamento, como cargas de trabalho analíticas, da réplica primária.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Disponibilidade redundante da zona da camada de serviço Premium e Comercialmente Crítico 

Por padrão, o cluster de nós para o modelo de disponibilidade Premium é criado no mesmo datacenter. Com a introdução do [zonas de disponibilidade do Azure](../../availability-zones/az-overview.md), o banco de dados SQL pode posicionar réplicas diferentes do banco de dados comercialmente crítico em diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../../traffic-manager/traffic-manager-overview.md). Como a configuração com redundância de zona nas camadas de serviço Premium ou Comercialmente Crítico não cria redundância de banco de dados adicional, você pode habilitá-la sem custo adicional. Ao selecionar uma configuração com redundância de zona, você pode tornar os bancos de dados Premium ou Comercialmente Crítico resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração na lógica do aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico existentes para a configuração com redundância de zona.

Como os bancos de dados com redundância de zona têm réplicas em data centers diferentes com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, portanto, afetar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Esse processo é uma operação online semelhante à atualização da camada de serviço normal. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Ao usar a camada de Comercialmente Crítico, a configuração com redundância de zona só estará disponível quando o hardware de computação Gen5 for selecionado. Para obter informações atualizadas sobre as regiões que dão suporte a bancos de dados com redundância de zona, consulte [suporte a serviços por região](../../availability-zones/az-region.md).

> [!NOTE]
> Este recurso não está disponível no SQL Instância Gerenciada.

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:

![Arquitetura de alta disponibilidade com redundância de zona](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Disponibilidade da camada de serviço de hiperescala

A arquitetura da camada de serviço de hiperescala é descrita na [arquitetura de funções distribuídas](./service-tier-hyperscale.md#distributed-functions-architecture) e só está disponível no momento para o banco de dados SQL, não o SQL instância gerenciada.

![Arquitetura funcional de hiperescala](./media/high-availability-sla/hyperscale-architecture.png)

O modelo de disponibilidade em hiperescala inclui quatro camadas:

- Uma camada de computação sem estado que executa os `sqlservr.exe` processos e contém somente dados transitórios e armazenados em cache, como o cache RBPEX de não abrangendo, tempdb, banco de dado modelo, etc. no SSD anexado, e no cache de planos, no pool de buffers e no pool columnstore na memória. Essa camada sem estado inclui a réplica de computação primária e, opcionalmente, um número de réplicas de computação secundárias que podem servir como destinos de failover.
- Uma camada de armazenamento sem estado formada por servidores de página. Essa camada é o mecanismo de armazenamento distribuído para os `sqlservr.exe` processos em execução nas réplicas de computação. Cada servidor de página contém apenas dados transitórios e em cache, como cobrindo o cache RBPEX no SSD anexado e páginas de dados armazenadas em cache na memória. Cada servidor de página tem um servidor de páginas emparelhado em uma configuração ativo-ativo para fornecer balanceamento de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de log de transações com estado formada pelo nó de computação executando o processo do serviço de log, a zona de aterrissagem do log de transações e o armazenamento de longo prazo do log de transações. Zona de aterrissagem e armazenamento de longo prazo usam o armazenamento do Azure, que fornece disponibilidade e [redundância](../../storage/common/storage-redundancy.md) para o log de transações, garantindo a durabilidade dos dados para transações confirmadas.
- Uma camada de armazenamento de dados com monitoração de estado com os arquivos (. MDF/. ndf) armazenados no armazenamento do Azure e são atualizados por servidores de página. Essa camada usa recursos de [redundância](../../storage/common/storage-redundancy.md) e disponibilidade de dados do armazenamento do Azure. Ele garante que cada página em um arquivo de dados será preservada mesmo se os processos em outras camadas de falha de arquitetura de hiperescala ou se os nós de computação falharem.

Os nós de computação em todas as camadas de hiperescala são executados no Azure Service Fabric, que controla a integridade de cada nó e executa failovers para nós íntegros disponíveis, conforme necessário.

Para obter mais informações sobre alta disponibilidade em hiperescala, consulte [alta disponibilidade do banco de dados em hiperescala](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Recuperação Acelerada de Banco de Dados (ADR)

A [ADR (recuperação de banco de dados acelerada)](../accelerated-database-recovery.md) é um novo recurso de mecanismo de banco de dados que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longa execução. ADR está disponível no momento para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Testando a resiliência de falha do aplicativo

A alta disponibilidade é uma parte fundamental do Banco de Dados SQL e da plataforma de Instância Gerenciada de SQL que funciona de forma transparente para o aplicativo de banco de dados. No entanto, reconhecemos que talvez você queira testar como as operações de failover automático iniciadas durante os eventos planejados ou não planejados afetariam um aplicativo antes de implantá-lo na produção. Você pode disparar um failover manualmente chamando uma API especial para reiniciar um banco de dados, um pool elástico ou uma instância gerenciada. No caso de um banco de dados com redundância de zona ou pool elástico, a chamada à API resultaria no redirecionamento de conexões de cliente para o novo primário em uma zona de disponibilidade diferente da zona de disponibilidade do primário antigo. Assim, além de testar como o failover afeta as sessões de banco de dados existentes, você também pode verificar se ele altera o desempenho de ponta a ponta devido a alterações na latência de rede. Como a operação de reinicialização é intrusiva e um grande número delas poderia enfatizar a plataforma, apenas uma chamada de failover é permitida a cada 15 minutos para cada banco de dados, pool elástico ou instância gerenciada.

Um failover pode ser iniciado usando o PowerShell, a API REST ou CLI do Azure:

|Tipo de implantação|PowerShell|API REST| CLI do Azure|
|:---|:---|:---|:---|
|Banco de dados|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Failover de banco de dados](/rest/api/sql/databases/failover)|[AZ REST](/cli/azure/reference-index#az-rest) pode ser usado para invocar uma chamada à API rest de CLI do Azure|
|Pool elástico|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Failover de pool elástico](/rest/api/sql/elasticpools/failover)|[AZ REST](/cli/azure/reference-index#az-rest) pode ser usado para invocar uma chamada à API rest de CLI do Azure|
|Banco de Dados SQL|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Instâncias gerenciadas-failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[AZ SQL mi failover](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> O comando de failover não está disponível para réplicas secundárias legíveis de bancos de dados de hiperescala.

## <a name="conclusion"></a>Conclusão

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada apresentam uma solução de alta disponibilidade interna, que está profundamente integrada com a plataforma Azure. Depende de Service Fabric para detecção de falha e recuperação, no armazenamento de BLOBs do Azure para proteção de dados e em Zonas de Disponibilidade para maior tolerância a falhas (como mencionado anteriormente no documento que ainda não se aplica ao Instância Gerenciada SQL do Azure). Além disso, o banco de dados SQL e o SQL Instância Gerenciada aproveitam a tecnologia do grupo de disponibilidade Always On da instância SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam totalmente os benefícios de um modelo de armazenamento misto e ofereçam suporte aos SLAs mais exigentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../../traffic-manager/traffic-manager-overview.md)
- Saiba [como iniciar um failover manual no SQL instância gerenciada](../managed-instance/user-initiated-failover.md)
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md)
