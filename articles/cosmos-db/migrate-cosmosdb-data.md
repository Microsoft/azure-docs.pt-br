---
title: Migrar centenas de terabytes de dados para o Azure Cosmos DB
description: Este documento descreve como você pode migrar centenas de terabytes de dados para Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: b24ea79737c9e1f64abb7f62807352dbd9573695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018064"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrar centenas de terabytes de dados para o Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB pode armazenar terabytes de dados. Você pode executar uma migração de dados em larga escala para mover sua carga de trabalho de produção para o Azure Cosmos DB. Este artigo descreve os desafios envolvidos na movimentação de dados em grande escala para o Azure Cosmos DB e apresenta a ferramenta que ajuda a enfrentá-los e migra os dados para o Azure Cosmos DB. Nesse estudo de caso, o cliente usou a API do SQL do Cosmos DB.  

Antes de migrar toda a carga de trabalho para Azure Cosmos DB, você pode migrar um subconjunto de dados para validar alguns dos aspectos, como escolha de chave de partição, desempenho de consulta e modelagem de dados. Depois de validar a prova de conceito, você pode mover toda a carga de trabalho para Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Ferramentas para migração de dados 

Atualmente, as estratégias de migração de Azure Cosmos DB são diferentes com base na escolha da API e no tamanho dos dados. Para migrar conjuntos de dados menores – para validar a modelagem, o desempenho da consulta, a opção de chave de partição etc. – você pode escolher a [ferramenta de migração de dados](import-data.md) ou o [conector de Azure Cosmos DB do Azure data Factory](../data-factory/connector-azure-cosmos-db.md). Se você estiver familiarizado com o Spark, também poderá optar por usar o [conector Azure Cosmos DB Spark](spark-connector.md) para migrar dados.

## <a name="challenges-for-large-scale-migrations"></a>Desafios para migrações em larga escala 

As ferramentas existentes para migrar dados para Azure Cosmos DB têm algumas limitações que se tornam especialmente aparentes em grandes escalas:

 * **Recursos limitados de scale out**: para migrar terabytes de dados para Azure Cosmos DB o mais rápido possível, e para consumir efetivamente a taxa de transferência provisionada inteira, os clientes de migração devem ter a capacidade de escalar horizontalmente indefinidamente.  

* **Falta de rastreamento de progresso e ponto de verificação**: é importante acompanhar o progresso da migração e ter o ponto de verificação durante a migração de grandes conjuntos de dados. Caso contrário, qualquer erro que ocorra durante a migração interromperá a migração e você precisará iniciar o processo do zero. Não seria produtivo reiniciar todo o processo de migração quando 99% de ti já tiver sido concluído.  

* **Falta de fila de mensagens mortas**: em conjuntos de dados grandes, em alguns casos, pode haver problemas com partes dos dados de origem. Além disso, pode haver problemas transitórios com o cliente ou a rede. Qualquer um desses casos não deve fazer com que toda a migração falhe. Embora a maioria das ferramentas de migração tenha recursos robustos de repetição que protegem contra problemas intermitentes, nem sempre é suficiente. Por exemplo, se menos de 0, 1% dos documentos de dados de origem forem maiores que 2 MB de tamanho, isso fará com que a gravação do documento falhe em Azure Cosmos DB. Idealmente, é útil que a ferramenta de migração persista esses documentos ' com falha ' para outra fila de mensagens mortas, que pode ser processada após a migração. 

Muitas dessas limitações estão sendo corrigidas para ferramentas como o Azure data Factory, serviços de migração de dados do Azure. 

## <a name="custom-tool-with-bulk-executor-library"></a>Ferramenta personalizada com biblioteca de executor em massa 

Os desafios descritos na seção acima podem ser resolvidos usando uma ferramenta personalizada que pode ser facilmente dimensionada em várias instâncias e é resiliente a falhas transitórias. Além disso, a ferramenta personalizada pode pausar e retomar a migração em vários pontos de verificação. O Azure Cosmos DB já fornece a [biblioteca de executores em massa](./bulk-executor-overview.md) que incorpora alguns desses recursos. Por exemplo, a biblioteca de executores em massa já tem a funcionalidade para lidar com erros transitórios e pode expandir os threads em um único nó para consumir cerca de 500 K RUs por nó. A biblioteca de executores em massa também particiona o conjunto de fonte de origem em micro lotes que são operados de forma independente como uma forma de ponto de verificação.  

A ferramenta personalizada usa a biblioteca de executores em massa e dá suporte à expansão em vários clientes e para rastrear erros durante o processo de ingestão. Para usar essa ferramenta, os dados de origem devem ser particionados em arquivos distintos no Azure Data Lake Storage (ADLS) para que os diferentes operadores de migração possam pegar cada arquivo e ingerir em Azure Cosmos DB. A ferramenta personalizada usa uma coleção separada, que armazena metadados sobre o progresso da migração para cada arquivo de origem individual no ADLS e rastreia os erros associados a eles.  

A imagem a seguir descreve o processo de migração usando essa ferramenta personalizada. A ferramenta está em execução em um conjunto de máquinas virtuais, e cada máquina virtual consulta a coleção de rastreamento em Azure Cosmos DB para adquirir uma concessão em uma das partições de dados de origem. Quando isso for feito, a partição de dados de origem será lida pela ferramenta e ingerida no Azure Cosmos DB usando a biblioteca de executor em massa. Em seguida, a coleção de rastreamento é atualizada para registrar o progresso da ingestão de dados e todos os erros encontrados. Depois que uma partição de dados é processada, a ferramenta tenta consultar a próxima partição de origem disponível. Ele continua processando a próxima partição de origem até que todos os dados sejam migrados. O código-fonte da ferramenta está disponível no repositório de [ingestão em Azure Cosmos DB em massa](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion) .  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Instalação da ferramenta de migração" border="false":::
 

 

A coleção de rastreamento contém documentos, conforme mostrado no exemplo a seguir. Você verá esses documentos como um para cada partição nos dados de origem.  Cada documento contém os metadados para a partição de dados de origem, como seu local, status de migração e erros (se houver):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Pré-requisitos para a migração de dados 

Antes de iniciar a migração de dados, há alguns pré-requisitos a serem considerados:  

#### <a name="estimate-the-data-size"></a>Estimar o tamanho dos dados:  

O tamanho dos dados de origem pode não ser exatamente mapeado para o tamanho dos dados em Azure Cosmos DB. Alguns documentos de exemplo da fonte podem ser inseridos para verificar o tamanho dos dados em Azure Cosmos DB. Dependendo do tamanho do documento de exemplo, o tamanho total dos dados em Azure Cosmos DB após a migração poderá ser estimado. 

Por exemplo, se cada documento após a migração no Azure Cosmos DB estiver em cerca de 1 KB e se houver cerca de 60.000.000.000 documentos no conjunto de fonte de origem, isso significaria que o tamanho estimado em Azure Cosmos DB seria próximo de 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Pré-criar contêineres com RUs suficiente: 

Embora Azure Cosmos DB Escale horizontalmente o armazenamento automaticamente, não é aconselhável iniciar a partir do menor tamanho de contêiner. Contêineres menores têm disponibilidade de taxa de transferência mais baixa, o que significa que a migração levará muito mais tempo para ser concluída. Em vez disso, é útil criar os contêineres com o tamanho final dos dados (conforme estimado na etapa anterior) e garantir que a carga de trabalho de migração esteja totalmente consumindo a taxa de transferência provisionada.  

Na etapa anterior. como o tamanho dos dados foi estimado em cerca de 60 TB, um contêiner de pelo menos 2,4 M RUs é necessário para acomodar todo o conjunto.  

 

#### <a name="estimate-the-migration-speed"></a>Estimar a velocidade de migração: 

Supondo que a carga de trabalho de migração possa consumir toda a taxa de transferência provisionada, o provisionamento em si forneceria uma estimativa da velocidade de migração. Continuando o exemplo anterior, 5 RUs são necessárias para escrever um documento de 1 KB para Azure Cosmos DB conta da API do SQL.  2,4 milhões RUs permitiria uma transferência de 480.000 documentos por segundo (ou 480 MB/s). Isso significa que a migração completa de 60 TB levará 125.000 segundos ou cerca de 34 horas.  

Caso você queira que a migração seja concluída dentro de um dia, você deve aumentar a taxa de transferência provisionada para 5 milhões RUs. 

 

#### <a name="turn-off-the-indexing"></a>Desative a indexação:  

Como a migração deve ser concluída assim que possível, é aconselhável minimizar o tempo e o RUs gasto na criação de índices para cada um dos documentos ingeridos.  Azure Cosmos DB indexa automaticamente todas as propriedades, vale a pena minimizar a indexação para alguns termos selecionados ou desativá-la completamente para o curso de migração. Você pode desativar a política de indexação do contêiner alterando o indexingMode para nenhum, conforme mostrado abaixo:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Depois que a migração for concluída, você poderá atualizar a indexação.  

## <a name="migration-process"></a>Processo de migração 

Depois que os pré-requisitos forem concluídos, você poderá migrar dados com as seguintes etapas:  

1. Primeiro, importe os dados da origem para o armazenamento de BLOBs do Azure. Para aumentar a velocidade da migração, é útil paralelizar entre partições de origem distintas. Antes de iniciar a migração, o conjunto de dados de origem deve ser particionado em arquivos com tamanho em tamanho de 200 MB.   

2. A biblioteca de executores em massa pode escalar verticalmente para consumir 500.000 RUs em uma única VM de cliente. Como a taxa de transferência disponível é 5 milhões RUs, as 10 VMs Ubuntu 16, 4 (Standard_D32_v3) devem ser provisionadas na mesma região em que o banco de dados Cosmos do Azure está localizado. Você deve preparar essas VMs com a ferramenta de migração e seu arquivo de configurações.  

3. Execute a etapa de fila em uma das máquinas virtuais do cliente. Esta etapa cria a coleção de rastreamento, que examina o contêiner ADLS e cria um documento de acompanhamento de progresso para cada um dos arquivos de partição do conjunto de dados de origem.  

4. Em seguida, execute a etapa de importação em todas as VMs do cliente. Cada um dos clientes pode assumir a propriedade em uma partição de origem e ingerir seus dados em Azure Cosmos DB. Depois que ele for concluído e seu status for atualizado na coleção de rastreamento, os clientes poderão consultar a próxima partição de origem disponível na coleção de rastreamento.  

5. Esse processo continua até que todo o conjunto de partições de origem tenha sido ingerido. Depois que todas as partições de origem são processadas, a ferramenta deve ser executada novamente no modo de correção de erro na mesma coleção de controle. Essa etapa é necessária para identificar as partições de origem que devem ser processadas novamente devido a erros.  

6. Alguns desses erros podem ser devido a documentos incorretos nos dados de origem. Eles devem ser identificados e corrigidos. Em seguida, você deve executar novamente a etapa de importação nas partições com falha para ingerir. 

Quando a migração for concluída, você poderá validar que a contagem de documentos em Azure Cosmos DB é igual à contagem de documentos no banco de dados de origem. Neste exemplo, o tamanho total em Azure Cosmos DB foi desativado para 65 terabytes. Após a migração, a indexação pode ser ativada seletivamente e o RUs pode ser reduzido para o nível exigido pelas operações da carga de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais experimentando os aplicativos de exemplo que consomem a biblioteca de executores em massa no [.net](bulk-executor-dot-net.md) e no [Java](bulk-executor-java.md). 
* A biblioteca de executores em massa é integrada ao conector do Cosmos DB Spark, para saber mais, consulte o artigo [Azure Cosmos DB conector do Spark](spark-connector.md) .  
* Entre em contato com a equipe de produto Azure Cosmos DB abrindo um tíquete de suporte no tipo de problema "consultoria geral" e no subtipo de problema "migrações grandes (TB +)" para obter ajuda adicional com migrações em larga escala.