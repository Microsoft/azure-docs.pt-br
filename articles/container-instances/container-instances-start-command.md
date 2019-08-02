---
title: Usar uma linha de comando inicial em instâncias de contêiner do Azure
description: Substituir o ponto de entrada configurado em uma imagem de contêiner ao implantar uma instância de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 99440e22eb736522a25c2ee56bb07ef1d9967e66
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325660"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Definir a linha de comando em uma instância de contêiner para substituir a operação de linha de comando padrão

Ao criar uma instância de contêiner, especifique opcionalmente um comando para substituir a instrução de linha de comando padrão inclusas na imagem de contêiner. Esse comportamento é semelhante ao `--entrypoint` argumento de linha de comando para. `docker run`

Como definir [variáveis de ambiente](container-instances-environment-variables.md) para instâncias de contêiner, especificar uma linha de comando inicial é útil para trabalhos em lotes onde você precisa preparar cada contêiner dinamicamente com a configuração específica de tarefa.

## <a name="command-line-guidelines"></a>Diretrizes de linha de comando

* Por padrão, a linha de comando especifica um *único processo que inicia sem um shell* no contêiner. Por exemplo, a linha de comando pode executar um script Python ou um arquivo executável. 

* Para executar vários comandos, comece sua linha de comando definindo um ambiente de shell com suporte no sistema operacional do contêiner. Exemplos:

  |Sistema operacional  |Shell padrão  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Siga as convenções do Shell para combinar vários comandos a serem executados em sequência.

* Dependendo da configuração do contêiner, talvez seja necessário definir um caminho completo para o executável ou os argumentos da linha de comando.

* Defina uma [política](container-instances-restart-policy.md) de reinicialização apropriada para a instância de contêiner, dependendo se a linha de comando especifica uma tarefa de execução longa ou uma tarefa de execução única. Por exemplo, uma política de reinicialização do `Never` ou `OnFailure` é recomendada para uma tarefa de execução única. 

* Se você precisar de informações sobre o ponto de entrada padrão definido em uma imagem de contêiner, use o comando [Docker Image eninspecione](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Sintaxe da linha de comando

A sintaxe da linha de comando varia dependendo da API do Azure ou da ferramenta usada para criar as instâncias. Se você especificar um ambiente de Shell, observe também as convenções de sintaxe de comando do Shell.

* comando [AZ container Create][az-container-create] : Passe uma cadeia de caracteres `--command-line` com o parâmetro. Exemplo: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: Passe uma cadeia de caracteres `-Command` com o parâmetro. Exemplo: `-Command "echo hello"`.

* Portal do Azure: Na propriedade de **substituição de comando** da configuração do contêiner, forneça uma lista separada por vírgulas de cadeias de caracteres, sem aspas. Exemplo: `python, myscript.py, arg1, arg2`). 

* Modelo do Resource Manager ou arquivo YAML ou um dos SDKs do Azure: Especifique a propriedade de linha de comando como uma matriz de cadeias de caracteres. Exemplo: a matriz `["python", "myscript.py", "arg1", "arg2"]` JSON em um modelo do Resource Manager. 

  Se você estiver familiarizado com a sintaxe [Dockerfile](https://docs.docker.com/engine/reference/builder/) , esse formato será semelhante ao formulário *exec* da instrução cmd.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| Comando único | `--command-line "python myscript.py arg1 arg2"` | **Substituição de comando**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Vários comandos | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Substituição de comando**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplos de CLI do Azure

Por exemplo, modifique o comportamento da imagem de contêiner [Microsoft/ACI-WordCount][aci-wordcount] , que analisa o texto em *Hamlet* de Shakespeare para localizar as palavras que ocorrem com mais frequência. Em vez de analisar *Hamlet*, você pode definir uma linha de comando que aponta para uma fonte de texto diferente.

Para ver a saída do comando [Microsoft/ACI-WordCount][aci-wordcount] container when it analyzes the default text, run it with the following [az container create][az-container-create] . Nenhuma linha de comando inicial é especificada, portanto, o comando de contêiner padrão é executado. Para fins de ilustração, este exemplo define [variáveis de ambiente](container-instances-environment-variables.md) para localizar as três principais palavras que têm pelo menos cinco letras de comprimento:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Quando o estado do contêiner aparecer como *encerrado* (use [AZ container show][az-container-show] to check state), display the log with [az container logs][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Agora, configure um segundo contêiner de exemplo para analisar o texto diferente especificando uma linha de comando diferente. O script Python executado pelo contêiner, *WordCount.py*, aceita uma URL como um argumento e processa o conteúdo dessa página em vez do padrão.

Por exemplo, para determinar as três principais palavras que têm pelo menos cinco letras de comprimento em *Romeu e Julieta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, depois que o contêiner estiver *Encerrado*, exiba a saída, mostrando os logs do contêiner:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Próximas etapas

Cenários baseados em tarefas, como processamento em lote de um grande conjunto de grandes com vários contêineres, podem se beneficiar de linhas de comando personalizadas em tempo de execução. Para obter mais informações sobre a execução de contêineres baseados em tarefas, consulte [executar tarefas em contêineres com políticas](container-instances-restart-policy.md)de reinicialização.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
