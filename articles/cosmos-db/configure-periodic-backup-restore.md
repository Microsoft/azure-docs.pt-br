---
title: Configurar Azure Cosmos DB conta com backup periódico
description: Este artigo descreve como configurar contas de Azure Cosmos DB com backup periódico com o intervalo de backup. e retenção. Além disso, como contatar o suporte para restaurar seus dados.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69a9f0a82f5c19504564825e47f69ab8414e0909
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565814"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Configurar Azure Cosmos DB conta com backup periódico
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB faz backups automáticos de seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho ou a disponibilidade das operações do banco de dados. Todos os backups são armazenados separadamente em um serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. Com o Azure Cosmos DB, não apenas seus dados, mas também os backups de seus dados são altamente redundantes e resilientes a desastres regionais. As etapas a seguir mostram como o Azure Cosmos DB executa o backup de dados:

* Azure Cosmos DB faz automaticamente um backup completo do banco de dados a cada 4 horas e a qualquer momento, somente os dois backups mais recentes são armazenados por padrão. Se os intervalos padrão não forem suficientes para suas cargas de trabalho, você poderá alterar o intervalo de backup e o período de retenção do portal do Azure. Você pode alterar a configuração de backup durante ou após a criação da conta do Azure Cosmos. Se o contêiner ou banco de dados for excluído, Azure Cosmos DB manterá os instantâneos existentes de um determinado contêiner ou banco de dados por 30 dias.

* O Azure Cosmos DB armazena esses backups no armazenamento de BLOBs do Azure, enquanto os dados reais residem localmente no Azure Cosmos DB.

* Para garantir baixa latência, o instantâneo do backup é armazenado no armazenamento de BLOBs do Azure na mesma região que a região de gravação atual (ou **uma** das regiões de gravação, caso você tenha uma configuração de gravação de várias regiões). Para resiliência contra desastres regionais, cada captura instantânea dos dados de backup no armazenamento do Azure Blob é novamente replicada para outra região por meio de armazenamento geo-redundante (GRS). A região na qual o backup é replicado é baseada em sua região de origem e no par regional associado à região de origem. Para saber mais, consulte a [lista de artigos de pares geo-redundantes de regiões do Azure](../best-practices-availability-paired-regions.md). Você não pode acessar esse backup diretamente. A equipe do Azure Cosmos DB restaurará o backup quando você realizar uma solicitação de suporte.

  A imagem a seguir mostra como é feito o backup de um contêiner do Azure Cosmos com todas as três partições físicas primárias no oeste dos EUA em uma conta remota do Armazenamento de Blobs do Azure no oeste dos EUA e, em seguida, replicada para o leste dos EUA:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Backups completos periódicos de todas as entidades de Cosmos DB no armazenamento do Azure GRS." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* Os backups são feitos sem afetar o desempenho ou a disponibilidade de seu aplicativo. Azure Cosmos DB executa o backup de dados em segundo plano sem consumir nenhuma taxa de transferência provisionada extra (RUs) ou afetar o desempenho e a disponibilidade do banco de dados.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Modificar o intervalo de backup e o período de retenção

Azure Cosmos DB faz automaticamente um backup completo de seus dados para cada 4 horas e a qualquer momento, os dois backups mais recentes são armazenados. Essa configuração é a opção padrão e é oferecida sem nenhum custo extra. Você pode mudar o intervalo de backup padrão e o período de retenção durante a criação da conta do Azure Cosmos ou após a criação da conta. A configuração de backup é definida no nível da conta do Azure Cosmos e deve ser aplicada em cada conta. Depois de configurar as opções de backup para uma conta, ela é aplicada a todos os contêineres dentro dessa conta. No momento, é possível alterar as opções de backup somente no portal do Azure.

Se você acidentalmente excluiu ou danificou seus dados, **antes de criar uma solicitação de suporte para restaurar os dados, certifique-se de aumentar a retenção de backup para sua conta para pelo menos sete dias. É melhor aumentar sua retenção dentro de 8 horas desse evento.** Dessa forma, a equipe do Azure Cosmos DB tem tempo suficiente para restaurar a conta.

Use as etapas a seguir para alterar as opções de backup padrão para uma conta existente do Azure Cosmos:

1. Entre no [portal do Azure.](https://portal.azure.com/)
1. Navegue até sua conta do Azure Cosmos e abra o painel **Backup & restauração** . Atualize o intervalo de backup e o período de retenção de backup conforme necessário.

   * **Intervalo de backup** – é o intervalo no qual Azure Cosmos DB tenta fazer um backup dos dados. O backup leva um período de tempo diferente de zero e, em algum caso, poderia falhar possivelmente devido a dependências de downstream. O Azure Cosmos DB tenta o melhor fazer um backup no intervalo configurado, no entanto, não garante que o backup seja concluído dentro desse intervalo de tempo. Você pode configurar esse valor em horas ou minutos. O intervalo de backup não pode ser inferior a 1 hora e maior que 24 horas. Quando você altera esse intervalo, o novo intervalo entra em vigor a partir da hora em que o último backup foi feito.

   * **Retenção de backup** – representa o período em que cada backup é retido. Você pode configurá-lo em horas ou dias. O período de retenção mínimo não pode ser menor que duas vezes o intervalo de backup (em horas) e não pode ser maior que 720 horas.

   * **Cópias de dados retidas** -por padrão, duas cópias de backup de seus dados são oferecidas gratuitamente. Haverá um encargo extra se você precisar de mais de duas cópias. Consulte a seção armazenamento consumido na [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para saber o preço exato para cópias extras.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Configure o intervalo de backup e a retenção para uma conta existente do Azure Cosmos." border="true":::

Se você configurar as opções de backup durante a criação da conta, poderá configurar a **política de backup**, que é **periódica** ou **contínua**. A política periódica permite que você configure o intervalo de backup e a retenção de backup. Atualmente, a política contínua está disponível apenas para inscrição. A equipe de Azure Cosmos DB avaliará sua carga de trabalho e aprovará sua solicitação.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Configure a política de backup periódica ou contínua para novas contas do Azure Cosmos." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Solicitar restauração de dados de um backup

Se você excluir acidentalmente seu banco de dados ou um contêiner, poderá [arquivar um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [chamar o suporte do Azure](https://azure.microsoft.com/support/options/) para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como **Standard**, **Developer** e Plans maiores do que os. Suporte do Azure não está disponível com plano **Basic**. Para saber mais sobre os diferentes planos de suporte, consulte a página [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

Para restaurar um instantâneo específico do backup, Azure Cosmos DB requer que os dados estejam disponíveis durante o ciclo de backup para esse instantâneo.
Você deve ter os seguintes detalhes antes de solicitar uma restauração:

* Ter sua ID de assinatura pronta.

* Com base em como seus dados foram excluídos ou modificados acidentalmente, você deve se preparar para obter informações adicionais. É aconselhável que você tenha as informações disponíveis à frente para minimizar o processo de back-and-forth que pode ser prejudicial em alguns casos sensíveis ao tempo.

* Se toda a conta do Azure Cosmos DB for excluída, você precisará fornecer o nome da conta excluída. Se você criar outra conta com o mesmo nome da conta excluída, compartilhe-a com a equipe de suporte, pois isso ajudará a determinar a conta certa a ser escolhida. É recomendável arquivar tíquetes de suporte diferentes para cada conta excluída porque minimiza a confusão para o estado da restauração.

* Se um ou mais bancos de dados forem excluídos, você deverá fornecer a conta do Azure Cosmos e os nomes do banco de dados Cosmos do Azure e especificar se existe um novo banco de dados com o mesmo nome.

* Se um ou mais contêineres forem excluídos, você deverá fornecer o nome da conta do Azure Cosmos, os nomes do banco de dados e os nomes dos contêineres. E especifique se existe um contêiner com o mesmo nome.

* Se você acidentalmente excluiu ou danificou seus dados, deve entrar em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe de Azure Cosmos DB possa ajudá-lo a restaurar os dados dos backups. **Antes de criar uma solicitação de suporte para restaurar os dados, certifique-se de [aumentar a retenção de backup](#configure-backup-interval-retention) para sua conta para pelo menos sete dias. É melhor aumentar sua retenção dentro de 8 horas desse evento.** Dessa forma, a equipe de suporte do Azure Cosmos DB terá tempo suficiente para restaurar sua conta.

Além do nome da conta do Azure Cosmos, nomes de banco de dados, nomes de contêiner, você deve especificar o ponto no tempo para o qual os dados podem ser restaurados. É importante ser o mais preciso possível para nos ajudar a determinar os melhores backups disponíveis naquele momento. **Também é importante especificar o horário no UTC.**

A captura de tela a seguir ilustra como criar uma solicitação de suporte para um contêiner (coleção/gráfico/tabela) para restaurar dados usando o portal do Azure. Forneça outros detalhes, como o tipo de dados, a finalidade da restauração, a hora em que os dados foram excluídos para nos ajudar a priorizar a solicitação.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Crie uma solicitação de suporte de backup usando portal do Azure." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Considerações para restaurar os dados de um backup

Você pode excluir ou modificar seus dados acidentalmente em um dos seguintes cenários:  

* Exclua toda a conta do Azure Cosmos.

* Exclua um ou mais bancos de dados do Azure Cosmos.

* Exclua um ou mais contêineres de Cosmos do Azure.

* Exclua ou modifique os itens Cosmos do Azure (por exemplo, documentos) dentro de um contêiner. Esse caso específico é normalmente chamado de corrupção de dados.

* Um banco de dados de oferta compartilhado ou contêineres em um banco de dados de oferta compartilhado são excluídos ou corrompidos.

O Azure Cosmos DB pode restaurar dados em todos os cenários acima. Durante a restauração, uma nova conta do Azure cosmos é criada para armazenar os dados restaurados. O nome da nova conta, se não for especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1` . O último dígito é incrementado quando várias restaurações são tentadas. Não é possível restaurar dados para uma conta do Azure Cosmos criada previamente.

Quando você exclui acidentalmente uma conta do Azure Cosmos, podemos restaurar os dados em uma nova conta com o mesmo nome, se o nome da conta não estiver em uso. Portanto, recomendamos que você não crie novamente a conta depois de excluí-la. Porque ele não apenas impede que os dados restaurados usem o mesmo nome, mas também torna a descoberta da conta correta para restaurar de difícil.

Quando você exclui acidentalmente um banco de dados Cosmos do Azure, podemos restaurar todo o banco de dados ou um subconjunto dos contêineres dentro desse banco de dados. Também é possível selecionar contêineres específicos em bancos de dados e restaurá-los para uma nova conta do Azure Cosmos.

Quando você exclui ou modifica acidentalmente um ou mais itens dentro de um contêiner (o caso de corrupção de dados), precisa especificar a hora de restauração. O tempo é importante se houver dados corrompidos. Como o contêiner está ativo, o backup ainda está em execução. portanto, se você aguardar além do período de retenção (o padrão é de oito horas), os backups serão substituídos. Para impedir que o backup seja substituído, aumente a retenção de backup da sua conta para pelo menos sete dias. É melhor aumentar sua retenção dentro de 8 horas a partir da corrupção de dados.

Se você acidentalmente excluiu ou danificou seus dados, deve entrar em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe de Azure Cosmos DB possa ajudá-lo a restaurar os dados dos backups. Dessa forma, a equipe de suporte do Azure Cosmos DB terá tempo suficiente para restaurar sua conta.

> [!NOTE]
> Depois de restaurar os dados, nem todos os recursos ou as configurações de origem são transferidos para a conta restaurada. As configurações a seguir não são transportadas para a nova conta:
> * Listas de controle de acesso VNET
> * Procedimentos armazenados, gatilhos e funções definidas pelo usuário
> * Configurações de várias regiões  

Se você provisionar a taxa de transferência no nível do banco de dados, o processo de backup e restauração, nesse caso, ocorrerá em todo o nível do banco de dados, e não no nível de contêineres individuais. Nesses casos, você não pode selecionar um subconjunto de contêineres para restaurar.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Permissões necessárias para alterar a retenção ou restauração no portal
As entidades que fazem parte da função [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), proprietário ou colaborador têm permissão para solicitar uma restauração ou alterar o período de retenção.

## <a name="understanding-costs-of-extra-backups"></a>Noções básicas sobre custos de backups extras
Dois backups são fornecidos gratuitamente e os backups extras são cobrados de acordo com os preços baseados em região para o armazenamento de backup descrito em [preços de armazenamento de backup](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/). Por exemplo, se a retenção de backup estiver configurada para 240 h, 10 dias e o intervalo de backup para 24 horas. Isso implica 10 cópias dos dados de backup. Supondo 1 TB de dados no oeste dos EUA 2, o custo seria de 0,12 * 1000 * 8 para armazenamento de backup no mês determinado. 


## <a name="options-to-manage-your-own-backups"></a>Opções para gerenciar seus próprios backups

Com as contas da API do Azure Cosmos DB SQL, você também pode manter seus próprios backups usando uma das seguintes abordagens:

* Use o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados periodicamente para um armazenamento de sua escolha.

* Use Azure Cosmos DB [feed de alterações](change-feed.md) para ler dados periodicamente para backups completos ou para alterações incrementais e armazená-los em seu próprio armazenamento.

## <a name="post-restore-actions"></a>Ações de pós-restauração

O objetivo principal da restauração de dados é recuperar os dados que você excluiu ou modificou acidentalmente. Portanto, recomendamos que você primeiro inspecione o conteúdo dos dados recuperados para garantir que ele contenha o que você está esperando. Se tudo estiver correto, você poderá migrar os dados de volta para a conta primária. Embora seja possível usar a conta restaurada como sua nova conta ativa, ela não será uma opção recomendada se você tiver cargas de trabalho de produção. 

Depois de restaurar os dados, você recebe uma notificação sobre o nome da nova conta (normalmente, no formato `<original-name>-restored1`) e a hora em que a conta foi restaurada. A conta restaurada terá o mesmo rendimento provisionado, políticas de indexação e está na mesma região da conta original. Um usuário que é o administrador da assinatura ou um coadmin pode ver a conta restaurada.

### <a name="migrate-data-to-the-original-account"></a>Migrar dados para a conta original

A seguir, são diferentes maneiras de migrar dados de volta para a conta original:

* Use a [ferramenta de migração de dados Azure Cosmos DB](import-data.md).
* Use o [Azure data Factory](../data-factory/connector-azure-cosmos-db.md).
* Use o [feed de alterações](change-feed.md) no Azure Cosmos DB.
* Você pode escrever seu próprio código personalizado.

É aconselhável excluir o contêiner ou banco de dados imediatamente após a migração dos dados. Se você não excluir os bancos de dados ou contêineres restaurados, eles incorrerão em custos para unidades de solicitação, armazenamento e saída.

## <a name="next-steps"></a>Próximas etapas

* Para fazer uma solicitação de restauração, entre em contato com o suporte do Azure, [arquivo um tíquete do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Configure e gerencie o backup contínuo usando [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
