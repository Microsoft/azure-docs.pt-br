---
title: Tutorial - Use a biblioteca de cliente do lote do Azure para Node.js | Microsoft Docs
description: Aprenda os conceitos básicos do Lote do Azure e crie uma solução simples usando o Node.js.
services: batch
author: shwetams
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: nodejs
ms.topic: conceptual
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shg
ms.openlocfilehash: a6895773e0109aa0fb643e4fadf8a31ac5b1a33a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323396"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Introdução ao SDK do lote para o Node.js

Aprenda as noções básicas da criação de cliente em lote no Node.js usando [SDK do Node.js do lote do Azure ](/javascript/api/overview/azure/batch). Podemos adotar uma abordagem passo a passo de compressão de um cenário para um aplicativo de lote e, em seguida, configurá-lo usando um cliente do Node.js.  

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tem um conhecimento prático do Node.js e que esteja familiarizado com o Linux. Ele também pressupõe que você tenha uma configuração de conta do Azure com direitos de acesso para criar serviços de armazenamento e de lote.

Recomendamos a leitura da [Visão geral técnica do lote do Azure](batch-technical-overview.md) antes de percorrer as etapas descritas neste artigo.

## <a name="the-tutorial-scenario"></a>O cenário do tutorial
Vamos entender o cenário de fluxo de trabalho em lotes. Temos um script simples escrito em Python que baixa todos os arquivos csv de um contêiner de armazenamento de Blobs do Azure e os converte em JSON. Para processar vários contêineres de conta de armazenamento em paralelo, podemos implantar o script como um trabalho de lote do Azure.

## <a name="azure-batch-architecture"></a>Arquitetura de lote do Azure
O diagrama a seguir mostra como podemos escalar o script de Python usando o lote do Azure e um cliente do Node.js.

![Cenário de lote do Azure](./media/batch-nodejs-get-started/BatchScenario.png)

O cliente do Node.js implanta um trabalho em lote com uma tarefa de preparação (explicado em detalhes mais adiante) e um conjunto de tarefas, dependendo do número de contêineres na conta de armazenamento. Você pode baixar os scripts do repositório do GitHub.

* [Cliente Node.js](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [Shell scripts de tarefa de preparação](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [CSV do Python para o processador JSON](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> O cliente do Node.js no link especificado não contém código específico para ser implantado como um aplicativo de função do Azure. Você pode consultar os links a seguir para obter instruções para criar um.
> - [Como criar um aplicativo de função](../azure-functions/functions-create-first-azure-function.md)
> - [Como criar uma função de gatilho de temporizador](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>Compilar o aplicativo

Agora, vamos seguir o processo passo a passo na criação do cliente Node.js:

### <a name="step-1-install-azure-batch-sdk"></a>Etapa 1: instalar o SDK do Lote do Azure

Você pode instalar o SDK do lote do Azure para Node.js usando o comando npm install.

`npm install azure-batch`

Este comando instala a versão mais recente do SDK do nó de lote do azure.

>[!Tip]
> Em um aplicativo de função do Azure, você pode acessar "Console Kudu" na guia de configurações da função do Azure para executar os comandos npm install. Nesse caso, para instalar o SDK do lote do Azure para Node.js.
>
>

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
O snippet de código a seguir primeiro importa o módulo do Node.js do azure batch e, em seguida, cria um cliente de serviço de lote. Primeiro, você precisa criar um objeto SharedKeyCredentials com a chave de conta de lote copiada na etapa anterior.

```nodejs
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

![URI de lote do Azure](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>Etapa 4: criar um pool do Lote do Azure
Um pool de lote do Azure consiste em várias VMs (também conhecidos como nós de lote). O serviço de lote do Azure implanta as tarefas em nós e os gerencia. Você pode definir os seguintes parâmetros de configuração para o pool.

* Tipo de imagem da máquina virtual
* Tamanho dos nós da máquina virtual
* Número de nós da máquina virtual

> [!Tip]
> O tamanho e o número de nós de máquina Virtual dependem principalmente do número de tarefas que você deseja executar em paralelo e também da própria tarefa. Recomendamos realizar testes para determinar o tamanho e o número ideais.
>
>

O snippet de código a seguir cria a configuração de objetos de parâmetro.

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Para obter a lista de imagens da VM do Linux disponível para o lote do Azure e seus IDs de SKU, confira a [Lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images).
>
>

Quando a configuração do pool é definida, você pode criar o pool de lote do Azure. O comando de pool de lote cria nós de máquina virtual do Azure e os prepara para estar pronto para receber as tarefas a serem executadas. Cada pool deve ter um ID exclusivo para referência em etapas subsequentes.

O snippet de código a seguir cria um pool de lote do Azure.

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Você pode verificar o status do pool criado e garantir o estado seja "ativo" antes de prosseguir com o envio de um trabalho ao pool.

```nodejs
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
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>Etapa 4: enviar um trabalho do Lote do Azure
Um trabalho de lote do Azure é um grupo lógico de tarefas semelhantes. No nosso cenário, é "CSV de processo para JSON". Cada uma dessas tarefas poderia processar arquivos CSV presentes nos contêineres de armazenamento do Azure.

Essas tarefas seriam executadas em paralelo e implantadas em vários nós, orquestrados pelo serviço de lote do Azure.

> [!Tip]
> Você pode usar a propriedade [maxTasksPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) para especificar o número máximo de tarefas que podem ser executadas simultaneamente em um único nó.
>
>

#### <a name="preparation-task"></a>Tarefa de preparação

Os nós da VM criados são nós em branco do Ubuntu. Geralmente, você precisa instalar um conjunto de programas como pré-requisitos.
Normalmente, para nós de Linux, você pode ter um shell script que instala os pré-requisitos antes de executar as tarefas reais. No entanto, pode ser qualquer executável programável.
Neste exemplo, o [shell script](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) instala Python-pip e o SDK de armazenamento do Azure para Python.

Você pode carregar o script em uma conta de armazenamento do Azure e gerar um URI da SAS para acessar o script. Esse processo também pode ser automatizado usando o SDK do Node.js do armazenamento do Azure.

> [!Tip]
> Uma tarefa de preparação de um trabalho é executada apenas em nós de VM em que a tarefa específica precisa ser executada. Se você quiser que a tarefa de pré-requisitos a serem instalados em todos os nós, independentemente das tarefas que são executadas nele, você pode usar a propriedade [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) durante a adição de um pool. Você pode usar a seguinte definição de tarefa de preparação para referência.
>
>

Uma tarefa de preparação é especificada durante o envio do trabalho de lote do Azure. A seguir estão os parâmetros de configuração da tarefa de preparação:

* **ID**: um identificador exclusivo para a tarefa de preparação
* **commandLine**: linha de comando para executar o executável da tarefa
* **resourceFiles**: matriz de objetos que fornece detalhes dos arquivos que devem ser baixados para executar essa tarefa.  Seguem as suas opções
    - blobSource: o URI da SAS do arquivo
    - filePath: o caminho local para baixar e salvar o arquivo
    - fileMode: aplicável somente a nós do Linux, fileMode está em formato octal com um valor padrão de 0770
* **waitForSuccess**: se definido como verdadeiro, a tarefa não é executada quando a tarefa de preparação apresenta falha
* **runElevated**: definir como verdadeiro se for necessário usar privilégios elevados para executar a tarefa.

O snippet de código a seguir mostra um exemplo de configuração do script da tarefa de preparação:

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Se não houver nenhum pré-requisito a ser instalado para executar suas tarefas, você poderá ignorar as tarefas de preparação. O código a seguir cria um trabalho cujo nome de exibição é "arquivos de CSV do processo."

 ```nodejs
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

* nome do contêiner: o contêiner de Armazenamento do qual os arquivos são baixados
* padrão: um parâmetro opcional do padrão de nome do arquivo

Supondo que temos quatro contêineres "con1", "con2", "con3" e "con4", o código a seguir mostra o envio de tarefas ao "CSV do processo" do trabalho de lote do Azure que criamos anteriormente.

```nodejs
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

O código adiciona várias tarefas ao pool. E cada uma das tarefas é executada em um nó no pool de VMs criadas. Se o número de tarefas exceder o número de VMs em um pool ou na propriedade maxTasksPerNode, as tarefas esperam até que um nó seja disponibilizado. O lote do Azure lida com a orquestração automaticamente.

O portal tem exibições detalhadas nas tarefas e nos status do trabalho. Você também pode usar a lista e obter funções no SDK do nó do Azure. Os detalhes são fornecidos no [link](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html) de documentação.

## <a name="next-steps"></a>Próximas etapas

- Examine o artigo [Visão geral dos recursos do Lote do Azure](batch-api-basics.md) , que é recomendável se ainda não estiver familiarizado com o serviço.
- Confira a [referência Node.js em lotes](/javascript/api/overview/azure/batch) para explorar a API do lote.

