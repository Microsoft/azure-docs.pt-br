---
title: Visão geral da proteção de dados
titleSuffix: Azure Storage
description: As opções de proteção de dados disponíveis para seu armazenamento de BLOBs e dados de Azure Data Lake Storage Gen2 permitem que você proteja seus dados sejam excluídos ou substituídos. Se você precisar recuperar dados que foram excluídos ou substituídos, este guia poderá ajudá-lo a escolher a opção de recuperação mais adequada para seu cenário.
services: storage
author: tamram
ms.service: storage
ms.date: 03/22/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: afd98e629500bc90cc9ddd1ed4ab2472f733e845
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803627"
---
# <a name="data-protection-overview"></a>Visão geral da proteção de dados

O armazenamento do Azure fornece proteção de dados para armazenamento de BLOBs e Azure Data Lake Storage Gen2 para ajudá-lo a se preparar para cenários em que você precisa recuperar dados que foram excluídos ou substituídos. É importante pensar em como proteger melhor seus dados antes que ocorra um incidente que possa comprometa-lo. Este guia pode ajudá-lo a decidir com antecedência quais recursos de proteção de dados seu cenário requer e como implementá-los. Se você precisar recuperar dados que foram excluídos ou substituídos, essa visão geral também fornecerá orientação sobre como proceder, com base em seu cenário.

Na documentação do armazenamento do Azure, a *proteção de dados* refere-se a estratégias para proteger a conta de armazenamento e os dados dentro dele de serem excluídos ou modificados, ou para restaurar dados depois que eles forem excluídos ou modificados. O armazenamento do Azure também oferece opções de *recuperação de desastre*, incluindo vários níveis de redundância para proteger seus dados contra interrupções de serviço devido a problemas de hardware ou desastres naturais, e failover gerenciado pelo cliente caso o Data Center na região primária fique indisponível. Para obter mais informações sobre como os dados são protegidos contra interrupções de serviço, consulte [recuperação de desastre](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Recomendações para proteção de dados básica

Se você estiver procurando cobertura básica de proteção de dados para sua conta de armazenamento e os dados que ela contém, a Microsoft recomenda executar as seguintes etapas para começar com:

- Configure um bloqueio de Azure Resource Manager na conta de armazenamento para proteger a conta contra alterações de exclusão ou configuração. [Saiba Mais...](../common/lock-account-resource.md)
- Habilite exclusão reversível de contêiner para a conta de armazenamento para recuperar um contêiner excluído e seu conteúdo. [Saiba Mais...](soft-delete-container-enable.md)
- Salve o estado de um blob em intervalos regulares:
  - Para cargas de trabalho de armazenamento de BLOBs, habilite o controle de versão de BLOB para salvar automaticamente o estado de seus dados cada vez que um blob for excluído ou substituído. [Saiba Mais...](versioning-enable.md)
  - Para cargas de trabalho de Azure Data Lake Storage, faça instantâneos manuais para salvar o estado de seus dados em um determinado momento. [Saiba Mais...](snapshots-overview.md)

Essas opções, bem como opções de proteção de dados adicionais para outros cenários, são descritas mais detalhadamente na seção a seguir.

Para obter uma visão geral dos custos envolvidos com esses recursos, consulte [Resumo das considerações de custo](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Visão geral das opções de proteção de dados

A tabela a seguir resume as opções disponíveis no armazenamento do Azure para cenários comuns de proteção de dados. Escolha os cenários que se aplicam à sua situação para saber mais sobre as opções disponíveis para você. Observe que nem todos os recursos estão disponíveis no momento para contas de armazenamento com um namespace hierárquico habilitado.

| Cenário | Opção de proteção de dados | Recomendações | Benefício de proteção | Disponível para Data Lake Storage |
|--|--|--|--|--|
| Impedir que uma conta de armazenamento seja excluída ou modificada. | Bloqueio de Azure Resource Manager<br />[Saiba Mais...](../common/lock-account-resource.md) | Bloqueie todas as suas contas de armazenamento com um bloqueio de Azure Resource Manager para impedir a exclusão da conta de armazenamento. | Protege a conta de armazenamento contra alterações de exclusão ou configuração.<br /><br />Não protege contêineres ou BLOBs na conta de serem excluídos ou substituídos. | Sim |
| Impedir que um contêiner e seus BLOBs sejam excluídos ou modificados para um intervalo que você controla. | Política de imutabilidade em um contêiner<br />[Saiba Mais...](storage-blob-immutable-storage.md) | Defina uma política de imutabilidade em um contêiner para proteger documentos críticos para os negócios, por exemplo, para atender aos requisitos de conformidade legais ou regulatórios. | Protege um contêiner e seus blobs de todas as exclusões e substituições.<br /><br />Quando um controle legal ou uma política de retenção baseada em tempo está em vigor, a conta de armazenamento também é protegida contra exclusão. Os contêineres para os quais nenhuma política de imutabilidade foi definida não estão protegidos contra exclusão. | Sim, em versão prévia |
| Restaurar um contêiner excluído dentro de um intervalo especificado. | Exclusão reversível de contêiner (visualização)<br />[Saiba Mais...](soft-delete-container-overview.md) | Habilite a exclusão reversível de contêiner para todas as contas de armazenamento, com um intervalo de retenção mínimo de 7 dias.<br /><br />Habilite o controle de versão de BLOB e a exclusão reversível de blob junto com a exclusão reversível de contêiner para proteger BLOBs individuais em um contêiner.<br /><br />Armazene contêineres que exigem períodos de retenção diferentes em contas de armazenamento separadas. | Um contêiner excluído e seu conteúdo podem ser restaurados dentro do período de retenção.<br /><br />Somente operações em nível de contêiner (por exemplo, [excluir contêiner](/rest/api/storageservices/delete-container)) podem ser restauradas. A exclusão reversível de contêiner não permite que você restaure um blob individual no contêiner se esse blob for excluído. | Sim, em versão prévia |
| Salve automaticamente o estado de um blob em uma versão anterior quando ele for substituído ou excluído. | Controle de versão de BLOB<br />[Saiba Mais...](versioning-overview.md) | Habilite o controle de versão de BLOB, junto com exclusão reversível de contêiner e exclusão reversível de BLOB, para contas de armazenamento em que você precisa de proteção ideal para dados de BLOB.<br /><br />Armazene dados de BLOB que não exigem controle de versão em uma conta separada para limitar os custos. | Cada operação de exclusão ou substituição de blob cria uma nova versão. Um blob poderá ser restaurado de uma versão anterior se o blob for excluído ou substituído. | Não |
| Restaurar uma versão excluída de BLOB ou BLOB dentro de um intervalo especificado. | Exclusão reversível de BLOB<br />[Saiba Mais...](soft-delete-blob-overview.md) | Habilite a exclusão reversível de BLOB para todas as contas de armazenamento, com um intervalo de retenção mínimo de 7 dias.<br /><br />Habilite o controle de versão de BLOB e exclusão reversível de contêiner junto com a exclusão reversível de BLOB para proteção ideal de dados de BLOB.<br /><br />Armazenar blobs que exigem períodos de retenção diferentes em contas de armazenamento separadas. | Uma versão excluída de BLOB ou blob pode ser restaurada dentro do período de retenção. | Não |
| Restaurar um conjunto de blobs de blocos para um ponto anterior no tempo. | Restauração em um momento determinado<br />[Saiba Mais...](point-in-time-restore-overview.md) | Para usar a restauração pontual para reverter para um estado anterior, crie seu aplicativo para excluir blobs de blocos individuais em vez de excluir contêineres. | Um conjunto de blobs de blocos pode ser revertido para seu estado em um ponto específico no passado.<br /><br />Somente as operações executadas em blobs de blocos são revertidas. Quaisquer operações executadas em contêineres, blobs de páginas ou BLOBs de acréscimo não são revertidas. | Não |
| Salve manualmente o estado de um blob em um determinado ponto no tempo. | Instantâneo de BLOB<br />[Saiba Mais...](snapshots-overview.md) | Recomendado como uma alternativa ao controle de versão de blob quando o controle de versão não é apropriado para seu cenário, devido a custos ou outras considerações, ou quando a conta de armazenamento tem um namespace hierárquico habilitado. | Um blob poderá ser restaurado de um instantâneo se o blob for substituído. Se o blob for excluído, os instantâneos também serão excluídos. | Sim, em versão prévia |
| Um blob pode ser excluído ou substituído, mas os dados são copiados regularmente para uma segunda conta de armazenamento. | Distribua sua própria solução para copiar dados para uma segunda conta usando a replicação de objeto de armazenamento do Azure ou uma ferramenta como AzCopy ou Azure Data Factory. | Recomendado para proteção de paz de espírito contra ações inesperadas ou cenários imprevisíveis.<br /><br />Crie a segunda conta de armazenamento na mesma região que a conta primária para evitar incorrer em encargos de egresso. | Os dados podem ser restaurados da segunda conta de armazenamento se a conta principal for comprometida de alguma forma. | Há suporte para AzCopy e Azure Data Factory.<br /><br />Não há suporte para a replicação de objeto. |

## <a name="data-protection-by-resource-type"></a>Proteção de dados por tipo de recurso

A tabela a seguir resume as opções de proteção de dados do armazenamento do Azure de acordo com os recursos que elas protegem.

| Opção de proteção de dados | Protege uma conta contra exclusão | Protege um contêiner da exclusão | Protege um blob da exclusão | Protege um blob contra substituições |
|--|--|--|--|--|
| Bloqueio de Azure Resource Manager | Sim | Não<sup>1</sup> | Não | Não |
| Política de imutabilidade em um contêiner | Sim<sup>2</sup> | Sim | Sim | Sim |
| Exclusão reversível do contêiner | Não | Sim | Não | Não |
| Controle de versão do blob<sup>3</sup> | Não | Não | Sim | Sim |
| Exclusão reversível de blob<sup>3</sup> | Não | Não | Sim | Sim |
| Restauração pontual<sup>3</sup> | Não | Não | Sim | Sim |
| Instantâneo de BLOB | Não | Não | Não | Sim |
| Distribuir sua própria solução para copiar dados para uma segunda conta<sup>4</sup> | Não | Sim | Sim | Sim |

<sup>1</sup> um bloqueio de Azure Resource Manager não protege um contêiner da exclusão.<br />
<sup>2</sup> embora uma retenção legal ou uma política bloqueada baseada em tempo esteja em vigor para um contêiner, a conta de armazenamento também é protegida contra exclusão.<br />
<sup>3</sup> no momento, não há suporte para cargas de trabalho de data Lake Storage.<br />
<sup>4</sup> AzCopy e Azure data factory são opções com suporte para cargas de trabalho de armazenamento de BLOBs e data Lake Storage. A replicação de objeto tem suporte apenas para cargas de trabalho de armazenamento de BLOBs.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Recuperar dados excluídos ou substituídos

Se você precisar recuperar dados que foram substituídos ou excluídos, a maneira como proceder depende de quais opções de proteção de dados você habilitou e qual recurso foi afetado. A tabela a seguir descreve as ações que você pode executar para recuperar dados.

| Recurso excluído ou substituído | Possíveis ações de recuperação | Requisitos para recuperação |
|--|--|--|
| Conta de armazenamento | Tentativa de recuperar a conta de armazenamento excluída<br />[Saiba Mais...](../common/storage-account-recover.md) | A conta de armazenamento foi criada originalmente com o modelo de implantação Azure Resource Manager e foi excluída nos últimos 14 dias. Uma nova conta de armazenamento com o mesmo nome não foi criada desde que a conta original foi excluída. |
| Contêiner | Recuperar o contêiner de exclusão reversível e seu conteúdo<br />[Saiba Mais...](soft-delete-container-enable.md) | A exclusão reversível de contêiner está habilitada e o período de retenção de exclusão reversível do contêiner ainda não expirou. |
| Contêineres e blobs | Restaurar dados de uma segunda conta de armazenamento | Todas as operações de contêiner e BLOB foram efetivamente replicadas para uma segunda conta de armazenamento. |
| BLOB (qualquer tipo) | Restaurar um blob de uma versão anterior<sup>1</sup><br />[Saiba Mais...](versioning-enable.md) | O controle de versão do blob está habilitado e o blob tem uma ou mais versões anteriores. |
| BLOB (qualquer tipo) | Recuperar um blob com exclusão reversível<sup>1</sup><br />[Saiba Mais...](soft-delete-blob-enable.md) | A exclusão reversível de blob está habilitada e o intervalo de retenção de exclusão reversível não expirou. |
| BLOB (qualquer tipo) | Restaurar um blob de um instantâneo<br />[Saiba Mais...](snapshots-manage-dotnet.md) | O blob tem um ou mais instantâneos. |
| Conjunto de blobs de blocos | Recuperar um conjunto de blobs de blocos para seu estado em um ponto anterior no tempo<sup>1</sup><br />[Saiba Mais...](point-in-time-restore-manage.md) | A restauração pontual está habilitada e o ponto de restauração está dentro do intervalo de retenção. A conta de armazenamento não foi comprometida ou está corrompida. |
| Versão do blob | Recuperar uma versão de exclusão reversível<sup>1</sup><br />[Saiba Mais...](soft-delete-blob-enable.md) | A exclusão reversível de blob está habilitada e o intervalo de retenção de exclusão reversível não expirou. |

<sup>1</sup> no momento, não há suporte para cargas de trabalho de data Lake Storage.

## <a name="summary-of-cost-considerations"></a>Resumo das considerações de custo

A tabela a seguir resume as considerações de custo para as várias opções de proteção de dados descritas neste guia.

| Opção de proteção de dados | Considerações de custo |
|-|-|
| Bloqueio de Azure Resource Manager para uma conta de armazenamento | Nenhum encargo para configurar um bloqueio em uma conta de armazenamento. |
| Política de imutabilidade em um contêiner | Nenhum encargo para configurar uma política de imutabilidade em um contêiner. |
| Exclusão reversível do contêiner | Sem encargos para habilitar a exclusão reversível de contêiner para uma conta de armazenamento. Os dados em um contêiner excluído de forma reversível são cobrados com a mesma taxa que os dados ativos até que o contêiner excluído por software seja permanentemente excluído. |
| Controle de versão de BLOB | Nenhum encargo para habilitar o controle de versão de BLOB para uma conta de armazenamento. Após a habilitação do controle de versão do blob, todas as operações de gravação ou exclusão em um blob na conta criarão uma nova versão, o que pode levar a aumento dos custos de capacidade.<br /><br />Uma versão de blob é cobrada com base em blocos ou páginas exclusivos. Portanto, os custos aumentam à medida que o blob de base é divergência de uma versão específica. A alteração da camada de um BLOB ou de uma versão de blob pode ter um impacto de cobrança. Para obter mais informações, consulte [preços e cobrança](versioning-overview.md#pricing-and-billing).<br /><br />Use o gerenciamento do ciclo de vida para excluir versões mais antigas, conforme necessário, para controlar os custos. Para obter mais informações, consulte [otimizar custos automatizando as camadas de acesso do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md). |
| Exclusão reversível de BLOB | Nenhum encargo para habilitar a exclusão reversível de BLOB para uma conta de armazenamento. Os dados em um blob com exclusão reversível são cobrados com a mesma taxa que os dados ativos até que o blob excluído por software seja permanentemente excluído. |
| Restauração em um momento determinado | Sem encargos para habilitar a restauração pontual para uma conta de armazenamento; no entanto, a habilitação da restauração pontual também permite o controle de versão do blob, exclusão reversível e feed de alteração, e cada um deles pode resultar em encargos adicionais.<br /><br />Você será cobrado pela restauração pontual ao executar uma operação de restauração. O custo de uma operação de restauração depende da quantidade de dados que estão sendo restaurados. Para obter mais informações, consulte [preços e cobrança](point-in-time-restore-overview.md#pricing-and-billing). |
| Instantâneos de BLOB | Os dados em um instantâneo são cobrados com base em blocos ou páginas exclusivos. Portanto, os custos aumentam à medida que o blob de base é divergência do instantâneo. A alteração de um BLOB ou camada de instantâneo pode ter um impacto de cobrança. Para obter mais informações, consulte [preços e cobrança](snapshots-overview.md#pricing-and-billing).<br /><br />Use o gerenciamento do ciclo de vida para excluir instantâneos mais antigos conforme necessário para controlar os custos. Para obter mais informações, consulte [otimizar custos automatizando as camadas de acesso do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md). |
| Copiar dados para uma segunda conta de armazenamento | A manutenção de dados em uma segunda conta de armazenamento incorrerá nos custos de capacidade e de transação. Se a segunda conta de armazenamento estiver localizada em uma região diferente da conta de origem, a cópia de dados para essa segunda conta também incorrerá em encargos de egresso. |

## <a name="disaster-recovery"></a>Recuperação de desastre

O armazenamento do Azure sempre mantém várias cópias de seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas transitórias de hardware, interrupções de rede ou energia e desastres naturais em massa. A redundância garante que sua conta de armazenamento atenda às suas metas de disponibilidade e durabilidade mesmo diante de falhas. Para obter mais informações sobre como configurar sua conta de armazenamento para alta disponibilidade, consulte [redundância de armazenamento do Azure](../common/storage-redundancy.md).

Caso ocorra uma falha em um data center, se sua conta de armazenamento for redundante em duas regiões geográficas (com redundância geográfica), você terá a opção de fazer failover de sua conta da região primária para a região secundária. Para saber mais, confira [Recuperação de desastre e failover da conta de armazenamento](../common/storage-disaster-recovery-guidance.md).

Atualmente, o failover gerenciado pelo cliente não tem suporte para contas de armazenamento com um namespace hierárquico habilitado. Para obter mais informações, consulte [recursos de armazenamento de BLOBs disponíveis em Azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Próximas etapas

- [Redundância do Armazenamento do Azure](../common/storage-redundancy.md)
- [Recuperação de desastres e failover da conta de armazenamento](../common/storage-disaster-recovery-guidance.md)
