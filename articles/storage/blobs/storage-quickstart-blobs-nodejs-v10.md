---
title: Carregar, baixar, listar e excluir blobs usando o SDK do Armazenamento do Azure v10 para JavaScript
description: Criar, carregar e excluir blobs e contêineres no Node.js com o Armazenamento do Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: e2deda6bc9a5d13a631e9917f3020cfa68ee1e10
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536167"
---
# <a name="quickstart-upload-download-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascript"></a>Início Rápido: Carregar, baixar, listar e excluir blobs usando o SDK do Armazenamento do Azure v10 para JavaScript

Neste início rápido, você aprenderá a usar o [SDK do Armazenamento do Azure v10 para JavaScript](https://github.com/Azure/azure-sdk-for-js) no Node.js para carregar, baixar, listar e excluir blobs e gerenciar contêineres.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) deste início rápido é um aplicativo de console Node.js básico. Para começar, clone o repositório para seu computador usando o comando a seguir:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Em seguida, altere as pastas para o aplicativo:

```bash
cd azure-storage-js-v10-quickstart
```

Agora, abra a pasta em seu ambiente de edição de código favorito.

## <a name="configure-your-storage-credentials"></a>Configurar suas credenciais de armazenamento

Antes de executar o aplicativo, você deve fornecer as credenciais de segurança para sua conta de armazenamento. O repositório de exemplo inclui um arquivo chamado *.env.example*. Renomeie esse arquivo, removendo a extensão *.example*, resultando em um arquivo chamado *.env*. No arquivo *.env*, adicione os valores de chave de acesso e o nome da conta após as chaves *AZURE_STORAGE_ACCOUNT_NAME* e *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

## <a name="install-required-packages"></a>Instalar os pacotes necessários

No diretório de aplicativo, execute *npm install* para instalar os pacotes necessários para o aplicativo.

```bash
npm install
```

## <a name="run-the-sample"></a>Execute o exemplo
Agora que as dependências estão instaladas, você pode executar o exemplo emitindo o comando a seguir:

```bash
npm start
```

A saída do aplicativo será semelhante ao exemplo a seguir:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```
Se você estiver usando uma nova conta de armazenamento para este início rápido, poderá não ver os nomes de contêiner listados sob o rótulo "*Contêineres*".

## <a name="understanding-the-code"></a>Compreender o código
O exemplo começa com a importação de diversas classes e funções do namespace de Armazenamento de Blobs do Azure. Cada um dos itens importados é discutido no contexto conforme são usados no exemplo.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

As credenciais são lidas de variáveis de ambiente com base no contexto apropriado.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

O módulo *dotenv* carrega variáveis de ambiente ao executar o aplicativo localmente para depuração. Valores são definidos em um arquivo chamado *.env* e carregados no contexto de execução atual. Em produção, a configuração do servidor fornece esses valores, por isso, esse código é executado somente quando o script *não* estiver em execução em um ambiente de "produção".

O próximo bloco de módulos é importado para ajudar a fazer interface com o sistema de arquivos.

```javascript
const fs = require('fs');
const path = require('path');
```

O propósito desses módulos é o seguinte: 

- *fs* é o módulo nativo do Node.js usado para trabalhar com o sistema de arquivos

- *caminho* é necessário para determinar o caminho absoluto do arquivo, que é usado ao carregar um arquivo para o Armazenamento de Blobs

Em seguida, valores de variáveis de ambiente são lidos e separados em constantes.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
O próximo conjunto de constantes ajuda a revelar a intenção de cálculos de tamanho do arquivo durante as operações de upload.
```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```
As solicitações feitas pela API podem ser definidas como o tempo limite após um determinado intervalo. A classe [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-preview) é responsável por gerenciar como as solicitações atingem o tempo limite e a seguinte constante é usada para definir tempos limite usados neste exemplo.
```javascript
const ONE_MINUTE = 60 * 1000;
```
### <a name="calling-code"></a>Código de chamada

Para dar suporte à sintaxe *async/await* do JavaScript, todo o código de chamada é encapsulado em uma função denominada *execute*. Em seguida, *execute* é chamado e tratado como uma promessa.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Todo o código a seguir é executado dentro da função execute em que o comentário `// commands...` está inserido.

Em primeiro lugar, as variáveis relevantes são declaradas para atribuir nomes e conteúdo de exemplo e para apontar para o arquivo local a ser carregado no Armazenamento de Blobs.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

As credenciais da conta são usadas para criar um pipeline responsável por gerenciar como as solicitações são enviadas para a API REST. Pipelines são thread-safe e especificam a lógica para políticas de repetição, registro em log, regras de desserialização de resposta HTTP e muito mais.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```
As classes a seguir são usadas neste bloco de código:

- A classe [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-preview) é responsável por encapsular as credenciais de conta de armazenamento para fornecê-las a um pipeline de solicitação.

- A classe [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-preview) é responsável por criar um novo pipeline.

- [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-preview) modela uma URL usada na API REST. As instâncias dessa classe permitem que você execute ações como listar contêineres e fornecer informações de contexto para gerar URLs de contêiner.

A instância de *ServiceURL* é usada com as instâncias [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-preview) e [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-preview) para gerenciar contêineres e blobs na conta de armazenamento.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```
As variáveis *containerURL* e *blockBlobURL* são reutilizadas em todo o exemplo para atuar na conta de armazenamento. 

Neste ponto, o contêiner não existe na conta de armazenamento. A instância do *ContainerURL* representa uma URL com relação à qual você pode agir. Usando essa instância, você pode criar e excluir o contêiner. O local desse contêiner é igual a um local como este:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

O *blockBlobURL* é usado para gerenciar blobs individuais, permitindo que você carregue, baixe e exclua conteúdo do blob. A URL representada aqui é semelhante a este local:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```
Assim como acontece com o contêiner, o blob de blocos ainda não existe. A variável *blockBlobURL* é usada posteriormente para criar o blob carregando conteúdo.

### <a name="using-the-aborter-class"></a>Usando a classe Aborter

As solicitações feitas pela API podem ser definidas como o tempo limite após um determinado intervalo. A classe *Aborter* é responsável por gerenciar como as solicitações atingiram o tempo limite. O código a seguir cria um contexto em que um conjunto de solicitações recebe 30 minutos para ser executado.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```
Aborters dão a você controle sobre solicitações, permitindo que você:

- designe a quantidade de tempo fornecida para um lote de solicitações
- designe por quanto tempo uma solicitação individual deve ser executada no lote
- cancelar solicitações
- use o membro estático *Aborter.none* para impedir que suas solicitações atinjam o tempo limite todas juntas

### <a name="show-container-names"></a>Mostrar nomes de contêiner
Contas podem armazenar um grande número de contêineres. O código a seguir demonstra como listar os contêineres de uma maneira segmentada, que permite que você percorra um grande número de contêineres. À função *showContainerNames*, são passadas instâncias de *ServiceURL* e *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```
A função *showContainerNames* usa o método *listContainersSegment* para solicitar lotes de nomes de contêiner da conta de armazenamento.
```javascript
async function showContainerNames(aborter, serviceURL) {

    let response;
    let marker;

    do {
        response = await serviceURL.listContainersSegment(aborter, marker);
        marker = response.marker;
        for(let container of response.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```
Quando a resposta é retornada, *containerItems* são iterados para registrar em log o nome no console. 

### <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner, o método *create* de *ContainerURL* é usado.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```
Uma vez que o nome do contêiner é definido chamando *ContainerURL.fromServiceURL (serviceURL, containerName)* , basta chamar o método *create* para criar o contêiner.

### <a name="upload-text"></a>Carregar texto
Para carregar o texto para o blob, use o método *upload*.
```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```
Aqui, o texto e seu comprimento são passados para o método.
### <a name="upload-a-local-file"></a>Carregar um arquivo local
Para carregar um arquivo local para o contêiner, você precisa de uma URL de contêiner e do caminho para o arquivo.
```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```
A função *uploadLocalFile* chama a função *uploadFileToBlockBlob*, que usa o caminho do arquivo e uma instância de destino do blob de blocos como argumentos.
```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```
### <a name="upload-a-stream"></a>Carregar um fluxo
Também há suporte para carregar fluxos. Este exemplo abre um arquivo local como um fluxo para passar para o método de upload.
```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```
A função *uploadStream* chama *uploadStreamToBlockBlob* para carregar o fluxo para o contêiner de armazenamento.
```javascript
async function uploadStream(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```
Durante o upload, *uploadStreamToBlockBlob* alocará buffers aos dados de cache do fluxo caso uma nova tentativa seja necessária. O valor *maxBuffers* designa quantos buffers no máximo serão usados, uma vez que cada buffer cria uma solicitação de upload separada. Idealmente, ter mais buffers equivale a ter velocidades mais altas, mas ao custo de um maior uso de memória. A velocidade de upload atinge um platô quando o número de buffers é alto o suficiente para que o gargalo mude para a rede ou o disco, em vez do cliente.

### <a name="show-blob-names"></a>Mostrar nomes de blob
Assim como contas podem conter muitos contêineres, cada contêiner pode conter uma grande quantidade de blobs. O acesso a cada blob em um contêiner está disponível por meio de uma instância da classe *ContainerURL*. 
```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```
A função *showBlobNames* chama *listBlobFlatSegment* para solicitar lotes de blobs do contêiner.
```javascript
async function showBlobNames(aborter, containerURL) {

    let response;
    let marker;

    do {
        response = await containerURL.listBlobFlatSegment(aborter);
        marker = response.marker;
        for(let blob of response.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```
### <a name="download-a-blob"></a>Baixar um blob
Depois que um blob é criado, você pode baixar o conteúdo usando o método *download*.
```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = downloadResponse.readableStreamBody.read(content.length).toString();
console.log(`Downloaded blob content: "${downloadedContent}"`);
```
A resposta é retornada como um fluxo. Neste exemplo, o fluxo é convertido em uma cadeia de caracteres para fazer logon no console.
### <a name="delete-a-blob"></a>Excluir um blob
O método *delete* de uma instância *BlockBlobURL* exclui um blob do contêiner.
```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```
### <a name="delete-a-container"></a>Excluir um contêiner
O método *delete* de uma instância *ContainerURL* exclui um contêiner da conta de armazenamento.
```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```
## <a name="clean-up-resources"></a>Limpar recursos
Todos os dados gravados para a conta de armazenamento são excluídos automaticamente no final do exemplo de código. 

## <a name="next-steps"></a>Próximas etapas

Este início rápido demonstra como gerenciar blobs e contêineres no Armazenamento de Blobs do Azure usando Node.js. Para saber mais sobre como trabalhar com esse SDK, veja o repositório do GitHub.

> [!div class="nextstepaction"]
> [SDK de Armazenamento do Azure v10 para o repositório JavaScript](https://github.com/Azure/azure-storage-js)
> [Referência de API de JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/storage/client?view=azure-node-preview)
