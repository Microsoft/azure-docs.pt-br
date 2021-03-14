---
title: Sobre registros, repositórios, imagens e artefatos
description: Introdução aos principais conceitos de registros de contêiner do Azure, repositórios, imagens de contêiner e outros artefatos.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578120"
---
# <a name="about-registries-repositories-and-artifacts"></a>Sobre registros, repositórios e artefatos

Este artigo apresenta os principais conceitos de registros de contêiner, repositórios e imagens de contêiner e artefatos relacionados. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registro, repositórios e artefatos":::

## <a name="registry"></a>Registro

Um *registro* de contêiner é um serviço que armazena e distribui imagens de contêiner e artefatos relacionados. O Hub do Docker é um exemplo de um registro de contêiner público que serve como um catálogo geral de imagens de contêiner do Docker. O registro de contêiner do Azure fornece aos usuários o controle direto de seu conteúdo de contêiner, com autenticação integrada, [replicação geográfica](container-registry-geo-replication.md) que dá suporte à distribuição global e confiabilidade para implantações de fechamento de rede, [configuração de rede virtual com link privado](container-registry-private-link.md), [bloqueio de marca](container-registry-image-lock.md)e muitos outros recursos avançados. 

Além das imagens de contêiner compatíveis com o Docker, o registro de contêiner do Azure dá suporte a uma variedade de [artefatos de conteúdo](container-registry-image-formats.md) , incluindo gráficos de Helm e formatos de imagem de OCI (Open container Initiative).

## <a name="repository"></a>Repositório

Um *repositório* é uma coleção de imagens de contêiner ou outros artefatos em um registro que têm o mesmo nome, mas marcas diferentes. Por exemplo, as três imagens a seguir estão no `acr-helloworld` repositório:

- *ACR-HelloWorld: Latest*
- *ACR-HelloWorld: v1*
- *ACR-HelloWorld: v2*

Também podem incluir nomes de repositório [namespaces](container-registry-best-practices.md#repository-namespaces). Os namespaces permitem que você identifique os repositórios relacionados e a propriedade do artefato em sua organização usando nomes delimitados por barra. No entanto, o registro gerencia todos os repositórios de forma independente, não como uma hierarquia. Por exemplo: 

- *Marketing/campaign10-18/Web: v2*
- *Marketing/campaign10-18/API: V3*
- *Marketing/campaign10-18/email-remetente: v2*
- *retorno de produto/envio da Web: 20180604*
- *produto-Returns/Legacy-Integrator: 20180715*

Os nomes de repositórios só podem incluir caracteres alfanuméricos minúsculos, pontos, traços, sublinhados e barras. 

Para obter as regras de nomenclatura completas do repositório, consulte a [especificação de distribuição da iniciativa Open container](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Artefato

Uma imagem de contêiner ou outro artefato dentro de um registro é associado a uma ou mais marcas, tem uma ou mais camadas e é identificado por um manifesto. Entender como esses componentes se relacionam entre si pode ajudá-lo a gerenciar seu registro com eficiência.

### <a name="tag"></a>Marca

A *marca* para uma imagem ou outro artefato especifica sua versão. Um único artefato dentro de um repositório pode ser atribuído a uma ou várias marcas e também pode ser "não marcado". Ou seja, você pode excluir todas as marcas de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registro.

O repositório (ou repositório e namespace) mais uma tag define o nome de uma imagem. Você pode empurrar e puxar uma imagem especificando seu nome na operação push ou pull. A marca `latest` será usada por padrão se você não fornecer um nos comandos do Docker.

A maneira como você marca as imagens de contêiner é guiada por seus cenários para desenvolver ou implantá-las. Por exemplo, marcas estáveis são recomendadas para manter suas imagens base e marcas exclusivas para a implantação de imagens. Para obter mais informações, consulte [recomendações para marcação e controle de versão de imagens de contêiner](container-registry-image-tag-version.md).

Para regras de nomenclatura de marca, consulte a [documentação do Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Camada

As imagens de contêiner e os artefatos são compostos por uma ou mais *camadas*. Diferentes tipos de artefato definem camadas de maneira diferente. Por exemplo, em uma imagem de contêiner do Docker, cada camada corresponde a uma linha no Dockerfile que define a imagem:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Camadas de uma imagem de contêiner":::

Os artefatos em um registro compartilham camadas comuns, aumentando a eficiência do armazenamento. Por exemplo, várias imagens em repositórios diferentes podem ter uma camada de base de ASP.NET Core comum, mas apenas uma cópia dessa camada é armazenada no registro. O compartilhamento de camada também otimiza a distribuição de camadas para nós, com vários artefatos compartilhando camadas comuns. Se uma imagem que já está em um nó incluir a camada de ASP.NET Core como sua base, a extração subsequente de uma imagem diferente que faz referência à mesma camada não transferirá a camada para o nó. Em vez disso, ele faz referência à camada já existente no nó.

Para fornecer isolamento seguro e proteção contra possíveis manipulações de camada, as camadas não são compartilhadas entre registros.

### <a name="manifest"></a>Manifest

Cada imagem de contêiner ou artefato enviado por push para um registro de contêiner é associado a um *manifesto*. O manifesto, gerado pelo registro quando o conteúdo é enviado por push, identifica exclusivamente os artefatos e especifica as camadas. Você pode listar os manifestos para um repositório com o comando de CLI do Azure [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests]. 

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

Você pode listar os manifestos de um repositório com o comando da CLI do Azure [az acr repository show-manifests ][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, liste os manifestos para o repositório "ACR-HelloWorld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Resumo do manifesto

Manifestos são identificados por um único hash SHA-256, ou *manifesto digest*. Cada imagem ou artefato – se marcado ou não, é identificado por seu resumo. O valor de Digest é exclusivo mesmo que os dados da camada do artefato sejam idênticos aos de outro artefato. Esse mecanismo é o que permite que você envie repetidamente imagens com tags idênticas para um registro. Por exemplo, você pode enviar repetidamente `myimage:latest` para o seu registro sem erro, porque cada imagem é identificada pelo seu resumo único.

Você pode extrair um artefato de um registro especificando seu resumo na operação de pull. Alguns sistemas podem ser configurados para efetuar pull por Resumo porque garantem a versão da imagem que está sendo puxada, mesmo que uma imagem marcada de forma idêntica seja enviada posteriormente ao registro.

> [!IMPORTANT]
> Se você enviar por push artefatos modificados com marcas idênticas, você poderá criar "órfãos" – artefatos que não são marcados, mas que ainda consomem espaço no registro. Imagens não marcadas não são mostradas na CLI do Azure ou no portal do Azure quando você lista ou exibe imagens por marca. No entanto, suas camadas ainda existem e consomem espaço no seu registro. A exclusão de uma imagem não marcada libera o espaço do registro quando o manifesto é o único, ou o último, apontando para uma camada específica. Para obter informações sobre como liberar espaço usado por imagens não marcadas, consulte [Excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Endereçando um artefato

Para resolver um artefato do registro para operações de push e pull com o Docker ou outras ferramentas de cliente, combine o nome totalmente qualificado do registro, o nome do repositório (incluindo o caminho do namespace, se aplicável) e uma marca de artefato ou resumo do manifesto. Consulte as seções anteriores para obter explicações desses termos.

  **Endereço por marca**: `[loginServerUrl]/[repository][:tag]`
    
  **Endereço por Resumo**: `[loginServerUrl]/[repository@sha256][:digest]`  

Ao usar o Docker ou outras ferramentas de cliente para efetuar pull ou enviar artefatos por push a um registro de contêiner do Azure, use a URL totalmente qualificada do registro, também chamada de nome do *servidor de logon* . Na nuvem do Azure, a URL totalmente qualificada de um registro de contêiner do Azure está no formato `myregistry.azurecr.io` (todas as letras minúsculas).

> [!NOTE]
> * Você não pode especificar um número de porta na URL do servidor de logon do registro, como `myregistry.azurecr.io:443` . 
> * A marca `latest` será usada por padrão se você não fornecer uma marca em seu comando.  

   
### <a name="push-by-tag"></a>Push por marca

Exemplos: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Efetuar pull por marca

Exemplo: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Extrair por Resumo do manifesto


Exemplo:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [armazenamento do registro](container-registry-storage.md) e os [formatos de conteúdo com suporte](container-registry-image-formats.md) no registro de contêiner do Azure.

Saiba como [enviar por push e](container-registry-get-started-docker-cli.md) efetuar pull de imagens do registro de contêiner do Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


