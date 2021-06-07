---
title: Visão geral de operações de gerenciamento
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a duração e as melhores práticas de operações de gerenciamento da Instância Gerenciada de SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 2da7311e61aa39be69a6a0a29eff686baaad7ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323185"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Visão geral de operações de gerenciamento da Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Instância Gerenciada de SQL do Azure fornece operações de gerenciamento que você poderá usar para implantar automaticamente novas instâncias gerenciadas, atualizar propriedades de instância e excluir instâncias quando não forem mais necessárias.

## <a name="what-are-management-operations"></a>O que são operações de gerenciamento?

Todas as operações de gerenciamento podem ser categorizadas da seguinte maneira:

- Implantação de instância (nova criação de instância)
- Atualização de instância (alterando propriedades de instância, como vCores ou armazenamento reservado)
- Exclusão da instância

Para dar suporte a [implantações nas redes virtuais do Azure](../../virtual-network/virtual-network-for-azure-services.md) e fornecer isolamento e segurança para clientes, a Instância Gerenciada de SQL depende de [clusters virtuais](connectivity-architecture-overview.md#high-level-connectivity-architecture). O cluster virtual representa um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede da rede virtual do cliente. Essencialmente, cada instância gerenciada implantada em uma sub-rede vazia resulta em uma nova construção de cluster virtual.

As operações de gerenciamento subsequentes em instâncias gerenciadas podem afetar o cluster virtual subjacente. Alterações que afetam o cluster virtual subjacente podem afetar a duração de operações de gerenciamento, porque a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa ser considerada quando você planeja novas implantações ou atualizações em instâncias gerenciadas existentes.


## <a name="duration"></a>Duração

A duração das operações no cluster virtual pode variar, mas elas normalmente têm a duração mais longa. 

Estes são os valores que você normalmente pode esperar, com base em dados de telemetria de serviço existentes:

- **Criação de cluster virtual**:  a criação é uma etapa síncrona em operações de gerenciamento de instância. <br/> **90% das operações são concluídas em 4 horas**.
- **Redimensionamento do cluster virtual (expansão ou redução)** : A expansão é uma etapa síncrona; já a redução é executada de forma assíncrona (sem afetar a duração das operações de gerenciamento de instância). <br/>**90% das expansões de cluster são concluídas em menos de 2,5 horas**.
- **Exclusão do cluster virtual**: A exclusão é uma etapa assíncrona, mas também pode ser [iniciada manualmente](virtual-cluster-delete.md) em um cluster virtual vazio; nesse caso, ela é executada de forma síncrona. <br/>**90% das exclusões de cluster virtual são concluídas em 1,5 horas**.

Além disso, o gerenciamento de instâncias também pode incluir uma das operações em bancos de dados hospedados, o que resulta em durações mais longas:

- **Anexar arquivos de banco de dados do Armazenamento do Azure**:  Uma etapa síncrona, como a escala de computação (vCores), ou o aumento ou a redução do armazenamento na camada de serviço Uso Geral. <br/>**90% dessas operações são concluídas em 5 minutos**.
- **Propagação de grupo de disponibilidade Always On**: uma etapa síncrona, como a computação (vCores), a escala de armazenamento na camada de serviço Comercialmente Crítico ou a alteração da camada de serviço Uso Geral para Comercialmente Crítico (ou vice-versa). A duração dessa operação é proporcional ao tamanho total do banco de dados e à atividade atual do banco de dados (número de transações ativas). A atividade do banco de dados quando atualiza uma instância pode introduzir uma variância significativa na duração total. <br/>**90% dessas operações são executadas a 220 GB/hora ou mais**.

As seguintes tabelas resumem as operações e as durações gerais típicas, com base na categoria da operação:

**Categoria: Implantação**

|Operação  |Segmento de execução longa  |Duração estimada  |
|---------|---------|---------|
|Primeira instância em uma sub-rede vazia|Criação de cluster virtual|90% das operações são concluídas em 4 horas.|
|Primeira instância de outra geração de hardware em uma sub-rede não vazia (por exemplo, primeira instância de Geração 5 em uma sub-rede com instâncias de Geração 4)|Criação de cluster virtual<sup>1</sup>|90% das operações são concluídas em 4 horas.|
|Criação de instância subsequente dentro da sub-rede não vazia (2ª, 3ª instância e assim por diante)|Redimensionamento de cluster virtual|90% das operações são concluídas em 2,5 horas.|
| | | 

<sup>1</sup> O cluster virtual é criado por geração de hardware.

**Categoria: Atualização**

|Operação  |Segmento de execução longa  |Duração estimada  |
|---------|---------|---------|
|Alteração na propriedade de instância (senha de administrador, logon do Azure AD, sinalizador de Benefício Híbrido do Azure)|N/D|Até 1 minuto.|
|Expansão/redução do armazenamento de instância (camada de serviço Uso Geral)|Anexação de arquivos de banco de dados|90% das operações são concluídas em 5 minutos.|
|Expansão/redução do armazenamento de instância (camada de serviço Comercialmente Crítico)|- Redimensionamento de cluster virtual<br>- Propagação de grupo de disponibilidade Always On|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora).|
|Expansão e redução verticais de computação de instância (vCores) (Uso Geral)|- Redimensionamento de cluster virtual<br>- Anexação de arquivos de banco de dados|90% das operações são concluídas em 2,5 horas.|
|Expansão e redução verticais de computação de instância (vCores) (Comercialmente Crítico)|- Redimensionamento de cluster virtual<br>- Propagação de grupo de disponibilidade Always On|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora).|
|Alteração da camada de serviço de instância (Uso Geral para Comercialmente Crítico e vice-versa)|- Redimensionamento de cluster virtual<br>- Propagação de grupo de disponibilidade Always On|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora).|
| | | 

**Categoria: Exclusão**

|Operação  |Segmento de execução longa  |Duração estimada  |
|---------|---------|---------|
|Exclusão da instância|Back da parte final do log de todos os bancos de dados|90% das operações são concluídas em até 1 minuto.<br>Observação: se a última instância na sub-rede for excluída, essa operação agendará a exclusão do cluster virtual após 12 horas.<sup>1</sup>|
|Exclusão de cluster virtual (como operação iniciada pelo usuário)|Exclusão do cluster virtual|90% das operações terminam em até 1,5 hora.|
| | | 

<sup>1</sup>12 horas é a configuração atual, mas isso está sujeito a alterações no futuro. Se você precisar excluir um cluster virtual antes (para liberar a sub-rede, por exemplo), confira [Excluir uma sub-rede depois de excluir uma instância gerenciada](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Disponibilidade da instância

A Instância Gerenciada de SQL **está disponível durante operações de atualização**, exceto por um breve tempo de inatividade causado pelo failover que ocorre no final da atualização. Normalmente, ele dura até 10 segundos mesmo no caso de transações de longa execução interrompidas, graças à [recuperação acelerada de banco de dados](../accelerated-database-recovery.md).

A Instância Gerenciada de SQL não está disponível para aplicativos cliente durante operações de implantação e exclusão.

> [!IMPORTANT]
> Não é recomendável dimensionar a computação ou o armazenamento da Instância Gerenciada de SQL do Azure nem alterar a camada de serviço ao mesmo tempo que transações de longa execução (importação de dados, trabalhos de processamento de dados, recompilação de índice etc.). O failover do banco de dados no final da operação cancela todas as transações em andamento. 

## <a name="management-operations-steps"></a>Etapas de operações de gerenciamento

As operações de gerenciamento são compostas por várias etapas. Com a [API de operações introduzida](management-operations-monitor.md), essas etapas são expostas para o subconjunto de operações (implantação e atualização). A operação de implantação é composta por três etapas enquanto a operação de atualização é executada em seis etapas. Para obter detalhes sobre a duração das operações, confira a seção [duração de operações de gerenciamento](#duration). As etapas são listadas por ordem de execução.

### <a name="managed-instance-deployment-steps"></a>Etapas de implantação da instância gerenciada

|Nome da etapa  |Descrição da etapa  |
|----|---------|
|Validação de solicitação |Os parâmetros enviados são validados. No caso de configuração incorreta, a operação falhará com um erro. |
|Redimensionamento/criação de cluster virtual |Dependendo do estado da sub-rede, o cluster virtual entrará em criação ou redimensionamento. |
|Nova inicialização da instância SQL |O processo SQL é iniciado no cluster virtual implantado. |

### <a name="managed-instance-update-steps"></a>Etapas de atualização de instância gerenciada

|Nome da etapa  |Descrição da etapa  |
|----|---------|
|Validação de solicitação | Os parâmetros enviados são validados. No caso de configuração incorreta, a operação falhará com um erro. |
|Redimensionamento/criação de cluster virtual |Dependendo do estado da sub-rede, o cluster virtual entrará em criação ou redimensionamento. |
|Nova inicialização da instância SQL | O processo SQL é iniciado no cluster virtual implantado. |
|Como propagar/anexar arquivos de banco de dados |Dependendo do tipo da operação de atualização, a propagação do banco de dados ou a anexação de arquivos de banco de dados é executada. |
|Como preparar o failover |Depois que os dados tiverem sido propagados ou os arquivos de banco de dados tiverem sido reanexados, o sistema vai se preparar para o failover. Quando tudo estiver pronto, o failover será executado **com um breve tempo de inatividade**. |
|Limpeza da instância SQL antiga |Como remover o antigo processo SQL antigo do cluster virtual |

> [!NOTE]
> Em decorrência da escala de instâncias, o cluster virtual subjacente passará pelo processo de liberação de capacidade não utilizada e uma possível desfragmentação da capacidade, o que poderia afetar instâncias que não participaram de operações de criação/escala. 


## <a name="management-operations-cross-impact"></a>Operações de gerenciamento com impacto cruzado

As operações de gerenciamento em uma instância gerenciada pode afetar outras operações de gerenciadas das instâncias colocadas dentro do mesmo cluster virtual:

- **As operações de restauração de execução prolongada** em um cluster virtual colocarão em espera outras operações de escala ou de criação de instância na mesma sub-rede.<br/>**Exemplo:** se houver uma operação de restauração de execução prolongada e uma solicitação de criação ou escala na mesma sub-rede, essa solicitação levará mais tempo para ser concluída, pois aguardará a conclusão da operação de restauração antes de continuar.

- Uma operação de **criação ou escala de instância subsequente** é colocada em espera pela escala ou criação de instância iniciada anteriormente que iniciou o redimensionamento do cluster virtual.<br/>**Exemplo:** se houver várias solicitações de criação e/ou escala na mesma sub-rede no mesmo cluster virtual e uma delas iniciar o redimensionamento de um cluster virtual, todas as solicitações que foram enviadas mais de cinco minutos após a solicitação de operação inicial durarão mais do que o esperado, pois elas precisarão aguardar a conclusão do redimensionamento antes de continuar.

- **As operações de criação/escala enviadas em uma janela de cinco minutos** serão colocadas em lote e executadas em paralelo.<br/>**Exemplo:** somente um redimensionamento de cluster virtual será executado para todas as operações enviadas em uma janela de cinco minutos (medida desde o momento da execução da primeira solicitação de operação). Caso outra solicitação seja enviada mais de cinco minutos depois do envio da primeira, ela aguardará a conclusão do redimensionamento do cluster virtual antes de iniciar a execução.

> [!IMPORTANT]
> As operações de gerenciamento colocadas em espera devido a outra operação em andamento serão automaticamente retomadas quando as condições para a retomada forem atendidas. Nenhuma ação do usuário é necessária para retomar as operações de gerenciamento temporariamente em pausa.

## <a name="monitoring-management-operations"></a>Como monitorar operações de gerenciamento

Para saber como monitorar o progresso e o status da operação de gerenciamento, confira [Monitorar operações de gerenciamento](management-operations-monitor.md).

## <a name="canceling-management-operations"></a>Cancelando operações de gerenciamento

Para saber como cancelar a operação de gerenciamento, confira [Cancelar operações de gerenciamento](management-operations-cancel.md).


## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira instância gerenciada, confira o [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e de comparação, confira [Recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração de VNet, confira [Configuração de VNet de Instância Gerenciada de SQL](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, confira [Criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o Serviço de Migração de Banco de Dados do Azure para migração, confira [Migração de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).
