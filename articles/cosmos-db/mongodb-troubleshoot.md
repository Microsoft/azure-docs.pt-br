---
title: Solucionar erros comuns na API do Azure Cosmos DB para o Mongo DB
description: Este documento discute as maneiras de solucionar problemas comuns encontrados na API do Azure Cosmos DB para MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692225"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Solucionar problemas comuns na API do Azure Cosmos DB para o MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O artigo a seguir descreve erros comuns e soluções para implantações usando a API do Azure Cosmos DB para MongoDB.

>[!Note]
> O Azure Cosmos DB não hospeda o mecanismo do MongoDB. Ele fornece uma implementação do [protocolo de transferência versão 4.0](mongodb-feature-support-40.md), [3.6](mongodb-feature-support-36.md) e suporte herdado para o [protocolo de transferência versão 3.2](mongodb-feature-support.md) do MongoDB. Portanto, alguns desses erros só são encontrados na API do Azure Cosmos DB para MongoDB.

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Código       | Erro do                | Descrição  | Solução  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Uma causa comum é o caminho de índice correspondente ao item order-by especificado ter sido excluído ou a consulta order-by não ter um índice composto correspondente do qual ele pode ser atendido. A consulta solicita uma classificação em um campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de classificação que está sendo tentada. |
| 2 | A transação não está ativa | A transação de vários documentos ultrapassou o limite de tempo fixo de 5 segundos. | Repita a transação de vários documentos ou limite o escopo de operações dentro da transação de vários documentos para que ela seja concluída dentro do limite de 5 segundos. |
| 13 | Não Autorizado | A solicitação não tem as permissões para ser concluída. | Verifique se você está usando as chaves corretas.  |
| 26 | NamespaceNotFound | O banco de dados ou a coleção que está sendo referenciada na consulta não pode ser encontrada. | Verifique se o nome do banco de dados/da coleção corresponde precisamente ao nome em sua consulta.|
| 50 | ExceededTimeLimit | A solicitação excedeu o tempo limite de 60 segundos de execução. |  Pode haver muitas causas para esse erro. Uma das causas é a capacidade das unidades de solicitação alocadas atualmente não ser suficiente para concluir a solicitação. Isso pode ser resolvido aumentando as unidades de solicitação da coleção ou do banco de dados. Em outros casos, esse erro pode ser solucionado por meio da divisão de uma solicitação grande em partes menores. Repetir uma operação de gravação que recebeu esse erro pode resultar em uma gravação duplicada. <br><br>Se quiser tentar excluir grandes quantidades de dados sem afetar as RUs: <br>- Considere o uso de TTL (com base no carimbo de data/hora): [Expirar os dados com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md) <br>- Use o tamanho do cursor/lote para executar a exclusão. É possível buscar um documento por vez e excluí-lo por meio de um loop. Isso ajudará você a excluir dados lentamente sem afetar seu aplicativo de produção.|
| 61 | ShardKeyNotFound | O documento em sua solicitação não tinha a chave de fragmentação da coleção (chave de partição de Azure Cosmos DB). | Verifique se a chave de fragmentação da coleção está sendo usada na solicitação.|
| 66 | ImmutableField | A solicitação está tentando mudar um campo imutável | Os campos "_id" são imutáveis. A sua solicitação não deve tentar atualizar esse campo ou o campo de chave de fragmento. |
| 67 | CannotCreateIndex | A solicitação para criar um índice não pode ser concluída. | Até 500 índices de campo único podem ser criados em um contêiner. Até oito campos podem ser incluídos em um índice composto (há suporte para índices compostos na versão 3.6+). |
| 112 | WriteConflict | A transação de vários documentos falhou devido a uma transação com vários documentos conflitantes | Repita a transação de vários documentos até que ocorra corretamente. |
| 115 | CommandNotSupported | Não há compatibilidade com a solicitação tentada. | Informações adicionais devem ser incluídas no erro. Se essa funcionalidade for importante para suas implantações, crie um tíquete de suporte no [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e a equipe do Azure Cosmos DB entrará em contato com você. |
| 11000 | DuplicateKey | A chave de fragmentação (chave de partição do Azure Cosmos DB) do documento que você está inserindo já existe na coleção, ou uma restrição de campo de índice exclusivo foi violada. | Use a função update() para atualizar um documento existente. Se a restrição de campo do índice exclusivo tiver sido violada, insira ou atualize o documento com um valor de campo que ainda não exista no fragmento/na partição. Outra opção seria usar um campo que tenha uma combinação dos campos de ID e chave de fragmento. |
| 16500 | TooManyRequests  | O número total de unidades de solicitação consumidas é mais do que a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere a possibilidade de dimensionar a taxa de transferência atribuída a um contêiner ou um conjunto de contêineres do portal do Azure ou tentar a operação novamente. Se você habilitar SSR (repetição do lado do servidor), o Azure Cosmos DB repetirá automaticamente as solicitações que falham devido a esse erro. |
| 16501 | ExceededMemoryLimit | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. Isso só é aplicável à API Azure Cosmos DB para MongoDB versão 3.2. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome da fase do pipeline não reconhecido. | O nome da fase em sua solicitação de pipeline de agregação não foi reconhecido. | Verifique se todos os nomes do pipeline de agregação são válidos em sua solicitação. |
| - | Problemas de versão de fios do MongoDB | As versões mais antigas dos drivers do MongoDB não conseguem detectar o nome da conta do Azure Cosmos nas cadeias de conexão. | Acrescente `appName=@accountName@` ao final da cadeia de conexão, em que `accountName` é o nome da sua conta do Azure Cosmos DB. |
| - | Problemas de rede do cliente do MongoDB (como exceções de soquete ou endOfStream)| Falha na solicitação de rede. Isso geralmente é causado por uma conexão TCP inativa que o cliente do MongoDB está tentando usar. Os drivers do MongoDB geralmente utilizam o pool de conexões, o que resulta em uma conexão aleatória escolhida a partir do pool que está sendo usado para uma solicitação. Conexões inativas normalmente expiram no lado do Azure Cosmos DB depois de quatro minutos. | Você pode repetir essas solicitações com falha no código do aplicativo, mudar as configurações do cliente do MongoDB (driver) para desmontar conexões TCP inativas antes da janela de tempo limite de quatro minutos ou definir as configurações de `keepalive` do sistema operacional para manter as conexões TCP em um estado ativo.<br><br>Para evitar mensagens de conectividade, mude a cadeia de conexão para definir `maxConnectionIdleTime` como de 1 a 2 minutos.<br>- Driver do Mongo: configure `maxIdleTimeMS=120000` <br>- Node.JS: configure `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minutos
| - | O Shell do Mongo não está funcionando no portal do Azure | Quando o usuário tenta abrir um shell do Mongo, nada acontece, e a guia fica em branco.  | Verifique o Firewall. Não há compatibilidade do firewall com o shell do Mongo no portal do Azure. <br>- Instalar o shell do Mongo no computador local dentro das regras de firewall <br>- Usar o shell herdado do Mongo
| - | Não é possível se conectar com a cadeia de conexão  | A cadeia de conexão foi alterada ao atualizar do 3.2 para o 3.6 | Observe que, ao usar a API do Azure Cosmos DB para contas do MongoDB, a versão 3.6 das contas tem o ponto de extremidade no formato `*.mongo.cosmos.azure.com`, enquanto a versão 3.2 das contas tem o ponto de extremidade no formato `*.documents.azure.com`.  

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
