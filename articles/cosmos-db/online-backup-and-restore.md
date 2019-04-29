---
title: Backup automático on-line e restauração de dados sob demanda no Azure Cosmos DB
description: Este artigo descreve como o backup online automático e a restauração de dados sob demanda funcionam no Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930228"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup on-line e restauração de dados sob demanda no Azure Cosmos DB

O Azure Cosmos DB faz backups automáticos de seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho ou a disponibilidade das operações do banco de dados. Todos os backups são armazenados separadamente em um serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. Os backups automáticos são úteis em cenários quando você acidentalmente exclui ou atualiza sua conta, banco de dados ou contêiner do Azure Cosmos e, posteriormente, exige a recuperação de dados.

## <a name="automatic-and-online-backups"></a>Backups automáticos e online

Com o Azure Cosmos DB, não apenas seus dados, mas também os backups de seus dados são altamente redundantes e resilientes a desastres regionais. As etapas a seguir mostram como o Azure Cosmos DB executará o backup de dados:

* O Azure Cosmos DB faz automaticamente um backup do banco de dados a cada 4 horas e em qualquer momento, apenas os últimos 2 backups são armazenados. No entanto, se o contêiner ou banco de dados for excluído, o Azure Cosmos DB reterá os instantâneos existentes de um determinado contêiner ou banco de dados por 30 dias.

* O Azure Cosmos DB armazena esses backups no armazenamento de BLOBs do Azure, enquanto os dados reais residem localmente no Azure Cosmos DB.

*  Para garantir a baixa latência, o instantâneo do backup é armazenado no armazenamento de BLOBs do Azure na mesma região que a região de gravação atual (ou uma das regiões de gravação, caso você tenha uma configuração de vários mestre) de seu Cosmos do Azure conta banco de dados. Para resiliência contra desastres regionais, cada captura instantânea dos dados de backup no armazenamento do Azure Blob é novamente replicada para outra região por meio de armazenamento geo-redundante (GRS). A região na qual o backup é replicado é baseada em sua região de origem e no par regional associado à região de origem. Para saber mais, consulte a [lista de artigos de pares geo-redundantes de regiões do Azure](../best-practices-availability-paired-regions.md). Você não pode acessar esse backup diretamente. O Azure Cosmos DB usará esse backup somente se uma restauração de backup for iniciada.

* Os backups são feitos sem afetar o desempenho ou a disponibilidade de seu aplicativo. O Azure Cosmos DB executa backup de dados em segundo plano sem consumir nenhuma taxa de transferência provisionada (RUs) adicional ou afetar o desempenho e a disponibilidade de seu banco de dados.

* Se você tiver acidentalmente excluído ou corrompido de seus dados, você deverá contatar [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe do Azure Cosmos DB pode ajudar a restaurar os dados dos backups.

A imagem a seguir mostra como é feito o backup de um contêiner do Azure Cosmos com todas as três partições físicas primárias no oeste dos EUA em uma conta remota do Armazenamento de Blobs do Azure no oeste dos EUA e, em seguida, replicada para o leste dos EUA:

![Backups completos periódicos de todas as entidades do Cosmos DB no Armazenamento do Azure GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opções para gerenciar seus próprios backups

Com as contas da API do Azure Cosmos DB SQL, você também pode manter seus próprios backups usando uma das seguintes abordagens:

* Use o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados periodicamente para um armazenamento de sua escolha.

* Use o DB do Azure Cosmos [ alterar o feed ](change-feed.md) para ler dados periodicamente para backups completos, bem como para alterações incrementais, e armazene-os em seu próprio armazenamento.

## <a name="backup-retention-period"></a>Período de retenção do backup

O Azure Cosmos DB tira instantâneos de seus dados a cada quatro horas. A qualquer momento, somente os últimos dois instantâneos são mantidos. No entanto, se o contêiner ou banco de dados for excluído, o Azure Cosmos DB reterá os instantâneos existentes de um determinado contêiner ou banco de dados por 30 dias.

## <a name="restoring-data-from-online-backups"></a>Restaurando dados de backups online

A exclusão acidental ou modificação de dados pode acontecer em um dos seguintes cenários:  

* Toda a conta do Azure Cosmos é excluída

* Um ou mais bancos de dados do Azure Cosmos são excluídos

* Um ou mais contêineres do Azure Cosmos são excluídos

* Os itens do Azure Cosmos (por exemplo, documentos) em um contêiner são excluídos ou modificados. Esse caso específico é normalmente chamado de "corrupção de dados".

* Um banco de dados de oferta compartilhada ou contêineres dentro de um banco de dados de oferta compartilhada são excluídos ou corrompidos

O Azure Cosmos DB pode restaurar dados em todos os cenários acima. O processo de restauração sempre cria uma nova conta do Azure Cosmos para conter os dados restaurados. O nome da nova conta, se não especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1`. O último dígito é incrementado, se várias restaurações forem tentadas. Você não pode restaurar dados em uma conta do Azure Cosmos pré-criada.

Quando uma conta do Azure Cosmos é excluída, podemos restaurar os dados em uma conta com o mesmo nome, desde que o nome da conta não esteja em uso. Nesses casos, recomenda-se não recriar a conta após a exclusão, pois ela não apenas impede que os dados restaurados usem o mesmo nome, mas também torna mais difícil descobrir a conta certa para restaurar. 

Quando um banco de dados do Azure Cosmos é excluído, é possível restaurar o banco de dados inteiro ou um subconjunto dos contêineres desse banco de dados. Também é possível selecionar contêineres nos bancos de dados e restaurá-los, e todos os dados restaurados são colocados em uma nova conta do Azure Cosmos.

Quando um ou mais itens dentro de um contêiner são excluídos ou alterados acidentalmente (o caso de corrupção de dados), é necessário especificar o tempo para restauração. O tempo é essencial para este caso. Como o contêiner está ativo, o backup ainda está em execução; portanto, se você esperar além do período de retenção (o padrão é oito horas), os backups serão substituídos. No caso de exclusões, seus dados não são mais armazenados porque não serão sobrescritos pelo ciclo de backup. Os backups de bancos de dados ou contêineres excluídos são salvos por 30 dias.

Se você provisionar a taxa de transferência no nível do banco de dados (isto é, onde um conjunto de contêineres compartilha a taxa de transferência provisionada), o processo de backup e restauração nesse caso acontecerá em todo o nível do banco de dados e não no nível de contêiner individual. Nesses casos, selecionar um subconjunto de contêineres para restauração não é uma opção.

## <a name="migrating-data-to-the-original-account"></a>Migrando dados para a conta original

O objetivo principal da restauração de dados é fornecer uma maneira de recuperar todos os dados que você excluir ou modificar acidentalmente. Portanto, recomendamos que você primeiro inspecione o conteúdo dos dados recuperados para garantir que ele contenha o que você está esperando. Em seguida, trabalhe na migração dos dados de volta para a conta principal. Embora seja possível usar a conta restaurada como a conta ativa, não é uma opção recomendada se você tiver cargas de trabalho de produção.  

Estas são maneiras diferentes de migrar dados de volta para a conta original do Azure Cosmos:

* Usando [Ferramenta de Migração de Dados do BD Cosmos](import-data.md)
* Usando o [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Usando o [feed de alterações](change-feed.md) no Azure Cosmos DB 
* Escrever código personalizado

Excluir restaurado contas assim que você terminar de migrar, porque eles incorrerá em encargos contínuos.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender como restaurar dados de uma conta do Azure Cosmos ou aprender como migrar dados para uma conta do Azure Cosmos

* Para fazer uma solicitação de restauração, entre em contato com o Suporte do Azure, [arquive um ticket no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Como restaurar dados de uma conta do Azure Cosmos](how-to-backup-and-restore.md)
* [Use o feed de alteração do Cosmos DB](change-feed.md) para mover dados para o Azure Cosmos DB.
* [Use o Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.

