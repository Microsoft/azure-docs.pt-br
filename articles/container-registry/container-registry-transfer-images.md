---
title: Transferir artefatos
description: Transferir coleções de imagens ou outros artefatos de um registro de contêiner para outro registro criando um pipeline de transferência usando contas de armazenamento do Azure
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: 4fe36366011fb790d25419ac46a54c4bf5ad94bf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785811"
---
# <a name="transfer-artifacts-to-another-registry"></a>Transferir artefatos para outro registro

Este artigo mostra como transferir coleções de imagens ou outros artefatos de registro de um registro de contêiner do Azure para outro registro. Os registros de origem e de destino podem estar na mesma assinatura ou em assinaturas diferentes, Active Directory locatários, nuvens do Azure ou nuvens fisicamente desconectadas. 

Para transferir artefatos, você cria um *pipeline de transferência* que Replica artefatos entre dois registros usando o armazenamento de [BLOBs](../storage/blobs/storage-blobs-introduction.md):

* Os artefatos de um registro de origem são exportados para um blob em uma conta de armazenamento de origem 
* O blob é copiado da conta de armazenamento de origem para uma conta de armazenamento de destino
* O blob na conta de armazenamento de destino é importado como artefatos no registro de destino. Você pode configurar o pipeline de importação para disparar sempre que o artefato blob for atualizado no armazenamento de destino.

A transferência é ideal para copiar conteúdo entre dois registros de contêiner do Azure em nuvens fisicamente desconectadas, mediadas por contas de armazenamento em cada nuvem. Se, em vez disso, você quiser copiar imagens de registros de contêiner em nuvens conectadas, incluindo o Hub do Docker e outros fornecedores de nuvem, a [importação de imagem](container-registry-import-images.md) é recomendada.

Neste artigo, você usa Azure Resource Manager implantações de modelo para criar e executar o pipeline de transferência. O CLI do Azure é usado para provisionar os recursos associados, como os segredos de armazenamento. É recomendável CLI do Azure versão 2.2.0 ou posterior. Se você precisar instalar ou atualizar a CLI, confira como [instalar a CLI do Azure][azure-cli].

Esse recurso está disponível na camada de serviço **Premium** do registro de contêiner. Para obter informações sobre os limites e as camadas de serviço do registro, confira [Camadas do Registro de Contêiner do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="prerequisites"></a>Pré-requisitos

* **Registros de contêiner** -você precisa de um registro de origem existente com artefatos para transferência e um registro de destino. A transferência de ACR destina-se à movimentação entre nuvens desconectadas fisicamente. Para teste, os registros de origem e de destino podem estar no mesmo ou em uma assinatura do Azure diferente, Active Directory locatário ou nuvem. 

   Se você precisar criar um registro, consulte [início rápido: criar um registro de contêiner privado usando o CLI do Azure](container-registry-get-started-azure-cli.md). 
* **Contas de armazenamento** – crie contas de armazenamento de origem e de destino em uma assinatura e no local de sua escolha. Para fins de teste, você pode usar a mesma assinatura ou assinaturas que os registros de origem e de destino. Para cenários de nuvem cruzada, normalmente você cria uma conta de armazenamento separada em cada nuvem. 

  Se necessário, crie as contas de armazenamento com o [CLI do Azure](../storage/common/storage-account-create.md?tabs=azure-cli) ou outras ferramentas. 

  Crie um contêiner de BLOB para transferência de artefato em cada conta. Por exemplo, crie um contêiner chamado *transferência*. Dois ou mais pipelines de transferência podem compartilhar a mesma conta de armazenamento, mas devem usar escopos de contêiner de armazenamento diferentes.
* **Cofres de chaves** -cofres de chaves são necessários para armazenar segredos de token SAS usados para acessar contas de armazenamento de origem e de destino. Crie os cofres de chave de origem e de destino na mesma assinatura ou assinaturas do Azure que os registros de origem e de destino. Para fins de demonstração, os modelos e os comandos usados neste artigo também pressupõem que os cofres de chave de origem e destino estão localizados nos mesmos grupos de recursos que os registros de origem e de destino, respectivamente. Esse uso de grupos de recursos comuns não é necessário, mas simplifica os modelos e comandos usados neste artigo.

   Se necessário, crie cofres de chaves com o [CLI do Azure](../key-vault/secrets/quick-create-cli.md) ou outras ferramentas.

* **Variáveis de ambiente** – por exemplo, comandos neste artigo definem as seguintes variáveis de ambiente para os ambientes de origem e de destino. Todos os exemplos são formatados para o shell bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Visão geral do cenário

Você cria os três recursos de pipeline a seguir para transferência de imagem entre registros. Todos são criados usando operações PUT. Esses recursos operam em seus registros de *origem* e de *destino* e contas de armazenamento. 

A autenticação de armazenamento usa tokens SAS, gerenciados como segredos em cofres de chaves. Os pipelines usam identidades gerenciadas para ler os segredos nos cofres.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** – recurso de longa duração que contém informações de alto nível sobre o registro de *origem* e a conta de armazenamento. Essas informações incluem o URI do contêiner de blob de armazenamento de origem e o cofre de chaves que gerenciam o token SAS de origem. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** – recurso de longa duração que contém informações de alto nível sobre o registro de *destino* e a conta de armazenamento. Essas informações incluem o URI do contêiner de blob de armazenamento de destino e o cofre de chaves que gerenciam o token SAS de destino. Um gatilho de importação é habilitado por padrão, portanto, o pipeline é executado automaticamente quando um blob de artefato chega no contêiner de armazenamento de destino. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** -o recurso usado para invocar um recurso ExportPipeline ou ImportPipeline.  
  * Execute o ExportPipeline manualmente criando um recurso PipelineRun e especifique os artefatos a serem exportados.  
  * Se um gatilho de importação estiver habilitado, o ImportPipeline será executado automaticamente. Ele também pode ser executado manualmente usando um PipelineRun. 
  * No momento, no máximo **50 artefatos** podem ser transferidos com cada PipelineRun.

### <a name="things-to-know"></a>Observações importantes
* O ExportPipeline e o ImportPipeline normalmente estarão em locatários Active Directory diferentes associados às nuvens de origem e de destino. Esse cenário requer identidades gerenciadas separadas e cofres de chaves para os recursos de exportação e importação. Para fins de teste, esses recursos podem ser colocados na mesma nuvem, compartilhando identidades.
* Por padrão, os modelos ExportPipeline e ImportPipeline permitem que uma identidade gerenciada atribuída pelo sistema acesse segredos do cofre de chaves. Os modelos ExportPipeline e ImportPipeline também dão suporte a uma identidade atribuída pelo usuário que você fornece. 

## <a name="create-and-store-sas-keys"></a>Criar e armazenar chaves SAS

A transferência usa tokens de SAS (assinatura de acesso compartilhado) para acessar as contas de armazenamento nos ambientes de origem e de destino. Gere e armazene tokens conforme descrito nas seções a seguir.  

### <a name="generate-sas-token-for-export"></a>Gerar token SAS para exportação

Execute o comando [AZ Storage container Generate-SAS][az-storage-container-generate-sas] para gerar um token SAS para o contêiner na conta de armazenamento de origem, usado para exportação de artefato.

*Permissões de token recomendadas*: leitura, gravação, lista, adicionar. 

No exemplo a seguir, a saída do comando é atribuída à variável de ambiente EXPORT_SAS, prefixada com o caractere '? '. Atualize o `--expiry` valor para o seu ambiente:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Armazenar token SAS para exportação

Armazene o token SAS em seu cofre de chaves do Azure de origem usando [AZ keyvault segredo Set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Gerar token SAS para importação

Execute o comando [AZ Storage container Generate-SAS][az-storage-container-generate-sas] para gerar um token SAS para o contêiner na conta de armazenamento de destino, usado para importação de artefato.

*Permissões de token recomendadas*: ler, excluir, listar

No exemplo a seguir, a saída do comando é atribuída à variável de ambiente IMPORT_SAS, prefixada com o caractere '? '. Atualize o `--expiry` valor para o seu ambiente:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Armazenar token SAS para importação

Armazene o token SAS em seu cofre de chaves do Azure de destino usando [AZ keyvault segredo Set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Criar ExportPipeline com o Gerenciador de recursos

Crie um recurso ExportPipeline para o registro de contêiner de origem usando Azure Resource Manager implantação de modelo.

Copie [os arquivos de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) do ExportPipeline Resource Manager para uma pasta local.

Insira os seguintes valores de parâmetro no arquivo `azuredeploy.parameters.json` :

|Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registro de contêiner de origem      |
|exportPipelineName     |  Nome que você escolher para o pipeline de exportação       |
|targetUri     |  URI do contêiner de armazenamento em seu ambiente de origem (o destino do pipeline de exportação).<br/>Exemplo: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Nome do cofre de chaves de origem  |
|sasTokenSecretName  | Nome do segredo do token SAS no cofre da chave de origem <br/>Exemplo: acrexportsas

### <a name="export-options"></a>Opções de exportação

A `options` propriedade para os pipelines de exportação dá suporte a valores Boolianos opcionais. Os seguintes valores são recomendados:

|Parâmetro  |Valor  |
|---------|---------|
|opções | OverwriteBlobs-substituir os blobs de destino existentes<br/>ContinueOnErrors-continue a exportação de artefatos restantes no registro de origem se uma exportação de artefato falhar.

### <a name="create-the-resource"></a>Criar o recurso

Execute o [grupo de implantação AZ Create][az-deployment-group-create] para criar um recurso chamado *exportPipeline* , conforme mostrado nos exemplos a seguir. Por padrão, com a primeira opção, o modelo de exemplo habilita uma identidade atribuída pelo sistema no recurso ExportPipeline. 

Com a segunda opção, você pode fornecer o recurso com uma identidade atribuída pelo usuário. (A criação da identidade atribuída pelo usuário não é mostrada.)

Com qualquer opção, o modelo configura a identidade para acessar o token SAS no cofre de chaves de exportação. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opção 1: criar recurso e habilitar a identidade atribuída pelo sistema

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opção 2: criar recurso e fornecer identidade atribuída pelo usuário

Neste comando, forneça a ID de recurso da identidade atribuída pelo usuário como um parâmetro adicional.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Na saída do comando, anote a ID do recurso ( `id` ) do pipeline. Você pode armazenar esse valor em uma variável de ambiente para uso posterior executando o [grupo de implantação AZ show][az-deployment-group-show]. Por exemplo:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Criar ImportPipeline com o Gerenciador de recursos 

Crie um recurso ImportPipeline no registro de contêiner de destino usando Azure Resource Manager implantação de modelo. Por padrão, o pipeline é habilitado para importar automaticamente quando a conta de armazenamento no ambiente de destino tem um blob de artefato.

Copie [os arquivos de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) do ImportPipeline Resource Manager para uma pasta local.

Insira os seguintes valores de parâmetro no arquivo `azuredeploy.parameters.json` :

Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registro de contêiner de destino      |
|importPipelineName     |  Nome que você escolher para o pipeline de importação       |
|sourceUri     |  URI do contêiner de armazenamento em seu ambiente de destino (a origem do pipeline de importação).<br/>Exemplo: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Nome do cofre de chaves de destino |
|sasTokenSecretName     |  Nome do segredo do token SAS no cofre de chaves de destino<br/>Exemplo: ACR importas |

### <a name="import-options"></a>Opções de importação

A `options` propriedade para o pipeline de importação dá suporte a valores Boolianos opcionais. Os seguintes valores são recomendados:

|Parâmetro  |Valor  |
|---------|---------|
|opções | OverwriteTags-substituir marcas de destino existentes<br/>DeleteSourceBlobOnSuccess-excluir o blob de armazenamento de origem após a importação bem-sucedida para o registro de destino<br/>ContinueOnErrors-continue a importação de artefatos restantes no registro de destino se uma importação de artefato falhar.

### <a name="create-the-resource"></a>Criar o recurso

Execute o [grupo de implantação AZ Create][az-deployment-group-create] para criar um recurso chamado *importPipeline* , conforme mostrado nos exemplos a seguir. Por padrão, com a primeira opção, o modelo de exemplo habilita uma identidade atribuída pelo sistema no recurso ImportPipeline. 

Com a segunda opção, você pode fornecer o recurso com uma identidade atribuída pelo usuário. (A criação da identidade atribuída pelo usuário não é mostrada.)

Com qualquer opção, o modelo configura a identidade para acessar o token SAS no cofre de chaves de importação. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opção 1: criar recurso e habilitar a identidade atribuída pelo sistema

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opção 2: criar recurso e fornecer identidade atribuída pelo usuário

Neste comando, forneça a ID de recurso da identidade atribuída pelo usuário como um parâmetro adicional.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Se você planeja executar a importação manualmente, anote a ID do recurso ( `id` ) do pipeline. Você pode armazenar esse valor em uma variável de ambiente para uso posterior executando o comando [AZ Deployment Group show][az-deployment-group-show] . Por exemplo:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Criar PipelineRun para exportação com o Gerenciador de recursos 

Crie um recurso PipelineRun para o registro de contêiner de origem usando Azure Resource Manager implantação de modelo. Esse recurso executa o recurso ExportPipeline que você criou anteriormente e exporta os artefatos especificados do registro de contêiner como um blob para sua conta de armazenamento de origem.

Copie [os arquivos de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) do PipelineRun Resource Manager para uma pasta local.

Insira os seguintes valores de parâmetro no arquivo `azuredeploy.parameters.json` :

|Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registro de contêiner de origem      |
|pipelineRunName     |  Nome que você escolher para a execução       |
|pipelineResourceId     |  ID de recurso do pipeline de exportação.<br/>Exemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Nome que você escolher para o blob de artefatos exportado para sua conta de armazenamento de origem, como *myblob*
|artefatos | Matriz de artefatos de origem a serem transferidos, como marcas ou resumos de manifesto<br/>Exemplo: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Se reimplantar um recurso PipelineRun com propriedades idênticas, você também deverá usar a propriedade [forceUpdateTag](#redeploy-pipelinerun-resource) .

Execute o [grupo de implantação AZ Create][az-deployment-group-create] para criar o recurso PipelineRun. O exemplo a seguir nomeia o *exportPipelineRun* de implantação.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Para uso posterior, armazene a ID de recurso da execução do pipeline em uma variável de ambiente:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Pode levar vários minutos para que os artefatos exportem. Quando a implantação for concluída com êxito, verifique a exportação de artefatos, listando o blob exportado no contêiner de *transferência* da conta de armazenamento de origem. Por exemplo, execute o comando [AZ Storage blob List][az-storage-blob-list] :

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Transferir BLOB (opcional) 

Use a ferramenta AzCopy ou outros métodos para [transferir dados de blob](../storage/common/storage-use-azcopy-v10.md#transfer-data) da conta de armazenamento de origem para a conta de armazenamento de destino.

Por exemplo, o comando a seguir [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) copia myblob do contêiner de *transferência* na conta de origem para o contêiner de *transferência* na conta de destino. Se o blob existir na conta de destino, ele será substituído. A autenticação usa tokens SAS com permissões apropriadas para os contêineres de origem e de destino. (As etapas para criar tokens não são mostradas.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Disparar recurso ImportPipeline

Se você habilitou o `sourceTriggerStatus` parâmetro de ImportPipeline (o valor padrão), o pipeline será disparado depois que o blob for copiado para a conta de armazenamento de destino. Pode levar vários minutos para que os artefatos sejam importados. Quando a importação for concluída com êxito, verifique a importação de artefatos listando os repositórios no registro de contêiner de destino. Por exemplo, execute [AZ ACR Repository List][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Se você não habilitou o `sourceTriggerStatus` parâmetro do pipeline de importação, execute o recurso ImportPipeline manualmente, conforme mostrado na seção a seguir. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Criar PipelineRun para importação com o Resource Manager (opcional) 
 
Você também pode usar um recurso PipelineRun para disparar um ImportPipeline para importação de artefato para o registro de contêiner de destino.

Copie [os arquivos de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) do PipelineRun Resource Manager para uma pasta local.

Insira os seguintes valores de parâmetro no arquivo `azuredeploy.parameters.json` :

|Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registro de contêiner de destino      |
|pipelineRunName     |  Nome que você escolher para a execução       |
|pipelineResourceId     |  ID de recurso do pipeline de importação.<br/>Exemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Nome do blob existente para artefatos exportados em sua conta de armazenamento, como *myblob*

Se reimplantar um recurso PipelineRun com propriedades idênticas, você também deverá usar a propriedade [forceUpdateTag](#redeploy-pipelinerun-resource) .

Execute o [grupo de implantação AZ Create][az-deployment-group-create] para executar o recurso.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Para uso posterior, armazene a ID de recurso da execução do pipeline em uma variável de ambiente:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Quando a implantação for concluída com êxito, verifique a importação de artefatos listando os repositórios no registro de contêiner de destino. Por exemplo, execute [AZ ACR Repository List][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Reimplantar recurso PipelineRun

Se reimplantar um recurso PipelineRun com *Propriedades idênticas*, você deverá aproveitar a propriedade **forceUpdateTag** . Essa propriedade indica que o recurso PipelineRun deve ser recriado mesmo que a configuração não tenha sido alterada. Verifique se forceUpdateTag é diferente sempre que você reimplantar o recurso PipelineRun. O exemplo a seguir recria um PipelineRun para exportação. O DateTime atual é usado para definir forceUpdateTag, garantindo assim que essa propriedade seja sempre exclusiva.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Excluir recursos de pipeline

Os comandos de exemplo a seguir usam [AZ Resource Delete][az-resource-delete] para excluir os recursos de pipeline criados neste artigo. As IDs de recurso foram armazenadas anteriormente em variáveis de ambiente.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Solução de problemas

* **Falhas de Implantação de modelo ou erros**
  * Se uma execução de pipeline falhar, examine a `pipelineRunErrorMessage` Propriedade do recurso executar.
  * Para erros comuns de implantação de modelo, consulte [solucionar problemas de implantações de modelo do ARM](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemas com a exportação ou importação de blobs de armazenamento**
  * O token SAS pode estar expirado ou pode ter permissões insuficientes para a execução de exportação ou importação especificada
  * O blob de armazenamento existente na conta de armazenamento de origem pode não ser substituído durante várias execuções de exportação. Confirme se a opção OverwriteBlob está definida na execução de exportação e se o token SAS tem permissões suficientes.
  * O blob de armazenamento na conta de armazenamento de destino pode não ser excluído após a execução bem-sucedida da importação. Confirme se a opção DeleteBlobOnSuccess está definida na execução de importação e se o token SAS tem permissões suficientes.
  * O blob de armazenamento não foi criado ou excluído. Confirme se o contêiner especificado na exportação ou na execução de importação existe ou se existe um blob de armazenamento especificado para execução manual de importação. 
* **Problemas de AzCopy**
  * Consulte [solucionar problemas de AzCopy](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Problemas de transferência de artefatos**
  * Nem todos os artefatos, ou nenhum, são transferidos. Confirme a ortografia dos artefatos na execução de exportação e o nome do blob nas execuções de exportação e importação. Confirme que você está transferindo um máximo de 50 artefatos.
  * A execução do pipeline pode não ter sido concluída. Uma execução de exportação ou importação pode levar algum tempo. 
  * Para outros problemas de pipeline, forneça a [ID de correlação](../azure-resource-manager/templates/deployment-history.md) de implantação da execução de exportação ou a execução de importação para a equipe de registro de contêiner do Azure.
* **Problemas ao extrair a imagem em um ambiente isolado fisicamente**
  * Se você vir erros relacionados a camadas estrangeiras ou tentar resolver mcr.microsoft.com ao tentar efetuar pull de uma imagem em um ambiente isolado fisicamente, o manifesto da imagem provavelmente terá camadas não distribuíveis. Devido à natureza de um ambiente isolado fisicamente, essas imagens geralmente falharão ao efetuar pull. Você pode confirmar que esse é o caso verificando o manifesto da imagem em busca de quaisquer referências a registros externos. Se esse for o caso, você precisará enviar as camadas não distribuíveis para o ACR da nuvem pública antes de implantar um pipeline de exportação-executar para essa imagem. Para obter orientação sobre como fazer isso, consulte [como fazer enviar camadas não distribuíveis por push para um registro?](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Próximas etapas

Para importar imagens de contêiner único para um registro de contêiner do Azure de um registro público ou outro registro privado, consulte a referência do comando [AZ ACR Import][az-acr-import] .

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
