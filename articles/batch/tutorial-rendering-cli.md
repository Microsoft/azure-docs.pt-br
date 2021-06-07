---
title: Tutorial – Renderizar uma cena na nuvem
description: Saiba como renderizar uma cena do Autodesk 3DS Max com Arnold usando o Serviço de Renderização do Lote e a CLI do Azure
ms.topic: tutorial
ms.date: 12/30/2020
ms.custom: mvc, devx-track-azurecli
ROBOTS: NOINDEX
ms.openlocfilehash: dc55ca15c74bd71365e514424757df3d37cec0f6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768159"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Tutorial: Renderizar uma cena com o Lote do Azure

O Lote do Azure fornece recursos de renderização em escala de nuvem com pagamento baseado no uso. O Lote do Azure oferece suporte a aplicativos de renderização incluindo Autodesk Maya, 3ds Max, Arnold, e V-Ray. Este tutorial mostra as etapas para renderizar uma cena pequena com o Lote usando a CLI do Azure. Você aprenderá como:

> [!div class="checklist"]
> - Carregar uma cena no armazenamento do Azure
> - Criar um pool do Lote para renderização
> - Renderizar uma cena de quadro único
> - Dimensionar o pool e renderizar uma cena com vários quadros
> - Baixar a saída renderizada

Neste tutorial, você renderiza uma cena 3ds Max com o Lote usando o renderizador de rastreamento de raio [Arnold](https://www.autodesk.com/products/arnold/overview). O pool do Lote usa uma imagem do Azure Marketplace com gráficos pré-instalados e aplicativos de renderização que fornecem o licenciamento com pagamento por uso.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa de uma assinatura pré-paga ou de outra opção de compra do Azure para usar aplicativos de renderização no Lote e pagar por uso. **O licenciamento de pagamento por uso não terá suporte se você usar uma oferta gratuita do Azure que forneça crédito monetário.**

- A cena do 3ds Max para este tutorial está no [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene), junto com um exemplo de script de Bash e arquivos de configuração JSON. A cena do 3ds Max é dos [arquivos de exemplo do Autodesk 3ds Max](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Os arquivos de exemplo do Autodesk 3ds Max estão disponíveis em uma licença Creative Commons Attribution-NonCommercial-Share Alike. Copyright &copy; Autodesk, Inc.)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este tutorial requer a versão 2.0.20 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

> [!TIP]
> Veja os [modelos de trabalho do Arnold](https://github.com/Azure/batch-extension-templates/tree/master/templates/arnold/render-windows-frames) no repositório GitHub de Modelos de Extensão do Lote do Azure.

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Se você ainda não fez isso, crie um grupo de recursos, uma conta do Lote e uma conta de armazenamento vinculada em sua assinatura.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus2*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus2
```

Crie uma conta de Armazenamento do Azure em seu grupo de recursos com o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). Para este tutorial, você pode usar a conta de armazenamento para armazenar uma cena do 3ds Max de entrada e a saída renderizada.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Criar uma conta do Lote com o comando [az batch account create](/cli/azure/batch/account#az_batch_account_create). O exemplo a seguir cria uma conta do Lote denominada *mybatchaccount* em *myResourceGroup* e vincula a conta de armazenamento que você criou.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Para criar e gerenciar trabalhos e pools de computação, você precisa autenticar com o Lote. Faça logon na conta com o comando [az batch account login](/cli/azure/batch/account#az_batch_account_login). Depois que você fizer logon, seus comandos `az batch` usarão esse contexto de conta. O exemplo a seguir usa a autenticação de chave compartilhada, com base no nome da conta e na chave do Lote. O Lote também dá suporte à autenticação por meio do [Azure Active Directory](batch-aad-auth.md) para autenticar usuários individuais ou um aplicativo autônomo.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="upload-a-scene-to-storage"></a>Carregar uma cena para armazenamento

Para carregar a cena de entrada no armazenamento, você primeiro precisa acessar a conta de armazenamento e criar um contêiner de destino para os blobs. Para acessar a conta de armazenamento do Azure, exporte as variáveis de ambiente `AZURE_STORAGE_KEY` e `AZURE_STORAGE_ACCOUNT`. O primeiro comando do shell Bash usa o comando [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) para obter a primeira chave de conta. Depois de definir essas variáveis de ambiente, os comandos de armazenamento usarão esse contexto de conta.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Agora, crie um contêiner de blob na conta de armazenamento para os arquivos da cena. O exemplo a seguir usa o comando [az storage container create](/cli/azure/storage/container#az_storage_container_create) para criar um contêiner de blob denominado *scenefiles*, que permite acesso de leitura público.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Baixe a cena `MotionBlur-Dragon-Flying.max` do [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) para um diretório de trabalho local. Por exemplo: 

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Carregue o arquivo da cena do diretório de trabalho local para o contêiner de blob. O exemplo a seguir usa o comando [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch), que pode carregar vários arquivos:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Criar um pool de renderização

Criar um pool do Lote para renderização usando o comando [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create). Neste exemplo, você deve especificar as configurações do pool em um arquivo JSON. No shell atual, crie um nome de arquivo *mypool.json* e copie e cole o conteúdo a seguir. Verifique se todo o texto foi copiado corretamente. (Você pode baixar o arquivo do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```

O Lote dá suporte a nós dedicados e a [nós de baixa prioridade](batch-low-pri-vms.md), e você pode usar um ou ambos em seus pools. Nós dedicados são reservados para o pool. Nós de baixa prioridade são oferecidos a um preço menor do excedente de capacidade da VM no Azure. Nós de baixa prioridade ficam indisponíveis quando o Azure não tem capacidade suficiente.

O pool especificado contém um único nó de baixa prioridade executando uma imagem do Windows Server com o software para o Serviço de Renderização do Lote. Esse pool é licenciado para renderizar com 3ds Max e Arnold. Em uma etapa posterior, você dimensiona o pool para um número maior de nós.

Se você ainda não tiver entrado em sua conta do Lote, use o comando [az batch account login](/cli/azure/batch/account#az_batch_account_login) para fazer isso. Depois, crie o pool passando o arquivo JSON para o comando `az batch pool create`:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
```

Leva alguns minutos para provisionar o pool. Para ver o status do pool, execute o comando [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show). O seguinte comando obtém o estado de alocação do pool:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Continue as etapas a seguir para criar um trabalho e tarefas enquanto o estado do pool está sendo alterado. O pool é provisionado completamente quando o estado de alocação é `steady` e os nós estão sendo executados.  

## <a name="create-a-blob-container-for-output"></a>Criar um contêiner de blob para saída

Nos exemplos neste tutorial, todas as tarefas no trabalho de renderização criam um arquivo de saída. Antes de agendar o trabalho, crie um contêiner de blob em sua conta de armazenamento como o destino dos arquivos de saída. O exemplo a seguir usa o comando [az storage container create](/cli/azure/storage/container#az_storage_container_create) para criar o contêiner *job-myrenderjob* com acesso de leitura público.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Para gravar arquivos de saída no contêiner, o Lote precisa usar um token SAS (assinatura de acesso compartilhado). Crie o token com o comando [az storage account generate-sas](/cli/azure/storage/account#az_storage_account_generate_sas). Este exemplo cria um token para gravar em qualquer contêiner de blobs na conta. O token expira em 15 de novembro de 2021:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2021-11-15
```

Anote o token retornado pelo comando, que é mais ou menos assim. Você usará esse token em uma etapa posterior.

`se=2021-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

## <a name="render-a-single-frame-scene"></a>Renderizar uma cena de quadro único

### <a name="create-a-job"></a>Criar um trabalho

Crie um trabalho de renderização para execução no pool usando o comando [az batch job create](/cli/azure/batch/job#az_batch_job_create). Inicialmente, o trabalho não tem nenhuma tarefa.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Cria uma tarefa

Use o comando [az batch task create](/cli/azure/batch/task#az_batch_task_create) para criar uma tarefa de renderização no trabalho. Neste exemplo, você deve especificar as configurações da tarefa em um arquivo JSON. No shell atual, crie um arquivo chamado *myrendertask.json* e copie e cole o conteúdo a seguir. Verifique se todo o texto foi copiado corretamente. (Você pode baixar o arquivo do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).)

A tarefa especifica um comando do 3ds Max para renderizar um único quadro da cena *MotionBlur DragonFlying.max*.

Modifique os elementos `blobSource` e `containerURL` no arquivo JSON para que eles incluam o nome da sua conta de armazenamento e seu token SAS. 

> [!TIP]
> A `containerURL` termina com o token SAS e é semelhante a: `https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Adicione a tarefa ao trabalho com o seguinte comando:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

O Lote agenda a tarefa e ela é executada assim que um nó no pool fica disponível.

### <a name="view-task-output"></a>Exibir saída da tarefa

A tarefa leva alguns minutos para ser executada. Use o comando [az batch task show](/cli/azure/batch/task#az_batch_task_show) para exibir os detalhes sobre a tarefa.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

A tarefa gera *dragon0001.jpg* no nó de computação e o carrega no contêiner *job-myrenderjob* na conta de armazenamento. Para exibir a saída, baixe o arquivo do armazenamento para o computador local usando o comando [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Abra *dragon.jpg* em seu computador. A imagem renderizada é semelhante à seguinte:

![Quadro de dragão renderizado 1](./media/tutorial-rendering-cli/dragon-frame.png) 

## <a name="scale-the-pool"></a>Dimensionar o pool

Agora, modifique o pool para se preparar para um trabalho de renderização maior, com vários quadros. O Lote fornece várias maneiras para dimensionar os recursos de computação, incluindo [dimensionamento automático](batch-automatic-scaling.md), que adiciona ou remove nós, de acordo com as alterações na demanda. Para este exemplo básico, use o comando [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) para aumentar o número de nós de baixa prioridade no pool para *6*:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

O pool leva alguns minutos para ser redimensionado. Durante esse processo, configure as tarefas seguintes para execução no trabalho de renderização existente.

## <a name="render-a-multiframe-scene"></a>Renderizar uma cena com vários quadros

Como no exemplo de quadro único, use o comando [az batch task create](/cli/azure/batch/task#az_batch_task_create) para criar tarefas de renderização do trabalho chamado *myrenderjob*. Aqui, especifique as configurações da tarefa em um arquivo JSON chamado *myrendertask_multi.json*. (Você pode baixar o arquivo do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json).) Cada uma das seis tarefas especifica uma linha de comando Arnold para renderizar um quadro da cena 3ds Max *MotionBlur DragonFlying.max*.

Crie um arquivo no shell atual denominado *myrendertask_multi.json* e copie e cole o conteúdo do arquivo baixado. Modifique os elementos `blobSource` e `containerURL` no arquivo JSON para incluir o nome da sua conta de armazenamento e seu token SAS. Não deixe de alterar as configurações para cada uma das seis tarefas. Salve o arquivo e execute o seguinte comando para enfileirar as tarefas:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Exibir saída da tarefa

A tarefa leva alguns minutos para ser executada. Use o comando [az batch task list](/cli/azure/batch/task#az_batch_task_list) para exibir o estado das tarefas. Por exemplo: 

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Use o comando [az batch task show](/cli/azure/batch/task#az_batch_task_show) para exibir os detalhes de tarefas específicas. Por exemplo: 

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```

As tarefas geram arquivos de saída denominados *dragon0002.jpg* - *dragon0007.jpg* nos nós de computação e os carregam no contêiner *job-myrenderjob* na sua conta de armazenamento. Para exibir a saída, baixe os arquivos para uma pasta no seu computador local usando o comando [az storage blob download-batch](/cli/azure/storage/blob). Por exemplo: 

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Abra um dos arquivos em seu computador. O quadro renderizado 6 é semelhante ao seguinte:

![Quadro de dragão renderizado 6](./media/tutorial-rendering-cli/dragon-frame6.png) 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, conta do Lote, pools e os recursos relacionados. Exclua os recursos da seguinte maneira:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> - Carregar cenas no armazenamento do Azure
> - Criar um pool do Lote para renderização
> - Renderizar uma cena de quadro único com Arnold
> - Dimensionar o pool e renderizar uma cena com vários quadros
> - Baixar a saída renderizada

Para saber mais sobre a renderização de escala de nuvem, confira a documentação de renderização do Lote.

> [!div class="nextstepaction"]
> [Serviço de Renderização em Lotes](batch-rendering-service.md)
