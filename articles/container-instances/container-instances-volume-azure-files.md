---
title: Monte um volume de Arquivos do Azure em Instâncias de Contêiner do Azure
description: Saiba como montar um volume de Arquivos do Azure para persistir o estado com Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564014"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure

Por padrão, as Instâncias de Contêiner do Azure são sem monitoração de estado. Se o contêiner parar ou falhar, todo o seu estado será perdido. Para persistir o estado além do tempo de vida do contêiner, você deve montar um volume de um repositório externo. Este artigo mostra como montar um compartilhamento de arquivos do Azure com [Arquivos do Azure](../storage/files/storage-files-introduction.md) para ser usado com Instâncias de Contêiner do Azure. Os arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do protocolo SMB padrão do setor. Usar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure fornece recursos de compartilhamento de arquivos semelhantes ao uso de um compartilhamento de arquivos do Azure com máquinas virtuais do Azure.

> [!NOTE]
> Montar um Compartilhamento de Arquivos do Azure está atualmente restrito a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Antes de usar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure, você deve criá-lo. Execute o seguinte script para criar uma conta de armazenamento para hospedar o compartilhamento de arquivos e o próprio compartilhamento. O nome da conta de armazenamento deve ser globalmente exclusivo para que o script adicione um valor aleatório à cadeia de caracteres de base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obter credenciais de armazenamento

Para montar um compartilhamento de arquivos do Azure como um volume nas Instâncias de Contêiner do Azure, você precisa de três valores: o nome da conta de armazenamento, o nome do compartilhamento e a chave de acesso de armazenamento.

Se você tiver usado o script acima, o nome da conta de armazenamento foi armazenado na variável $ACI_PERS_STORAGE_ACCOUNT_NAME. Para ver o nome da conta, digite:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

O nome do compartilhamento já é conhecido (definido como *acishare* no script acima), portanto, falta apenas a chave da conta de armazenamento, que pode ser encontrada usando o seguinte comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Implantar o contêiner e montar o volume

Para montar um compartilhamento de arquivos do Azure como um volume em um contêiner, especifique o compartilhamento e o ponto de montagem do volume ao criar o contêiner com [az container create][az-container-create]. Se você seguiu as etapas anteriores, será possível montar o compartilhamento já criado usando o comando a seguir para criar um contêiner:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

O valor `--dns-name-label` deve ser exclusivo dentro da região do Azure em que você criar a instância do contêiner. Atualize o valor no comando anterior se você receber uma mensagem de erro do **rótulo do nome DNS** ao executar o comando.

## <a name="manage-files-in-mounted-volume"></a>Gerenciar arquivos no volume montado

Depois que o contêiner for iniciado, você pode usar o aplicativo web simples implantado por meio da Microsoft [aci-hellofiles] [ aci-hellofiles] imagem para criar arquivos de texto pequenos no compartilhamento de arquivos do Azure no caminho de montagem especificado. Obtenha o FQDN (nome de domínio totalmente qualificado) do aplicativo Web com o comando [az container show] [ az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Você pode usar o [Portal do Azure][portal] ou uma ferramenta como o [Gerenciador de Armazenamento do Microsoft Azure][storage-explorer] para recuperar e inspecionar o arquivo gravado no compartilhamento de arquivos.

## <a name="mount-multiple-volumes"></a>Montar vários volumes

Para montar vários volumes em uma instância de contêiner, você deve implantar usando um [modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou um arquivo YAML.

Para usar um modelo, forneça os detalhes de compartilhamento e defina os volumes populando a matriz `volumes` na seção `properties` do modelo. Por exemplo, se você criasse dois compartilhamentos de Arquivos do Azure denominados *share1* e *share2* na conta de armazenamento *myStorageAccount*, a matriz `volumes` teria aparência semelhante à seguinte:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar os volumes, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner. Por exemplo, isso monta os dois volumes, *myvolume1* e *myvolume2*, definidos anteriormente:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Para ver um exemplo de implantação de instância de contêiner com um modelo do Azure Resource Manager, consulte [Implantar um grupo de contêineres](container-instances-multi-container-group.md). Para obter um exemplo usando um arquivo YAML, consulte [Implantar um grupo de vários contêineres com YAML](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um volume emptyDir em Instâncias de Contêiner do Azure](container-instances-volume-emptydir.md)
* [Montar um volume gitRepo em Instâncias de Contêiner do Azure](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em Instâncias de Contêiner do Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show