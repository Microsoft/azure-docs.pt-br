---
title: Artefato de OCI por push e pull
description: Artefatos de OCI e pull (Push Open container Initiative) usando um registro de contêiner privado no Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 8a73f295999888dab20531ffdd0fb042790a5357
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988224"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Enviar por push e efetuar pull de um artefato de OCI usando um registro de contêiner do Azure

Você pode usar um registro de contêiner do Azure para armazenar e gerenciar [artefatos de OCI (iniciativa de contêiner aberto)](container-registry-image-formats.md#oci-artifacts) , bem como imagens de contêiner do Docker e do Docker compatíveis.

Para demonstrar esse recurso, este artigo mostra como usar a ferramenta de [registro de OCI como armazenamento (oras)](https://github.com/deislabs/oras) para enviar por push um artefato de exemplo – um arquivo de texto-para um registro de contêiner do Azure. Em seguida, extraia o artefato do registro. Você pode gerenciar uma variedade de artefatos de OCI em um registro de contêiner do Azure usando diferentes ferramentas de linha de comando apropriadas para cada artefato.

## <a name="prerequisites"></a>Pré-requisitos

* **Registro de contêiner do Azure** -crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
* **Ferramenta oras** – Baixe e instale uma versão atual do oras para o seu sistema operacional do [repositório GitHub](https://github.com/deislabs/oras/releases). A ferramenta é lançada como um tarball compactado ( `.tar.gz` arquivo). Extraia e instale o arquivo usando os procedimentos padrão para seu sistema operacional.
* **Azure Active Directory entidade de serviço (opcional)** – para autenticar diretamente com o Oras, crie uma [entidade de serviço](container-registry-auth-service-principal.md) para acessar o registro. Verifique se a entidade de serviço recebe uma função como AcrPush para que ela tenha permissões para enviar e extrair artefatos.
* **CLI do Azure (opcional)** – para usar uma identidade individual, você precisa de uma instalação local do CLI do Azure. A versão 2.0.71 ou posterior é recomendada. Execute `az --version ` para localizar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* **Docker (opcional)** – para usar uma identidade individual, você também deve ter o Docker instalado localmente para autenticar com o registro. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Entrar em um registro

Esta seção mostra dois fluxos de trabalho sugeridos para entrar no registro, dependendo da identidade usada. Escolha o método apropriado para o seu ambiente.

### <a name="sign-in-with-oras"></a>Entrar com ORAS

Usando uma [entidade de serviço](container-registry-auth-service-principal.md) com direitos de push, execute o `oras login` comando para entrar no registro usando a ID do aplicativo da entidade de serviço e a senha. Especifique o nome totalmente qualificado do registro (todas as letras minúsculas), neste caso, *myregistry.azurecr.Io*. A ID do aplicativo da entidade de serviço é passada na variável de ambiente `$SP_APP_ID` e a senha na variável `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Para ler a senha de stdin, use `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Entrar com a CLI do Azure

[Entre](/cli/azure/authenticate-azure-cli) no CLI do Azure com sua identidade para enviar e extrair artefatos do registro de contêiner.

Em seguida, use o comando CLI do Azure [AZ ACR login](/cli/azure/acr#az-acr-login) para acessar o registro. Por exemplo, para autenticar em um registro chamado *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` usa o cliente do Docker para definir um token Azure Active Directory no `docker.config` arquivo. O cliente do Docker deve estar instalado e em execução para concluir o fluxo de autenticação individual.

## <a name="push-an-artifact"></a>Enviar um artefato por push

Crie um arquivo de texto em um diretório de trabalho de trabalho local com algum texto de exemplo. Por exemplo, em um shell bash:

```bash
echo "Here is an artifact" > artifact.txt
```

Use o `oras push` comando para enviar esse arquivo de texto por push para o registro. O exemplo a seguir envia por push o arquivo de texto de exemplo para o `samples/artifact` repositório. O registro é identificado com o nome de registro totalmente qualificado *myregistry.azurecr.Io* (todas as letras minúsculas). O artefato está marcado `1.0` . O artefato tem um tipo indefinido, por padrão, identificado pela cadeia de caracteres do *tipo de mídia* após o nome do arquivo `artifact.txt` . Consulte [artefatos de OCI](https://github.com/opencontainers/artifacts) para tipos adicionais. 

**Linux ou macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

A saída para um push bem-sucedido é semelhante ao seguinte:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Para gerenciar artefatos em seu registro, se você estiver usando o CLI do Azure, execute `az acr` comandos padrão para gerenciar imagens. Por exemplo, obtenha os atributos do artefato usando o comando [AZ ACR Repository show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

A saída deverá ser semelhante a esta:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Efetuar pull de um artefato

Execute o `oras pull` comando para efetuar pull do artefato do registro.

Primeiro, remova o arquivo de texto do diretório de trabalho local:

```bash
rm artifact.txt
```

Execute `oras pull` para efetuar pull do artefato e especifique o tipo de mídia usado para enviar o artefato por push:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verifique se o pull foi bem-sucedido:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Remover o artefato (opcional)

Para remover o artefato do registro de contêiner do Azure, use o comando [AZ ACR Repository Delete][az-acr-repository-delete] . O exemplo a seguir remove o artefato que você armazenou lá:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Exemplo: criar imagem do Docker do artefato de OCI

O código-fonte e os binários para criar uma imagem de contêiner podem ser armazenados como artefatos de OCI em um registro de contêiner do Azure. Você pode fazer referência a um artefato de origem como o contexto de compilação para uma [tarefa ACR](container-registry-tasks-overview.md). Este exemplo mostra como armazenar um Dockerfile como um artefato de OCI e, em seguida, fazer referência ao artefato para criar uma imagem de contêiner.

Por exemplo, crie um Dockerfile de uma linha:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Faça logon no registro de contêiner de destino.

```azurecli
az login
az acr login --name myregistry
```

Crie e envie um novo artefato de OCI para o registro de destino usando o `oras push` comando. Este exemplo define o tipo de mídia padrão para o artefato.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Execute o comando [AZ ACR Build](/cli/azure/acr#az-acr-build) para criar a imagem Hello-World usando o novo artefato como contexto de compilação:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a biblioteca oras](https://github.com/deislabs/oras/tree/master/docs), incluindo como configurar um manifesto para um artefato
* Visite o repositório de [artefatos de OCI](https://github.com/opencontainers/artifacts) para obter informações de referência sobre novos tipos de artefato



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
