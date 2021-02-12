---
title: Solucionar erros comuns na API do Azure Cosmos DB para o Mongo DB
description: Este documento discute as maneiras de solucionar problemas comuns encontrados na API do Azure Cosmos DB para MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258177"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Solucionar problemas comuns na API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O artigo a seguir descreve erros comuns e soluções para implantações usando a API de Azure Cosmos DB para MongoDB.

>[!Note]
> Azure Cosmos DB não hospeda o mecanismo do MongoDB. Ele fornece uma implementação do protocolo de transmissão do MongoDB. Portanto, alguns desses erros só são encontrados na API do Azure Cosmos DB para MongoDB. 

## <a name="common-errors-and-solutions"></a>Erros e soluções comuns

| Código       | Erro                | Descrição  | Solução  |
|------------|----------------------|--------------|-----------|
| 2 | O caminho de índice correspondente ao item de ordem por especificado é excluído ou a consulta order by não tem um índice composto correspondente do qual ele pode ser servido. | A consulta solicita uma classificação em um campo que não está indexado. | Crie um índice correspondente (ou índice composto) para a consulta de classificação que está sendo tentada. |
| 13 | Não Autorizado | A solicitação não tem as permissões a serem concluídas. | Verifique se você definiu as permissões adequadas para o banco de dados e a coleção.  |
| 16 | InvalidLength | A solicitação especificada tem um comprimento inválido. | Se você estiver usando a função explicar (), certifique-se de fornecer apenas uma operação. |
| 26 | NamespaceNotFound | O banco de dados ou a coleção que está sendo referenciada na consulta não pode ser encontrada. | Verifique se o nome do banco de dados/coleção corresponde precisamente ao nome em sua consulta.|
| 50 | ExceededTimeLimit | A solicitação excedeu o tempo limite de 60 segundos de execução. |  Pode haver muitas causas para esse erro. Uma das causas é quando a capacidade das unidades de solicitação atualmente alocadas não é suficiente para concluir a solicitação. Isso pode ser resolvido aumentando as unidades de solicitação da coleção ou do banco de dados. Em outros casos, esse erro pode ser solucionado por meio da divisão de uma solicitação grande em partes menores. Repetir uma operação de gravação que recebeu esse erro pode resultar em uma gravação duplicada. <br><br>Se você estiver tentando excluir grandes quantidades de dados sem afetar RUs: <br>-Considere usar TTL (com base no carimbo de data/hora): [expirar dados com a API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) <br>-Use o tamanho do cursor/lote para executar a exclusão. É possível buscar um documento por vez e excluí-lo por meio de um loop. Isso ajudará você a excluir dados lentamente sem afetar seu aplicativo de produção.|
| 61 | ShardKeyNotFound | O documento em sua solicitação não continha a chave de fragmentação da coleção (chave de partição de Azure Cosmos DB). | Verifique se a chave de fragmentação da coleção está sendo usada na solicitação.|
| 66 | Imutável | A solicitação está tentando alterar um campo imutável | os campos "ID" são imutáveis. Certifique-se de que sua solicitação não tente atualizar esse campo. |
| 67 | CannotCreateIndex | A solicitação para criar um índice não pode ser concluída. | Até 500 índices de campo único podem ser criados em um contêiner. Até oito campos podem ser incluídos em um índice composto (há suporte para índices compostos na versão 3.6 +). |
| 115 | CommandNotSupported | Não há suporte para a solicitação tentada. | Detalhes adicionais devem ser fornecidos no erro. Se essa funcionalidade for importante para suas implantações, informe-nos criando um tíquete de suporte no [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | A chave de fragmentação (Azure Cosmos DB chave de partição) do documento que você está inserindo já existe na coleção ou uma restrição de campo de índice exclusivo foi violada. | Use a função Update () para atualizar um documento existente. Se a restrição de campo de índice exclusivo tiver sido violada, insira ou atualize o documento com um valor de campo que ainda não existe no fragmento/partição. |
| 16500 | TooManyRequests  | O número total de unidades de solicitação consumidas é mais do que a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere a possibilidade de dimensionar a taxa de transferência atribuída a um contêiner ou um conjunto de contêineres do portal do Azure ou tentar a operação novamente. Se você [habilitar SSR](prevent-rate-limiting-errors.md) (repetição do lado do servidor), Azure Cosmos DB repetirá automaticamente as solicitações que falham devido a esse erro. |
| 16501 | ExceededMemoryLimit | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou entre em contato com o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome do estágio de pipeline não reconhecido. | O nome do estágio em sua solicitação de pipeline de agregação não foi reconhecido. | Verifique se todos os nomes de pipeline de agregação são válidos em sua solicitação. |
| - | Problemas de versão de fios do MongoDB | As versões mais antigas dos drivers do MongoDB não conseguem detectar o nome da conta do Azure Cosmos nas cadeias de conexão. | Acrescente *AppName = @**accountName** @* no final da API da sua Cosmos DB para a cadeia de conexão do MongoDB, em que ***accountName** _ é o nome da sua conta de Cosmos DB. |
| - | Problemas de rede do cliente do MongoDB (como exceções de soquete ou endOfStream)| Falha na solicitação de rede. Isso geralmente é causado por uma conexão TCP inativa que o cliente MongoDB está tentando usar. Os drivers do MongoDB geralmente utilizam o pool de conexões, o que resulta em uma conexão aleatória escolhida a partir do pool que está sendo usado para uma solicitação. Conexões inativas normalmente expiram no Azure Cosmos DB terminar após quatro minutos. | Você pode repetir essas solicitações com falha no código do aplicativo, alterar as configurações do cliente MongoDB (driver) para desmontar conexões TCP inativas antes da janela de tempo limite de quatro minutos ou definir as configurações de KeepAlive do sistema operacional para manter as conexões TCP em um estado ativo.<br><br>Para evitar mensagens de conectividade, mude a cadeia de conexão para definir maxConnectionIdleTime como de 1 a 2 minutos.<br>-Driver Mongo: configurar _maxIdleTimeMS = 120000 * <br>-Node.JS: configure *socketTimeoutMS = 120000*, *falha* = true, *keepAlive* = true, *keepAliveInitialDelay* = 3 minutos

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
