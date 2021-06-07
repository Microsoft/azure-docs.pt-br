---
title: Atualizar grupo de contêineres
description: Saiba como atualizar os contêineres em execução nos grupos de contêineres das Instâncias de Contêiner do Azure.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: fb31eeda83532c408a303e879439006bcd7d4e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200641"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contêineres nas Instâncias de Contêiner do Azure

Durante a operação normal das instâncias de contêiner, talvez você ache necessário atualizar os contêineres em execução em um [grupo de contêineres](./container-instances-container-groups.md). Por exemplo, talvez você queira atualizar uma propriedade, como uma versão de imagem, um nome DNS ou uma variável de ambiente, ou atualizar uma propriedade em um contêiner cujo aplicativo tenha falhado.

Atualize os contêineres em um grupo de contêineres em execução Reimplantando um grupo existente com pelo menos uma propriedade modificada. Quando você atualiza um grupo de contêineres, todos os contêineres em execução no grupo são reiniciados no local, geralmente no mesmo host de contêiner subjacente.

> [!NOTE]
> Grupos de contêineres encerrados ou excluídos não podem ser atualizados. Depois que um grupo de contêineres termina (está em um estado de êxito ou de falha) ou foi excluído, o grupo deve ser implantado como novo. Consulte outras [limitações](#limitations).

## <a name="update-a-container-group"></a>Atualizar um grupo de contêineres

Para atualizar um grupo de contêineres existente:

* Emita o comando Create (ou use o portal do Azure) e especifique o nome de um grupo existente 
* Modifique ou adicione pelo menos uma propriedade do grupo que ofereça suporte à atualização ao reimplantar. Determinadas propriedades [não dão suporte a atualizações](#properties-that-require-container-delete).
* Defina outras propriedades com os valores que você forneceu anteriormente. Se você não definir um valor para uma propriedade, ele será revertido para seu valor padrão.

> [!TIP]
> Um [arquivo YAML](./container-instances-container-groups.md#deployment) ajuda a manter a configuração de implantação de um grupo de contêineres e fornece um ponto de partida para implantar um grupo atualizado. Se você usou um método diferente para criar o grupo, poderá exportar a configuração para YAML usando [AZ container Export][az-container-export], 

### <a name="example"></a>Exemplo

O exemplo da CLI do Azure a seguir atualiza um grupo de contêineres com um novo rótulo de nome DNS. Como a propriedade rótulo do nome DNS do grupo é aquela que pode ser atualizada, o grupo de contêineres é reimplantado e seus contêineres são reiniciados.

Implantação inicial com o rótulo de nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contêineres com um novo rótulo de nome DNS, *MyApplication* e defina as propriedades restantes com os valores usados anteriormente:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Benefícios da atualização

O principal benefício da atualização de um grupo de contêineres existente é a implantação mais rápida. Ao reimplantar um grupo de contêineres existente, é efetuado o pull das camadas de imagem de contêiner daquelas armazenados em cache pela implantação anterior. Em vez efetuar pull de todas as camadas de imagem novas do registro como é feito com novas implantações, somente é efetuado o pull das camadas modificadas (se houverem).

Aplicativos com base em imagens de contêiner maiores, como o Windows Server Core podem ter uma melhoria significativa na velocidade de implantação ao atualizar, em vez de excluir e implantar novos.

## <a name="limitations"></a>Limitações

* Nem todas as propriedades de um grupo de contêineres dão suporte a atualizações. Para alterar algumas propriedades de um grupo de contêineres, primeiro você deve excluir e, sem seguida, reimplantar o grupo. Consulte [Propriedades que exigem exclusão de contêiner](#properties-that-require-container-delete).
* Todos os contêineres em um grupo de contêineres são reiniciados quando você atualiza o grupo de contêineres. Não é possível executar uma atualização ou uma reinicialização local de um contêiner específico em um grupo de vários contêineres.
* O endereço IP de um grupo de contêineres normalmente é mantido entre as atualizações, mas não é garantido que permaneça o mesmo. Desde que o grupo de contêineres seja implantado no mesmo host subjacente, o grupo de contêineres retém o endereço IP. Embora seja raro, há alguns eventos internos do Azure que podem causar reimplantação em um host diferente. Para atenuar esse problema, é recomendável usar um rótulo de nome DNS para suas instâncias de contêiner.
* Grupos de contêineres encerrados ou excluídos não podem ser atualizados. Quando um grupo de contêineres é interrompido (está no estado *encerrado* ) ou excluído, o grupo é implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que exigem exclusão de contêiner

Nem todas as propriedades do grupo de contêineres podem ser atualizadas. Por exemplo, para alterar a política de reinicialização de um contêiner, primeiro você deve excluir o grupo de contêineres e, em seguida, criá-lo novamente.

As alterações nessas propriedades exigem a exclusão do grupo de contêineres antes da reimplantação:

* Tipo do SO
* Recursos de CPU, memória ou GPU
* Política de reinicialização
* Perfil de rede

Ao excluir um grupo de contêineres e recriá-lo, ele não será "reimplantado", mas criado como novo. É efetuado o pull de todas as camadas de imagem atualizadas do registro, não aquelas armazenadas em cache por uma implantação anterior. O endereço IP do contêiner também pode ser alterado por estar sendo implantado em um host subjacente diferente.

## <a name="next-steps"></a>Próximas etapas

**Grupo de contêineres** foi mencionado várias vezes neste artigo. Cada contêiner em Instâncias de Contêiner do Azure é implantado em um grupo de contêineres, sendo que grupos de contêineres podem conter mais de um contêiner.

[Grupos de contêineres em Instâncias de Contêiner do Azure](./container-instances-container-groups.md)

[Implantar um grupo de multicontêiner](container-instances-multi-container-group.md)

[Parar e iniciar manualmente os contêineres nas Instâncias de Contêiner do Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
