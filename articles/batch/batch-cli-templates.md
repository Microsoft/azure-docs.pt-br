---
title: Executar trabalhos de ponta a ponta usando modelos - Lote do Azure | Microsoft Docs
description: Crie pools de lote, trabalhos e tarefas com arquivos de amostra e a CLI do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 5e5ed8a69d5140814899c24e96eded6dc61e5908
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323666"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Usar modelos CLI do Azure de Lote e o arquivo de transferência

Ao usar a extensão do Lote do Microsoft Azure é possível executar trabalhos do Lote sem escrever código.

Criar e usar arquivos de modelo JSON com a CLI do Azure para criar pools de Lote, trabalhos e tarefas. Use os comandos de extensão CLI para carregar facilmente os arquivos de entrada de trabalho na conta de armazenamento associada à conta do Lote e baixe os arquivos de saída de trabalho.

## <a name="overview"></a>Visão geral

Uma extensão da CLI do Azure permite que o Lote seja usado de ponta a ponta por usuários que não são desenvolvedores. Somente com comandos CLI é possível criar um pool, carregar dados de entrada, criar trabalhos e tarefas associadas e baixar os dados de saída resultantes. Nenhum código adicional é necessário. Execute os comandos CLI diretamente ou integre-os aos scripts.

Os modelos do Lote compilam o [suporte do Lote existente na CLI do Azure](batch-cli-get-started.md#json-files-for-resource-creation) para que os arquivos JSON especifiquem os valores de propriedade ao criar pools, trabalhos, tarefas e outros itens. Os modelos do lote adicionam os seguintes recursos:

-   Parâmetros podem ser definidos. Quando o modelo é usado, somente os valores de parâmetro são especificados para criar o item, com outros valores de propriedade de item sendo especificados no corpo do modelo. Um usuário que entende o Lote e os aplicativos a serem executados pelo Lote pode criar modelos especificando os valores de propriedade de pool, de trabalho e de tarefa. Um usuário menos familiarizado com o Lote e/ou os aplicativos só precisa especificar os valores para os parâmetros definidos.

-   As fábricas de tarefa de trabalho criam uma ou mais tarefas associadas a um trabalho, evitando a necessidade de criar muitas definições de tarefa e simplificando significativamente o envio do trabalho.


Os trabalhos geralmente usam arquivos de dados de entrada e produzem arquivos de dados de saída. Uma conta de armazenamento é associada, por padrão, a cada conta do Lote. Transfira arquivos de e para essa conta de armazenamento usando a CLI, sem codificação e sem credenciais de armazenamento.

Por exemplo, [ffmpeg](https://ffmpeg.org/) é um aplicativo popular que processa arquivos de áudio e vídeo. Aqui estão as etapas com a CLI do Lote do Azure para invocar o ffmpeg para transcodificar arquivos de vídeo de origem em diferentes resoluções.

-   Crie um modelo de pool. O usuário criando o modelo sabe como chamar o aplicativo ffmpeg e os respectivos requisitos; ele especifica o sistema operacional apropriado, o tamanho da VM, como o ffmpeg é instalado (de um pacote de aplicativos ou usando um gerenciador de pacotes, por exemplo) e outros valores de propriedade do pool. Parâmetros são criados para que, quando o modelo for usado, somente a ID do pool e o número de VMs precisem ser especificados.

-   Crie um modelo de trabalho. O usuário criando o modelo sabe como o ffmpeg precisa ser invocado para transcodificar o vídeo de origem para uma resolução diferente e especifica a linha de comando da tarefa; ele também sabe que há uma pasta contendo os arquivos de vídeo de origem, com uma tarefa necessária por arquivo de entrada.

-   Um usuário final com um conjunto de arquivos de vídeo para transcodificar cria primeiro um pool usando o modelo de pool, especificando somente a ID do pool e o número de VMs necessário. Em seguida, ele pode carregar os arquivos de origem para transcodificar. Um trabalho pode ser enviado usando o modelo de trabalho, especificando somente a ID do pool e o local dos arquivos de origem carregados. O trabalho do Lote é criado, com uma tarefa por arquivo de entrada sendo gerada. Finalmente, os arquivos de saída transcodificados podem ser baixados.

## <a name="installation"></a>Instalação

Para instalar a extensão da CLI do Lote do Azure, primeiro [instale a CLI do Azure 2.0](/cli/azure/install-azure-cli) ou execute a CLI do Azure no [Azure Cloud Shell](../cloud-shell/overview.md).

Instale a última versão da extensão do Lote, usando o comando da CLI do Azure seguir:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Para obter mais informações sobre as opções de instalação adicionais e extensão da CLI do lote, consulte o [repositório GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Para usar os recursos de extensão da CLI, você precisa de uma conta do Lote do Microsoft Azure e, para os comandos que transferir arquivos para e do armazenamento, uma conta de armazenamento vinculada.

Para fazer logon em uma conta do lote com a CLI do Azure, consulte [Gerenciar recursos do lote com a CLI do Azure](batch-cli-get-started.md).

## <a name="templates"></a>Modelos

Os modelos do Lote do Azure são semelhantes aos modelos do Azure Resource Manager, na funcionalidade e na sintaxe. Eles são arquivos JSON que contêm valores e nomes de propriedade do item, mas adicionam os conceitos principais a seguir:

-   **Parâmetros**

    -   Permitem que os valores de propriedade sejam especificados em uma seção de corpo, com necessidade de fornecer apenas os valores de parâmetro quando o modelo é usado. Por exemplo, a definição completa para um pool pode ser colocada no corpo e apenas um parâmetro definido para a ID do pool; assim, para criar um pool, é necessário fornecer apenas uma cadeia de caracteres de ID do pool.
        
    -   O corpo do modelo pode ser criado por uma pessoa com conhecimento do Lote e dos aplicativos a serem executados pelo Lote; somente os valores dos parâmetros definidos pelo autor devem ser fornecidos quando o modelo é usado. Portanto, um usuário sem o conhecimento detalhado do Lote e/ou do aplicativo pode usar os modelos.

-   **Variáveis**

    -   Permitem que valores de parâmetro simples ou complexos sejam especificados em um local e usado em um ou mais locais no corpo do modelo. As variáveis podem simplificar e reduzir o tamanho do modelo, além de torná-lo mais sustentável, tendo um local para alterar as propriedades.

-   **Construções de nível superior**

    -   Há algumas construções de nível superior disponíveis no modelo que ainda não estão disponíveis nas APIs do Lote. Por exemplo, uma fábrica de tarefas pode ser definida em um modelo de trabalho que cria várias tarefas para o trabalho usando uma definição de tarefa comum. Esses constructos evitam a necessidade de codificar para criar dinamicamente vários arquivos JSON, como um arquivo por tarefa, bem como criar arquivos de script para instalar aplicativos por meio de um gerenciador de pacotes.

    -   Em algum momento, esses constructos podem ser adicionados ao serviço do Lote e disponibilizados nas APIs do Lote, interfaces do usuário e etc.

### <a name="pool-templates"></a>Modelos de pool

Os modelos de pool dão suporte a recursos de modelo standard de parâmetros e variáveis. Eles também são suporte para o constructo de nível superior a seguir:

-   **Referências do pacote**

    -   Opcionalmente, permite que o software seja copiado para nós de pool usando gerenciadores de pacotes. O gerenciador de pacotes e a ID do pacote são especificados. Ao declarar um ou mais pacotes, você evita criar um script que obtém os pacotes necessários, instalar o script e executar o script em cada nó de pool.

A seguir, um exemplo de um modelo que cria um pool de VMs do Linux com o ffmpeg instalado. Para usá-lo, forneça apenas uma cadeia de caracteres da ID do pool e o número de VMs no pool:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Se o arquivo de modelo foi nomeado _pool-ffmpeg.json_, invoque o modelo conforme a seguir:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

A CLI solicita que você forneça valores para o `poolId` e `nodeCount` parâmetros. Você também pode fornecer os parâmetros em um arquivo JSON. Por exemplo:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Se o arquivo JSON de parâmetros foi nomeado *pool-ffmpeg.json*, invoque o modelo conforme a seguir:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Modelos de trabalho

Os modelos de trabalho dão suporte a recursos de modelo padrão de parâmetros e variáveis. Eles também são suporte para o constructo de nível superior a seguir:

-   **Fábrica de tarefas**

    -   Com base em uma definição de tarefa, cria várias tarefas para um trabalho. Três tipos de fábrica de tarefas têm suporte – limpeza paramétrica, tarefa por arquivo e coleção de tarefas.

O seguinte é um exemplo de um modelo que cria um trabalho para transcodificar arquivos de vídeo MP4 com ffmpeg para uma das duas resoluções mais baixas. Cria uma tarefa por arquivo de vídeo de origem. Consulte [grupos de arquivos e transferência de arquivos](#file-groups-and-file-transfer) para obter mais informações sobre grupos de arquivos para o trabalho de entrada e saída.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Se o arquivo de modelo foi nomeado _job-ffmpeg.json_, invoque o modelo conforme a seguir:

```azurecli
az batch job create --template job-ffmpeg.json
```

Como anteriormente, a CLI solicita que você forneça valores para os parâmetros. Você também pode fornecer os parâmetros em um arquivo JSON.

### <a name="use-templates-in-batch-explorer"></a>Use os modelos no Azure Batch Explorer

Você pode carregar um modelo de CLI do lote para o aplicativo de desktop do [Azure Batch Explorer](https://github.com/Azure/BatchExplorer) (anteriormente chamado BatchLabs) para criar um trabalho ou pool do lote. Você também pode selecionar a partir de modelos predefinidos de pool e o trabalho na Galeria do Azure Batch Explorer.

Para carregar um modelo:

1. No Azure Batch Explorer, selecione **Galeria** > **Modelos locais**.

2. Selecione, ou arraste e solte, um pool local ou o modelo de trabalho.

3. Selecione **Usar este modelo**e siga os prompts na tela.

## <a name="file-groups-and-file-transfer"></a>Transferência de arquivos e grupos de arquivo

A maioria dos trabalhos e tarefas exigem arquivos de entrada e produzem arquivos de saída. Geralmente, os arquivos de entrada e os arquivos de saída são transferidos, do cliente para o nó ou do nó para o cliente. A extensão de CLI do Lote do Azure abstrai a transferência de arquivo e utiliza a conta de armazenamento que você pode associar à conta do Lote.

Um grupo de arquivo é igual a um contêiner que é criado na conta de armazenamento do Azure. O grupo de arquivo pode ter subpastas.

A extensão de CLI do Lote fornece comandos para carregar arquivos do cliente para um grupo de arquivos especificado e baixar arquivos do grupo de arquivos especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Modelos de pool e de trabalho permitem que os arquivos armazenados em grupos de arquivo sejam especificados para cópia em nós de pool ou transferidos de nós de pool para um grupo de arquivo. Por exemplo, no trabalho de modelo especificado anteriormente, o grupo de arquivos *ffmpeg-input* é especificado para a fábrica de tarefas como o local dos arquivos de vídeo de origem copiados para baixo até o nó para transcodificação. O grupo de arquivos *ffmpeg-output* é o local onde os arquivos de saída transcodificados são copiados do nó que executa cada tarefa.

## <a name="summary"></a>Resumo

Atualmente, o suporte à transferência de arquivo e de modelo foi adicionado somente à CLI do Azure. A meta é expandir o público que pode usar o Lote para usuários que não precisam desenvolver código usando as APIs de Lote, tais como pesquisadores, usuários de TI. Sem codificação, os usuários com conhecimento do Azure, do Lote e dos aplicativos a serem executados pelo Lote podem criar modelos para criação de pools e trabalhos. Com os parâmetros de modelo, os usuários sem conhecimento detalhado do Lote e dos aplicativos podem usar esses modelos.

Experimente a extensão do Lote para a CLI do Azure e forneça comentários ou sugestões, seja nos comentários deste artigo ou no [repositório da Comunidade do Lote](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Próximas etapas

- Documentação detalhada de instalação e uso, amostras e código-fonte estão disponíveis no [repositório GitHub do Azure](https://github.com/Azure/azure-batch-cli-extensions).

- Saiba mais sobre como usar o [Gerenciador do lote](https://github.com/Azure/BatchExplorer) para criar e gerenciar recursos do lote.
