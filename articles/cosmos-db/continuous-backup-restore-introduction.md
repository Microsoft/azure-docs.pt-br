---
title: Backup contínuo com o recurso de restauração pontual no Azure Cosmos DB
description: O recurso de restauração pontual do Azure Cosmos DB ajuda a recuperar dados de uma operação de gravação acidental, exclusão ou restauração de dados em qualquer região. Saiba mais sobre preços e limitações atuais.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: 036f086c88267f6a20da51746ca875c48a248712
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538765"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Backup contínuo com o recurso de restauração pontual (versão prévia) no Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O recurso de restauração pontual do Azure Cosmos DB (versão prévia) ajuda em vários cenários, como o seguinte:

* Para recuperar-se de uma operação de gravação ou exclusão acidental em um contêiner.
* Para restaurar uma conta, um banco de dados ou um contêiner excluído.
* Para restaurar em qualquer região (onde os backups existiam) no momento da restauração.

Azure Cosmos DB executa o backup de dados em segundo plano sem consumir nenhuma taxa de transferência provisionada extra (RUs) ou afetar o desempenho e a disponibilidade do banco de dados. Os backups contínuos são feitos em todas as regiões em que a conta existe. A imagem a seguir mostra como um contêiner com região de gravação no oeste dos EUA, as regiões de leitura no leste e leste dos EUA 2 são submetidas a backup em uma conta de armazenamento de BLOBs do Azure remota nas respectivas regiões. Por padrão, cada região armazena o backup em contas de armazenamento com redundância local. Se a região tiver [zonas de disponibilidade](high-availability.md#availability-zone-support) habilitadas, o backup será armazenado em Zone-Redundant contas de armazenamento.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB backup de dados para o armazenamento de BLOBs do Azure." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

A janela de tempo disponível para restauração (também conhecida como período de retenção) é o valor mais baixo dos dois seguintes: "30 dias atrás de agora" ou "até a hora de criação do recurso". O ponto no tempo para a restauração pode ser qualquer carimbo de data/hora dentro do período de retenção.

Na visualização pública, você pode restaurar a conta de Azure Cosmos DB para a API do SQL ou o ponto de conteúdo do MongoDB no tempo para outra conta usando [portal do Azure](continuous-backup-restore-portal.md), a [interface de linha de comando do Azure](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)ou a [Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>O que é restaurado?

Em um estado estável, todas as mutações executadas na conta de origem (que inclui bancos de dados, contêineres e itens) são submetidas a backup de forma assíncrona em 100 segundos. Se a mídia de backup (que é o armazenamento do Azure) estiver inoperante ou indisponível, as mutações serão persistidas localmente até que a mídia esteja disponível de volta e, em seguida, elas serão liberadas para evitar qualquer perda na fidelidade das operações que possam ser restauradas.

Você pode optar por restaurar qualquer combinação de contêineres de taxa de transferência provisionados, banco de dados de produtividade compartilhada ou a conta inteira. A ação restaurar restaura todos os dados e suas propriedades de índice para uma nova conta. O processo de restauração garante que todos os dados restaurados em uma conta, banco de dados ou contêiner tenham a garantia de serem consistentes até o tempo de restauração especificado. A duração da restauração dependerá da quantidade de dados que precisam ser restaurados.

> [!NOTE]
> Com o modo de backup contínuo, os backups são feitos em todas as regiões em que sua conta de Azure Cosmos DB está disponível. Os backups feitos para cada conta de região são localmente redundantes por padrão e com redundância de zona se sua conta tiver o recurso de [zona de disponibilidade](high-availability.md#availability-zone-support) habilitado para essa região. A ação restaurar sempre restaura os dados em uma nova conta.

## <a name="what-is-not-restored"></a>O que não é restaurado?

As seguintes configurações não são restauradas após a recuperação pontual:

* Configurações de firewall, VNET e ponto de extremidade privado.
* Configurações de consistência. Por padrão, a conta é restaurada com consistência de sessão.  
* Regiões.
* Procedimentos armazenados, gatilhos, UDFs.

Você pode adicionar essas configurações à conta restaurada após a conclusão da restauração.

## <a name="restore-scenarios"></a>Cenários de restauração

A seguir estão alguns dos principais cenários que são abordados pelo recurso de restauração pontual. Cenários [a] até [c] demonstram como disparar uma restauração se o carimbo de data/hora de restauração for conhecido com antecedência. No entanto, pode haver cenários em que você não sabe o tempo exato de exclusão acidental ou corrupção. Cenários [d] e [e] demonstram como _descobrir_ o carimbo de data/hora de restauração usando as novas APIs de feed de eventos no banco de dados restaurável ou contêineres.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Eventos de ciclo de vida com carimbos de data/hora para uma conta restaurável." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Restaurar conta excluída** -todas as contas excluídas que você pode restaurar são visíveis no painel de **restauração** . Por exemplo, se "conta A" for excluída em T3 de carimbo de data/hora. Nesse caso, o carimbo de data/hora pouco antes do T3, local, nome da conta de destino, grupo de recursos e nome da conta de destino é suficiente para restaurar de [portal do Azure](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ou [CLI](continuous-backup-restore-command-line.md#trigger-restore).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Eventos de ciclo de vida com carimbos de data/hora para um banco de dados e contêiner restauráveis." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Restaurar dados de uma conta em uma região específica** – por exemplo, se "conta a" existir em duas regiões "leste dos EUA" e "oeste dos EUA" no carimbo de data/hora T3. Se você precisar de uma cópia da conta A em "oeste dos EUA", poderá fazer uma restauração pontual do [portal do Azure](continuous-backup-restore-portal.md), do [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ou da [CLI](continuous-backup-restore-command-line.md#trigger-restore) com o oeste dos EUA como o local de destino.

c. **Recuperar-se de uma operação de gravação ou exclusão acidental em um contêiner com um carimbo de data/hora de restauração conhecido** , por exemplo, se você **souber** que o conteúdo do "contêiner 1" no "banco de dados 1" foi modificado acidentalmente no carimbo de data/hora em T3. Você pode fazer uma restauração pontual de [portal do Azure](continuous-backup-restore-portal.md#restore-live-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ou [CLI](continuous-backup-restore-command-line.md#trigger-restore) em outra conta em T3 de carimbo de data/hora para recuperar o estado desejado do contêiner.

d. **Restaurar uma conta para um ponto anterior no tempo antes da exclusão acidental do banco de dados** -no [portal do Azure](continuous-backup-restore-portal.md#restore-live-account), você pode usar o painel feed de eventos para determinar quando um banco de dados foi excluído e encontrar o tempo de restauração. Da mesma forma, com [CLI do Azure](continuous-backup-restore-command-line.md#trigger-restore) e o [PowerShell](continuous-backup-restore-powershell.md#trigger-restore), você pode descobrir o evento de exclusão do banco de dados enumerando o feed de eventos do banco de dados e, em seguida, disparar o comando Restore com os parâmetros necessários.

e. **Restaure uma conta para um ponto anterior no tempo antes da exclusão acidental ou modificação das propriedades do contêiner.** -Em [portal do Azure](continuous-backup-restore-portal.md#restore-live-account), você pode usar o painel feed de eventos para determinar quando um contêiner foi criado, modificado ou excluído para localizar o tempo de restauração. Da mesma forma, com [CLI do Azure](continuous-backup-restore-command-line.md#trigger-restore) e o [PowerShell](continuous-backup-restore-powershell.md#trigger-restore), você pode descobrir todos os eventos de contêiner enumerando o feed de eventos de contêiner e, em seguida, disparar o comando Restore com os parâmetros necessários.

## <a name="permissions"></a>Permissões

Azure Cosmos DB permite isolar e restringir as permissões de restauração para a conta de backup contínuo a uma função específica ou a uma entidade de segurança. O proprietário da conta pode disparar uma restauração e atribuir uma função a outras entidades de segurança para executar a operação de restauração. Para saber mais, confira o artigo [permissões](continuous-backup-restore-permissions.md) .

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Preços

Azure Cosmos DB contas com backup contínuo habilitado incorrerão em um custo mensal adicional para "armazenar o backup" e "restaurar seus dados". O custo de restauração é adicionado toda vez que a operação de restauração é iniciada. Se você configurar uma conta com backup contínuo, mas não restaurar os dados, somente o custo de armazenamento de backup será incluído em sua fatura.

O exemplo a seguir baseia-se no preço de uma conta do Azure Cosmos implantada em uma região não governamental nos EUA. O preço e o cálculo podem variar dependendo da região que você está usando, consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações mais recentes sobre preços.

* Todas as contas habilitadas com a política de backup contínuo incorrem em um custo mensal adicional para o armazenamento de backup que é calculado da seguinte maneira:

  US $0,20/GB * Tamanho dos dados em GB na conta * número de regiões

* Cada invocação de API de restauração incorre em uma única taxa de tempo. A cobrança é uma função da quantidade de restauração de dados e é calculada da seguinte maneira:

  tamanho de dados de $0,15/GB * em GB.

Por exemplo, se você tiver 1 TB de dados em duas regiões, então:

* O custo de armazenamento de backup é calculado como (1000 * 0,20 * 2) = $400 por mês

* O custo de restauração é calculado como (1000 * 0,15) = $150 por restauração

## <a name="current-limitations-public-preview"></a>Limitações atuais (visualização pública)

Atualmente, a funcionalidade de restauração pontual está em visualização pública e tem as seguintes limitações:

* Somente Azure Cosmos DB APIs para SQL e MongoDB têm suporte para backup contínuo. As APIs Cassandra, Table e Gremlin ainda não têm suporte.

* Uma conta existente com a política de backup periódico padrão não pode ser convertida para usar o modo de backup contínuo.

* As regiões de nuvem do Azure soberanas e Azure governamental ainda não têm suporte.

* Contas com chaves gerenciadas pelo cliente não têm suporte para usar o backup contínuo.

* Não há suporte para contas de gravação de várias regiões.

* Não há suporte para contas com o link Synapse habilitado.

* A conta restaurada é criada na mesma região em que a conta de origem existe. Não é possível restaurar uma conta em uma região em que a conta de origem não exista.

* A janela restaurar é de apenas 30 dias e não pode ser alterada.

* Os backups não são resistentes a desastres geograficamente automaticamente. Você precisa adicionar outra região explicitamente para ter resiliência para a conta e o backup.

* Enquanto uma restauração está em andamento, não modifique ou exclua as políticas de IAM (gerenciamento de acesso e identidade) que concedem as permissões para a conta ou alteram qualquer VNET, configuração de firewall.

* Não há suporte para o backup contínuo Azure Cosmos DB API para contas SQL ou MongoDB que criam um índice exclusivo após o contêiner. Somente os contêineres que criam um índice exclusivo como parte da criação do contêiner inicial têm suporte. Para contas do MongoDB, você cria um índice exclusivo usando [comandos de extensão](mongodb-custom-commands.md).

* A funcionalidade de restauração pontual sempre restaura para uma nova conta do Azure Cosmos. Atualmente, não há suporte para a restauração de uma conta existente. Se você estiver interessado em fornecer comentários sobre a restauração in-loco, entre em contato com a equipe de Azure Cosmos DB por meio de seu representante de conta ou [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Todas as novas APIs expostas para listagem,,,, `RestorableDatabaseAccount` `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` estão sujeitas a alterações enquanto o recurso está em visualização.

* Após a restauração, é possível que, para determinadas coleções, o índice consistente possa ser reconstruído. Você pode verificar o status da operação de recompilação por meio da propriedade [IndexTransformationProgress](how-to-manage-indexing-policy.md) .

* O processo de restauração restaura todas as propriedades de um contêiner, incluindo sua configuração de TTL. Como resultado, é possível que os dados restaurados sejam excluídos imediatamente se você configurou essa maneira. Para evitar essa situação, o carimbo de data/hora de restauração deve ser antes das propriedades de TTL serem adicionadas ao contêiner.

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
