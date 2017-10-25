---
title: 'Azure Cosmos DB: API do DocumentDB | Microsoft Docs'
description: "Aprenda a usar o Azure Cosmos DB para armazenar e consultar grandes volumes de documentos JSON com baixa latência usando o SQL e o JavaScript."
keywords: banco de dados JSON, banco de dados de documentos
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: mimig
ms.openlocfilehash: 2cb4bd74ea973c8ff980d208a8c5f63a98ec1edd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-documentdb-api"></a>Introdução ao Azure Cosmos DB: API do DocumentDB

O [Azure Cosmos DB](introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece [distribuição global imediata](distribute-data-globally.md), [dimensionamento elástico da taxa de transferência e do armazenamento](partition-data.md) mundialmente, latências de milissegundos de um dígito no 99º percentil, [cinco níveis de consistência bem-definidos](consistency-levels.md) e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa dados automaticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e suporta modelos de dados de colunas, gráficos, valores-chave e documentos. 

![API do Azure DocumentDB](./media/documentdb-introduction/cosmosdb-documentdb.png) 

Com a API do DocumentDB, o Azure Cosmos DB oferece [recursos de consulta SQL](documentdb-sql-query.md) familiares e avançados com menos latências consistentes em dados JSON sem esquemas. Neste artigo, fornecemos uma visão geral da API do DocumentDB do Azure Cosmos DB, como você pode usá-lo para armazenar grandes volumes de dados JSON, consultá-los em ordem de latência de milissegundos e evoluir facilmente o esquema. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Quais benefícios e os principais recursos que o Azure Cosmos DB oferece?
O Azure Cosmos DB, oferece os seguintes principais recursos e benefícios por meio da API do DocumentDB:

* **Taxa de transferência e armazenamento elasticamente escalonáveis:** escalone o seu banco de dados JSON para atender às necessidades do seu aplicativo. Os dados são armazenados em SSD (discos de estado sólido) para fornecer latências baixas previsíveis. O Azure Cosmos DB dá suporte a contêineres para armazenar dados JSON chamados de coleções, que podem ser dimensionados para tamanhos de armazenamento praticamente ilimitados e taxa de transferência provisionada. Você pode dimensionar elasticamente o Azure Cosmos DB com desempenho previsível à medida que o aplicativo cresce. 


* **Replicação de várias regiões:** o Azure Cosmos DB replica de forma transparente os seus dados para todas as regiões associadas à sua conta, permitindo que você desenvolva aplicativos que exigem acesso global aos dados e fornecendo compensações entre consistência, disponibilidade e desempenho, tudo isso com garantias correspondentes. O Azure Cosmos DB fornece failover regional transparente com APIs de hospedagem múltipla, e capacidade de dimensionar de forma elástica a taxa de transferência e o armazenamento em todo o mundo. Saiba mais em [Como distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

* **Consultas ad hoc com sintaxe SQL familiar:** armazene documentos JSON heterogêneos e consulte-os com uma sintaxe SQL familiar. O Azure Cosmos DB utiliza uma tecnologia de indexação estruturada por log, sem bloqueio e altamente simultânea para indexar automaticamente todo o conteúdo do documento. Isso habilita consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou exibições. Saiba mais em [Consulta do Azure Cosmos DB](documentdb-sql-query.md). 
* **Execução de JavaScript dentro do banco de dados:** expresse a lógica do aplicativo como procedimentos armazenados, gatilhos e UDFs usando JavaScript padrão. Isso permite que a lógica do aplicativo opere em dados sem se preocupar com incompatibilidade entre o aplicativo e o esquema do banco de dados. A API do DocumentDB oferece execução transacional completa da lógica do aplicativo em JavaScript diretamente dentro do mecanismo do banco de dados. A profunda integração do JavaScript habilita a execução das operações INSERT, REPLACE, DELETE e SELECT por meio de um programa JavaScript como uma transação isolada. Saiba mais em [Programação no lado servidor do DocumentDB](programming.md).

* **Níveis de consistência ajustáveis:** escolha entre cinco níveis de consistência bem-definidos para chegar ao equilíbrio ideal entre consistência e desempenho. Para operações de consulta e leitura, o Azure Cosmos DB oferece cinco níveis de consistência diferentes: forte, desatualização limitada, sessão, prefixo constante e eventual. Esses níveis de consistência granulares e bem-definidos permitem que você faça compensações seguras entre consistência, disponibilidade e latência. Saiba mais em [Como usar níveis de consistência para maximizar a disponibilidade e o desempenho](consistency-levels.md).

* **Completamente gerenciado:** elimine a necessidade de gerenciar recursos de banco de dados e máquina. Como um serviço do Microsoft Azure totalmente gerenciado, você não precisa gerenciar máquinas virtuais, implantar e configurar software,gerenciar o dimensionamento nem lidar com atualizações de camadas de dados complexas. Cada banco de dados é salvo em backup automaticamente e protegido contra falhas regionais. Você pode adicionar facilmente uma conta do Azure Cosmos DB e provisionar a capacidade conforme for necessário, permitindo que você se concentre em seu aplicativo sem se preocupar com a operação e com o gerenciamento do banco de dados. 

* **Aberto pelo design:** comece a usar o Banco de Dados de Documentos rapidamente, com habilidades e ferramentas já existentes. A programação da API do DocumentDB é simples, acessível e não requer a adoção de novas ferramentas nem a adesão de extensões personalizadas para JSON ou JavaScript. Você pode acessar toda a funcionalidade do banco de dados, incluindo CRUD, consulta e processamento do JavaScript em uma interface HTTP RESTful simples. A API do DocumentDB engloba formatos, linguagens e padrões existentes, além de oferecer recursos de banco de dados de alto valor.

* **Indexação automática:** por padrão, o Azure Cosmos DB indexa automaticamente todos os documentos no banco de dados e não espera nem exige qualquer esquema ou criação de índices secundários. Não deseja indexar tudo? Não se preocupe, você também pode [recusar caminhos nos arquivos JSON](indexing-policies.md) .

* **Suporte ao feed de alterações:** o feed de alterações fornece uma lista classificada de documentos em uma coleção do Azure Cosmos DB na ordem em que eles foram modificados. Este feed pode ser usado para escutar as modificações nos dados para replicar dados, disparar chamadas de API ou executar o processamento de fluxo em atualizações. O feed de alterações é habilitado automaticamente e fácil de usar: [saiba mais sobre o feed de alterações](https://docs.microsoft.com/azure/cosmos-db/change-feed). 

## <a name="data-management"></a>Como gerenciar dados com a API do DocumentDB?
A API do DocumentDB ajuda a gerenciar os dados JSON por meio de recursos do banco de dados bem-definidos. Esses recursos são replicados para alta disponibilidade e são endereçáveis exclusivamente por seu URI lógico. A API do DocumentDB oferece um modelo de programação RESTful baseado em HTTP simples para todos os recursos. 


A conta de banco de dados do Azure Cosmos DB é um namespace exclusivo que fornece acesso ao Azure Cosmos DB. Antes de criar uma conta de banco de dados, você deve ter uma assinatura do Azure, que dá acesso a uma variedade de serviços do Azure. 

Todos os recursos do Azure Cosmos DB são modelados e armazenados como documentos JSON. Os recursos são gerenciados como itens, que são documentos JSON contendo metadados e como feeds que são coleções de itens. Conjuntos de itens estão contidos dentro de seus respectivos feeds.

A imagem abaixo mostra os relacionamentos entre os recursos do Azure Cosmos DB:

![A relação hierárquica entre os recursos no Azure Cosmos DB][1] 

Uma conta do banco de dados é formada por um conjunto de bancos de dados, cada um contendo diversas coleções, cada uma delas pode conter procedimentos armazenados, gatilhos, UDFs, documentos e anexos relacionados. Um banco de dados também possui usuários associados, cada um com um conjunto de permissões para acessar várias outras coleções, procedimentos armazenados, gatilhos, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são recursos definidos pelo sistema com esquemas bastante conhecidos, os documentos, procedimentos armazenados, gatilhos, UDFs e anexos possuem conteúdo JSON arbitrário e definido pelo usuário.  

> [!NOTE]
> Como a API do DocumentDB estava disponível anteriormente como o serviço do Azure DocumentDB, você pode continuar a provisionar, monitorar e gerenciar as contas criadas por meio da API REST de gerenciamento de recursos do Azure ou por ferramentas usando nomes de recursos do Azure DocumentDB ou do Azure Cosmos DB. Usamos os nomes intercambiáveis para se referir às APIs do Azure DocumentDB. 

## <a name="develop"></a>Como é possível desenvolver aplicativos com a API do DocumentDB?

O Azure Cosmos DB expõe recursos pode meio de APIs REST que podem ser chamadas por qualquer linguagem capacitada para fazer solicitações HTTP/HTTPS. Além disso, oferecemos bibliotecas de programação para várias linguagens populares para a API do DocumentDB. Essas bibliotecas de cliente manipulam detalhes para simplificar aspectos do trabalho com a API , como cache de endereço, gerenciamento de exceções, novas tentativas automáticas e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas:  

| Baixar | Documentação |
| --- | --- |
| [SDK .NET](http://go.microsoft.com/fwlink/?LinkID=402989) |[Biblioteca .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [SDK do Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Biblioteca do Node.js](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Biblioteca do Java](/java/api/com.microsoft.azure.documentdb) |
| [SDK do JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) |[Biblioteca do JavaScript](http://azure.github.io/azure-documentdb-js/) |
| n/d |[SDK do JavaScript do lado servidor](http://azure.github.io/azure-documentdb-js-server/) |
| [SDK do Python](https://pypi.python.org/pypi/pydocumentdb) |[Biblioteca do Python](http://azure.github.io/azure-documentdb-python/) |
| n/d | [API para MongoDB](mongodb-introduction.md)


Com o [Emulador do Azure Cosmos DB](local-emulator.md), você pode desenvolver e testar seu aplicativo localmente com a API do DocumentDB sem criar uma assinatura do Azure nem incidir custos. Quando estiver satisfeito com o funcionamento de seu aplicativo no emulador, você pode passar a usar uma conta do Azure Cosmos DB na nuvem.

Além das operações básicas de criação, leitura, atualização e exclusão, a API do DocumentDB fornece uma interface de consulta SQL avançada para recuperar documentos JSON e suporte do servidor para a execução transacional da lógica do aplicativo JavaScript. As interfaces de execução de script e consulta estão disponíveis em todas as bibliotecas da plataforma, bem como as APIs REST. 

### <a name="sql-query"></a>Consulta SQL
A API do DocumentDB dá suporte à consulta de documentos usando uma linguagem SQL baseada no sistema do tipo JavaScript e em expressões com suporte a consultas relacionais, hierárquicas e espaciais. A linguagem de consulta do DocumentDB é uma interface simples, mas poderosa para consultar documentos JSON. A linguagem suporta um subconjunto da gramática ANSI SQL e adiciona profunda integração do objeto JavaScript, matrizes, construção de objetos e invocação de funções. A PI do DocumentDB fornece seu modelo de consulta sem nenhum esquema explícito ou dicas de indexação do desenvolvedor.

As Funções definidas (UDFs) de usuário podem ser registradas com a API do DocumentDB e referenciadas como parte de uma consulta SQL, estendendo a gramática para dar suporte à lógica personalizada do aplicativo. Essas UDFs são gravadas como programas JavaScript e executadas no banco de dados. 

O [SDK do .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) da API do DocumentDB também oferece um provedor de consultas LINQ para desenvolvedores .NET. 

### <a name="transactions-and-javascript-execution"></a>Execução de transações e do JavaScript
A API do DocumentDB permite gravar a lógica do aplicativo como programas nomeados escritos inteiramente em JavaScript. Esses programas são registrados para uma coleção e podem emitir operações de banco de dados nos documentos dentro de determinada coleção. O JavaScript pode ser registrado para execução como gatilho, procedimento armazenado ou função definida pelo usuário (UDF). Procedimentos armazenados e gatilhos podem criar, ler, atualizar e excluir documentos enquanto executar funções definidas pelo usuário como parte da lógica de execução de consulta sem acesso de gravação à coleção.

A execução do JavaScript dentro do BD Cosmos é baseada nos conceitos suportados pelos sistemas do banco de dados relacional, com o JavaScript sendo uma substituição moderna para Transact-SQL. Toda lógica do JavaScript é executada em uma transação ACID ambiente com isolamento de instantâneo. Durante sua execução, se o JavaScript lançar uma exceção, então, toda a transação será abortada.

## <a name="are-there-any-online-courses-on-azure-cosmos-db"></a>Há algum processo online no Azure Cosmos DB?

Sim, há um curso da [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) sobre o Azure DocumentDB. 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>Próximas etapas
Já possui uma conta do Azure? Em seguida, acompanhe os nossos [Inícios rápidos](../cosmos-db/create-documentdb-dotnet.md), que exibem um passo a passo sobre a criação de uma conta e a introdução ao Azure Cosmos DB.

[1]: ./media/documentdb-introduction/json-database-resources1.png

