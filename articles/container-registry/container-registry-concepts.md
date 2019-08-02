---
title: Sobre repositórios e imagens no registro de contêiner do Azure
description: Introdução aos principais conceitos de registros de contêiner do Azure, repositórios e imagens de contêiner.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800322"
---
# <a name="about-registries-repositories-and-images"></a>Sobre registros, repositórios e imagens

Este artigo apresenta os principais conceitos de registros de contêiner, repositórios e imagens de contêiner e artefatos relacionados. 

## <a name="registry"></a>Registro

Um registro de *contêiner* é um serviço que armazena e distribui imagens de contêiner. Hub do docker é um registro de contêiner público que dá suporte à comunidade de software livre e serve como um catálogo geral de imagens. O registro de contêiner do Azure fornece aos usuários controle direto de suas imagens, com a autenticação integrada, [replicação geográfica](container-registry-geo-replication.md) que dão suporte a distribuição global e a confiabilidade para implantações de fechamento de rede, [ configuração de firewall e rede virtual](container-registry-vnet.md), [marca bloqueio](container-registry-image-lock.md), e muitos outros recursos aprimorados. 

Além de imagens de contêiner do Docker, registro de contêiner do Azure suporta relacionados [artefatos de conteúdo](container-registry-image-formats.md) incluindo formatos de imagem de contêiner OCI (iniciativa aberto).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementos de conteúdo endereçável de um artefato

O endereço de um artefato em um registro de contêiner do Azure inclui os seguintes elementos. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -o nome totalmente qualificado do host do registro. O host de registro em um registro de contêiner do Azure está no formato *myregistry*. azurecr.io (todas as letras minúsculas). Você deve especificar o loginUrl ao usar o Docker ou outras ferramentas de cliente para artefatos de pull ou push para um registro de contêiner do Azure. 
* **namespace** - delimitado por barra "/" agrupamento lógico de imagens relacionadas ou artefatos - por exemplo, para um grupo de trabalho ou aplicativo
* **artefato** -o nome de um repositório para uma imagem em particular ou artefato
* **marca** -uma versão específica de uma imagem ou armazenados em um repositório de artefato


Por exemplo, o nome completo de uma imagem em um registro de contêiner do Azure pode parecer com:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Consulte as seções a seguir para obter detalhes sobre esses elementos.

## <a name="repository-name"></a>Nome do repositório

Gerenciar registros de contêiner *repositórios*, coleções de imagens de contêiner ou outros artefatos com o mesmo nome, mas as marcas diferentes. Por exemplo, as três imagens a seguir estão no repositório "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Também podem incluir nomes de repositório [namespaces](container-registry-best-practices.md#repository-namespaces). Namespaces permitem que você para agrupar imagens usando nomes de repositório avanço delimitada por barra "/", por exemplo:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Uma imagem de contêiner ou outro artefato dentro de um registro associado a uma ou mais marcas, tem uma ou mais camadas e é identificado por um manifesto. Noções básicas sobre como esses componentes se relacionam entre si pode ajudá-lo a gerenciar seu registro com eficiência.

### <a name="tag"></a>Marca

O *marca* para uma imagem ou outro artefato especifica sua versão. Um único artefato em um repositório pode ser atribuído a uma ou várias marcas e também pode ser "sem marcas". Ou seja, você pode excluir todas as marcas de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registro.

O repositório (ou repositório e namespace) mais uma tag define o nome de uma imagem. Você pode empurrar e puxar uma imagem especificando seu nome na operação push ou pull.

Como marcar imagens de contêiner é orientado pelos seus cenários para desenvolver ou implantá-los. Por exemplo, marcas estáveis são recomendadas para manter as imagens base e marcas exclusivas para a implantação de imagens. Para obter mais informações, consulte [recomendações para as imagens de contêiner de marcação e controle de versão](container-registry-image-tag-version.md).

### <a name="layer"></a>Camada

Imagens de contêiner são compostas de um ou mais *camadas*, cada um correspondendo a uma linha no Dockerfile que define a imagem. As imagens em um registro compartilham camadas comuns, aumentando a eficiência do armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada base de Linux Alpine, mas apenas uma cópia dessa camada é armazenada no registro.

Também é o compartilhamento de camada otimiza a distribuição de camada para nós com várias camadas comuns de compartilhamento de imagens. Por exemplo, se uma imagem já está em um nó inclui a camada de Linux Alpine como sua base, o pull subsequentes de uma imagem diferente referenciando a mesma camada não transfere a camada para o nó. Em vez disso, ele faz referência à camada já existente no nó.

Para fornecer um isolamento seguro e proteção contra potenciais manipulação de camada, camadas não são compartilhadas entre os registros.

### <a name="manifest"></a>Manifesto

Cada imagem de contêiner ou artefato enviados por push para um registro de contêiner está associado com um *manifesto*. O manifesto, gerado pelo registro quando a imagem é enviada, identifica de forma exclusiva a imagem e especifica suas camadas. Você pode listar os manifestos para um repositório com o comando de CLI do Azure [show-manifestos do repositório de acr az][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, lista de que resumos de manifesto para o repositório "acr-helloworld":

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

Manifestos são identificados por um único hash SHA-256, ou *manifesto digest*. Cada imagem ou artefato – se marcadas ou não – é identificado por seu resumo. O valor de resumo é exclusivo, mesmo se os dados da camada da imagem forem idênticos aos de outra imagem. Esse mecanismo é o que permite que você envie repetidamente imagens com tags idênticas para um registro. Por exemplo, você pode enviar repetidamente `myimage:latest` para o seu registro sem erro, porque cada imagem é identificada pelo seu resumo único.

Você pode receber uma imagem de um registro, especificando seu resumo na operação de pull. Alguns sistemas podem ser configurados para extrair por digest, pois ela garante que a versão da imagem que está sendo extraída, mesmo se uma imagem de forma idêntica marcada subsequentemente é enviada por push ao registro.

Por exemplo, receber uma imagem do repositório "acr-helloworld" por digest manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se você enviar repetidamente imagens modificadas com marcas idênticas, poderá criar imagens órfãs - imagens sem tag, mas que ainda consomem espaço no registro. Imagens não marcadas não são mostradas na CLI do Azure ou no portal do Azure quando você lista ou exibe imagens por marca. No entanto, suas camadas ainda existem e consomem espaço no seu registro. Para obter informações sobre a liberação de espaço usado por imagens sem marcas, consulte [excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [armazenamento de imagens](container-registry-storage.md) e [suporte para formatos de conteúdo](container-registry-image-formats.md) no registro de contêiner do Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


