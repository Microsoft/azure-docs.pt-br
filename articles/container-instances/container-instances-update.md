---
title: Atualizar contêineres nas Instâncias de Contêiner do Azure
description: Saiba como atualizar os contêineres em execução nos grupos de contêineres das Instâncias de Contêiner do Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 2df6a2724cbdcd6bbb6c6ca6636256b7e399da8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686884"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contêineres nas Instâncias de Contêiner do Azure

Durante a operação normal das instâncias de contêiner, talvez seja necessário atualizar os contêineres em um grupo de contêineres. Por exemplo, é recomendável atualizar a versão da imagem, alterar um nome DNS, atualizar as variáveis de ambiente ou atualizar o estado de um contêiner cujo aplicativo falhou.

## <a name="update-a-container-group"></a>Atualizar um grupo de contêineres

Atualize os contêineres em um grupo de contêineres com a reimplantação de um grupo existente com pelo menos uma propriedade modificada. Ao atualizar um grupo de contêineres, todos os contêineres em execução no grupo são reiniciados no local.

Reimplante um grupo de contêiner existente, emitindo o comando criar (ou use o portal do Azure) e especifique o nome de um grupo existente. Modifique pelo menos uma propriedade válida do grupo ao emitir o comando criar para disparar a reimplantação. Nem todas as propriedades do grupo de contêineres são válidas para reimplantação. Consulte as [Propriedades que exigem exclusão](#properties-that-require-container-delete) para obter uma lista de propriedades sem suporte.

O exemplo da CLI do Azure a seguir atualiza um grupo de contêineres com um novo rótulo de nome DNS. Como a propriedade do rótulo de nome DNS do grupo é modificada, o grupo de contêineres é reimplantado e os contêineres reiniciados.

Implantação inicial com o rótulo de nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contêiner com um novo rótulo de nome DNS, *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Benefícios da atualização

O principal benefício da atualização de um grupo de contêineres existente é a implantação mais rápida. Ao reimplantar um grupo de contêineres existente, é efetuado o pull das camadas de imagem de contêiner daquelas armazenados em cache pela implantação anterior. Em vez efetuar pull de todas as camadas de imagem novas do registro como é feito com novas implantações, somente é efetuado o pull das camadas modificadas (se houverem).

Aplicativos com base em imagens de contêiner maiores, como o Windows Server Core podem ter uma melhoria significativa na velocidade de implantação ao atualizar, em vez de excluir e implantar novos.

## <a name="limitations"></a>Limitações

Nem todas as propriedades de um grupo de contêineres dão suporte a atualizações. Para alterar algumas propriedades de um grupo de contêineres, primeiro você deve excluir e, sem seguida, reimplantar o grupo. Para obter detalhes, consulte [Propriedades que exigem exclusão de contêiner](#properties-that-require-container-delete).

Todos os contêineres em um grupo de contêineres são reiniciados quando você atualiza o grupo de contêineres. Não é possível executar uma atualização ou uma reinicialização local de um contêiner específico em um grupo de vários contêineres.

O endereço IP de um contêiner normalmente não será alterado entre as atualizações, mas não há garantia de que permaneça o mesmo. Desde que o grupo de contêineres seja implantado no mesmo host subjacente, o grupo de contêineres retém o endereço IP. Embora seja raro, e enquanto as Instâncias de Contêiner do Azure não poupam esforços para reimplantar no mesmo host, há alguns eventos internos do Azure que podem resultar na reimplantação em um host diferente. Para atenuar esse problema, sempre use um rótulo de nome DNS para as instâncias de contêiner.

Grupos de contêineres encerrados ou excluídos não podem ser atualizados. Após um grupo de contêineres ser interrompido (está no *Terminated*) ou se tiver sido excluído, o grupo será implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que exigem exclusão de contêiner

Como mencionado anteriormente, nem todas as propriedades do grupo de contêineres podem ser atualizadas. Por exemplo, para alterar as portas ou reiniciar a política de um contêiner, primeiro é necessário excluir o grupo de contêineres e criá-lo novamente.

Essas propriedades exigem a exclusão do grupo de contêineres antes da reimplantação:

* Tipo do SO
* CPU
* Memória
* Política de reinicialização
* Portas

Ao excluir um grupo de contêineres e recriá-lo, ele não será "reimplantado", mas criado como novo. É efetuado o pull de todas as camadas de imagem atualizadas do registro, não aquelas armazenadas em cache por uma implantação anterior. O endereço IP do contêiner também pode ser alterado por estar sendo implantado em um host subjacente diferente.

## <a name="next-steps"></a>Próximas etapas

**Grupo de contêineres** foi mencionado várias vezes neste artigo. Cada contêiner em Instâncias de Contêiner do Azure é implantado em um grupo de contêineres, sendo que grupos de contêineres podem conter mais de um contêiner.

[Grupos de contêineres em Instâncias de Contêiner do Azure](container-instances-container-groups.md)

[Implantar um grupo de vários contêineres](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
