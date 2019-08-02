---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348521"
---
As entradas no `inferenceconfig.json` documento são mapeadas para os parâmetros da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . A tabela a seguir descreve o mapeamento entre entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para o arquivo local que contém o código a ser executado para a imagem. |
| `runtime` | `runtime` | Qual tempo de execução usar para a imagem. Os tempos de execução com suporte atuais são ' Spark-py ' e ' Python '. |
| `condaFile` | `conda_file` | Opcional. Caminho para o arquivo local que contém uma definição de ambiente Conda a ser usada para a imagem. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcional. Caminho para o arquivo local que contém etapas adicionais do Docker a serem executadas durante a configuração da imagem. |
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contém todos os arquivos para criar a imagem. |
| `enableGpu` | `enable_gpu` | Opcional. Se o suporte à GPU deve ser habilitado na imagem. A imagem de GPU deve ser usada no serviço Microsoft Azure. Por exemplo, instâncias de contêiner do Azure, computação Azure Machine Learning, máquinas virtuais do Azure e serviço kubernetes do Azure. O padrão é false. |
| `baseImage` | `base_image` | Opcional. Uma imagem personalizada a ser usada como imagem base. Se nenhuma imagem base for fornecida, a imagem base será usada com base em um determinado parâmetro de tempo de execução. |
| `baseImageRegistry` | `base_image_registry` | Opcional. Registro de imagem que contém a imagem base. |
| `cudaVersion` | `cuda_version` | Opcional. Versão do CUDA a ser instalada para imagens que precisam de suporte à GPU. A imagem de GPU deve ser usada em serviços de Microsoft Azure. Por exemplo, instâncias de contêiner do Azure, computação Azure Machine Learning, máquinas virtuais do Azure e serviço kubernetes do Azure. As versões com suporte são 9,0, 9,1 e 10,0. Se ' enable_gpu ' for definido, o padrão será ' 9,1 '. |
| `description` | `description` |  Uma descrição para esta imagem. |

O JSON a seguir é uma configuração de inferência de exemplo para uso com a CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```