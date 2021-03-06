---
title: Usar a biblioteca de clientes do Lote do Azure para JavaScript
description: Aprenda os conceitos básicos do Lote do Azure e crie uma solução simples usando o JavaScript.
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 1f53c793e4c676df319d46f30595ac330846dfa5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230783"
---
# <a name="get-started-with-batch-sdk-for-javascript"></a>Introdução ao SDK do Lote para JavaScript

Aprenda os conceitos básicos da criação de um cliente do Lote em JavaScript usando o [SDK do Lote do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/). Adotaremos uma abordagem passo a passo para a compressão de um cenário referente a um aplicativo de lote e a configuração dele com o JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha um conhecimento prático de JavaScript e esteja familiarizado com o Linux. Ele também pressupõe que você tenha uma configuração de conta do Azure com direitos de acesso para criar serviços de armazenamento e de lote.

Recomendamos a leitura da [Visão geral técnica do lote do Azure](batch-technical-overview.md) antes de percorrer as etapas descritas neste artigo.

## <a name="understand-the-scenario"></a>Compreender o cenário

Aqui temos um script simples escrito em Python que baixa todos os arquivos csv de um contêiner do Armazenamento de blobs do Azure e os converte em JSON. Para processar vários contêineres de conta de armazenamento em paralelo, podemos implantar o script como um trabalho de lote do Azure.

## <a name="azure-batch-architecture"></a>Arquitetura do Lote do Azure

O diagrama a seguir mostra como podemos escalar o script Python usando o Lote do Azure e um cliente.

![Diagrama mostrando a arquitetura do cenário.](./media/batch-js-get-started/batch-scenario.png)

O exemplo de JavaScript implanta um trabalho em lotes com uma tarefa de preparação (explicado em detalhes mais adiante) e um conjunto de tarefas, dependendo do número de contêineres na conta de armazenamento. Você pode baixar os scripts do repositório do GitHub.

- [Código de exemplo](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Shell scripts de tarefa de preparação](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [CSV do Python para o processador JSON](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> O exemplo de JavaScript no link especificado não contém um código específico para implantação como um aplicativo de funções do Azure. Você pode consultar os links a seguir para obter instruções para criar um.
> - [Como criar um aplicativo de função](../azure-functions/functions-get-started.md)
> - [Como criar uma função de gatilho de temporizador](../azure-functions/functions-bindings-timer.md)

## <a name="build-the-application"></a>Compilar o aplicativo

Agora, vamos seguir o processo passo a passo da criação do cliente JavaScript:

### <a name="step-1-install-azure-batch-sdk"></a>Etapa 1: instalar o SDK do Lote do Azure

Você pode instalar o SDK do Lote do Azure para JavaScript usando o comando npm install.

`npm install azure-batch`

Esse comando instala a última versão do SDK do azure-batch para JavaScript.

>[!Tip]
> Em um aplicativo de função do Azure, você pode acessar "Console Kudu" na guia de configurações da função do Azure para executar os comandos npm install. Nesse caso, para instalar o SDK do Lote do Azure para JavaScript.

### <a name="step-2-create-an-azure-batch-account"></a>Etapa 2: Criar uma Conta de Lote do Azure

Você pode criá-lo no [portal do Azure](batch-account-create-portal.md) ou na linha de comando ([Powershell](batch-powershell-cmdlets-get-started.md) /[CLI do Azure](/cli/azure)).

A seguir estão os comandos para criar um através da CLI do Azure.

Crie um grupo de recursos, ignore esta etapa se já tiver um onde você deseja criar a conta de lote:

`az group create -n "<resource-group-name>" -l "<location>"`

Em seguida, crie uma conta de lote do Azure.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Cada conta de lote tem suas chaves de acesso correspondentes. Essas chaves são necessárias para criar mais recursos na conta de lote do Azure. É uma boa prática para o ambiente de produção usar o Azure Key Vault para armazenar essas chaves. Em seguida, você pode criar uma entidade de serviço para o aplicativo. Usando essa entidade de serviço, o aplicativo pode criar um token OAuth para chaves de acesso do cofre de chaves.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Copie e armazene a chave a ser usada nas etapas subsequentes.

### <a name="step-3-create-an-azure-batch-service-client"></a>Etapa 3: criar um cliente de serviço do Lote do Azure

O snippet de código a seguir primeiro importa o módulo do azure-batch para JavaScript e cria um cliente do Serviço de Lote. Primeiro, você precisa criar um objeto SharedKeyCredentials com a chave de conta de lote copiada na etapa anterior.

```javascript
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

O URI do lote do Azure pode ser encontrado na guia Visão geral do portal do Azure. Ele está no formato:

`https://accountname.location.batch.azure.com`

Confira a captura de tela:

![URI de lote do Azure](./media/batch-js-get-started/batch-uri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>Etapa 4: criar um pool do Lote do Azure

Um pool de lote do Azure consiste em várias VMs (também conhecidos como nós de lote). O serviço de lote do Azure implanta as tarefas em nós e os gerencia. Você pode definir os seguintes parâmetros de configuração para o pool.

- Tipo de imagem da máquina virtual
- Tamanho dos nós da máquina virtual
- Número de nós da máquina virtual

> [!TIP]
> O tamanho e o número de nós de máquina Virtual dependem principalmente do número de tarefas que você deseja executar em paralelo e também da própria tarefa. Recomendamos realizar testes para determinar o tamanho e o número ideais.

O snippet de código a seguir cria a configuração de objetos de parâmetro.

```javascript
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> Para obter a lista de imagens da VM do Linux disponível para o lote do Azure e seus IDs de SKU, confira a [Lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images).

Quando a configuração do pool é definida, você pode criar o pool de lote do Azure. O comando de pool de lote cria nós de máquina virtual do Azure e os prepara para estar pronto para receber as tarefas a serem executadas. Cada pool deve ter um ID exclusivo para referência em etapas subsequentes.

O snippet de código a seguir cria um pool de lote do Azure.

```javascript
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Você pode verificar o status do pool criado e garantir o estado seja "ativo" antes de prosseguir com o envio de um trabalho ao pool.

```javascript
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

A seguir temos um objeto de resultado de exemplo retornado pela função pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>Etapa 4: enviar um trabalho do Lote do Azure

Um trabalho de lote do Azure é um grupo lógico de tarefas semelhantes. No nosso cenário, é "CSV de processo para JSON". Cada uma dessas tarefas poderia processar arquivos CSV presentes nos contêineres de armazenamento do Azure.

Essas tarefas seriam executadas em paralelo e implantadas em vários nós, orquestrados pelo serviço de lote do Azure.

> [!TIP]
> Você pode usar a propriedade [taskSlotsPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) para especificar o número máximo de tarefas que podem ser executadas simultaneamente em um único nó.

#### <a name="preparation-task"></a>Tarefa de preparação

Os nós da VM criados são nós em branco do Ubuntu. Geralmente, você precisa instalar um conjunto de programas como pré-requisitos.
Normalmente, para nós de Linux, você pode ter um shell script que instala os pré-requisitos antes de executar as tarefas reais. No entanto, pode ser qualquer executável programável.

Neste exemplo, o [shell script](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) instala Python-pip e o SDK de armazenamento do Azure para Python.

Você pode carregar o script em uma conta de armazenamento do Azure e gerar um URI da SAS para acessar o script. Esse processo também pode ser automatizado com o SDK do Armazenamento do Azure para JavaScript.

> [!TIP]
> Uma tarefa de preparação de um trabalho é executada apenas em nós de VM em que a tarefa específica precisa ser executada. Se você quiser que a tarefa de pré-requisitos a serem instalados em todos os nós, independentemente das tarefas que são executadas nele, você pode usar a propriedade [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) durante a adição de um pool. Você pode usar a seguinte definição de tarefa de preparação para referência.

Uma tarefa de preparação é especificada durante o envio do trabalho de lote do Azure. A seguir estão os parâmetros de configuração da tarefa de preparação:

- **ID**: um identificador exclusivo para a tarefa de preparação
- **commandLine**: linha de comando para executar o executável da tarefa
- **resourceFiles**: matriz de objetos que fornece detalhes dos arquivos que devem ser baixados para executar essa tarefa.  Seguem as suas opções
  - blobSource: o URI da SAS do arquivo
  - filePath: o caminho local para baixar e salvar o arquivo
  - fileMode: aplicável somente a nós do Linux, fileMode está em formato octal com um valor padrão de 0770
- **waitForSuccess**: se definido como verdadeiro, a tarefa não é executada quando a tarefa de preparação apresenta falha
- **runElevated**: definir como verdadeiro se for necessário usar privilégios elevados para executar a tarefa.

O snippet de código a seguir mostra um exemplo de configuração do script da tarefa de preparação:

```javascript
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Se não houver nenhum pré-requisito a ser instalado para executar suas tarefas, você poderá ignorar as tarefas de preparação. O código a seguir cria um trabalho cujo nome de exibição é "arquivos de CSV do processo."

 ```javascript
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Etapa 5: enviar tarefas do Lote do Azure para um trabalho

Depois de criar o trabalho de CSV do processo, vamos criar as tarefas para esse trabalho. Supondo que temos quatro contêineres, precisamos criar quatro tarefas, uma para cada contêiner.

Ao observar o [script Python](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py), percebemos que ele aceita dois parâmetros:

- nome do contêiner: o contêiner de Armazenamento do qual os arquivos são baixados
- padrão: um parâmetro opcional do padrão de nome do arquivo

Supondo que temos quatro contêineres "con1", "con2", "con3" e "con4", o código a seguir mostra o envio de tarefas ao "CSV do processo" do trabalho de lote do Azure que criamos anteriormente.

```javascript
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

O código adiciona várias tarefas ao pool. E cada uma das tarefas é executada em um nó no pool de VMs criadas. Se o número de tarefas exceder o número de VMs em um pool ou na propriedade taskSlotsPerNode, as tarefas aguardarão até que um nó seja disponibilizado. O lote do Azure lida com a orquestração automaticamente.

O portal tem exibições detalhadas nas tarefas e nos status do trabalho. Use também a lista e obtenha funções no SDK do Azure para JavaScript. Os detalhes são fornecidos no [link](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html) de documentação.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Confira a [referência do Lote para JavaScript](/javascript/api/overview/azure/batch) para explorar a API do Lote.