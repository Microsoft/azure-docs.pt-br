---
title: Alterar feed no armazenamento de BLOBs do Azure | Microsoft Docs
description: Saiba mais sobre os logs do feed de alterações no armazenamento de BLOBs do Azure e como usá-los.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 9a439541880cc8e20457edc8d24c5600ba2747c8
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979208"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Suporte ao feed de alterações no armazenamento de BLOBs do Azure

A finalidade do feed de alterações é fornecer logs de transação de todas as alterações que ocorrem nos BLOBs e nos metadados de BLOB em sua conta de armazenamento. O feed de alterações fornece o log **ordenado**, **garantido**, **durável**, **imutável** e **somente leitura** dessas alterações. Os aplicativos cliente podem ler esses logs a qualquer momento, seja no streaming ou no modo de lote. O feed de alterações permite que você crie soluções eficientes e escalonáveis que processam eventos de alteração que ocorrem em sua conta de armazenamento de BLOBs a um custo baixo.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Como o feed de alterações funciona

O feed de alterações é armazenado como [BLOBs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) em um contêiner especial em sua conta de armazenamento em custo de [preço de blob](https://azure.microsoft.com/pricing/details/storage/blobs/) padrão. Você pode controlar o período de retenção desses arquivos com base em seus requisitos (consulte as [condições](#conditions) da versão atual). Os eventos de alteração são anexados ao feed de alterações como registros na especificação de formato [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) : um formato compacto, rápido e binário que fornece estruturas de dados avançadas com esquema embutido. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory.

Você pode processar esses logs de forma assíncrona, incremental ou completa. Qualquer número de aplicativos cliente pode ler de forma independente o feed de alterações, em paralelo e em seu próprio ritmo. Aplicativos de análise, como [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) ou [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) , podem consumir logs diretamente como arquivos Avro, o que permite processá-los a um baixo custo, com alta largura de banda e sem precisar escrever um aplicativo personalizado.

O diagrama a seguir mostra como os registros são adicionados ao feed de alterações:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagrama mostrando como o feed de alterações funciona para fornecer um log ordenado de alterações em BLOBs":::

O suporte ao feed de alterações é bem adequado para cenários que processam dados com base em objetos que foram alterados. Por exemplo, os aplicativos podem:

  - Atualizar um índice secundário e fazer a sincronização com um cache, mecanismo de pesquisa ou outros cenários de gerenciamento de conteúdo.
  
  - Extrair insights e métricas de análise de negócios, com base nas alterações que ocorrem em seus objetos, seja na forma de streaming ou em modo em lote.
  
  - Armazenar, auditar e analisar alterações em seus objetos, em qualquer período, para segurança, conformidade ou inteligência para o gerenciamento de dados corporativos.

  - Crie soluções para fazer backup, espelhar ou replicar o estado do objeto em sua conta para gerenciamento de desastres ou conformidade.

  - Crie pipelines de aplicativo conectados que reajam a eventos de alteração ou agende execuções com base no objeto criado ou alterado.
  
O feed de alterações é um recurso de pré-requisito para a [replicação de objeto](object-replication-overview.md) e a [restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md).

> [!NOTE]
> O feed de alterações fornece um modelo de log durável e ordenado das alterações que ocorrem em um blob. As alterações são gravadas e disponibilizadas no log do feed de alterações em uma ordem de alguns minutos da alteração. Se seu aplicativo tiver que reagir a eventos muito mais rápido do que isso, considere usar [eventos de armazenamento de BLOBs](storage-blob-event-overview.md) em vez disso. Os [eventos de armazenamento de BLOBs](storage-blob-event-overview.md) fornecem eventos one-time em tempo real que permitem que seus Azure Functions ou aplicativos reajam rapidamente às alterações que ocorrem em um blob. 

## <a name="enable-and-disable-the-change-feed"></a>Habilitar e desabilitar o feed de alterações

Você deve habilitar o feed de alterações em sua conta de armazenamento para começar a capturar e gravar as alterações. Desabilite o feed de alterações para parar de capturar as alterações. Você pode habilitar e desabilitar as alterações usando Azure Resource Manager modelos no portal ou no PowerShell.

Aqui estão algumas coisas para ter em mente quando você habilita o feed de alterações.

- Há apenas um feed de alteração para o serviço blob em cada conta de armazenamento e é armazenado no contêiner de **$blobchangefeed** .

- As alterações de criar, atualizar e excluir são capturadas somente no nível de serviço BLOB.

- O feed de alterações captura *todas* as alterações para todos os eventos disponíveis que ocorrem na conta. Os aplicativos cliente podem filtrar os tipos de evento conforme necessário. (Consulte as [condições](#conditions) da versão atual).

- Somente as contas de armazenamento de GPv2 e BLOB podem habilitar o feed de alterações. As contas do Premium BlockBlobStorage e as contas habilitadas para namespace hierárquico não têm suporte no momento. Não há suporte para contas de armazenamento GPv1, mas elas podem ser atualizadas para GPv2 sem tempo de inatividade, consulte [atualizar para uma conta de armazenamento GPv2](../common/storage-account-upgrade.md) para obter mais informações.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Habilite o feed de alterações em sua conta de armazenamento usando portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), selecione a conta de armazenamento.

2. Navegue até a opção **Proteção de dados** em **Serviço do blob**.

3. Clique em **habilitado** no **feed de alterações de blob**.

4. Escolha o botão **salvar** para confirmar as configurações de **proteção de dados** .

    ![Captura de tela que mostra as configurações de proteção de dados.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Habilitar o feed de alterações usando o PowerShell:

1. Instale o PowershellGet mais recente.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Feche e reabra o console do PowerShell.

3. Instale a versão 2.5.0 ou posterior do módulo **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

   ```powershell
   Connect-AzAccount
   ```

5. Habilite o feed de alterações para sua conta de armazenamento.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Modelo](#tab/template)
Use um modelo de Azure Resource Manager para habilitar o feed de alterações em sua conta de armazenamento existente por meio de portal do Azure:

1. Na portal do Azure, escolha **criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **Enter**.

3. Escolha **[implantar um modelo personalizado](https://portal.azure.com/#create/Microsoft.Template)** e, em seguida, escolha **criar seu próprio modelo no editor**.

4. No editor de modelo, Cole o JSON a seguir. Substitua o espaço reservado `<accountName>` pelo nome da sua conta de armazenamento.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Escolha o botão **salvar** , especifique o grupo de recursos da conta e, em seguida, escolha o botão **comprar** para implantar o modelo e habilitar o feed de alterações.

---

## <a name="consume-the-change-feed"></a>Consumir o feed de alterações

O feed de alterações produz vários arquivos de log e metadados. Esses arquivos estão localizados no contêiner de **$blobchangefeed** da conta de armazenamento. 

> [!NOTE]
> Na versão atual, o contêiner $blobchangefeed é visível somente no portal do Azure, mas não visível no Gerenciador de Armazenamento do Azure. No momento, você não pode ver o contêiner $blobchangefeed ao chamar a API ListContainers, mas você pode chamar a API ListBlobs diretamente no contêiner para ver os BLOBs

Os aplicativos cliente podem consumir o feed de alterações usando a biblioteca do processador do feed de alterações de BLOB que é fornecida com o SDK do processador do feed de alterações. 

Consulte [processar logs do feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Entender a organização do feed de alterações

<a id="segment-index"></a>

### <a name="segments"></a>Segmentos

O feed de alterações é um log de alterações que são organizadas em *segmentos* por **hora** , mas acrescentadas e atualizadas a cada poucos minutos. Esses segmentos são criados somente quando há eventos de alteração de BLOB que ocorrem nessa hora. Isso permite que seu aplicativo cliente consuma alterações que ocorrem dentro de intervalos de tempo específicos sem precisar pesquisar em todo o log. Para saber mais, consulte as [especificações](#specifications).

Um segmento por hora disponível do feed de alterações é descrito em um arquivo de manifesto que especifica os caminhos para os arquivos do feed de alterações para esse segmento. A listagem do `$blobchangefeed/idx/segments/` diretório virtual mostra esses segmentos ordenados por tempo. O caminho do segmento descreve o início do intervalo de tempo por hora que o segmento representa. Você pode usar essa lista para filtrar os segmentos de logs que são interessantes para você.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> O `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` é criado automaticamente quando você habilita o feed de alterações. Você pode ignorar esse arquivo com segurança. É um arquivo de inicialização sempre vazio. 

O arquivo de manifesto de segmento ( `meta.json` ) mostra o caminho dos arquivos do feed de alterações para esse segmento na `chunkFilePaths` propriedade. Aqui está um exemplo de um arquivo de manifesto de segmento.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> O `$blobchangefeed` contêiner será exibido somente depois que você habilitar o recurso de feed de alterações em sua conta. Você precisará aguardar alguns minutos depois de habilitar o feed de alterações antes de poder listar os BLOBs no contêiner. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Alterar registros de eventos

Os arquivos do feed de alterações contêm uma série de registros de eventos de alteração. Cada registro de evento de alteração corresponde a uma alteração em um blob individual. Os registros são serializados e gravados no arquivo usando a especificação de formato [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) . Os registros podem ser lidos usando a especificação de formato de arquivo Avro. Há várias bibliotecas disponíveis para processar arquivos nesse formato.

Os arquivos do feed de alterações são armazenados no `$blobchangefeed/log/` diretório virtual como [blobs de acréscimo](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). O primeiro arquivo de feed de alteração em cada caminho terá `00000` o nome do arquivo (por exemplo `00000.avro` ). O nome de cada arquivo de log subsequente adicionado a esse caminho será incrementado em 1 (por exemplo: `00001.avro` ).

Os seguintes tipos de evento são capturados nos registros do feed de alterações:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Aqui está um exemplo de registro de evento de alteração do arquivo do feed de alterações convertido em JSON.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Para obter uma descrição de cada propriedade, consulte [esquema de evento da grade de eventos do Azure para armazenamento de BLOBs](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). Os eventos BlobPropertiesUpdated e BlobSnapshotCreated são atualmente exclusivos para o feed de alterações e ainda não têm suporte para eventos de armazenamento de BLOBs.

> [!NOTE]
> Os arquivos do feed de alterações de um segmento não aparecem imediatamente depois que um segmento é criado. O comprimento do atraso está dentro do intervalo normal de latência de publicação do feed de alterações, que está dentro de alguns minutos da alteração.

<a id="specifications"></a>

## <a name="specifications"></a>Especificações

- Os registros de eventos de alteração são anexados apenas ao feed de alterações. Depois que esses registros são anexados, eles são imutáveis e a posição do registro é estável. Os aplicativos cliente podem manter seu próprio ponto de verificação na posição de leitura do feed de alterações.

- Os registros de eventos de alteração são anexados dentro de uma ordem de alguns minutos da alteração. Os aplicativos cliente podem optar por consumir registros à medida que são acrescentados para acesso de streaming ou em massa em qualquer outro momento.

- Os registros de eventos de alteração são ordenados por ordem de modificação **por blob**. A ordem das alterações entre BLOBs é indefinida no armazenamento de BLOBs do Azure. Todas as alterações em um segmento anterior são anteriores às alterações nos segmentos subsequentes.

- Os registros de eventos de alteração são serializados no arquivo de log usando a especificação de formato [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) .

- Altere os registros de evento em que o `eventType` tem um valor de `Control` são registros internos do sistema e não reflita uma alteração nos objetos em sua conta. Você pode ignorar esses registros com segurança.

- Os valores no `storageDiagnonstics` recipiente de propriedades são somente para uso interno e não são projetados para uso pelo seu aplicativo. Seus aplicativos não devem ter uma dependência contratual desses dados. Você pode ignorar essas propriedades com segurança.

- A hora representada pelo segmento é **aproximada** com limites de 15 minutos. Portanto, para garantir o consumo de todos os registros dentro de um tempo especificado, consuma o segmento de hora anterior e próximo consecutivo.

- Cada segmento pode ter um número diferente de `chunkFilePaths` devido ao particionamento interno do fluxo de log para gerenciar a taxa de transferência de publicação. Os arquivos de log em cada um `chunkFilePath` têm a garantia de conter BLOBs mutuamente exclusivos e podem ser consumidos e processados em paralelo sem violar a ordenação de modificações por blob durante a iteração.

- Os segmentos começam com `Publishing` status. Depois que o acréscimo dos registros ao segmento for concluído, ele será `Finalized` . Os arquivos de log em qualquer segmento que seja datado após a data da `LastConsumable` propriedade no `$blobchangefeed/meta/Segments.json` arquivo não devem ser consumidos pelo seu aplicativo. Veja um exemplo da `LastConsumable` propriedade em um `$blobchangefeed/meta/Segments.json` arquivo:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Condições e problemas conhecidos

Esta seção descreve os problemas e condições conhecidos na versão atual do feed de alterações. 

- Alterar registros de eventos para qualquer alteração única pode aparecer mais de uma vez em seu feed de alterações.
- Você ainda não pode gerenciar o tempo de vida dos arquivos de log do feed de alterações definindo a política de retenção baseada em tempo neles e não pode excluir os BLOBs.
- A `url` Propriedade do arquivo de log está sempre vazia.
- A `LastConsumable` propriedade da segments.jsno arquivo não lista o primeiro segmento que o feed de alterações finaliza. Esse problema ocorre somente depois que o primeiro segmento é finalizado. Todos os segmentos subsequentes após a primeira hora são capturados com precisão na `LastConsumable` propriedade.
- No momento, você não pode ver o contêiner **$blobchangefeed** ao chamar a API ListContainers e o contêiner não aparece em portal do Azure ou Gerenciador de armazenamento. Você pode exibir o conteúdo chamando a API ListBlobs no contêiner $blobchangefeed diretamente.
- As contas de armazenamento que iniciaram previamente um [failover de conta](../common/storage-disaster-recovery-guidance.md) podem ter problemas com o arquivo de log não aparecendo. Qualquer failover de conta futuro também pode afetar o arquivo de log.

## <a name="faq"></a>Perguntas frequentes

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual é a diferença entre o feed de alterações e o log de Análise de Armazenamento?
Os logs de análise têm registros de todas as operações de leitura, gravação, lista e exclusão com solicitações bem-sucedidas e com falha em todas as operações. Os logs de análise são de melhor esforço e nenhuma ordem é garantida.

O feed de alterações é uma solução que fornece log transacional de mutações bem-sucedidas ou alterações na sua conta, como criação de BLOB, modificação e exclusões. O feed de alterações garante que todos os eventos sejam registrados e exibidos na ordem de alterações bem-sucedidas por blob, portanto, você não precisa filtrar o ruído de um grande volume de operações de leitura ou solicitações com falha. O feed de alterações é fundamentalmente projetado e otimizado para o desenvolvimento de aplicativos que exigem determinadas garantias.

### <a name="should-i-use-change-feed-or-storage-events"></a>Devo usar o feed de alterações ou os eventos de armazenamento?
Você pode aproveitar os dois recursos, pois os eventos do feed de alterações e do [armazenamento de BLOBs](storage-blob-event-overview.md) fornecem as mesmas informações com a mesma garantia de confiabilidade de entrega, com a principal diferença de latência, ordenação e armazenamento de registros de eventos. O feed de alterações publica registros no log dentro de alguns minutos após a alteração e também garante a ordem das operações de alteração por blob. Os eventos de armazenamento são enviados em tempo real e podem não ser ordenados. Os eventos de feed de alteração são permanentemente armazenados dentro de sua conta de armazenamento como logs estáveis somente leitura com sua própria retenção definida, enquanto os eventos de armazenamento são transitórios para serem consumidos pelo manipulador de eventos, a menos que você os armazene explicitamente. Com o feed de alterações, qualquer número de aplicativos pode consumir os logs por sua própria conveniência usando APIs de BLOB ou SDKs. 

## <a name="next-steps"></a>Próximas etapas

- Consulte um exemplo de como ler o feed de alterações usando um aplicativo cliente .NET. Consulte [processar logs do feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed-how-to.md).
- Saiba mais sobre como reagir a eventos em tempo real. Consulte [reagindo a eventos de armazenamento de BLOBs](storage-blob-event-overview.md)
- Saiba mais sobre informações detalhadas de log para operações bem-sucedidas e com falha para todas as solicitações. Consulte [registro em log da análise de armazenamento do Azure](../common/storage-analytics-logging.md)