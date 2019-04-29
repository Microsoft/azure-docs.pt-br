---
title: Bloquear uma imagem no registro de contêiner do Azure
description: Definir atributos para uma imagem de contêiner ou um repositório para que ele não pode ser excluído ou substituído em um registro de contêiner do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828640"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloquear uma imagem de contêiner em um registro de contêiner do Azure

Em um registro de contêiner do Azure, você pode bloquear uma versão de imagem ou um repositório para que ele não pode ser excluído ou atualizado. Para bloquear uma imagem ou um repositório, atualize seus atributos usando o comando de CLI do Azure [atualização do repositório de acr az][az-acr-repository-update]. 

Este artigo requer que você execute a CLI do Azure no Azure Cloud Shell ou localmente (versão 2.0.55 ou posterior recomendado). Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

## <a name="scenarios"></a>Cenários

Por padrão, é uma imagem marcada no registro de contêiner do Azure *mutável*, portanto, com permissões apropriadas repetidamente você pode atualizar e enviar por push uma imagem com a mesma marcação para um registro. Imagens de contêiner também podem ser [excluído](container-registry-delete.md) conforme necessário. Esse comportamento é útil quando você desenvolve imagens e precisa manter um tamanho para o registro.

No entanto, quando você implanta uma imagem de contêiner para a produção, talvez seja necessário um *imutável* imagem de contêiner. Uma imagem imutável é aquele que você não pode excluir ou substituir acidentalmente. Use o [atualização do repositório de acr az] [ az-acr-repository-update] comando para definir atributos de repository para que você possa:

* Bloquear uma versão de imagem ou todo o repositório

* Proteger uma versão de imagem ou um repositório de exclusão, mas permitir atualizações

* Impedir que as operações de leitura (pull) em uma versão de imagem ou de todo o repositório

Consulte as seções a seguir para obter exemplos.

## <a name="lock-an-image-or-repository"></a>Uma imagem ou um repositório de bloqueio 

### <a name="show-the-current-repository-attributes"></a>Exibir os atributos do repositório atual
Para ver os atributos atuais de um repositório, execute o seguinte [show do az acr repositório] [ az-acr-repository-show] comando:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Exibir os atributos da imagem atual
Para ver os atributos atuais de uma marca, execute o seguinte [show do az acr repositório] [ az-acr-repository-show] comando:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloquear uma imagem por marca

Para bloquear a *myrepo / myimage:tag* da imagem no *myregistry*, execute o seguinte [atualização do repositório de acr az] [ az-acr-repository-update] comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloquear uma imagem pelo manifesto digest

Para bloqueio de um *myrepo/myimage* imagem identificada pelo manifesto digest (hash de SHA-256, representado como `sha256:...`), execute o comando a seguir. (Para localizar o resumo de manifesto associado a uma ou mais marcas de imagem, execute as [show-manifestos do repositório de acr az] [ az-acr-repository-show-manifests] comando.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Um repositório de bloqueio

Para bloquear a *myrepo/myimage* repositório e todas as imagens, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteger uma imagem ou um repositório de exclusão

### <a name="protect-an-image-from-deletion"></a>Proteger uma imagem de exclusão

Para permitir que o *myrepo / myimage:tag* imagem ser atualizado, mas não excluída, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteger um repositório de exclusão

O seguinte comando define a *myrepo/myimage* repositório para que ele não pode ser excluído. Imagens individuais ainda podem ser atualizadas ou excluídas.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Evitar operações de leitura em uma imagem ou um repositório

Para impedir que as operações de leitura (pull) sobre o *myrepo / myimage:tag* imagem, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Para evitar operações de leitura em todas as imagens a *myrepo/myimage* repositório, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Desbloquear uma imagem ou um repositório

Para restaurar o comportamento padrão do *myrepo / myimage:tag* de imagem para que possa ser excluído e atualizado, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Para restaurar o comportamento padrão do *myrepo/myimage* repositório e todas as imagens para que possa ser excluídos e atualizados, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre como usar o [atualização do repositório de acr az] [ az-acr-repository-update] comando para impedir que a exclusão ou atualização de versões de imagem em um repositório. Para definir atributos adicionais, consulte o [atualização do repositório de acr az] [ az-acr-repository-update] referência do comando.

Para ver os atributos definido para uma versão de imagem ou um repositório, use o [show do az acr repositório] [ az-acr-repository-show] comando.

Para obter detalhes sobre as operações de exclusão, consulte [excluir imagens de contêiner no registro de contêiner do Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

