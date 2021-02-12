---
title: Imagens de várias arquiteturas no registro
description: Use o registro de contêiner do Azure para criar, importar, armazenar e implantar imagens de várias arquiteturas (em vários arcos)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: f8467cd3108ae4faea9ecb4c9d9ae339f476c311
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012292"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Imagens de várias arquiteturas no registro de contêiner do Azure

Este artigo apresenta imagens de *várias arquiteturas* (*em vários arcos*) e como você pode usar os recursos do registro de contêiner do Azure para ajudar a criar, armazenar e usá-las. 

Uma imagem de vários arcos é um tipo de imagem de contêiner que pode combinar variantes para diferentes arquiteturas e, às vezes, para sistemas operacionais diferentes. Ao executar uma imagem com suporte a várias arquiteturas, os clientes do contêiner selecionarão automaticamente uma variante de imagem que corresponda ao seu sistema operacional e à arquitetura.

## <a name="manifests-and-manifest-lists"></a>Manifestos e listas de manifesto

Imagens de vários arcos são baseadas em manifestos de imagem e listas de manifesto.

### <a name="manifest"></a>Manifest

Cada imagem de contêiner é representada por um [manifesto](container-registry-concepts.md#manifest). Um manifesto é um arquivo JSON que identifica exclusivamente a imagem, referenciando suas camadas e seus tamanhos correspondentes. 

Um manifesto básico para uma imagem do Linux `hello-world` é semelhante ao seguinte:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Você pode exibir um manifesto no registro de contêiner do Azure usando o portal do Azure ou ferramentas como o comando [AZ ACR Repository show-manifestas](/cli/azure/acr/repository#az_acr_repository_show_manifests) no CLI do Azure.

### <a name="manifest-list"></a>Lista de manifestos

Uma *lista de manifestos* para uma imagem em vários arcos (conhecida de forma mais geral como um [índice de imagem](https://github.com/opencontainers/image-spec/blob/master/image-index.md) para imagens de OCI) é uma coleção (índice) de imagens e você cria uma especificando um ou mais nomes de imagem. Ele inclui detalhes sobre cada uma das imagens, como o sistema operacional e a arquitetura, o tamanho e o resumo do manifesto com suporte. A lista de manifestos pode ser usada da mesma maneira que um nome de imagem nos `docker pull` `docker run` comandos e. 

A `docker` CLI gerencia manifestos e listas de manifesto usando o comando de [manifesto do Docker](https://docs.docker.com/engine/reference/commandline/manifest/) .

> [!NOTE]
> Atualmente, o `docker manifest` comando e os subcomandos são experimentais. Consulte a documentação do Docker para obter detalhes sobre como usar comandos experimentais.

Você pode exibir uma lista de manifestos usando o `docker manifest inspect` comando. Veja a seguir a saída da imagem em vários arcos `mcr.microsoft.com/mcr/hello-world:latest` , que tem três manifestos: duas para arquiteturas do sistema operacional Linux e outra para uma arquitetura do Windows.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Quando uma lista de manifestos de vários arcos é armazenada no registro de contêiner do Azure, você também pode exibir a lista de manifestos usando o portal do Azure ou com ferramentas como o comando [AZ ACR Repository show-manifestas](/cli/azure/acr/repository#az_acr_repository_how_manifests) .

## <a name="import-a-multi-arch-image"></a>Importar uma imagem em vários arcos 

Uma imagem de vários arcos existente pode ser importada para um registro de contêiner do Azure usando o comando [AZ ACR Import](/cli/azure/acr#az_acr_import) . A sintaxe de importação de imagem é a mesma de uma imagem de arquitetura única. Como a importação de uma imagem de arquitetura única, a importação de uma imagem em vários arcos não usa comandos do Docker. 

Para obter detalhes, consulte [importar imagens de contêiner para um registro de contêiner](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Enviar por push uma imagem em vários arcos

Quando você tem fluxos de trabalho de compilação para criar imagens de contêiner para diferentes arquiteturas, siga estas etapas para enviar por push uma imagem em vários arcos para o registro de contêiner do Azure.

1. Marque e envie por push cada imagem específica da arquitetura para o registro de contêiner. O exemplo a seguir pressupõe duas arquiteturas do Linux: arm64 e amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Execute `docker manifest create` para criar uma lista de manifestos para combinar as imagens anteriores em uma imagem em vários arcos.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Envie por push o manifesto para o registro de contêiner usando `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Use o `docker manifest inspect` comando para exibir a lista de manifestos. Um exemplo de saída de comando é mostrado em uma seção anterior.

Depois de enviar por push o manifesto de vários arcos para o registro, trabalhe com a imagem em vários arcos da mesma maneira que faz com uma imagem de arquitetura única. Por exemplo, puxe a imagem usando `docker pull` e use os comandos [AZ ACR Repository](/cli/azure/acr/repository#az_acr_repository) para exibir marcas, manifestos e outras propriedades da imagem.

## <a name="build-and-push-a-multi-arch-image"></a>Criar e enviar por push uma imagem em vários arcos

Usando recursos de [tarefas ACR](container-registry-tasks-overview.md), você pode criar e enviar por push uma imagem em vários arcos para o registro de contêiner do Azure. Por exemplo, defina uma [tarefa de várias etapas](container-registry-tasks-multi-step.md) em um arquivo YAML que cria uma imagem em vários arcos do Linux.

O exemplo a seguir pressupõe que você tenha Dockerfiles separados para duas arquiteturas, arm64 e amd64. Ele cria e envia por push as imagens específicas da arquitetura e, em seguida, cria e envia um manifesto em vários arcos que tem a `latest` marca:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Próximas etapas

* Use [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) para criar imagens de contêiner para diferentes arquiteturas.
* Saiba mais sobre como criar imagens de várias plataformas usando o plug-in experimental do Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) .

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
