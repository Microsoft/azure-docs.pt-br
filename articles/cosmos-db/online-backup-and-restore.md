---
title: "Backup e restauração online com o Azure Cosmos DB | Microsoft Docs"
description: "Saiba como executar o backup e a restauração automáticos em um banco de dados do Azure Cosmos DB."
keywords: "backup e restauração, backup online"
services: cosmos-db
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/18/2017
ms.author: raprasa
ms.openlocfilehash: 84b26c9ff354adef3f1bc1e61f235c520b63df13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Backup e restauração online automáticos com o Azure Cosmos DB
O Azure Cosmos DB faz backup automaticamente de todos os seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho nem a disponibilidade das operações de banco de dados. Todos os backups são armazenados separadamente em outro serviço de armazenamento, e esses backups são replicados globalmente para resiliência contra desastres regionais. Os backups automáticos são destinados a cenários em que você exclui acidentalmente seu contêiner do Cosmos DB e, mais tarde, precisa de uma solução de recuperação de desastre ou de dados.  

Este artigo começa com uma recapitulação rápida da redundância e disponibilidade de dados no Cosmos DB e, em seguida, aborda os backups. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Alta disponibilidade com o Cosmos DB – recapitulação
O Cosmos DB foi desenvolvido para ser [distribuído globalmente](distribute-data-globally.md) – ele permite que você dimensione a produtividade em várias regiões do Azure, juntamente com failover controlado por política e APIs transparentes de hospedagem múltipla. Como um sistema de banco de dados que oferece [SLAs de disponibilidade de 99,99%](https://azure.microsoft.com/support/legal/sla/cosmos-db), todas as gravações no Cosmos DB são permanentemente confirmadas em discos locais por um quorum de réplicas em um data center local antes de serem confirmadas no cliente. Observe que a alta disponibilidade do Cosmos DB se baseia no armazenamento local e não depende de nenhuma tecnologia de armazenamento externo. Além disso, se sua conta de banco de dados está associada a mais de uma região do Azure, suas gravações são replicadas em outras regiões também. Para dimensionar seus dados de produtividade e acesso em latências menores, você pode ter quantas regiões de leitura associadas à sua conta de banco de dados quantas quiser. Em cada região de leitura, os dados (replicados) são persistidos em um conjunto de réplicas.  

Conforme ilustrado no diagrama a seguir, um único contêiner do Cosmos DB é [particionado horizontalmente](partition-data.md). Uma “partição” é indicada por um círculo no diagrama a seguir, e cada partição torna-se altamente disponível por meio de um conjunto de réplicas. Essa é a distribuição local dentro de uma única região do Azure (indicada pelo eixo X). Além disso, cada partição (com seu conjunto de réplicas correspondente) é distribuída globalmente entre várias regiões associadas à sua conta de banco de dados (por exemplo, neste caso, três regiões: Leste dos EUA, Oeste dos EUA e Índia Central). O "conjunto de partição" é uma entidade distribuída globalmente que consiste em várias cópias de seus dados em cada região (indicado pelo eixo Y). Você pode atribuir prioridade às regiões associadas à conta de banco de dados e o Cosmos DB fará failover de forma transparente para a próxima região em caso de desastre. Você pode simular o failover manualmente para testar a disponibilidade de ponta a ponta de seu aplicativo.  

A imagem a seguir ilustra o alto grau de redundância com o Cosmos DB.

![Alto grau de redundância com o Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Alto grau de redundância com o Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Backups online completos, automáticos
Opa, excluí meu contêiner ou banco de dados! Com o Cosmos DB, não apenas os dados, mas também os backups dos dados ficam altamente redundantes e resilientes em desastres regionais. Esses backups automatizados são feitos atualmente a cada quatro horas, aproximadamente, e os últimos 2 backups são armazenados o tempo todo. Se os dados forem acidentalmente descartados ou estiverem corrompidos, [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/) em até oito horas. 

Os backups são feitos sem afetar o desempenho ou a disponibilidade de suas operações de banco de dados. O Cosmos DB faz o backup em segundo plano, sem consumir as RUs provisionadas nem afetar o desempenho e sem afetar a disponibilidade do banco de dados. 

Ao contrário dos dados que são armazenados no Cosmos DB, os backups automáticos são armazenados no serviço Armazenamento de Blobs do Azure. Para garantir um upload eficiente e a baixa latência, o instantâneo do backup é carregado em uma instância do Armazenamento de blobs do Azure na mesma região da região de gravação atual de sua conta de banco de dados do Cosmos DB. Para resiliência contra desastres regionais, cada instantâneo dos seus dados de backup no Armazenamento de Blobs do Azure é replicado novamente via GRS (armazenamento com redundância geográfica) para outra região. O diagrama a seguir mostra que o contêiner inteiro do Cosmos DB (com todas as três partições primárias no Oeste dos EUA, neste exemplo) é copiado em backup para uma conta remota de Armazenamento de Blobs do Azure no Oeste dos EUA e, então, replicadas por GRS no Leste dos EUA. 

A imagem a seguir ilustra os backups completos periódicos de todas as entidades do Cosmos DB no Armazenamento do Azure GRS.

![Backups completos periódicos de todas as entidades do Cosmos DB no Armazenamento do Azure GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Período de retenção do backup
Conforme descrito acima, o Azure Cosmos DB usa instantâneos dos seus dados a cada quatro horas no nível da partição. A qualquer momento, somente os últimos dois instantâneos são mantidos. No entanto, se a coleção/banco de dados é excluída, mantemos os instantâneos existentes para todas as partições excluídas dentro de determinada coleção/banco de dados por 30 dias.

Se você quiser manter seus próprios instantâneos, pode usar o opção Exportar para JSON na [ferramenta de Migração de Dados](import-data.md#export-to-json-file) do Azure Cosmos DB para agendar backups adicionais.

## <a name="restoring-a-database-from-an-online-backup"></a>Restauração de um banco de dados de um backup online
Caso exclua seu banco de dados ou coleção acidentalmente, você pode [criar um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [ligar para o suporte do Azure](https://azure.microsoft.com/support/options/) a fim de restaurar os dados usando o último backup automático. Se você precisar restaurar seu banco de dados devido a problema de corrupção de dados (inclui casos em que os documentos em uma coleção são excluídos), veja [tratamento corrupção de dados](#handling-data-corruption) conforme necessário executar etapas adicionais para impedir que os dados corrompidos substituam os backups existentes. Para que um instantâneo específico do backup seja restaurado, o Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup do instantâneo.

## <a name="handling-data-corruption"></a>Manipulação de dados corrompidos
O Azure Cosmos DB retém os últimos dois backups de todas as partições na conta do banco de dados. Esse modelo funciona bem quando um contêiner (coleção de documentos, grafo, tabela) ou um banco de dados forem excluído acidentalmente desde que uma das últimas versões pode ser restaurada. No entanto, caso os usuários introduzam um problema de corrupção de dados, o Azure Cosmos DB pode não estar ciente da corrupção de dados, e é possível que a corrupção possa ter substituído os backups existentes. Assim que a corrupção for detectada, o usuário deverá excluir o contêiner corrompido (coleção/grafo/tabela) para que os backups sejam protegidos contra a substituição por dados corrompidos.

## <a name="next-steps"></a>Próximas etapas

Para replicar o banco de dados em vários data centers, consulte [Distribuir os dados globalmente com o Cosmos DB](distribute-data-globally.md). 

Para entrar em contato com o Suporte do Azure, [crie um tíquete no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

