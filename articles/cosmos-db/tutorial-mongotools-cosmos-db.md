---
title: Migrar o MongoDB offline para a API do Azure Cosmos DB para MongoDB, usando as ferramentas nativas do MongoDB
description: Saiba como as ferramentas nativas do MongoDB podem ser usadas para migrar pequenos conjuntos de dados de instâncias do MongoDB para o Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101655744"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Tutorial: Migrar o MongoDB offline para a API do Azure Cosmos DB para MongoDB, usando as ferramentas nativas do MongoDB

Use as ferramentas nativas do MongoDB para fazer uma migração offline (única) de bancos de dados de uma instância local ou de nuvem do MongoDB para a API do Azure Cosmos DB para MongoDB.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Escolha a ferramenta nativa do MongoDB apropriada para o seu caso de uso
> * Executar a migração.
> * Monitorar a migração.
> * Verifique se a migração foi bem-sucedida.

Neste tutorial, você vai migrar um conjunto de dados do MongoDB hospedado em uma Máquina Virtual do Azure para a API do Azure Cosmos DB para MongoDB usando as ferramentas nativas do MongoDB. As ferramentas nativas do MongoDB são um conjunto de binários que facilitam a manipulação de dados em uma instância existente do MongoDB. Como o Azure Cosmos DB expõe uma API do Mongo, as ferramentas nativas do MongoDB são capazes de inserir dados no Azure Cosmos DB. O foco deste documento é a migração de dados para fora de uma instância do MongoDB usando *mongoexport/mongoimport* ou *mongodump/mongorestore*. Como as ferramentas nativas se conectam ao MongoDB usando cadeias de conexão, você pode executar as ferramentas em qualquer lugar; no entanto, é recomendável executar essas ferramentas na mesma rede que a instância do MongoDB para evitar problemas de firewall. 

As ferramentas nativas do MongoDB podem mover dados apenas na velocidade que o hardware do host permitir; as ferramentas nativas podem ser a solução mais simples para pequenos conjuntos de dados em que o tempo total de migração não é uma preocupação. O [conector Spark do MongoDB](https://docs.mongodb.com/spark-connector/current/), o [DMS (Serviço de Migração de Dados) do Azure](../dms/tutorial-mongodb-cosmos-db.md) ou o [ADF (Azure Data Factory)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) podem ser melhores alternativas se você precisa de um pipeline de migração escalonável.

Se não houver uma origem do MongoDB configurada, confira o artigo [Instalar e configurar o MongoDB em uma VM do Windows no Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* [Concluir as etapas de pré-migração](../cosmos-db/mongodb-pre-migration.md), tais como estimar a taxa de transferência, escolher uma chave de partição e a política de indexação.
* [Criar uma conta da API do Azure Cosmos DB para MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Faça logon na sua instância do MongoDB
    * [Baixe e instale as ferramentas nativas do MongoDB neste link](https://www.mongodb.com/try/download/database-tools).
        * **Verifique se a versão das ferramentas nativas do MongoDB corresponde à instância existente do MongoDB.**
        * Se a sua instância do MongoDB tiver uma versão diferente da API do Mongo para Azure Cosmos DB, **instale as versões da ferramenta nativa do MongoDB e use a versão de ferramenta apropriada para a API do MongoDB e do Mongo para Azure Cosmos DB, respectivamente.**
    * Adicione um usuário com as permissões do `readWrite`, a menos que já exista um. Posteriormente neste tutorial, forneça esse nome de usuário/senha para as ferramentas *mongoexport* e *mongodump*.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Configurar as repetições do lado do servidor do Azure Cosmos DB Server

Os clientes que migram do MongoDB para o Azure Cosmos DB se beneficiam de funcionalidades de governança de recursos, que garantem a capacidade de utilizar completamente as RU/s de taxa de transferência provisionadas. O Azure Cosmos DB poderá restringir uma determinada solicitação no decorrer da migração se essa solicitação exceder as RU/s provisionadas do contêiner. Em seguida, essa solicitação precisará ser repetida. O tempo da viagem de ida e volta envolvido no salto de rede entre a ferramenta de migração e o Azure Cosmos DB impacta o tempo de resposta geral dessa solicitação. Além disso, as ferramentas nativas do MongoDB podem não administrar novas tentativas. O recurso *Repetição do Lado do Servidor* do Azure Cosmos DB permite que o serviço intercepte os códigos de erro de restrição e tente novamente com um tempo de ida e volta muito menor, aprimorando drasticamente os tempos de resposta da solicitação. Da perspectiva das ferramentas nativas do MongoDB, a necessidade de lidar com novas tentativas é minimizada, o que afetará positivamente a sua experiência durante a migração.

Você pode encontrar a funcionalidade Repetição do Lado do Servidor na folha *Recursos* do portal do Azure Cosmos DB

![Captura de tela do recurso SSR do MongoDB.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Se ele estiver *Desabilitado*, recomendamos habilitá-lo, conforme mostrado abaixo

![Captura de tela da habilitação do SSR do MongoDB.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Escolha a ferramenta nativa MongoDB apropriada

![Diagrama de seleção da melhor ferramenta nativa do MongoDB.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* é o melhor par de ferramentas de migração para migrar um subconjunto do seu banco de dados MongoDB.
    * O *mongoexport* exporta os dados existentes para um arquivo JSON ou CSV legível. O *mongoexport* usa um argumento especificando o subconjunto dos dados existentes a serem exportados. 
    * O *mongoimport* abre um arquivo JSON ou CSV e insere o conteúdo na instância do banco de dados de destino (Azure Cosmos DB, nesse caso). 
    * Observe que JSON e CSV não são formatos de compactação. Você pode incorrer em excesso de encargos de rede, pois o *mongoimport* envia dados para o Azure Cosmos DB.
* O *mongodump/mongorestore* é o melhor par de ferramentas de migração para migrar todo o banco de dados MongoDB. O formato BSON compacto fará uso mais eficiente dos recursos de rede à medida que os dados são inseridos no Azure Cosmos DB.
    * O *mongodump* exporta os dados existentes como um arquivo BSON.
    * O *mongorestore* importa o despejo do arquivo BSON no Azure Cosmos DB.
* Entretanto, se você simplesmente tiver um pequeno arquivo JSON que deseja importar para a API do Mongo para Azure Cosmos DB, a ferramenta *mongoimport* será uma solução rápida para ingerir os dados.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Coletar as credenciais da API Mongo para Azure Cosmos DB

A API do Mongo para Azure Cosmos DB fornece credenciais de acesso compatíveis que podem ser utilizadas pelas ferramentas nativas do MongoDB. Você precisará ter essas credenciais de acesso disponíveis para migrar dados para a API do Mongo para Azure Cosmos DB. Para localizar essas credenciais:

1. Abrir o portal do Azure
1. Navegue até a conta da API do Mongo para Azure Cosmos DB
1. No painel de navegação à esquerda, selecione a folha *Cadeia de Conexão* e você verá uma exibição semelhante à seguinte:

    ![Captura de tela das credenciais do Azure Cosmos DB.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *HOST* – As funções do ponto de extremidade do Azure Cosmos DB como um nome de host do MongoDB
    * *PORT* – Quando as ferramentas nativas do MongoDB se conectam ao Azure Cosmos DB, você precisa especificar essa porta explicitamente
    * *USERNAME* – O prefixo do nome de domínio do ponto de extremidade do Azure Cosmos DB funciona como o nome de usuário do MongoDB
    * *PASSWORD* – A chave mestra do Azure Cosmos DB funciona como a senha do MongoDB
    * Além disso, observe o campo *SSL* que é `true`. A ferramenta nativa do MongoDB **precisa** habilitar o SSL ao gravar dados no Azure Cosmos DB

## <a name="perform-the-migration"></a>Realizar a migração

1. Escolha quais bancos de dados e coleções você deseja migrar. Neste exemplo, estamos migrando a coleção de *consultas* no banco de dados *edx* do MongoDB para o Azure Cosmos DB.

O restante desta seção orientará você a como usar o do par de ferramentas que você selecionou na seção anterior.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Para exportar os dados da instância do MongoDB de origem, abra um terminal no computador da instância do MongoDB. Se ele for um computador Linux, digite

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    No Windows, o executável será `mongoexport.exe`. *HOST*, *PORT*, *USERNAME* e *PASSWORD* devem ser preenchidos com base nas propriedades da sua instância de banco de dados MongoDB existente. 
    
    Você também pode optar por exportar apenas um subconjunto do conjunto de dados do MongoDB. Um modo de fazer isso é adicionando um argumento de filtro adicional:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Somente os documentos que correspondem ao filtro `{"field1":"value1"}` serão exportados.

    Depois de executar a chamada, você verá que um arquivo `edx.json` é produzido:

    ![Captura de tela da chamada mongoexport.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Você pode usar o mesmo terminal para importar o `edx.json` no Azure Cosmos DB. Se você estiver executando o `mongoimport` em um computador Linux, digite

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    No Windows, o executável será `mongoimport.exe`. *HOST*, *PORT*, *USERNAME* e *PASSWORD* devem ser preenchidos com base nas credenciais do Azure Cosmos DB coletadas anteriormente. 
1. **Monitore** a saída do terminal de *mongoimport*. Você verá que ele imprime linhas de texto para o terminal que contém atualizações no status de migração:

    ![Captura de tela da chamada mongoimport.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Por fim, examine o Azure Cosmos DB para **validar** se a migração foi bem-sucedida. Abra o portal do Azure Cosmos DB e navegue até o Data Explorer. Você verá (1) que um banco de dados *edx* com uma coleção *importedQuery* foi criado e (2) se você exportou apenas um subconjunto de dados, *importedQuery* deve conter *apenas* documentos que correspondem ao subconjunto desejado dos dados. No seguinte exemplo, apenas um documento corresponde ao filtro `{"field1":"value1"}`:

    ![Captura de tela da verificação de dados do Cosmos DB.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Para criar um despejo de dados BSON da sua instância do MongoDB, abra um terminal no computador da instância do MongoDB. Se ele for um computador Linux, digite

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *HOST*, *PORT*, *USERNAME* e *PASSWORD* devem ser preenchidos com base nas propriedades da sua instância de banco de dados MongoDB existente. Você verá que um diretório `edx-dump` é produzido e que a estrutura de diretório de `edx-dump` reproduz a hierarquia de recursos (estrutura de banco de dados e de coleção) da sua instância do MongoDB de origem. Cada coleção é representada por um arquivo BSON:

    ![Captura de tela da chamada mongodump.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Você pode usar o mesmo terminal para restaurar o conteúdo de `edx-dump` no Azure Cosmos DB. Se você estiver executando o `mongorestore` em um computador Linux, digite

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    No Windows, o executável será `mongorestore.exe`. *HOST*, *PORT*, *USERNAME* e *PASSWORD* devem ser preenchidos com base nas credenciais do Azure Cosmos DB coletadas anteriormente. 
1. **Monitore** a saída do terminal de *mongorestore*. Você verá que ele imprime linhas na atualização do terminal no status de migração:

    ![Captura de tela da chamada mongorestore.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Por fim, examine o Azure Cosmos DB para **validar** se a migração foi bem-sucedida. Abra o portal do Azure Cosmos DB e navegue até o Data Explorer. Você verá (1) que um banco de dados *edx* com uma coleção *importedQuery* foi criado e (2) *importedQuery* deve conter o conjunto de dados *inteiro* da coleção de origem:

    ![Captura de tela da verificação dos dados de mongorestore do Cosmos DB.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Otimização após a migração

Depois de migrar os dados armazenados no banco de dados do MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar ao Azure Cosmos DB e gerenciar os dados. Você também pode executar outras etapas de otimização pós-migração, como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para sua conta do Azure Cosmos DB. Para obter mais informações, confira o artigo [Otimização pós-migração](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Informações sobre o serviço do Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)
* [Documentação de ferramentas do banco de dados MongoDB](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Próximas etapas

* Revisar as diretrizes de migração para saber sobre mais cenários no [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/) da Microsoft.