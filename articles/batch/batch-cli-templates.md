---
title: "Executar trabalhos do Lote do Azure de ponta a ponta sem escrever código (Versão Prévia) | Microsoft Docs"
description: Crie arquivos de modelo para a CLI do Azure criar pools de Lote, trabalhos e tarefas.
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: markscu
ms.openlocfilehash: 6b91466da46d1f4ca9f25bf1718be783603efc58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Usar modelos CLI do Lote do Azure e o arquivo de transferência (Versão prévia)

Ao usar a CLI do Azure é possível executar trabalhos do Lote sem escrever código.

É possível criar arquivos de modelo que podem ser usados com a CLI do Azure e permitem a criação de trabalhos, tarefas pools de Lote. Arquivos de entrada de trabalho podem ser facilmente carregados para a conta de armazenamento associada com a conta do Lote e os arquivos de saída de trabalho baixados.

## <a name="overview"></a>Visão geral

Uma extensão da CLI do Azure permite que o Lote seja usado de ponta a ponta por usuários que não são desenvolvedores. É possível criar um pool, carregar os dados de entrada, criar trabalhos e tarefas associadas e baixar os dados de saída resultantes – sem necessidade de nenhum código, com a CLI sendo usada diretamente ou integrada em scripts.

Os modelos do Lote usam o [suporte do Lote existente na CLI do Azure](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation), que permite que os arquivos JSON especifiquem valores de propriedade para a criação de pools, trabalhos, tarefas e outros itens. Com modelos de Lote, os seguintes recursos são adicionados além do que é possível com os arquivos JSON:

-   Parâmetros podem ser definidos. Quando o modelo é usado, somente os valores de parâmetro são especificados para criar o item, com outros valores de propriedade de item sendo especificados no corpo do modelo. Um usuário que entende o Lote e os aplicativos a serem executados pelo Lote pode criar modelos especificando os valores de propriedade de pool, de trabalho e de tarefa. Um usuário menos familiarizado com o Lote e/ou os aplicativos só precisa especificar os valores para os parâmetros definidos.

-   As fábricas de tarefa de trabalho criam uma ou mais tarefas associadas a um trabalho, evitando a necessidade de criar muitas definições de tarefa e simplificando significativamente o envio do trabalho.


Arquivos de dados de entrada devem ser fornecidos para trabalhos, sendo que arquivos de dados de saída são produzidos com frequência. Uma conta de armazenamento é associada por padrão com cada conta do Lote e os arquivos podem ser transferidos facilmente para e desta conta de armazenamento usando a CLI, sem necessidade de codificação nem de credenciais de armazenamento.

Por exemplo, [ffmpeg](http://ffmpeg.org/) é um aplicativo popular que processa arquivos de áudio e vídeo. A CLI do Lote do Azure pode ser usada para invocar ffmpeg para transcodificar arquivos de vídeo de origem para resoluções diferentes.

-   Um modelo de pool é criado. O usuário criando o modelo sabe como chamar o aplicativo ffmpeg e os respectivos requisitos; ele especifica o sistema operacional apropriado, o tamanho da VM, como o ffmpeg é instalado (de um pacote de aplicativos ou usando um gerenciador de pacotes, por exemplo) e outros valores de propriedade do pool. Parâmetros são criados para que, quando o modelo for usado, somente a ID do pool e o número de VMs precisem ser especificados.

-   Um modelo de trabalho é criado. O usuário criando o modelo sabe como o ffmpeg precisa ser invocado para transcodificar o vídeo de origem para uma resolução diferente e especifica a linha de comando da tarefa; ele também sabe que há uma pasta contendo os arquivos de vídeo de origem, com uma tarefa necessária por arquivo de entrada.

-   Um usuário final com um conjunto de arquivos de vídeo para transcodificar cria primeiro um pool usando o modelo de pool, especificando somente a ID do pool e o número de VMs necessário. Em seguida, ele pode carregar os arquivos de origem para transcodificar. Um trabalho pode ser enviado usando o modelo de trabalho, especificando somente a ID do pool e o local dos arquivos de origem carregados. O trabalho do Lote é criado, com uma tarefa por arquivo de entrada sendo gerada. Por fim, os arquivos de saída transcodificados podem ser baixados.

## <a name="installation"></a>Instalação

Os recursos de transferência de arquivo e de modelo requerem a instalação de uma extensão.

Para obter instruções sobre como instalar a CLI do Azure, consulte [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Depois que a CLI do Azure foi instalada, a extensão do Lote pode ser instalada usando o seguinte comando de CLI:

```azurecli
az component update --add batch-extensions --allow-third-party
```

Para obter mais informações sobre a extensão do Lote, consulte [Extensões de CLI do Lote do Microsoft Azure para Windows, Mac e Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Modelos

A CLI do Lote do Azure permite que os itens como pools, trabalhos e tarefas sejam criados, especificando um arquivo JSON contendo os valores e nomes de propriedades. Por exemplo:

```azurecli
az batch pool create –-json-file AppPool.json
```

Os modelos do Lote do Azure são semelhantes aos modelos do Azure Resource Manager, na funcionalidade e na sintaxe. Eles são arquivos JSON que contêm valores e nomes de propriedade do item, mas adicionam os conceitos principais a seguir:

-   **Parâmetros**

    -   Permitem que os valores de propriedade sejam especificados em uma seção de corpo, com necessidade de fornecer apenas os valores de parâmetro quando o modelo é usado. Por exemplo, a definição completa para um pool pode ser colocada no corpo e apenas um parâmetro definido para a ID do pool; assim, para criar um pool, é necessário fornecer apenas uma cadeia de caracteres de ID do pool.
        
    -   O corpo do modelo pode ser criado por uma pessoa com conhecimento do Lote e dos aplicativos a serem executados pelo Lote; somente os valores dos parâmetros definidos pelo autor devem ser fornecidos quando o modelo é usado. Portanto, um usuário sem o conhecimento detalhado do Lote e/ou do aplicativo pode usar os modelos.

-   **Variáveis**

    -   Permitem que valores de parâmetro simples ou complexos sejam especificados em um local e usado em um ou mais locais no corpo do modelo. Variáveis podem simplificar e reduzir o tamanho do modelo, bem como facilitar a manutenção dele por ter um local para alterar as propriedades cujo valor pode ser alterado.

-   **Construções de nível superior**

    -   Há algumas construções de nível superior disponíveis no modelo que ainda não estão disponíveis nas APIs do Lote. Por exemplo, uma fábrica de tarefas pode ser definida em um modelo de trabalho que cria várias tarefas para o trabalho usando uma definição de tarefa comum. Essas construções evitam a necessidade de codificar para criar dinamicamente vários arquivos JSON (assim como um arquivo por tarefa), bem como para criar arquivos de script para instalar aplicativos por meio de um gerenciador de pacotes, por exemplo.

    -   Em algum momento, quando aplicável, essas construções podem ser adicionadas ao serviço de Lote e disponível nas APIs de Lote, interfaces do usuário, etc.

### <a name="pool-templates"></a>Modelos de pool

Além dos recursos de modelo padrão de variáveis e parâmetros, as seguintes construções de nível superior têm suporte pelo modelo de pool:

-   **Referências do pacote**

    -   Opcionalmente, permite que o software seja copiado para nós de pool usando gerenciadores de pacotes. O gerenciador de pacotes e a ID do pacote são especificados. Ser capaz de declarar um ou mais pacotes evita a necessidade de criar um script que obtém os pacotes necessários, instalá-lo e executá-lo em cada nó de pool.

A seguir está um exemplo de um modelo que cria um pool de VMs do Linux com ffmpeg instalado, cujo uso requer apenas o fornecimento do número de VMs e de uma cadeia de caracteres de ID do pool:

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
                "description": "The pool id "
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
                    "sku": "16.04.0-LTS",
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

Se o arquivo de modelo fosse nomeado _pool-ffmpeg.json_, o modelo seria invocado da seguinte maneira:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Modelos de trabalho

Além dos recursos de modelo padrão de variáveis e parâmetros, as seguintes construções de nível superior têm suporte pelo modelo de trabalho:

-   **Fábrica de tarefas**

    -   Com base em uma definição de tarefa, cria várias tarefas para um trabalho. Três tipos de fábrica de tarefas têm suporte – limpeza paramétrica, tarefa por arquivo e coleção de tarefas.

Este é um exemplo de um modelo que cria um trabalho que, por sua vez, usa ffmpeg para transcodificar arquivos de vídeo MP4 para uma de duas resoluções mais baixas, com uma tarefa criada por arquivo de vídeo de origem:

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

Se o arquivo de modelo fosse nomeado _job-ffmpeg.json_, o modelo seria invocado da seguinte maneira:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Transferência de arquivos e grupos de arquivo

A maioria dos trabalhos e tarefas exigem arquivos de entrada e produzem arquivos de saída. Os arquivos de entrada e de saída normalmente precisam ser transferidos do cliente para o nó ou do nó para o cliente. A extensão de CLI do Lote do Azure abstrai a transferência de arquivo e utiliza a conta de armazenamento que é criada por padrão para cada conta do Lote.

Um grupo de arquivo é igual a um contêiner que é criado na conta de armazenamento do Azure. O grupo de arquivo pode ter subpastas.

A extensão de CLI do Lote fornece comandos para carregar arquivos do cliente para um grupo de arquivo especificado e baixar arquivos do grupo de arquivo especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Modelos de pool e de trabalho permitem que os arquivos armazenados em grupos de arquivo sejam especificados para cópia em nós de pool ou transferidos de nós de pool para um grupo de arquivo. Por exemplo, o modelo de trabalho especificado anteriormente, o grupo de arquivo "ffmpeg-input" é especificado para a fábrica de tarefas como o local dos arquivos de vídeo de origem copiados para o nó para transcodificação; o grupo de arquivo "ffmpeg-output" é usado como o local para o qual os arquivos de saída transcodificados são copiados do nó que executa cada tarefa.

## <a name="summary"></a>Resumo

Atualmente, o suporte à transferência de arquivo e de modelo foi adicionado somente à CLI do Azure. A meta é expandir o público que pode usar o Lote para usuários que não precisam desenvolver código usando as APIs de Lote, tais como pesquisadores, usuários de TI e assim por diante. Sem codificação, os usuários com conhecimento do Azure, do Lote e dos aplicativos a serem executados pelo Lote podem criar modelos para criação de pools e trabalhos. Com os parâmetros de modelo, os usuários sem conhecimento detalhado do Lote e dos aplicativos podem usar esses modelos.

Experimente a extensão do Lote para a CLI do Azure e forneça comentários ou sugestões, seja nos comentários deste artigo ou no [fórum do Lote do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Próximas etapas

- Veja a postagem de blog de modelos do Lote: [Executando trabalhos do Lote do Azure usando a CLI do Azure – não há necessidade de código](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Documentação detalhada de instalação e uso, amostras e código-fonte estão disponíveis no [repositório GitHub do Azure](https://github.com/Azure/azure-batch-cli-extensions).
