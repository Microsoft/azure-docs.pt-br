---
title: Como restaurar dados do Azure Cosmos DB de um backup
description: Este artigo descreve como restaurar dados do Azure Cosmos DB a partir de um backup, como entrar em contato com o suporte do Azure para restaurar dados, etapas a serem seguidas após a restauração dos dados.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1d886e146e9e18eb735e6f88d2cb2c1a4a472924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059616"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurar dados do backup no Azure Cosmos DB 

Se você excluir acidentalmente seu banco de dados ou um contêiner, poderá [arquivar um tíquete de suporte]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [chamar o suporte do Azure]( https://azure.microsoft.com/support/options/) para restaurar os dados de backups on-line automáticos. Suporte do Azure só está disponível para planos selecionados, como **Standard**, **desenvolvedor**e planos de mais do que-los. Suporte do Azure não está disponível com plano **Basic**. Para saber mais sobre os diferentes planos de suporte, consulte a página [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/). 

Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo.

## <a name="request-a-restore"></a>Solicitar uma restauração

Você deve ter os seguintes detalhes antes de solicitar uma restauração:

* Ter sua ID de assinatura pronta.

* Com base em como seus dados foram excluídos ou modificados acidentalmente, você deve se preparar para obter informações adicionais. É aconselhável que você tenha as informações disponíveis à frente para minimizar o processo de back-and-forth que pode ser prejudicial em alguns casos sensíveis ao tempo.

* Se toda a conta do Azure Cosmos DB for excluída, você precisará fornecer o nome da conta excluída. Se você criar outra conta com o mesmo nome da conta excluída, compartilhe-a com a equipe de suporte, pois isso ajudará a determinar a conta certa a ser escolhida. É recomendado arquivar tickets de suporte diferentes para cada conta excluída, pois isso minimiza a confusão do estado da restauração.

* Se um ou mais bancos de dados forem excluídos, você deverá fornecer a conta do Azure Cosmos, bem como os nomes do banco de dados do Azure Cosmos, e especificar se existe um novo banco de dados com o mesmo nome.

* Se um ou mais contêineres forem excluídos, você deverá fornecer o nome da conta do Azure Cosmos, os nomes do banco de dados e os nomes dos contêineres. E especifique se existe um contêiner com o mesmo nome.

* Se você tiver acidentalmente excluído ou corrompido de seus dados, você deverá contatar [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe do Azure Cosmos DB pode ajudar a restaurar os dados dos backups.
  
  * Se você excluiu acidentalmente seu banco de dados ou o contêiner, abra um caso de suporte Sev B ou Sev C Azure. 
  * Se você acidentalmente excluído ou corrompido alguns documentos dentro do contêiner, abra um caso de suporte severidades A. 

Quando ocorrer corrupção de dados e se os documentos dentro de um contêiner forem modificados ou excluídos, **exclua o contêiner o mais rápido possível**. Excluindo o contêiner, você pode evitar o Azure Cosmos DB substituam os backups. Se, por algum motivo, a exclusão não for possível, você deverá enviar um ticket o mais rápido possível. Além do nome da conta do Azure Cosmos, nomes de bancos de dados, nomes de coleções, você deve especificar o ponto no tempo para o qual os dados podem ser restaurados. É importante ser o mais preciso possível para nos ajudar a determinar os melhores backups disponíveis naquele momento. Também é importante especificar o horário no UTC. 

A captura de tela a seguir ilustra como criar uma solicitação de suporte para um contêiner (coleção/gráfico/tabela) para restaurar dados usando o portal do Azure. Forneça detalhes adicionais, como tipo de dados, finalidade da restauração, horário em que os dados foram excluídos para nos ajudar a priorizar a solicitação.

![Criar uma solicitação de suporte de backup usando o portal do Azure](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Ações de pós-restauração

Depois de restaurar os dados, você recebe uma notificação sobre o nome da nova conta (normalmente, no formato `<original-name>-restored1`) e a hora em que a conta foi restaurada. A conta restaurada terá o mesmo rendimento provisionado, políticas de indexação e está na mesma região da conta original. Um usuário que é o administrador da assinatura ou um coadmin pode ver a conta restaurada.

Depois que os dados são restaurados, você deve inspecionar e validar os dados na conta restaurada e certificar-se de que ela contenha a versão que você está esperando. Se tudo estiver bem, você deve migrar os dados de volta para sua conta original usando o [feed de alteração do Azure Cosmos DB](change-feed.md) ou [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

É aconselhável excluir o contêiner ou banco de dados imediatamente após a migração dos dados. Se você não excluir os bancos de dados ou contêineres restaurados, eles incorrerão em custos para unidades de solicitação, armazenamento e saída.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender sobre como migrar os dados de volta para sua conta original usando os seguintes artigos:

* Para fazer uma solicitação de restauração, entre em contato com o Suporte do Azure, [arquive um ticket no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Use o feed de alteração do Cosmos DB](change-feed.md) para mover dados para o Azure Cosmos DB.

* [Use o Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.
