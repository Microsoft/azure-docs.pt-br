---
title: Montar o volume de arquivos do Azure para o grupo de contêineres
description: Saiba como montar um volume de Arquivos do Azure para persistir o estado com Instâncias de Contêiner do Azure
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d52ad8ad02735c98b29a83d8ca69cdea8c6af7d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97954967"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure

Por padrão, as Instâncias de Contêiner do Azure não têm estado. Se o contêiner for reiniciado, falhar ou parar, todo o seu estado será perdido. Para persistir o estado além do tempo de vida do contêiner, é necessário montar um volume em um repositório externo. Conforme mostrado neste artigo, as instâncias de contêiner do Azure podem montar um compartilhamento de arquivos do Azure criado com [os arquivos do Azure](../storage/files/storage-files-introduction.md). Os arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados hospedados no armazenamento do Azure que são acessíveis por meio do protocolo SMB (servidor padrão do setor). Usar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure fornece recursos de compartilhamento de arquivos semelhantes ao uso de um compartilhamento de arquivos do Azure com máquinas virtuais do Azure.

> [!NOTE]
> Montar um Compartilhamento de Arquivos do Azure está atualmente restrito a contêineres do Linux. Encontre as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).
>
> A montagem de um compartilhamento de arquivos do Azure em uma instância de contêiner é semelhante a uma [montagem de ligação](https://docs.docker.com/storage/bind-mounts/)do Docker. Lembre-se de que se você montar um compartilhamento em um diretório de contêiner no qual os arquivos ou diretórios existem, esses arquivos ou diretórios serão obscurecidos pela montagem e não estarão acessíveis enquanto o contêiner for executado.
>

> [!IMPORTANT]
> Se você estiver implantando grupos de contêineres em uma rede virtual do Azure, deverá adicionar um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) à sua conta de armazenamento do Azure.

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivo do Azure

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obter credenciais de armazenamento

Para montar um compartilhamento de arquivos do Azure como um volume nas Instâncias de Contêiner do Azure, você precisa de três valores: o nome da conta de armazenamento, o nome do compartilhamento e a chave de acesso de armazenamento.

* **Nome da conta de armazenamento** – se você usou o script anterior, o nome da conta de armazenamento foi armazenado na `$ACI_PERS_STORAGE_ACCOUNT_NAME` variável. Para ver o nome da conta, digite:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Nome do compartilhamento** -esse valor já é conhecido (definido como `acishare` no script anterior)

* **Chave de conta de armazenamento** -esse valor pode ser encontrado usando o seguinte comando:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Implantar contêiner e montar volume-CLI

Para montar um compartilhamento de arquivos do Azure como um volume em um contêiner usando o CLI do Azure, especifique o compartilhamento e o ponto de montagem do volume ao criar o contêiner com [AZ container Create][az-container-create]. Se você seguiu as etapas anteriores, pode montar o compartilhamento criado anteriormente usando o seguinte comando para criar um contêiner:

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

O `--dns-name-label` valor deve ser exclusivo na região do Azure em que você cria a instância de contêiner. Atualize o valor no comando anterior se você receber uma mensagem de erro do **rótulo do nome DNS** ao executar o comando.

## <a name="manage-files-in-mounted-volume"></a>Gerenciar arquivos no volume montado

Depois que o contêiner for iniciado, você poderá usar o aplicativo Web simples implantado por meio da imagem do Microsoft [ACI-hellofiles][aci-hellofiles] para criar arquivos de texto pequenos no compartilhamento de arquivos do Azure no caminho de montagem especificado. Obtenha o FQDN (nome de domínio totalmente qualificado) do aplicativo Web com o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Depois de salvar o texto usando o aplicativo, você pode usar o [portal do Azure][portal] ou uma ferramenta como a [Gerenciador de armazenamento do Microsoft Azure][storage-explorer] para recuperar e inspecionar o arquivo ou os arquivos gravados no compartilhamento de arquivos.

## <a name="deploy-container-and-mount-volume---yaml"></a>Implantar contêiner e montar volume-YAML

Você também pode implantar um grupo de contêineres e montar um volume em um contêiner com o CLI do Azure e um [modelo YAML](container-instances-multi-container-yaml.md). A implantação do modelo YAML é um método preferencial ao implantar grupos de contêineres que consistem em vários contêineres.

O modelo YAML a seguir define um grupo de contêineres com um contêiner criado com a `aci-hellofiles` imagem. O contêiner monta o compartilhamento de arquivos do Azure *acishare* criado anteriormente como um volume. Quando indicado, insira o nome e a chave de armazenamento para a conta de armazenamento que hospeda o compartilhamento de arquivos. 

Como no exemplo da CLI, o `dnsNameLabel` valor deve ser exclusivo na região do Azure onde você cria a instância de contêiner. Atualize o valor no arquivo YAML, se necessário.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Para implantar com o modelo YAML, salve o YAML anterior em um arquivo nomeado `deploy-aci.yaml` e, em seguida, execute o comando [az container create][az-container-create] com o parâmetro `--file`:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Implantar contêiner e montar volume – Gerenciador de recursos

Além da implantação da CLI e do YAML, você pode implantar um grupo de contêineres e montar um volume em um contêiner usando um modelo do Azure [Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, popule a matriz `volumes` na seção `properties` do grupo de contêineres do modelo. 

Em seguida, para cada contêiner no qual você deseja montar o volume, preencha a `volumeMounts` matriz na `properties` seção da definição do contêiner.

O modelo do Resource Manager a seguir define um grupo de contêineres com um contêiner criado com a `aci-hellofiles` imagem. O contêiner monta o compartilhamento de arquivos do Azure *acishare* criado anteriormente como um volume. Quando indicado, insira o nome e a chave de armazenamento para a conta de armazenamento que hospeda o compartilhamento de arquivos. 

Como nos exemplos anteriores, o `dnsNameLabel` valor deve ser exclusivo na região do Azure em que você cria a instância de contêiner. Atualize o valor no modelo, se necessário.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Para implantar com o modelo do Resource Manager, salve o JSON anterior em um arquivo chamado `deploy-aci.json` e execute o comando [AZ Deployment Group Create][az-deployment-group-create] com o `--template-file` parâmetro:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montar vários volumes

Para montar vários volumes em uma instância de contêiner, você deve implantar usando um [modelo de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), um arquivo YAML ou outro método programático. Para usar um modelo ou arquivo YAML, forneça os detalhes do compartilhamento e defina os volumes preenchendo a `volumes` matriz na `properties` seção do arquivo. 

Por exemplo, se você tiver criado dois compartilhamentos de arquivos do Azure denominados *share1* e *share2* na conta de armazenamento *myStorageAccount*, a `volumes` matriz em um modelo do Resource Manager será semelhante ao seguinte:

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
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
