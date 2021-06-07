---
title: Referência de YAML – tarefas de ACR
description: Referência para definir tarefas na YAML para tarefas do ACR, incluindo propriedades da tarefa, tipos de etapas, propriedades das etapas e variáveis internas.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 042310d29f5561c2cd77b0b9cccfc587ca4aa767
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067576"
---
# <a name="acr-tasks-reference-yaml"></a>Referência das Tarefas do ACR: YAML

A definição de tarefas com várias etapas em Tarefas do ACR fornece um primitivo de computação centrado em contêiner, com foco em compilar, testar e corrigir contêineres. Este artigo aborda os comandos, parâmetros, propriedades e sintaxe para os arquivos YAML que definem as tarefas de várias etapas.

Este artigo contém uma referência para criar arquivos YAML de tarefas com várias etapas para Tarefas do ACR. Se você quiser uma introdução às Tarefas do ACR, confira a [Visão geral das Tarefas de ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>Formato de arquivo acr-task.yaml

As Tarefas do ACR dão suporte à declaração de tarefas com várias etapas na sintaxe YAML padrão. Você define as etapas de uma tarefa em um arquivo YAML. Em seguida, você pode executar a tarefa manualmente passando o arquivo para o comando [AZ ACR Run][az-acr-run] . Ou use o arquivo para criar uma tarefa com [AZ ACR tarefa Create][az-acr-task-create] que é disparada automaticamente em uma confirmação de git, uma atualização de imagem base ou uma agenda. Embora este artigo se refira a `acr-task.yaml` como o arquivo que contém as etapas, as Tarefas do ACR dão suporte a qualquer nome de arquivo válido com uma [extensão com suporte](#supported-task-filename-extensions).

Os primitivos `acr-task.yaml` de nível superior são **propriedades das tarefas**, **tipos de etapas** e **propriedades das etapas**:

* As [propriedades das tarefas](#task-properties) se aplicam a todas as etapas em toda a execução da tarefa. Há várias propriedades globais da tarefa, incluindo:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* Os [tipos de etapas das tarefas](#task-step-types) representam os tipos de ações que podem ser executadas em uma tarefa. Há três tipos de etapas:
  * `build`
  * `push`
  * `cmd`
* As [propriedades das etapas das tarefas](#task-step-properties) são parâmetros que se aplicam a uma etapa individual. Existem várias propriedades das etapas, incluindo:
  * `startDelay`
  * `timeout`
  * `when`
  * ... e muito mais.

O formato base de um arquivo `acr-task.yaml`, incluindo algumas propriedades das etapas comuns, segue. Apesar de não ser uma representação completa de todas as propriedades das etapas disponíveis ou do uso do tipo de etapa, fornece uma rápida visão geral do formato de arquivo básico.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Extensões de nome de arquivo das tarefas com suporte

As Tarefas do ACR reservaram diversas extensões de nome de arquivo, incluindo `.yaml`, que processará como um arquivo de tarefas. Qualquer extensão que *não* constar na lista a seguir será considerada pelas Tarefas do ACR como um Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML é o único formato de arquivo com suporte, no momento, pelas Tarefas do ACR. As outras extensões de nome de arquivo estão reservadas para um possível suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas de exemplo

Vários arquivos de tarefas de exemplo são referenciados nas próximas seções deste artigo. As tarefas de exemplo estão em um repositório público do GitHub, [Azure-Samples/acr-tasks][acr-tasks]. É possível executá-las com o comando da CLI do Azure [az acr run][az-acr-run]. Os comandos de exemplo são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos de exemplo pressupõe que você configurou um Registro padrão na CLI do Azure; portanto, o parâmetro `--registry` é omitido. Para configurar um Registro padrão, use o comando [az configure][az-configure] com o parâmetro `--defaults`, que aceita um valor `acr=REGISTRY_NAME`.

Por exemplo, para configurar a CLI do Azure com um Registro padrão denominado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades das tarefas

Normalmente, as propriedades da tarefa aparecem na parte superior de um `acr-task.yaml` arquivo e são propriedades globais que se aplicam durante a execução completa das etapas da tarefa. Algumas dessas propriedades globais podem ser substituídas em uma etapa individual.

| Propriedade | Tipo | Opcional | Descrição | Substituição com suporte | Valor padrão |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Sim | A versão do arquivo `acr-task.yaml` conforme analisado pelo serviço de Tarefas do ACR. Enquanto as Tarefas do ACR se esforçam para manter a compatibilidade com versões anteriores, esse valor permite que as Tarefas do ACR mantenham a compatibilidade dentro de uma versão definida. Se não for especificado, o padrão será a versão mais recente. | Não | Nenhum |
| `stepTimeout` | int (segundos) | Sim | O número máximo de segundos em que uma etapa pode ser executada. Se a propriedade for especificada em uma tarefa, ela definirá a `timeout` propriedade padrão de todas as etapas. Se a `timeout` propriedade for especificada em uma etapa, ela substituirá a propriedade fornecida pela tarefa. | Sim | 600 (10 minutos) |
| `workingDirectory` | string | Sim | O diretório de trabalho do contêiner durante o tempo de execução. Se a propriedade for especificada em uma tarefa, ela definirá a `workingDirectory` propriedade padrão de todas as etapas. Se especificado em uma etapa, ele substituirá a propriedade fornecida pela tarefa. | Sim | `c:\workspace` no Windows ou `/workspace` no Linux |
| `env` | [string, string, ...] | Sim |  Matriz de cadeias de caracteres no `key=value` formato que definem as variáveis de ambiente para a tarefa. Se a propriedade for especificada em uma tarefa, ela definirá a `env` propriedade padrão de todas as etapas. Se especificado em uma etapa, ele substituirá as variáveis de ambiente herdadas da tarefa. | Sim | Nenhum |
| `secrets` | [segredo, segredo,...] | Sim | Matriz de objetos [secretos](#secret) . | Não | Nenhum |
| `networks` | [rede, rede,...] | Sim | Matriz de objetos de [rede](#network) . | Não | Nenhum |
| `volumes` | [volume, volume,...] | Sim | Matriz de objetos de [volume](#volume) . Especifica os volumes com o conteúdo de origem a ser montado em uma etapa. | Não | Nenhum |

### <a name="secret"></a>segredo

O objeto secreto tem as propriedades a seguir.

| Propriedade | Tipo | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | Cadeia de caracteres | No | O identificador do segredo. | Nenhum |
| `keyvault` | string | Sim | A URL do segredo do Azure Key Vault. | Nenhum |
| `clientID` | string | Sim | A ID do cliente da [identidade gerenciada atribuída pelo usuário](container-registry-tasks-authentication-managed-identity.md) para recursos do Azure. | Nenhum |

### <a name="network"></a>network

O objeto de rede tem as propriedades a seguir.

| Propriedade | Tipo | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Cadeia de caracteres | No | O nome da rede. | Nenhum |
| `driver` | string | Sim | O driver para gerenciar a rede. | Nenhum |
| `ipv6` | bool | Sim | Se a rede IPv6 está habilitada. | `false` |
| `skipCreation` | bool | Sim | Se a criação da rede deve ser ignorada. | `false` |
| `isDefault` | bool | Sim | Se a rede é uma rede padrão fornecida com o registro de contêiner do Azure. | `false` |

### <a name="volume"></a>volume

O objeto de volume tem as propriedades a seguir.

| Propriedade | Tipo | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Cadeia de caracteres | No | O nome do volume a ser montado. Pode conter somente caracteres alfanuméricos, '-' e ' _ '. | Nenhum |
| `secret` | Cadeia de caracteres de mapa [cadeia de caracteres] | Não | Cada chave do mapa é o nome de um arquivo criado e populado no volume. Cada valor é a versão de cadeia de caracteres do segredo. Os valores secretos devem ser codificados em base64. | Nenhum |

## <a name="task-step-types"></a>Tipos de etapas das tarefas

As Tarefas do ACR dão suporte a três tipos de etapas. Cada tipo de etapa dá suporte a várias propriedades, detalhadas na seção para cada tipo de etapa.

| Tipo de etapa | Descrição |
| --------- | ----------- |
| [`build`](#build) | Compila uma imagem de contêiner usando a sintaxe `docker build` familiar. |
| [`push`](#push) | Executa um `docker push` de imagens recentemente compiladas ou remarcadas em um registro de contêiner. Há suporte para o Registro de Contêiner do Azure, outros Registros privados e o Hub do Docker público. |
| [`cmd`](#cmd) | Executa um contêiner como comando, com os parâmetros passados para o `[ENTRYPOINT]` do contêiner. O `cmd` tipo de etapa oferece suporte a parâmetros como `env` , `detach` e outras `docker run` Opções de comando familiares, permitindo testes de unidade e funcionais com a execução simultânea de contêiner. |

## <a name="build"></a>compilar

Compilar uma imagem de contêiner. O tipo de etapa `build` representa um meio seguro e com vários locatários de executar `docker build` na nuvem como primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: compilar

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O tipo de etapa `build` suporta os parâmetros na tabela a seguir. O tipo de etapa `build` também suporta todas as opções de construção do comando [docker build](https://docs.docker.com/engine/reference/commandline/build/), como `--build-arg` para definir variáveis de tempo de criação.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o `image:tag` totalmente qualificado da imagem compilada.<br /><br />Como as imagens podem ser utilizadas para validações de tarefas internas, como testes funcionais, nem todas as imagens exigem `push` para um Registro. No entanto, para criar uma instância de uma imagem dentro de uma execução de Tarefa, a imagem precisa de um nome para fazer referência.<br /><br />Ao contrário `az acr build` de, a execução de tarefas ACR não fornece o comportamento de push padrão. Com as Tarefas do ACR, o cenário padrão pressupõe a capacidade de compilar, validar e efetuar push de uma imagem. Confira [push](#push) para saber como efetuar push de imagens compiladas opcionalmente. | Sim |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não for especificado, o Dockerfile padrão na raiz do contexto será considerado. Para especificar um Dockerfile, passe o nome de arquivo relativo à raiz do contexto. | Sim |
| `context` | O diretório raiz passado para `docker build`. O diretório raiz de cada tarefa é definido como um [workingDirectory](#task-step-properties) compartilhado e inclui a raiz do diretório clonado Git associado. | Não |

### <a name="properties-build"></a>Propriedades: compilar

O tipo de etapa `build` dá suporte às propriedades a seguir. Encontre detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

| Propriedades | Tipo | Obrigatório |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `expose` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | string | Opcional |
| `keep` | bool | Opcional |
| `network` | objeto | Opcional |
| `ports` | [string, string, ...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | INT | Opcional |
| `retries` | INT | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `volumeMount` | objeto | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: compilar

#### <a name="build-image---context-in-root"></a>Compilar imagem – contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilar imagem – contexto no subdiretório

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>efetuar push

Efetuar push de uma ou mais imagens compiladas ou remarcadas em um registro de contêiner. Dá suporte ao push de Registros privados como o Registro de Contêiner do Azure ou o Hub do Docker público.

### <a name="syntax-push"></a>Sintaxe: efetuar push

O tipo de etapa `push` dá suporte a uma coleção de imagens. A sintaxe de coleção YAML dá suporte a formatos embutidos e aninhados. O push de uma única imagem geralmente é representado usando a sintaxe embutida:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Para maior legibilidade, use a sintaxe aninhada ao efetuar push de várias imagens:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: efetuar push

O tipo de etapa `push` dá suporte às propriedades a seguir. Encontre detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

| Propriedade | Tipo | Obrigatório |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |

### <a name="examples-push"></a>Exemplos: efetuar push

#### <a name="push-multiple-images"></a>Efetuar push de várias imagens

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Compilar, efetuar push e executar

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

O tipo de etapa `cmd` executa um contêiner.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O tipo de etapa `cmd` dá suporte às propriedades a seguir:

| Propriedade | Tipo | Obrigatório |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `expose` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | string | Opcional |
| `keep` | bool | Opcional |
| `network` | objeto | Opcional |
| `ports` | [string, string, ...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | INT | Opcional |
| `retries` | INT | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `volumeMount` | objeto | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

É possível encontrar detalhes sobre essas propriedades na seção [Propriedades das etapas das tarefas](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem olá, mundo

Este comando executa o arquivo da tarefa `hello-world.yaml`, que faz referência à imagem [olá, mundo](https://hub.docker.com/_/hello-world/) no Hub do Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem Bash e ecoar "olá, mundo"

Este comando executa o arquivo da tarefa `bash-echo.yaml`, que faz referência à imagem [bash](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar a tag da imagem Bash específica

Para executar uma versão de imagem específica, especifique a tag no `cmd`.

Este comando executa o arquivo da tarefa `bash-echo-3.yaml`, que faz referência à imagem [bash:3.0](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O tipo de etapa `cmd` faz referência a imagens usando o formato `docker run` padrão. As imagens não precedidas por um registro são consideradas originárias de docker.io. O exemplo anterior também poderia ser representado como:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Usando a Convenção de `docker run` referência de imagem padrão, o `cmd` pode executar imagens de qualquer registro privado ou do Hub do Docker público. Se estiver fazendo referência a imagens no mesmo Registro em que uma Tarefa do ACR estiver em execução, você não precisará especificar credenciais do Registro.

* Execute uma imagem que seja de um registro de contêiner do Azure. O exemplo a seguir pressupõe que você tenha um registro chamado `myregistry` e uma imagem personalizada `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalizar a referência de registro com uma variável de execução ou alias

    Em vez de embutir em código o nome do registro em um `acr-task.yaml` arquivo, você pode torná-lo mais portátil usando uma [variável de execução](#run-variables) ou [alias](#aliases). A `Run.Registry` variável ou o `$Registry` alias expande em tempo de execução para o nome do registro no qual a tarefa está sendo executada.

    Por exemplo, para generalizar a tarefa anterior para que ela funcione em qualquer registro de contêiner do Azure, faça referência à variável $Registry no nome da imagem:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Acessar volumes secretos

A `volumes` propriedade permite que volumes e seu conteúdo secreto sejam especificados para `build` `cmd` as etapas e em uma tarefa. Dentro de cada etapa, uma `volumeMounts` propriedade opcional lista os volumes e os caminhos de contêiner correspondentes a serem montados no contêiner nessa etapa. Os segredos são fornecidos como arquivos no caminho de montagem de cada volume.

Execute uma tarefa e monte dois segredos em uma etapa: um armazenado em um cofre de chaves e outro especificado na linha de comando:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Propriedades das etapas das tarefas

Cada tipo de etapa dá suporte a várias propriedades apropriadas para seu tipo. A tabela a seguir define todas as propriedades das etapas disponíveis. Nem todos os tipos de etapas dão suporte a todas as propriedades. Para ver quais dessas propriedades estão disponíveis para cada tipo de etapa, confira as seções de referência de tipo de etapa [cmd](#cmd), [compilar](#build) e [efetuar push](#push).

| Propriedade | Tipo | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Sim | Se o contêiner deve ser desanexado quando está em execução. | `false` |
| `disableWorkingDirectoryOverride` | bool | Sim | Se a funcionalidade de substituição deve ser desabilitada `workingDirectory` . Use isso em combinação com `workingDirectory` para ter controle total sobre o diretório de trabalho do contêiner. | `false` |
| `entryPoint` | string | Sim | Substitui o `[ENTRYPOINT]` do contêiner de uma etapa. | Nenhum |
| `env` | [string, string, ...] | Sim | Matriz de cadeias de caracteres no formato `key=value` que definem as variáveis de ambiente para a etapa. | Nenhum |
| `expose` | [string, string, ...] | Sim | Matriz de portas que são expostas do contêiner. |  Nenhum |
| [`id`](#example-id) | string | Sim | Identifica a etapa dentro da tarefa com exclusividade. Outras etapas na tarefa podem fazer referência ao `id` da etapa, como para verificação de dependência com `when`.<br /><br />O `id` também é o nome do contêiner em execução. Processos em execução em outros contêineres na tarefa podem consultar o `id` como seu nome de host DNS ou para acessá-lo com logs de docker [id], por exemplo. | `acb_step_%d`, em que `%d` é o índice baseado em 0 da etapa de cima para baixo no arquivo YAML |
| `ignoreErrors` | bool | Sim | Indica se a etapa será marcada como bem-sucedida, independentemente de ocorrer um erro durante a execução do contêiner. | `false` |
| `isolation` | string | Sim | O nível de isolamento do contêiner. | `default` |
| `keep` | bool | Sim | Se o contêiner da etapa deve ser mantido após a execução. | `false` |
| `network` | object | Sim | Identifica uma rede na qual o contêiner é executado. | Nenhum |
| `ports` | [string, string, ...] | Sim | Matriz de portas que são publicadas do contêiner para o host. |  Nenhum |
| `pull` | bool | Sim | Se deve forçar um pull do contêiner antes de executá-lo para evitar qualquer comportamento de cache. | `false` |
| `privileged` | bool | Sim | Se o contêiner deve ser executado no modo privilegiado. | `false` |
| `repeat` | INT | Sim | O número de tentativas para repetir a execução de um contêiner. | 0 |
| `retries` | INT | Sim | O número de tentativas para tentar se um contêiner falhar na sua execução. Uma nova tentativa só será tentada se o código de saída de um contêiner for diferente de zero. | 0 |
| `retryDelay` | int (segundos) | Sim | O atraso em segundos entre as tentativas da execução de um contêiner. | 0 |
| `secret` | object | Sim | Identifica um segredo Azure Key Vault ou [identidade gerenciada para recursos do Azure](container-registry-tasks-authentication-managed-identity.md). | Nenhum |
| `startDelay` | int (segundos) | Sim | Número de segundos para atrasar a execução de um contêiner. | 0 |
| `timeout` | int (segundos) | Sim | Número máximo de segundos em que uma etapa poderá ser executada antes de terminar. | 600 |
| [`when`](#example-when) | [string, string, ...] | Sim | Configura a dependência de uma etapa em relação a uma ou mais etapas diferentes dentro da tarefa. | Nenhum |
| `user` | string | Sim | O nome de usuário ou UID de um contêiner | Nenhum |
| `workingDirectory` | string | Sim | Define o diretório de trabalho para uma etapa. Por padrão, as Tarefas do ACR criam um diretório raiz como o diretório de trabalho. No entanto, se o build tiver várias etapas, as etapas anteriores poderão compartilhar artefatos com as etapas posteriores se o mesmo diretório de trabalho for especificado. | `c:\workspace` no Windows ou `/workspace` no Linux |

### <a name="volumemount"></a>volumeMount

O objeto volumeMount tem as propriedades a seguir.

| Propriedade | Tipo | Opcional | Descrição | Valor padrão |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Cadeia de caracteres | No | O nome do volume a ser montado. Deve corresponder exatamente ao nome de uma `volumes` propriedade. | Nenhum |
| `mountPath`   | string | não | O caminho absoluto para montar arquivos no contêiner.  | Nenhum |

### <a name="examples-task-step-properties"></a>Exemplos: propriedades das etapas das tarefas

#### <a name="example-id"></a>Exemplo: id

Compile duas imagens, criando uma instância de uma imagem de teste funcional. Cada etapa é identificada por um `id` exclusivo, ao qual as outras etapas da tarefa fazem referência na propriedade `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A propriedade `when` especifica a dependência de uma etapa em relação a uma ou mais etapas dentro da tarefa. Ela dá suporte a dois valores de parâmetro:

* `when: ["-"]` – Indica que não há dependência de outras etapas. Uma etapa que especifica `when: ["-"]` iniciará a execução imediatamente e habilita a execução em etapas simultânea.
* `when: ["id1", "id2"]` – Indica que a etapa é dependente das etapas com `id` "id1" e `id` "id2". Essa etapa não será executada até a conclusão das etapas "id1" e "id2".

Se `when` não estiver especificado em uma etapa, essa etapa dependerá da conclusão da etapa anterior no arquivo `acr-task.yaml`.

Execução em etapas sequenciais sem `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução em etapas sequenciais com `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Build de imagens paralelas:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilação de imagens paralelas e teste dependente:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

As Tarefas do ACR incluem um conjunto padrão de variáveis que estão disponíveis para as etapas das tarefas durante a execução. Essas variáveis podem ser acessadas usando o formato `{{.Run.VariableName}}`, em que `VariableName` é um dos seguintes:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Os nomes de variáveis são geralmente auto-explicativos. Os detalhes são seguidos para as variáveis usadas com frequência. A partir da versão YAML `v1.1.0` , você pode usar um [alias de tarefa](#aliases) Abreviado e predefinido no lugar da maioria das variáveis de execução. Por exemplo, no lugar de `{{.Run.Registry}}` , use o `$Registry` alias.

### <a name="runid"></a>Run.ID

Cada execução, por meio `az acr run` do ou gatilho com base em execução de tarefas criadas por meio de `az acr task create` , tem uma ID exclusiva. A ID representa a execução que está sendo executada no momento.

Normalmente usado para uma marcação exclusiva de uma imagem:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run. SharedVolume

O identificador exclusivo de um volume compartilhado que é acessível por todas as etapas de tarefa. O volume é montado `c:\workspace` no Windows ou `/workspace` no Linux. 

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do Registro. Normalmente usado para fazer uma referência genérica ao registro em que a tarefa está sendo executada.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Execute. Registryname

O nome do registro de contêiner. Normalmente usado em etapas de tarefas que não exigem um nome de servidor totalmente qualificado, por exemplo, `cmd` etapas que executam CLI do Azure comandos em registros.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

O horário UTC atual em que a execução começou.

### <a name="runcommit"></a>Executar. Commit

Para uma tarefa disparada por uma confirmação para um repositório GitHub, o identificador de confirmação.

### <a name="runbranch"></a>Executar. Branch

Para uma tarefa disparada por uma confirmação para um repositório GitHub, o nome da ramificação.

## <a name="aliases"></a>Aliases

Desde o `v1.1.0` , as tarefas ACR dão suporte a aliases que estão disponíveis para etapas de tarefa quando eles são executados. Os aliases são semelhantes em conceito a aliases (atalhos de comando) com suporte no bash e em outros shells de comando. 

Com um alias, você pode iniciar qualquer comando ou grupo de comandos (incluindo opções e nomes de texto) digitando uma única palavra.

As tarefas ACR dão suporte a vários aliases predefinidos e também a aliases personalizados criados por você.

### <a name="predefined-aliases"></a>Aliases predefinidos

Os aliases de tarefa a seguir estão disponíveis para uso no lugar de [variáveis de execução](#run-variables):

| Alias | Executar variável |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

Em etapas da tarefa, preceda um alias com a `$` diretiva, como neste exemplo:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliases de imagem

Cada um dos aliases a seguir aponta para uma imagem estável no registro de contêiner da Microsoft (MCR). Você pode fazer referência a cada um deles na `cmd` seção de um arquivo de tarefa sem usar uma diretiva.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A seguinte tarefa de exemplo usa vários aliases para [limpar](container-registry-auto-purge.md) as marcas de imagem com mais de 7 dias no repositório `samples/hello-world` no registro de execução:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Alias personalizado

Defina um alias personalizado no arquivo YAML e use-o conforme mostrado no exemplo a seguir. Um alias só pode conter caracteres alfanuméricos. A diretiva padrão para expandir um alias é o `$` caractere.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Você pode vincular a um arquivo YAML remoto ou local para obter definições de alias personalizadas. O exemplo a seguir contém links para um arquivo YAML no armazenamento de BLOBs do Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Próximas etapas

Para uma visão geral das tarefas com várias etapas, confira [Executar tarefas com várias etapas de compilar, testar e corrigir nas Tarefas do ACR](container-registry-tasks-multi-step.md).

Para builds de uma única etapa, confira a [Visão geral das Tarefas do ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
