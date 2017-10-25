---
title: "Computação de banco de dados sem servidor – Azure Functions e Azure Cosmos DB | Microsoft Docs"
description: "Saiba como o Azure Cosmos DB e o Azure Functions podem ser usados juntos para criar aplicativos de computação sem servidor controlados por evento."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 192bdde86e12472a6fd87ba50fd597ea8b01247d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: computação de banco de dados sem servidor usando o Azure Functions

A computação sem servidor trata da capacidade de se concentrar em partes individuais de lógica que são repetíveis e sem monitoração de estado. Essas partes não exigem nenhum gerenciamento de infraestrutura e consomem recursos somente pelos segundos ou milissegundos durante os quais são executadas. No núcleo do movimento da computação sem servidor estão as funções, que são disponibilizadas no ecossistema do Azure pelo [Azure Functions](https://azure.microsoft.com/services/functions).

Com a integração nativa entre o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e o Azure Functions, você pode criar gatilhos de banco de dados, associações de entrada e associações de saída diretamente em sua conta do Azure Cosmos DB. Usando o Azure Functions e o Azure Cosmos DB, você pode criar e implantar os aplicativos sem servidor controlados por evento com acesso de baixa latência a dados avançados para uma base de usuários global.

## <a name="overview"></a>Visão geral

O Azure Cosmos DB e o Azure Functions permitem integrar bancos de dados e aplicativos sem servidor das seguintes maneiras:

* Crie um **gatilho do Azure Cosmos DB** controlado por evento em um Azure Function. Esse gatilho depende de fluxos do [feed de alterações](change-feed.md) para monitorar o contêiner do Azure Cosmos DB em busca de alterações. Quando alterações forem feitas em um contêiner, o fluxo do feed de alterações é enviado para o gatilho, que invoca o Azure Function.
* Como alternativa, associe um Azure Function a uma coleção do Azure Cosmos DB usando uma **associação de entrada**. As associações de entrada leem dados de um contêiner quando uma função é executada.
* Associe uma função a uma coleção do Azure Cosmos DB usando uma **associação de saída**. As associações de saída gravam dados em um contêiner quando uma função é concluída.

O seguinte diagrama ilustra cada uma destas três integrações: 

![Como o Azure Cosmos DB e o Azure Functions são integrados](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

O gatilho, a associação de entrada e a associação de saída do Azure Cosmos DB podem ser usados nas seguintes combinações:
* Um gatilho do Azure Cosmos DB pode ser usado com uma associação de saída para um contêiner diferente do Azure Cosmos DB. Depois que uma função executa uma ação em um item no feed de alterações, você pode gravá-la em outro contêiner (sua gravação no mesmo contêiner de origem efetivamente criará um loop recursivo). Outra opção é usar um gatilho do Azure Cosmos DB para migrar efetivamente todos os itens alterados de um contêiner para um contêiner diferente, com o uso de uma associação de saída.
* As associações de entrada e as associações de saída do Azure Cosmos DB podem ser usadas no mesmo Azure Function. Isso funciona bem em casos em que você deseja localizar determinados dados com a associação de entrada, modificá-los no Azure Function e, em seguida, salvá-los no mesmo contêiner ou em um contêiner diferente, após a modificação.
* Uma associação de entrada a um contêiner do Azure Cosmos DB pode ser usada na mesma função de um gatilho do Azure Cosmos DB e pode ser usada com ou sem uma associação de saída também. Use essa combinação para aplicar informações de moeda atualizadas (extraídas com uma associação de entrada a um contêiner de câmbio) ao feed de alterações de novos pedidos em seu serviço de carrinho de compras. O total do carrinho de compras atualizado, com a conversão de moeda atual aplicada, pode ser gravado em um terceiro contêiner usando uma associação de saída.

> [!NOTE]
> Neste momento, o gatilho, as associações de entrada e as associações de saída do Azure Cosmos DB funcionam apenas com contas do DocumentDB, Tabela e API do Graph.

## <a name="use-cases"></a>Casos de uso

Os casos de uso a seguir demonstram algumas maneiras pelas quais você pode aproveitar ao máximo seus dados do Azure Cosmos DB – conectando-os ao Azure Functions controlado por evento.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Caso de uso de IoT – gatilho e associação de saída do Azure Cosmos DB

Em implementações de IoT, você pode invocar uma função quando a luz do mecanismo de verificação é exibida em um carro conectado.

**Implementação:** use um gatilho e uma associação de saída do Azure Cosmos DB

1. Um **gatilho do Azure Cosmos DB** é usado para disparar eventos relacionados a alertas de carro, como a luz do mecanismo de verificação proveniente de um carro conectado.
2. Quando a luz do mecanismo de verificação é acesa, os dados do sensor são enviados para o Azure Cosmos DB.
3. O Azure Cosmos DB cria ou atualiza novos documentos de dados do sensor e, em seguida, essas alterações são transmitidas para o gatilho do Azure Cosmos DB.
4. O gatilho é invocado em cada alteração de dados para a coleta de dados do sensor, pois todas as alterações são transmitidas por meio do feed de alterações.
5. Uma condição de limite é usada na função para enviar os dados do sensor para o departamento de garantia.
6. Se a temperatura também estiver acima de determinado valor, um alerta também será enviado para o proprietário.
7. A **associação de saída** na função atualiza o registro do carro em outra coleção do Azure Cosmos DB para armazenar informações sobre o evento de mecanismo de verificação.

A imagem a seguir mostra o código gravado no portal do Azure para esse gatilho.

![Criar um gatilho do Azure Cosmos DB no portal do Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de uso financeiro – Gatilho de temporizador e associação de entrada

Em implementações financeiras, você pode invocar uma função quando um saldo de conta bancária se enquadra em determinada quantia.

**Implementação:** um gatilho de temporizador com uma associação de entrada do Azure Cosmos DB

1. Usando um [gatilho de temporizador](../azure-functions/functions-bindings-timer.md), recupere as informações de saldo de conta bancária armazenadas em um contêiner do Azure Cosmos DB em intervalos de tempo usando uma **associação de entrada**.
2. Se o saldo estiver abaixo do limite de saldo baixo definido pelo usuário, acompanhe com uma ação do Azure Function.
3. A associação de saída pode ser uma [integração do SendGrid](../azure-functions/functions-bindings-sendgrid.md), que envia um email de uma conta de serviço para os endereços de email identificados para cada uma das contas de saldo baixo.

As imagens a seguir mostram o código no portal do Azure para este cenário.

![Arquivo Index.js para um gatilho de Temporizador em um cenário financeiro](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Arquivo Run.csx para um gatilho de Temporizador em um cenário financeiro](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Caso de uso de jogos – gatilho e associação de saída do Azure Cosmos DB

Em jogos, quando um novo usuário é criado, você pode pesquisar outros usuários que talvez o conheçam usando a [API do Graph do Azure Cosmos DB](graph-introduction.md). Em seguida, você pode gravar os resultados em um [banco de dados de Tabela do Azure Cosmos DB](table-introduction.md) para fácil recuperação.

**Implementação:** use um gatilho e uma associação de saída do Azure Cosmos DB

1. Usando um [banco de dados de gráfico](graph-introduction.md) do Azure Cosmos DB para armazenar todos os usuários, você pode criar uma nova função com um gatilho do Azure Cosmos DB. 
2. Sempre que um novo usuário é inserido, a função é invocada e, em seguida, o resultado é armazenado usando uma **associação de saída**.
3. A função consulta o banco de dados de gráfico para pesquisar todos os usuários que estão diretamente relacionados ao novo usuário e retorna o conjunto de dados para a função.
4. Em seguida, esses dados são armazenados em um [banco de dados de tabela](table-introduction.md) do Azure Cosmos DB como um conjunto de pares chave-valor, que pode ser recuperado com facilidade por qualquer aplicativo front-end que mostra ao novo usuário seus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caso de uso de varejo – Várias funções

Em implementações de varejo, quando um usuário adiciona um item à sua cesta agora você tem a flexibilidade de criar e invocar funções para componentes de pipeline de negócios opcionais.

**Implementação:** vários gatilhos do Azure Cosmos DB escutando uma coleção

1. Crie vários Azure Functions com a adição de gatilhos do Azure Cosmos DB a cada um – que escutam o mesmo feed de alterações de dados de um carrinho de compras. 
2. Sempre que um novo item é adicionado ao carrinho de compras de um usuário, cada função é invocada de forma independente pelo feed de alterações do contêiner de carrinho de compras.
    * Uma função pode usar o conteúdo da cesta atual para alterar a exibição de outros itens nos quais o usuário pode estar interessado.
    * Outra função pode atualizar os totais de inventário.
    * Outra função pode enviar informações do cliente sobre determinados produtos ao departamento de marketing, que envia um mensageiro promocional. 

    Qualquer departamento pode criar um gatilho do Azure Cosmos DB ouvindo o feed de alterações e garantir que ele não atrase eventos críticos de processamento de pedido no processo.

Em todos esses casos de uso, como a função desacoplou o próprio aplicativo, você não precisa criar novas instâncias de aplicativo o tempo todo. Em vez disso, o Azure Functions cria funções individuais para concluir processos discretos, conforme necessário.

## <a name="tooling"></a>Ferramentas

A integração nativa entre o Azure Cosmos DB e o Azure Functions está disponível no portal do Azure.
* No portal do Azure Functions, você pode criar um gatilho do Azure Cosmos DB. Para obter instruções sobre o guia de início rápido, consulte [Criar um gatilho do Azure Cosmos DB no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc) e ![Criar um gatilho do Azure Cosmos DB no portal do Azure Functions](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* No portal do Azure Functions, também adicione associações de entrada e associações de saída do Azure Cosmos DB a outros tipos de gatilhos. Para obter instruções sobre o guia de início rápido, consulte [Armazenar dados não estruturados usando o Azure Functions e o Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Criar um gatilho do Azure Cosmos DB no portal do Azure Functions](./media/serverless-computing-database/function-portal-input-binding.png)
*   No portal do Azure Cosmos DB, adicione um gatilho do Azure Cosmos DB a um aplicativo existente do Azure Function no mesmo grupo de recursos.
    ![Criar um gatilho do Azure Cosmos DB no portal do Azure Functions](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Por que escolher a integração do Azure Functions para a computação sem servidor?

O Azure Functions fornece a capacidade de criar unidades de trabalho escalonáveis ou partes concisas de lógica que podem ser executadas sob demanda, sem provisionamento nem gerenciamento de infraestrutura. Usando o Azure Functions, você não precisa criar um aplicativo completo para responder a alterações no banco de dados do Azure Cosmos DB; você pode criar pequenas funções reutilizáveis para tarefas específicas. Além disso, também use dados do Azure Cosmos DB como entrada ou saída para um Azure Function em resposta a um evento como solicitações HTTP ou um gatilho de tempo.

O Azure Cosmos DB é o banco de dados recomendado para a arquitetura de computação sem servidor pelos seguintes motivos:

* **Acesso instantâneo a todos os seus dados**: você tem acesso granular a cada valor armazenado porque o Azure Cosmos DB [indexa automaticamente](indexing-policies.md) todos os dados por padrão e disponibiliza esses índices imediatamente. Isso significa que você consegue consultar constantemente, atualizar e adicionar novos itens a seu banco de dados e ter acesso instantâneo por meio do Azure Functions.

* **Sem esquema**. O Azure Cosmos DB é sem esquema e, portanto, pode exclusivamente manipular qualquer saída de dados de um Azure Function. Essa abordagem de “manipular qualquer coisa” facilita a criação de uma variedade de Funções que serão todas geradas para o Azure Cosmos DB.

* **Taxa de transferência escalonável**. A taxa de transferência pode ser escalada e reduzida verticalmente de forma instantânea no Azure Cosmos DB. Caso você tenha centenas ou milhares de Funções consultando e gravando na mesma coleção, expanda as [RU/s](request-units.md) para manipular a carga. Todas as funções podem trabalhar em paralelo usando as RU/s alocadas e seus dados têm a garantia de serem [consistentes](consistency-levels.md).

* **Replicação global**. Replique dados do Azure Cosmos DB [em todo o mundo](distribute-data-globally.md) para reduzir a latência, localizando os dados na geografia mais próxima a onde os usuários se encontram. Assim como ocorre com todas as consultas do Azure Cosmos DB, os dados de gatilhos controlados por evento são dados lidos do Azure Cosmos DB mais próximo ao usuário.

Se você pretende fazer a integração com o Azure Functions para armazenar dados e não precisa de indexação profunda ou se precisa armazenar anexos e arquivos de mídia, o [gatilho do Armazenamento de Blobs do Azure](../azure-functions/functions-bindings-storage-blob.md) pode ser uma opção melhor.

Benefícios do Azure Functions: 

* **Controlado por evento**. O Azure Functions é controlado por eventos e pode escutar um feed de alterações do Azure Cosmos DB. Isso significa que você não precisa criar a lógica de escuta, apenas ficar atento às alterações que você está escutando. 

* **Sem limites**. O Functions é executado em paralelo e o serviço cria o número de funções que forem necessárias. Defina os parâmetros.

* **Bom para tarefas rápidas**. O serviço cria novas instâncias de funções sempre que um evento é acionado e fecha-as assim que a função é concluída. Você paga apenas pelo tempo durante o qual as funções são executadas.

Caso não tenha certeza se o Flow, os Aplicativos Lógicos, o Azure Functions ou o WebJobs é o melhor para sua implementação, consulte [Escolher entre o Flow, os Aplicativos Lógicos, o Azure Functions e o WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Próximas etapas

Agora vamos conectar o Azure Cosmos DB e o Azure Functions de verdade: 

* [Criar um gatilho do Azure Cosmos DB no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Criar um gatilho HTTP do Azure Functions com uma associação de entrada do Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Armazenar dados não estruturados usando o Azure Functions e o Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Associações e gatilhos do Azure Cosmos DB](../azure-functions/functions-bindings-documentdb.md)


 



