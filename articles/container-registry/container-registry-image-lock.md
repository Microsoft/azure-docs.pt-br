---
title: Bloquear imagens
description: Defina atributos para uma imagem de contêiner ou repositório para que ele não possa ser excluído ou substituído em um registro de contêiner do Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77659689"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloquear uma imagem de contêiner em um registro de contêiner do Azure

Em um registro de contêiner do Azure, você pode bloquear uma versão de imagem ou um repositório para que ele não possa ser excluído ou atualizado. Para bloquear uma imagem ou um repositório, atualize seus atributos usando o comando CLI do Azure [AZ ACR Repository Update][az-acr-repository-update]. 

Este artigo requer que você execute o CLI do Azure no Azure Cloud Shell ou localmente (versão 2.0.55 ou posterior recomendada). Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

> [!IMPORTANT]
> Este artigo não se aplica ao bloqueio de um registro inteiro, por exemplo, usando **configurações > bloqueios** no portal do Azure ou `az lock` comandos no CLI do Azure. O bloqueio de um recurso de registro não impede que você crie, atualize ou exclua dados em repositórios. O bloqueio de um registro afeta apenas as operações de gerenciamento, como adicionar ou excluir replicações, ou excluir o próprio registro. Mais informações em [Bloquear recursos para evitar alterações inesperadas](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Cenários

Por padrão, uma imagem marcada no registro de contêiner do Azure é *mutável*, portanto, com as permissões apropriadas, você pode atualizar repetidamente e enviar por push uma imagem com a mesma marca para um registro. As imagens de contêiner também podem ser [excluídas](container-registry-delete.md) conforme necessário. Esse comportamento é útil quando você desenvolve imagens e precisa manter um tamanho para o registro.

No entanto, ao implantar uma imagem de contêiner na produção, talvez você precise de uma imagem de contêiner *imutável* . Uma imagem imutável é aquela que você não pode excluir ou substituir acidentalmente.

Confira [recomendações para marcação e controle de versão de imagens de contêiner](container-registry-image-tag-version.md) para estratégias para marcas e imagens de versão no registro.

Use o comando [AZ ACR Repository Update][az-acr-repository-update] para definir atributos de repositório para que você possa:

* Bloquear uma versão de imagem ou um repositório inteiro

* Proteger uma versão de imagem ou repositório da exclusão, mas permitir atualizações

* Impedir operações de leitura (pull) em uma versão de imagem ou um repositório inteiro

Consulte as seções a seguir para obter exemplos. 

## <a name="lock-an-image-or-repository"></a>Bloquear uma imagem ou um repositório 

### <a name="show-the-current-repository-attributes"></a>Mostrar os atributos do repositório atual
Para ver os atributos atuais de um repositório, execute o comando [AZ ACR Repository show][az-acr-repository-show] a seguir:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostrar os atributos da imagem atual
Para ver os atributos atuais de uma marca, execute o comando [AZ ACR Repository show][az-acr-repository-show] a seguir:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloquear uma imagem por marca

Para bloquear a imagem *myrepositório/MyImage: tag* no *myregistry*, execute o seguinte comando [AZ ACR Repository Update][az-acr-repository-update] :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloquear uma imagem por Resumo do manifesto

Para bloquear uma imagem *myrepositório/MyImage* identificada pelo resumo do manifesto (SHA-256 hash, representado como `sha256:...` ), execute o comando a seguir. (Para localizar o resumo do manifesto associado a uma ou mais marcas de imagem, execute o comando [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests] .)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloquear um repositório

Para bloquear o repositório *myrepositório/MyImage* e todas as imagens, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteger uma imagem ou um repositório contra exclusão

### <a name="protect-an-image-from-deletion"></a>Proteger uma imagem contra exclusão

Para permitir que a imagem *myrepositório/MyImage: tag* seja atualizada, mas não excluída, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteger um repositório da exclusão

O comando a seguir define o repositório *myrepositório/MyImage* para que ele não possa ser excluído. As imagens individuais ainda podem ser atualizadas ou excluídas.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedir operações de leitura em uma imagem ou repositório

Para evitar operações de leitura (pull) na imagem *myrepositório/MyImage: tag* , execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Para evitar operações de leitura em todas as imagens no repositório *myrepositório/MyImage* , execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Desbloquear uma imagem ou um repositório

Para restaurar o comportamento padrão da imagem *myrepositório/MyImage: tag* para que ela possa ser excluída e atualizada, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Para restaurar o comportamento padrão do repositório *myrepositório/MyImage* e de todas as imagens para que eles possam ser excluídos e atualizados, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o comando [AZ ACR Repository Update][az-acr-repository-update] para impedir a exclusão ou a atualização de versões de imagem em um repositório. Para definir atributos adicionais, consulte a referência do comando [AZ ACR Repository Update][az-acr-repository-update] .

Para ver os atributos definidos para uma versão de imagem ou repositório, use o comando [AZ ACR Repository show][az-acr-repository-show] .

Para obter detalhes sobre as operações de exclusão, consulte [Excluir imagens de contêiner no registro de contêiner do Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

