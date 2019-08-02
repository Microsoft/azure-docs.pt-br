---
title: Implantar instâncias do contêiner do Azure habilitada no GPU
description: Saiba como implantar instâncias de contêiner do Azure para executar nos recursos GPU.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 300e9b82d578663a4d2ada3889a07d8b03051cc5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325943"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Implantar instâncias de contêiner que usam recursos GPU

Para executar determinadas cargas de trabalho de computação intensiva em Instâncias de Contêiner do Azure, implante seus [grupos de contêineres](container-instances-container-groups.md) com *recursos GPU*. As instâncias de contêiner no grupo podem acessar um ou mais GPUs NVIDIA Tesla ao executar cargas de trabalho de contêiner, como CUDA e aplicativos de aprendizado profundo.

Este artigo mostra como adicionar recursos de GPU ao implantar um grupo de contêineres usando um [arquivo YAML](container-instances-multi-container-yaml.md) ou um [modelo do Resource Manager](container-instances-multi-container-group.md). Você também pode especificar recursos de GPU ao implantar uma instância de contêiner usando o portal do Azure.

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

Na visualização, as seguintes limitações se aplicam ao usar recursos GPU em grupos de contêineres. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

O suporte será adicionado para regiões adicionais ao longo do tempo.

**Tipos de sistema operacional com suporte**: Apenas Linux

**Limitações adicionais**: Recursos GPU não podem ser usados durante a implantação de um grupo de contêineres em uma [rede virtual](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Sobre os recursos de GPU

### <a name="count-and-sku"></a>Contagem e SKU

Para usar GPUs em uma instância de contêiner, especifique um *recurso GPU* com as seguintes informações:

* **Contagem** -o número de GPUs: **1**, **2**, ou **4**.
* **SKU** - SKU de GPU: **K80**, **P100** ou **V100**. Cada SKU é mapeado para o GPU de NVIDIA Tesla em uma das seguintes famílias VM habilitada para GPU do Azure:

  | SKU | Família da VM |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Ao implantar recursos de GPU, defina os recursos de CPU e memória apropriados para a carga de trabalho, até os valores máximos mostrados na tabela anterior. Esses valores são atualmente maiores do que os recursos de CPU e memória disponíveis em grupos de contêineres sem recursos de GPU.  

### <a name="things-to-know"></a>Observações importantes

* **Tempo de implantação** - criação de um grupo de contêineres que contém recursos GPU que leva até **8 a 10 minutos**. Isso é devido ao tempo adicional para provisionar e configurar uma VM de GPU no Azure. 

* **Preços** – semelhante aos grupos de contêiner sem recursos GPU, as cobranças do Azure para recursos consumidos ao longo da *duração* de um grupo de contêiner com recursos GPU. A duração é calculada desde o momento para efetuar pull de sua primeira imagem do contêiner até que o grupo de contêineres termina. Não inclui o tempo de implantação do grupo de contêiner.

  Consulte [detalhes de preço](https://azure.microsoft.com/pricing/details/container-instances/).

* **Drivers CUDA** - instâncias de contêiner com os recursos GPU são previamente provisionadas com drivers NVIDIA CUDA e tempos de execução do contêiner, portanto, você pode usar imagens de contêiner desenvolvido para cargas de trabalho do CUDA.

  Damos suporte ao CUDA 9,0 neste estágio. Por exemplo, você pode usar as seguintes imagens base para o arquivo do Docker:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Exemplo YAML

Uma maneira de adicionar recursos GPU é implantar um grupo de contêineres usando um [arquivo YAML](container-instances-multi-container-yaml.md). Copie o YAML a seguir para um novo arquivo chamado *aci.yaml implantar gpu*, em seguida, salve o arquivo. Esse YAML cria um grupo de contêiner chamado *gpucontainergroup* especificando uma instância de contêiner com uma GPU K80. A instância é executada em um aplicativo de adição de vetor CUDA de exemplo. As solicitações de recurso são suficientes para executar a carga de trabalho.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Implante o grupo de contêineres com o comando [AZ container Create][az-container-create] , especificando o nome do arquivo `--file` YAML para o parâmetro. Você precisa fornecer o nome de um grupo de recursos e um local para o grupo de contêineres, como *eastus* que dá suporte a recursos GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

A implantação leva vários minutos para ser concluída. Em seguida, o contêiner inicia e executa uma operação de adição de vetor CUDA. Execute o comando [AZ container logs][az-container-logs] para exibir a saída do log:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Saída:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Exemplo de modelo do Resource Manager

Outra maneira de implantar um grupo de contêineres com recursos GPU é usando um [modelo do Resource Manager](container-instances-multi-container-group.md). Crie um arquivo chamado `gpudeploy.json` e copie o seguinte JSON para ele. Este exemplo implanta uma instância de contêiner com uma GPU V100 que executa um trabalho de treinamento [TensorFlow](https://www.tensorflow.org/) no conjunto de MNIST. As solicitações de recurso são suficientes para executar a carga de trabalho.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Implante o modelo com o comando [az group deployment create][az-group-deployment-create]. Você precisa fornecer o nome de um grupo de recursos que foi criado em uma região como *eastus* que é compatível com os recursos GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

A implantação leva vários minutos para ser concluída. Em seguida, o contêiner inicia e executa o trabalho de TensorFlow. Execute o comando [AZ container logs][az-container-logs] para exibir a saída do log:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Saída:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Limpar recursos

Como usar recursos GPU pode ser caro, certifique-se de que os contêineres não são executados inesperadamente por longos períodos. Monitore seus contêineres no portal do Azure ou verifique o status de um grupo de contêineres com o comando [AZ container show][az-container-show] . Por exemplo:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Quando terminar de trabalhar com as instâncias de contêiner que criou, exclua-as com os comandos a seguir:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como implantar um grupo de contêiner usando um [arquivo YAML](container-instances-multi-container-yaml.md) ou [modelo do Resource Manager](container-instances-multi-container-group.md).
* Saiba mais sobre [tamanhos de VM otimizados para GPU](../virtual-machines/linux/sizes-gpu.md) no Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
