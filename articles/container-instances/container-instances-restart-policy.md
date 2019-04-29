---
title: Usar políticas de reinicialização com tarefas em contêineres nas Instâncias de Contêiner do Azure
description: Saiba como usar as Instâncias de Contêiner do Azure para executar tarefas que são executadas até a conclusão, como na compilação, teste ou trabalhos de renderização de imagem.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608123"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Executar tarefas em contêineres com políticas de reinício

A facilidade e a velocidade de implantação de contêineres nas Instâncias de Contêiner do Azure fornece uma plataforma atraente para executar tarefas de execução única como compilação, teste e renderização de imagem em uma instância de contêiner.

Com uma política de reinicialização configurável, você pode especificar que os contêineres são interrompidos quando os seus processos são concluídos. Como as instâncias de contêiner são cobradas por segundo, você será cobrado somente pelos recursos de computação usados enquanto o contêiner que executa a tarefa estiver em execução.

Os exemplos apresentados neste artigo usam a CLI do Azure. Você precisa ter a CLI do Azure versão 2.0.21 ou superior [instalada localmente][azure-cli-install] ou usar a CLI no [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinicialização de contêiner

Quando cria um [grupo de contêineres](container-instances-container-groups.md) nas Instâncias de Contêiner do Azure, você pode especificar uma entre três configurações de política de reinicialização.

| Política de reinicialização   | DESCRIÇÃO |
| ---------------- | :---------- |
| `Always` | Contêineres no grupo de contêiner sempre são reiniciados. Este é a configuração **padrão** aplicada quando nenhuma política de reinicialização é especificada na criação do contêiner. |
| `Never` | Os contêineres no grupo de contêineres nunca reiniciados. Os contêineres são executados no máximo uma vez. |
| `OnFailure` | Os contêineres no grupo de contêineres são reiniciados somente quando o processo executado no contêiner falha (quando ele termina com um código de saída diferente de zero). Os contêineres são executados pelo menos uma vez. |

## <a name="specify-a-restart-policy"></a>Especificar uma política de reinicialização

Como especificar uma política de reinicialização depende de como você cria suas instâncias de contêiner, como com a CLI do Azure, cmdlets do Azure PowerShell, ou no portal do Azure. Na CLI do Azure, especifique o parâmetro `--restart-policy` ao chamar [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Executar o exemplo de conclusão

Para ver a política de reinicialização em ação, crie uma instância de contêiner da Microsoft [aci-wordcount] [ aci-wordcount-image] da imagem e, em seguida, especifique o `OnFailure` política de reinicialização. Este contêiner de exemplo executa um script de Python que, por padrão, analisa o texto de Shakespeare [Hamlet](http://shakespeare.mit.edu/hamlet/full.html), grava as 10 palavras mais comuns em STDOUT e, em seguida, sai.

Execute o contêiner de exemplo com o seguinte comando [az container create][az-container-create]:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

As Instâncias de Contêiner do Azure iniciam o contêiner e, em seguida, o interrompem quando seu aplicativo (ou script, neste caso) é encerrado. Quando as Instâncias de Contêiner do Azure param um contêiner cuja política de reinicialização é `Never` ou `OnFailure`, o status do contêiner é definido como **Encerrado**. Você pode verificar o status de um contêiner usando o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Saída de exemplo:

```bash
"Terminated"
```

Depois que o status do contêiner de exemplo mostrar *Encerrado*, você pode ver a saída da tarefa ao exibir os logs do contêiner. Execute o comando [az container logs][az-container-logs] comando para exibir a saída do script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Saída:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Este exemplo mostra a saída que o script envia para STDOUT. As tarefas em contêineres, no entanto, podem escrever a saída para o armazenamento persistente para recuperação posterior. Por exemplo, para um [compartilhamento de arquivos do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Próximas etapas

Cenários baseados em tarefas, como um conjunto de dados grande com vários contêineres, de processamento em lotes podem tirar proveito de custom [variáveis de ambiente](container-instances-environment-variables.md) ou [linhas de comando](container-instances-start-command.md) em tempo de execução.

Para obter detalhes sobre como persistir a saída de seus contêineres que são executados até a conclusão, consulte [Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
