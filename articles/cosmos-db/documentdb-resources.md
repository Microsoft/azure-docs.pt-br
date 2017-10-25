---
title: Conceitos e modelo de recurso do Azure Cosmos DB | Microsoft Docs
description: "Saiba mais sobre o modelo hierárquico de bancos de dados, coleções, UDF (função definida pelo usuário), documentos, permissões do Azure Cosmos DB para gerenciar recursos e muito mais."
keywords: "Modelo hierárquico, cosmosdb, azure, Microsoft azure"
services: cosmos-db
documentationcenter: 
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: anhoh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 53059c09d454dedbd65ef188a0dd91d497b0e502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Modelo de recurso hierárquico e principais conceitos do Azure Cosmos DB
As entidades de banco de dados gerenciadas pelo Azure Cosmos DB são chamadas de **recursos**. Cada recurso é identificado de maneira exclusiva por um URI lógico. Você pode interagir com os recursos usando verbos de HTTP padrão, cabeçalhos de solicitação/resposta e códigos de status. 

Após ler este artigo, você poderá responder as perguntas a seguir:

* O que é o modelo de recurso do Azure Cosmos DB?
* O que são recursos definidos pelo sistema em oposição a recursos definidos pelo usuário?
* Como eu acesso um recurso?
* Como eu trabalho com coleções?
* Como trabalhar com procedimentos armazenados, disparadores e UDFs (Funções Definidas pelo Usuário)?

## <a name="hierarchical-resource-model"></a>Modelo de recursos hierárquico
Conforme ilustrado pelo diagrama a seguir, o **modelo de recurso** hierárquico do Azure Cosmos DB é formado por conjuntos de recursos em uma conta de banco de dados, cada um endereçável por meio de um URI lógico e estável. Neste artigo, um conjunto de recursos será chamado de **feed** . 

> [!NOTE]
> O Azure Cosmos DB oferece um protocolo TCP altamente eficiente, que também é RESTful no seu modelo de comunicação, disponível por meio da [API do cliente .NET do DocumentDB](documentdb-sdk-dotnet.md).
> 
> 

![Modelo de recurso hierárquico do Azure Cosmos DB][1]  
**Modelo de recursos hierárquico**   

Para começar a trabalhar com os recursos, [crie uma conta do banco de dados](create-documentdb-dotnet.md) usando sua assinatura do Azure. Uma conta do banco de dados pode ser formada por um conjunto de **bancos de dados**, cada um contendo diversas **coleções**, cada uma delas, por sua vez, contendo **procedimentos armazenados, gatilhos, UDFs, documentos** e **anexos** relacionados. Um banco de dados também tem **usuários** associados, cada um com um conjunto de **permissões** para acessar coleções, procedimentos armazenados, gatilhos, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são recursos definidos pelo sistema com esquemas bastante conhecidos, os documentos e anexos possuem conteúdos JSON arbitrários, definidos pelo usuário.  

| Recurso | Descrição |
| --- | --- |
| Conta de banco de dados |Uma conta de banco de dados está associada a um conjunto de bancos de dados e uma quantidade fixa de armazenamento de blobs para anexos. Você pode criar uma ou mais contas do banco de dados usando sua assinatura do Azure. Para obter mais informações, visite nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Banco de dados |Um banco de dados é um contêiner lógico de armazenamento de documentos particionado em coleções. Ele também é um contêiner para usuários. |
| Usuário |O namespace lógico para obter o escopo das permissões. |
| Permissão |Um token de autorização associado a um usuário para acesso a um recurso específico. |
| Coleção |Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](performance-levels.md) é determinado pelo nível de desempenho associado à coleção. As coleções podem abranger uma ou mais partições/servidores e podem ser dimensionadas para lidar com volumes de armazenamento ou taxa de transferência praticamente ilimitados. |
| Procedimento armazenado |Lógica de aplicativo gravada no JavaScript que é registrada com uma coleção e executada de forma transacional dentro do mecanismo do banco de dados. |
| Gatilho |Lógica de aplicativo escrita em JavaScript executada antes ou depois de uma operação de inserção, substituição ou exclusão. |
| UDF |Uma lógica de aplicativo escrita em JavaScript. As UDFs permitem modelar um operador de consulta personalizada e, portanto, estender a linguagem de consulta principal da API do DocumentDB. |
| Documento |Conteúdo JSON (arbitrário) definido pelo usuário. Por padrão, nenhum esquema precisa ser definido e nenhum índice secundário precisa ser fornecido a todos os documentos adicionados a uma coleção. |
| Anexo |Um anexo é um documento especial que contém referências e metadados associados a blobs/mídias externos. O desenvolvedor pode optar por ter o blob gerenciado pelo Cosmos DB ou armazená-lo em um provedor de serviços blob externo, como OneDrive, Dropbox, etc. |

## <a name="system-vs-user-defined-resources"></a>Recursos definidos pelo sistema versus usuário
Recursos como contas do banco de dados, bancos de dados, coleções, usuários, permissões, procedimentos armazenados, gatilhos e UDFs, todos possuem um esquema fixo e são chamados de recursos do sistema. Em contraste, recursos como documentos e anexos não possuem restrições sobre o esquema e são exemplos de recursos definidos pelo usuário. No Cosmos DB, ambos os recursos definidos pelo sistema e pelo usuário são representados e gerenciados como JSON em conformidade com o padrão. Todos os recursos, definidos pelo usuário ou pelo sistema, possuem as seguintes propriedades em comum.

> [!NOTE]
> Observe que todas as propriedades geradas pelo sistema em um recurso têm como prefixo um sublinhado (_) na sua representação JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriedade</strong></p></td>
            <td valign="top"><p><strong>Configurável pelo usuário ou gerada pelo sistema?</strong></p></td>
            <td valign="top"><p><strong>Finalidade</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>Identificador hierárquico e exclusivo do recurso, gerado pelo sistema</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>etag do recurso necessário para controle de concorrência otimista</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>Último carimbo de data/hora atualizado do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Gerada pelo sistema</p></td>
            <td valign="top"><p>URI endereçável exclusivo do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Você pode usar o</p></td>
            <td valign="top"><p>Nome exclusivo definido pelo usuário para o recurso (com o mesmo valor de chave de partição). Se o usuário não especificar uma id, uma id será gerada pelo sistema</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Representação da conexão dos recursos
O Cosmos DB não exige nenhuma extensão proprietária no padrão JSON nem codificações especiais; ele trabalha com documentos JSON em conformidade com o padrão.  

### <a name="addressing-a-resource"></a>Endereçamento de um recurso
Todos os recursos são endereçáveis pelo URI. O valor da propriedade **_self** de um recurso representa o URI relativo do recurso. O formato do URI consiste nos segmentos do caminho /\<feed\>/{_rid}:  

| Valor de _self | Descrição |
| --- | --- |
| /dbs |Feed de bancos de dados em uma conta de banco de dados |
| /dbs/{dbName} |Banco de dados com uma ID correspondente ao valor {dbName} |
| /dbs/{dbName}/colls/ |Feed de coleções em um banco de dados |
| /dbs/{dbName}/colls/{collName} |Coleção com uma ID correspondente ao valor de {collName} |
| /dbs/{dbName}/colls/{collName}/docs |Feed de documentos em uma coleção |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Documento com uma ID correspondente ao valor {doc} |
| /dbs/{dbName}/users/ |Feed de usuários em um banco de dados |
| /dbs/{dbName}/users/{userId} |Usuário com uma ID correspondente ao valor {user} |
| /dbs/{dbName}/users/{userId}/permissions |Feed de permissões em um usuário |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Permissão com uma ID correspondente ao valor {permission} |

Cada recurso tem um nome de usuário exclusivo definido exposto por meio da propriedade id. Observação: para documentos, se o usuário não especificar uma id, os SDKs com suporte gerarão automaticamente uma id exclusiva para o documento. A ID é uma cadeia de caracteres definida pelo usuário, com até 256 caracteres e exclusiva no contexto de um recurso pai específico. 

Cada recurso também tem um identificador de recurso hierárquico gerado pelo sistema (também chamado de RID), que é disponibilizado pela propriedade _rid. O RID codifica a hierarquia inteira de um determinado recurso, sendo uma representação interna conveniente usada para impor integridade referencial de maneira distribuída. O RID é exclusivo em uma conta de banco de dados e é usado internamente pelo Cosmos DB para o roteamento eficiente, sem a necessidade de pesquisas entre partições. Os valores das propriedades _self e _rid são representações alternativas e canônicas de um recurso. 

As APIs REST têm suporte para manipular recursos e endereçamento de solicitações pelas propriedades de id e _rid.

## <a name="database-accounts"></a>Contas de banco de dados
É possível provisionar uma ou mais contas de bancos de dados do Cosmos DB usando sua assinatura do Azure.

Você pode criar e gerenciar contas de bancos de dados do Cosmos DB por meio do Portal do Azure em [http://portal.azure.com/](https://portal.azure.com/). Criar e gerenciar uma conta do banco de dados requer acesso administrativo e pode ser feito somente com sua assinatura do Azure. 

### <a name="database-account-properties"></a>Propriedades de contas de banco de dados
Como parte do provisionamento e gerenciamento de uma conta do banco de dados, você pode configurar e ler as seguintes propriedades:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nome da Propriedade</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Política de Consistência</p></td>
            <td valign="top"><p>Defina essa propriedade para configurar o nível de consistência padrão para todas as coleções em sua conta do banco de dados. Você pode substituir o nível de consistência com base na solicitação usando o cabeçalho de solicitação [x-ms-consistency-level]. <p><p>Observe que essa propriedade somente se aplica aos <i>recursos definidos pelo usuário</i>. Todos os recursos definidos pelo sistema são configurados para oferecer suporte a leituras/consultas com uma forte consistência.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chaves de autorização</p></td>
            <td valign="top"><p>Essas são as chaves primária e secundária principal e somente leitura que dão acesso administrativo a todos os recursos na conta de banco de dados.</p></td>
        </tr>
    </tbody>
</table>

Observe que, além de provisionar, configurar e gerenciar sua conta de banco de dados no Portal do Azure, também é possível criar e gerenciar de forma programática contas de bancos de dados do Cosmos DB usando as [APIs REST do Azure Cosmos DB](/rest/api/documentdb/) e os [SDKs do cliente](documentdb-sdk-dotnet.md).  

## <a name="databases"></a>Bancos de dados
Um banco de dados do Cosmos DB é um contêiner lógico de uma ou mais coleções e usuários, conforme mostrado no diagrama a seguir. Você pode criar qualquer quantidade de bancos de dados em uma conta de banco de dados do Cosmos DB, sujeito aos limites da oferta.  

![Modelo hierárquico de coleções e conta de banco de dados][2]  
**Um banco de dados é um contêiner lógico de usuários e coleções**

Um banco de dados pode conter armazenamento de documentos praticamente ilimitado dividido nas coleções.

### <a name="elastic-scale-of-a-azure-cosmos-db-database"></a>Escala elástica de um banco de dados do Azure Cosmos DB
Um banco de dados do Cosmos DB é elástico por padrão – variando de alguns GB a petabytes de armazenamento de documentos com suporte de SSD e produtividade provisionada. 

Ao contrário de um banco de dados em um RDBMS tradicional, um banco de dados do Cosmos DB não tem o escopo definido para um único computador. Com o Cosmos DB, conforme a escala de seu aplicativo precisa ser aumentada, você pode criar mais coleções, bancos de dados ou ambos. Na verdade, vários aplicativos internos da Microsoft utilizam o Azure Cosmos DB na escala do cliente criando bancos de dados do Azure Cosmos DB extremamente grandes, cada um contendo milhares de coleções com terabytes de armazenamento de documentos. É possível expandir ou reduzir um banco de dados adicionando ou removendo coleções para atender os requisitos de escada do seu aplicativo. 

Você pode criar qualquer número de coleções dentro de um banco de dados sujeito à oferta. Cada coleção tem armazenamento com SSD e taxa de transferência provisionada para você, dependendo do nível de desempenho selecionado.

Um banco de dados do Azure Cosmos DB também é um contêiner de usuários. Um usuário, por sua vez, é um namespace lógico para um conjunto de permissões que oferece autorização e acesso refinado a coleções, documentos e anexos.  

Assim como ocorre com outros recursos do modelo de recurso do Azure Cosmos DB, os bancos de dados podem ser criados, substituídos, excluídos, lidos ou enumerados facilmente usando as [APIs REST](/rest/api/documentdb/) ou um dos [SDKs do cliente](documentdb-sdk-dotnet.md). O Azure Cosmos DB garante uma coerência forte para leitura ou consulta dos metadados de um recurso do banco de dados. Excluir um banco de dados automaticamente garante que você não possa acessar qualquer uma das coleções ou usuários contidos nele.   

## <a name="collections"></a>Coleções
Uma coleção do Cosmos DB é um contêiner de seus documentos JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Armazenamento de documentos com suporte de SSD elástico
Uma coleção é intrinsicamente elástica; ela cresce e é reduzida automaticamente conforme você adiciona ou remove documentos. Coleções são recursos lógicos e podem abranger um ou mais servidores ou partições físicas. O número de partições em uma coleção é determinado pelo Cosmos DB com base no tamanho do armazenamento e na produtividade provisionada da coleção. Cada partição no DB Cosmos tem uma quantidade fixa de armazenamento com suporte de SSD associado a ela e é replicada para alta disponibilidade. O gerenciamento de partição é totalmente gerenciado pelo DB Cosmos do Azure e você não precisa escrever um código complexo ou gerenciar suas partições. As coleções do Cosmos DB são **praticamente ilimitadas** em termos de armazenamento e produtividade. 

### <a name="automatic-indexing-of-collections"></a>Indexação automática de coleções
O Azure Cosmos DB é um verdadeiro sistema de banco de dados sem esquemas. Ele não assume nem requer qualquer esquema para os documentos JSON. Conforme você adiciona documentos a uma coleção, o Azure Cosmos DB os indexa automaticamente e eles ficam disponíveis para consulta. A indexação automática de documentos sem a necessidade de esquemas ou de índices secundários é uma funcionalidade-chave do Azure Cosmos DB e é habilitada por técnicas de manutenção de índice otimizada para gravação, livre de bloqueios e estruturada para log. O Azure Cosmos DB dá suporte a um volume sustentado de gravações extremamente rápidas, ao mesmo tempo que oferece consultas consistentes. Ambos os armazenamentos de documentos e de índices são usados para calcular o armazenamento consumido por cada coleção. Você pode controlar os compromissos de armazenamento e desempenho associados à indexação configurando a política de indexação para uma coleção. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurando a política de indexação de uma coleção
A política de indexação de cada coleção permite realizar compromissos de desempenho e armazenamento associados à indexação. As seguintes opções estão disponíveis como parte da configuração de indexação:  

* Escolha se a coleção indexa automaticamente todos os documentos ou não. Por padrão, todos os documentos são indexados automaticamente. Você pode escolher desativar a indexação automática e adicionar seletivamente somente documentos específicos para o índice. Da mesma forma, é possível escolher seletivamente excluir somente documentos específicos. Isso pode ser feito definindo a propriedade automática como verdadeira ou falsa na indexingPolicy de uma coleção e usando o cabeçalho de solicitação [x-ms-indexingdirective] ao inserir, substituir ou excluir um documento.  
* Escolha se deseja incluir ou excluir caminhos ou padrões específicos em seus documentos do índice. Isso pode ser feito definindo includedPaths e excludedPaths na indexingPolicy de uma coleção, respectivamente. Também é possível configurar os compromissos de armazenamento e desempenho para consultas de intervalo e hash para padrões de caminho específicos. 
* Escolha entre atualizações de índice síncronas (consistentes) e assíncronas (lentas). Por padrão, o índice é atualizado sincronamente em cada inserção, substituição ou exclusão de um documento para a coleção. Isso permite que as consultas obedeçam ao mesmo nível de consistência das leituras de documentos. Embora o Azure Cosmos DB seja otimizado para gravação e dê suporte a volumes sustentados de gravações de documentos, junto com a manutenção síncrona de índice e fornecimento de consultas consistentes, você pode configurar determinadas coleções para atualizar seus índices lentamente. A indexação lenta aumenta ainda mais o desempenho de gravação, sendo ideal para cenários de ingestão em massa para coleções basicamente de leitura intensa.

A política de indexação pode ser alterada executando-se um PUT na coleção. Isso pode ser obtido por meio do [SDK de cliente](documentdb-sdk-dotnet.md), do [Portal do Azure](https://portal.azure.com) ou de [APIs REST](/rest/api/documentdb/).

### <a name="querying-a-collection"></a>Consultando uma coleção
Os documentos dentro de uma coleção podem ter esquemas arbitrários e os documentos podem ser consultados dentro de uma coleção sem oferecer qualquer esquema ou índices secundários de início. É possível consultar a coleção usando a [API DocumentDB do Azure Cosmos DB: referência de sintaxe SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx), que fornece operadores hierárquicos, relacionais e espaciais avançados, bem como extensibilidade por meio de UDFs baseadas em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com rótulos como os nós da árvore. Isso é explorado pelas técnicas de indexação automática da API do DocumentDB, bem como pelo dialeto SQL da API do DocumentDB. A linguagem de consulta da API do DocumentDB é formada por três aspectos principais:   

1. Um pequeno conjunto de operações de consulta que é mapeado naturalmente para a estrutura de árvore, incluindo projeções e consultas hierárquicas. 
2. Um subconjunto de operações relacionais, incluindo composição, filtragem, projeções, agregados e junções automáticas. 
3. UDFs puras baseadas em JavaScript que funcionam com (1) e (2).  

O modelo de consulta do Azure Cosmos DB tenta criar um equilíbrio entre funcionalidade, eficiência e simplicidade. O mecanismo de banco de dados do Azure Cosmos DB compila nativamente e executa as instruções de consulta SQL. Você pode consultar uma coleção usando as [APIs REST](/rest/api/documentdb/) ou um dos [SDKs do cliente](documentdb-sdk-dotnet.md). O SDK do .NET é fornecido com o provedor LINQ.

> [!TIP]
> Experimente a API do DocumentDB e execute consultas SQL em nosso conjunto de dados no [Espaço de Consulta](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transações entre vários documentos
As transações do banco de dados oferecem um modelo de programação seguro e previsível para lidar com alterações simultâneas aos dados. No RDBMS, a maneira tradicional de gravar a lógica de negócios é gravar **procedimentos armazenados** e/ou **gatilhos** e enviá-los ao servidor do banco de dados para execução transacional. No RDBMS, o programador do aplicativo precisa lidar com duas linguagens de programação diferentes: 

* A linguagem de programação (não transacional) do aplicativo (como JavaScript, Python, C#, Java etc.)
* T-SQL, a linguagem de programação transacional que é executada nativamente pelo banco de dados

Devido ao seu profundo compromisso com o JavaScript e JSON diretamente no mecanismo de banco de dados, o Azure Cosmos DB oferece um modelo de programação intuitivo para executar a lógica de aplicativos baseados em JavaScript diretamente nas coleções, com relação a procedimentos armazenados e gatilhos. Isso permite o seguinte:

* Implementação eficaz de controle de simultaneidade, recuperação e indexação automática dos gráficos de objeto JSON diretamente no mecanismo do banco de dados
* Expressar naturalmente o fluxo de controle, escopo de variáveis, atribuição e integração de primitivos de manuseio de exceções com transações de bancos de dados diretamente em termos da linguagem de programação JavaScript

A lógica de JavaScript registrada no nível da coleção pode, então, emitir operações do banco de dados nos documentos da coleção determinada. O Azure Cosmos DB encapsula implicitamente os procedimentos de armazenamento baseados em JavaScript e os gatilhos em transações ACID ambientes com isolamento de instantâneos entre documentos de uma coleção. Durante sua execução, se o JavaScript lançar uma exceção, então, toda a transação será abortada. O modelo de programação resultante é bastante simples, porém, poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação “durável”, ao mesmo tempo em que utilizam os construtores de linguagem e primitivas de biblioteca com os quais estão familiarizados.   

A capacidade de executar JavaScript diretamente dentro do mecanismo do banco de dados no mesmo espaço de endereço que o conjunto de buffer permite uma execução de desempenho e transacional das operações do banco de dados em relação aos documentos de uma coleção. Além disso, o mecanismo de banco de dados do Cosmos DB assume um forte compromisso com o JSON e o JavaScript elimina qualquer incompatibilidade de impedância entre os sistemas de tipos do aplicativo e do banco de dados.   

Depois de criar uma coleção, você pode registrar os procedimentos armazenados, disparadores e UDFs com uma coleção usando as [APIs REST](/rest/api/documentdb/) ou qualquer um dos [SDKs de cliente](documentdb-sdk-dotnet.md). Após o registro, você pode fazer referência a eles e executá-los. Considere que o seguinte procedimento armazenado gravado inteiramente em JavaScript, o código a seguir pega dois argumentos (nome do livro e nome do autor) e cria um novo documento, consulta um documento e, então, o atualiza; tudo em uma transação ACID implícita. A qualquer momento durante a execução, se uma exceção de JavaScript for lançada, toda a transação será abortada.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

O cliente pode “enviar” a lógica de JavaScript acima para o banco de dados para execução transacional por meio do HTTP POST. Para obter mais informações sobre como usar métodos HTTP, consulte [Interações RESTful com recursos do Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Observe que, como o banco de dados compreende nativamente JSON e JavaScript, não há incompatibilidade no sistema de tipos, nem é preciso um “mapeamento OR” ou mágica na geração de códigos.   

Os procedimentos armazenados e gatilhos interagem com uma coleção e os documentos em uma coleção por meio de um modelo de objeto bem-definido que expõe o contexto de coleção atual.  

As coleções na API do DocumentDB podem ser criadas, excluídas, lidas ou enumeradas facilmente usando as [APIs REST](/rest/api/documentdb/) ou um dos [SDKs do cliente](documentdb-sdk-dotnet.md). A API do DocumentDB sempre fornece uma coerência forte para leitura ou consulta dos metadados de uma coleção. Excluir uma coleção automaticamente garante que você não possa acessar qualquer documento, anexo, procedimento armazenado, gatilho e UDFs contidos nela.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedimentos armazenados, gatilhos e UDF (Funções Definidas pelo Usuário)
Conforme descrito na seção anterior, você pode gravar a lógica do aplicativo para ser executada diretamente de uma transação no mecanismo de banco de dados. A lógica do aplicativo pode ser gravada inteiramente em JavaScript e pode ser modelada como um procedimento armazenado, um gatilho ou uma UDF. O código de JavaScript em um procedimento armazenado ou um gatilho pode inserir, substituir, excluir, ler ou consultar documentos dentro de uma coleção. Por outro lado, o JavaScript dentro de uma UDF não pode inserir, substituir nem excluir documentos. UDFs enumeram os documentos do conjunto de resultados de uma consulta e geram outro conjunto de resultados. Para multilocação, o Azure Cosmos DB impõe uma rígida governança de recursos baseada em reserva. Cada procedimento armazenado, gatilho ou UDF obtém um quantum fixo de recursos do sistema operacional para realizar esse trabalho. Além disso, os procedimentos armazenados, gatilhos ou UDFs não podem ser vinculados a bibliotecas de JavaScript externas e são incluídos em listas negras se excederem os orçamentos de recursos alocados para eles. Você pode registrar e cancelar o registro de procedimentos armazenados, gatilhos ou UDFs com uma coleção por meio das APIs REST.  Após o registro, um procedimento armazenado, gatilho ou UDF é pré-compilado e armazenado como um código de byte que é executado posteriormente. A seção a seguir ilustra como você pode usar o SDK do JavaScript do Azure Cosmos DB para registrar, executar e cancelar o registro de um procedimento armazenado, um gatilho e uma UDF. O SDK do JavaScript é um wrapper simples das [APIs REST](/rest/api/documentdb/). 

### <a name="registering-a-stored-procedure"></a>Registrando um procedimento armazenado
O registro de um procedimento armazenado cria um novo recurso de procedimento armazenado em uma coleção por meio do HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Executando um procedimento armazenado
A execução de um procedimento armazenado é feita com a emissão de um HTTP POST para um recurso de procedimento armazenado existente, transferindo parâmetros para o procedimento no corpo da solicitação.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Cancelando o registro de um procedimento armazenado
Cancelar o registro de um procedimento armazenado é um processo simples, feito com a emissão de um HTTP DELETE para um recurso do procedimento armazenado existente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrando um pré-gatilho
O registro de um gatilho é realizado ao criar um novo recurso de gatilho em uma coleção por meio do HTTP POST. Você pode especificar se o gatilho é um pré ou pós-gatilho e o tipo de operação à qual ele pode ser associado (p. ex., Criar, Substituir, Excluir ou Tudo).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Executando um pré-gatilho
A execução de um gatilho é realizada especificando o nome de um gatilho existente ao mesmo tempo em que a solicitação POST/PUT/DELETE de um recurso de documento é emitida por meio do cabeçalho de solicitação.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Cancelando o registro de um pré-gatilho
O cancelamento do registro de um gatilho é feito simplesmente ao emitir um comando HTTP DELETE para um recurso de gatilho existente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrando uma UDF
O registro de uma UDF é realizado ao criar um novo recurso de UDF em uma coleção por meio do HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Executando uma UDF como parte da consulta
Uma UDF pode ser especificada como parte da consulta SQL e é usada como uma maneira de estender a [linguagem de consulta SQL principal para a API do DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Cancelando o registro de uma UDF
O cancelamento do registro de uma UDF é feito simplesmente ao emitir um comando HTTP DELETE para um recurso de UDF existente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Embora os trechos acima mostrem o registro (POST), cancelamento do registro (PUT), leitura/lista (GET) e execução (POST) por meio do [SDK do JavaScript](https://github.com/Azure/azure-documentdb-js), você também pode usar as [APIs REST](/rest/api/documentdb/) ou outros [SDKs do cliente](documentdb-sdk-dotnet.md). 

## <a name="documents"></a>Documentos
Você pode inserir, substituir, excluir, ler, enumerar e consultar documentos JSON arbitrários em uma coleção. O Azure Cosmos DB não exige nenhum esquema nem índices secundários para dar suporte à consulta em documentos de uma coleção. O tamanho máximo de um documento é de 2 MB.   

Sendo um serviço de banco de dados verdadeiramente aberto, o Azure Cosmos DB não inverte nenhum tipo de dados especializado (por exemplo, data e hora) nem codificações específicas para documentos JSON. Observe que o Azure Cosmos DB não exige nenhuma convenção JSON especial para codificar as relações entre vários documentos; a sintaxe SQL do Azure Cosmos DB fornece operadores de consulta hierárquica e relacional muito avançados para consultar e projetar documentos sem nenhuma anotação especial nem a necessidade de codificar relações entre documentos usando propriedades diferenciadas.  

Como ocorre com todos os outros recursos, documentos podem ser criados, substituídos, excluídos, lidos, enumerados e consultados facilmente usando APIs REST ou qualquer [SDK cliente](documentdb-sdk-dotnet.md). Excluir um documento libera imediatamente a cota correspondente a todos os anexos aninhados. O nível de consistência de leitura dos documentos segue a política de consistência da conta do banco de dados. Essa política pode ser substituída com base em cada solicitação, dependendo dos requisitos de consistência de dados de seu aplicativo. Ao consultar documentos, a consistência de leitura segue o conjunto do modo de indexação na coleção. Para fins de “consistência”, a política de consistência da conta é seguida. 

## <a name="attachments-and-media"></a>Anexos e mídia
O Azure Cosmos DB permite armazenar blobs binários/mídia no Azure Cosmos DB (máximo de 2 GB por conta) ou em seu próprio repositório de mídia remoto. Ele também permite representar os metadados de uma mídia de acordo com um documento especial chamado anexo. Um anexo do Azure Cosmos DB é um documento especial (JSON) que referencia a mídia ou o blob armazenado em outro local. Um anexo é simplesmente um documento especial que captura os metadados (p. ex., localização, autor etc.) de uma mídia em um armazenamento remoto de mídia. 

Considere um aplicativo de leitura social que usa o Azure Cosmos DB para armazenar anotações de tintas e metadados, incluindo comentários, destaques, favoritos, classificações, preferências/não preferências, etc., associados a um livro eletrônico de determinado usuário.   

* O conteúdo do livro em si é armazenado no armazenamento de mídia disponível como parte da conta de banco de dados do Azure Cosmos DB ou de um repositório de mídia remoto. 
* Um aplicativo poderá armazenar os metadados de cada usuário como um documento distinto, por exemplo, os metadados de Joe para o book1 são armazenados em um documento cuja referência é /colls/joe/docs/book1. 
* Anexos indicando para as páginas de conteúdo de um determinado livro de um usuário são armazenados no documento correspondente, p. ex., /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2, etc. 

Observe que os exemplos listados acima utilizam IDs amigáveis para transmitir a hierarquia de recursos. Os recursos são acessados por meio das APIs REST usando IDs de recurso exclusivos. 

Para a mídia gerenciada pelo Azure Cosmos DB, a propriedade _media do anexo referenciará a mídia por meio de seu URI. O Azure Cosmos DB garantirá que a mídia seja coletada como lixo quando todas as referências pendentes forem removidas. O Azure Cosmos DB gera o anexo automaticamente ao carregar a nova mídia e popula _media para que ela aponte para a mídia recém-adicionada. Se escolher armazenar a mídia em um armazenamento de blob remoto por conta própria (p. ex., OneDrive, Azure Storage, DropBox, etc.), você ainda poderá usar os anexos para fazer referência à mídia. Nesse caso, você criará o anexo por conta própria e preencherá a propriedade its _media.   

Assim como todos os outros recursos, os anexos podem ser criados, substituídos, excluídos, lidos ou enumerados facilmente usando as APIs REST ou qualquer SDK do cliente. Assim como com os documentos, o nível de consistência de leitura dos anexos segue a política de consistência na conta do banco de dados. Essa política pode ser substituída com base em cada solicitação, dependendo dos requisitos de consistência de dados de seu aplicativo. Ao consultar anexos, a consistência de leitura segue o conjunto do modo de indexação na coleção. Para fins de “consistência”, a política de consistência da conta é seguida. 
 

## <a name="users"></a>Usuários
Um usuário do Azure Cosmos DB representa um namespace lógico para agrupar permissões. Um usuário do Azure Cosmos DB pode corresponder a um usuário em um sistema de gerenciamento de identidades ou uma função de aplicativo predefinida. Para o Azure Cosmos DB, um usuário apenas representa uma abstração para agrupar um conjunto de permissões em um banco de dados.   

Para implementar multilocatários no aplicativo, você pode criar usuários no Azure Cosmos DB que correspondem aos seus usuários reais ou aos locatários de seu aplicativo. Você pode, então, criar permissões para um determinado usuário que corresponde ao controle de acesso em várias coleções, documentos, anexos etc.   

Como seus aplicativos precisam ser escalados conforme o crescimento do usuário, você pode adotar várias maneiras de fragmentar seus dados. Você pode modelar seus usuários da seguinte forma:   

* Cada usuário é mapeado para um banco de dados.
* Cada usuário é mapeado para uma coleção. 
* Documentos correspondentes a vários usuários vão para uma coleção dedicada. 
* Documentos correspondentes a vários usuários vão para um conjunto de coleções.   

Independentemente da estratégia de fragmentação específica escolhida, você pode modelar seus usuários reais como usuários no banco de dados do Azure Cosmos DB e associar permissões refinadas a cada usuário.  

![Coleções do usuário][3]  
**Estratégias de fragmentação e modelagem de usuários**

Assim como todos os outros recursos, os usuários do Azure Cosmos DB podem ser criados, substituídos, excluídos, lidos ou enumerados facilmente usando as APIs REST ou um dos SDKs do cliente. O Azure Cosmos DB sempre fornece uma coerência forte para leitura ou consulta dos metadados de um recurso do usuário. Vale destacar que excluir um usuário automaticamente assegura que você não poderá acessar nenhuma das permissões contidas nele. Embora o Azure Cosmos DB recupere a cota das permissões como parte do usuário excluído em segundo plano, as permissões excluídas estão disponíveis imediatamente mais uma vez para uso.  

## <a name="permissions"></a>Permissões
De uma perspectiva do controle de acesso, recursos como as contas do banco de dados, bancos de dados, usuários e permissão são considerados recursos *administrativos*, uma vez que requerem permissões administrativas. Por outro lado, recursos que incluem as coleções, documentos, anexos, procedimentos armazenados, gatilhos e UDFs têm seu escopo definido em um determinado banco de dados e são considerados como *recursos do aplicativo*. Correspondente aos dois tipos de recursos e à funções que os acessam (ou seja, o administrador e o usuário), o modelo de autorização define dois tipos de *chaves de acesso*: *chave mestra* e *chave de recurso*. A chave mestre é uma parte da conta do banco de dados e é fornecida ao desenvolvedor (ou administrador) que está provisionando a conta do banco de dados. Essa chave mestre possui uma semântica do administrador, e ela pode ser usada para autorizar o acesso aos recursos administrativos e do aplicativo. Em contraste, uma chave de recurso é uma chave de acesso granular que permite o acesso a um recurso de aplicativo *específico*. Portanto, ela captura a relação entre o usuário de um banco de dados e as permissões que o usuário possui para um recurso específico (p. ex., coleção, documento, anexo, procedimento armazenado, gatilho ou UDF).   

A única maneira de obter uma chave de recurso é criar um recurso de permissão em um determinado usuário. Observe que, a fim de criar ou recuperar uma permissão, uma chave mestre deve ser apresentada no cabeçalho de autorização. Um recurso de permissão vincula o recurso, seu acesso e o usuário. Após criar um recurso de permissão, o usuário só precisa apresentar a chave de recurso associada para obter acesso ao recurso relevante. Portanto, uma chave de recurso pode ser visualizada como uma representação lógica e compacta do recurso de permissão.  

Assim como todos os outros recursos, as permissões do Azure Cosmos DB podem ser criadas, substituídas, excluídas, lidas ou enumeradas facilmente usando as APIs REST ou um dos SDKs do cliente. O Azure Cosmos DB sempre fornece uma coerência forte para leitura ou consulta dos metadados de uma permissão. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como trabalhar com recursos usando comandos HTTP em [Interações RESTful com recursos do Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

