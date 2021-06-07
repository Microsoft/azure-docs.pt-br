---
title: Limpar marcas e manifestos
description: Use um comando de limpeza (purge) para excluir várias marcas e manifestos de um registro de contêiner do Azure com base na idade e em um filtro de marca e, opcionalmente, agende operações de limpeza.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 2dedfdd6eba73b7573743eba60294ac2231ffc56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722221"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Limpar automaticamente as imagens de um registro de contêiner do Azure

Quando você usa um registro de contêiner do Azure como parte de um fluxo de trabalho de desenvolvimento, o registro pode ficar rapidamente lotado de imagens ou outros artefatos que não são necessários após um curto período. Talvez seja conveniente excluir todas as marcas anteriores a uma determinada duração ou que correspondam a um filtro de nome especificado. Para excluir vários artefatos rapidamente, este artigo apresenta o comando `acr purge`, que você pode executar quando necessário ou como uma Tarefa do ACR [agendada](container-registry-tasks-scheduled.md). 

O comando `acr purge` está atualmente distribuído em uma imagem de contêiner pública (`mcr.microsoft.com/acr/acr-cli:0.4`), criada a partir do código-fonte no repositório [ACR-CLI](https://github.com/Azure/acr-cli) do GitHub.

Use o Azure Cloud Shell ou uma instalação local da CLI do Azure para executar os exemplos de comando deste artigo. Para usá-lo localmente, é necessária a versão 2.0.76 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install]. 

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

> [!WARNING]
> Use o comando `acr purge` com cautela, pois os dados excluídos da imagem são IRRECUPERÁVEIS. Se você tiver sistemas que extraem imagens pelo resumo do manifesto (e não pelo nome da imagem), não deve limpar imagens sem marcas. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de extrair pelo manifesto, considere a adoção de um esquema de *marcação exclusiva* esquema, uma [melhor prática recomendada](container-registry-image-tag-version.md).

Para excluir as marcas de imagem ou manifestos individuais usando comandos do CLI do Azure, consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Usar o comando de limpeza

O comando de contêiner `acr purge` exclui imagens por marca em um repositório que corresponda a um filtro de nome e que seja mais antigo do que uma duração especificada. Por padrão, somente referências da marca são excluídas, não os [manifestos](container-registry-concepts.md#manifest) subjacentes e os dados de camada. O comando tem uma opção para também excluir manifestos. 

> [!NOTE]
> O comando `acr purge` não exclui uma marca de imagem nem repositório em que o atributo `write-enabled` está definido como `false`. Para obter mais informações, confira [Bloquear uma imagem de contêiner em um Registro de Contêiner do Azure](container-registry-image-lock.md).

`acr purge` foi projetado para ser executado como um comando de contêiner em uma [Tarefa de ACR](container-registry-tasks-overview.md), para que seja autenticado automaticamente com o registro em que a tarefa é executada e realiza ações. Os exemplos de tarefas neste artigo usam o comando `acr purge` [alias](container-registry-tasks-reference-yaml.md#aliases) no lugar de um comando de imagem de contêiner totalmente qualificado.

No mínimo, especifique o seguinte ao executar `acr purge`:

* `--filter` - um repositório e uma *expressão regular* para filtrar marcas no repositório. Exemplos: `--filter "hello-world:.*"` corresponde a todas as marcas no repositório `hello-world` e `--filter "hello-world:^1.*"` corresponde a marcas que começam com `1`. Use vários parâmetros `--filter` para limpar vários repositórios.
* `--ago` - Uma [cadeia de caracteres de duração](https://golang.org/pkg/time/) no estilo Go para indicar uma duração após as imagens excluídas. A duração consiste em uma sequência de um ou mais números decimais, cada um com um sufixo de unidade. As unidades de tempo válidas incluem "d" para dias, "h" para horas e "m" para minutos. Por exemplo, `--ago 2d3h6m` seleciona todas as imagens filtradas cuja última modificação foi a mais de 2 dias, 3 horas e 6 minutos atrás. `--ago 1.5h` seleciona as imagens cuja última modificação foi a mais de 1,5 hora.

`acr purge` é compatível com vários parâmetros opcionais. Os dois exemplos a seguir são usados neste artigo:

* `--untagged` - especifica que os manifestos que não têm marcas associadas (*manifestos sem marca*) serão excluídos.
* `--dry-run` - especifica que nenhum dado é excluído, mas o resultado é o mesmo do comando executado sem esse sinalizador. Esse parâmetro é útil para testar um comando de limpeza a fim de garantir que ele não exclua inadvertidamente os dados que você pretende preservar.
* `--keep` -Especifica que o número x mais recente de marcas a serem excluídas é retido.
* `--concurrency` -Especifica um número de tarefas de limpeza a serem processadas simultaneamente. Um valor padrão será usado se esse parâmetro não for fornecido.

Para parâmetros adicionais, execute `acr purge --help`. 

O `acr purge` é compatível com outros recursos dos comandos das Tarefas do ACR, incluindo [variáveis de execução](container-registry-tasks-reference-yaml.md#run-variables) e [logs de execução de tarefa](container-registry-tasks-logs.md) que são transmitidos e também salvos para recuperação posterior.

### <a name="run-in-an-on-demand-task"></a>Executar em uma tarefa sob demanda

O exemplo a seguir usa o comando [az acr run][az-acr-run] para executar o comando `acr purge` sob demanda. Este exemplo exclui todas as marcas de imagem e manifestos no repositório de `hello-world` em *myregistry* que foram modificadas há mais de um dia. O comando do contêiner é passado usando uma variável de ambiente. A tarefa é executada sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Executar em uma tarefa agendada

O exemplo a seguir usa o comando [az acr task create][az-acr-task-create] para criar uma [tarefa de ACR agendada](container-registry-tasks-scheduled.md) diariamente. A tarefa limpa as marcas modificadas há mais de 7 dias no repositório `hello-world`. O comando do contêiner é passado usando uma variável de ambiente. A tarefa é executada sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Execute o comando [az acr task show][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Limpar grandes números de marcas e manifestos

A limpeza de um grande número de marcas e manifestos pode levar vários minutos ou mais. Para limpar milhares de marcas e manifestos, o comando pode precisar ser executado por mais tempo do que o período padrão de 600 segundos para uma tarefa sob demanda ou 3600 segundos para uma tarefa agendada. Se o tempo limite for excedido, apenas um subconjunto de marcas e manifestos será excluído. Para garantir que uma limpeza em larga escala seja concluída, use o parâmetro `--timeout` para aumentar o valor. 

Por exemplo, a seguinte tarefa sob demanda define um tempo limite de 3600 segundos (1 hora):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exemplo: Limpeza agendada de vários repositórios em um registro

Este exemplo demonstra o uso de `acr purge` para limpar periodicamente vários repositórios em um registro. Por exemplo, você pode ter um pipeline de desenvolvimento que efetua push das imagens para os repositórios `samples/devimage1` e `samples/devimage2`. Você importa periodicamente as imagens de desenvolvimento para um repositório de produção para suas implantações, então não precisa mais das imagens de desenvolvimento. Semanalmente, você limpa os repositórios `samples/devimage1` e `samples/devimage2`, em preparação para o trabalho da próxima semana.

### <a name="preview-the-purge"></a>Visualizar a limpeza

Antes de excluir dados, é recomendado executar uma tarefa de limpeza sob demanda usando o parâmetro `--dry-run`. Essa opção permite que você veja as marcas e os manifestos que o comando limpará, sem remover nenhum dado. 

No exemplo a seguir, o filtro em cada repositório seleciona todas as marcas. O parâmetro `--ago 0d` corresponde a imagens de todas as idades nos repositórios que correspondem aos filtros. Modifique os critérios de seleção conforme necessário para seu cenário. O parâmetro `--untagged` indica que os manifestos são excluídos, além das marcas. O comando do contêiner é passado para o comando [az acr run][az-acr-run] usando uma variável de ambiente.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Examine a saída do comando para ver as marcas e os manifestos que correspondem aos parâmetros de seleção. Como o comando é executado com `--dry-run`, nenhum dado é excluído.

Saída de exemplo:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Agendar a limpeza

Depois de verificar a simulação, crie uma tarefa agendada para automatizar a limpeza. O exemplo a seguir agenda uma tarefa semanal no domingo, 1:00 UTC, para executar o comando de limpeza anterior:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Execute o comando [az acr task show][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outras opções para [excluir dados de imagem](container-registry-delete.md) no Registro de Contêiner do Azure.

Para obter mais informações sobre o armazenamento de imagens, consulte [Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

