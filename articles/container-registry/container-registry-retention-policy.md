---
title: Política para reter manifestos não marcados
description: Saiba como habilitar uma política de retenção no registro de contêiner do Azure para exclusão automática de manifestos não marcados após um período definido.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 81ce92a2533cc8a688be43da9406cd5b0e726509
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781335"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Definir um política de retenção para manifestos não marcados

O Registro de Contêiner do Azure oferece a opção de definir uma *política de retenção* para manifestos de imagem armazenados que não têm marcas associadas (*manifestos não marcados*). Quando uma política de retenção é habilitada, os manifestos não marcados no registro são excluídos automaticamente após uma quantidade de dias definida por você. Esse recurso impede que o registro se preencha com artefatos que não são necessários e ajuda você a economizar em custos de armazenamento. Se o atributo `delete-enabled` de um manifesto não marcado for definido como `false`, o manifesto não poderá ser excluído e a política de retenção não se aplicará.

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para executar os exemplos de comando neste arquivo. Se você quer usá-lo localmente, é necessária a versão 2.0.74 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Uma política de retenção é um recurso **Premium** de registros de contêiner. Para obter informações sobre as camadas de serviço do Registro, consulte [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

> [!WARNING]
> Defina uma política de retenção com cuidado; os dados da imagem excluída são IRRECUPERÁVEIS. Se você tiver sistemas que obtêm imagens pelo resumo do manifesto (em oposição ao nome da imagem), não defina uma política de retenção para manifestos não marcados. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de receber pelo manifesto, considere adoção de um esquema de *marcação exclusiva*, uma [melhor prática recomendada](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Limitações de visualização

* Só é possível definir uma política de retenção para manifestos não marcados.
* No momento, a política de retenção se aplica somente a manifestos não marcados *depois* de a política ser habilitada. Os manifestos não marcados existentes no registro não estão sujeitos à política. Para excluir manifestos não marcados existentes, veja os exemplos em [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Sobre a política de retenção

O Registro de Contêiner do Azure faz referência à contagem de manifestos no registro. Quando um manifesto é desmarcado, ele marca a política de retenção. Se uma política de retenção for habilitada, uma operação de exclusão de manifesto será enfileirada, com uma data específica, de acordo com o número de dias definido na política.

Um trabalho de gerenciamento de filas separado processa mensagens constantemente, dimensionando conforme necessário. Por exemplo, suponhamos que você não marcou dois manifestos, com 1 hora de diferença, em um registro com uma política de retenção de 30 dias. Duas mensagens seriam enfileiradas. Então, 30 dias depois, com aproximadamente 1 hora de diferença, as mensagens seriam recuperadas da fila e processadas, supondo que a política ainda estava em vigor.

## <a name="set-a-retention-policy---cli"></a>Definir uma política de retenção - CLI

O exemplo a seguir mostra como usar a CLI do Azure para definir uma política de retenção para manifestos não marcados em um registro.

### <a name="enable-a-retention-policy"></a>Habilitar uma política de retenção

Por padrão, nenhuma política de retenção é definida em um registro de contêiner. Para definir ou atualizar uma política de retenção, execute o comando [az acr config retention update][az-acr-config-retention-update] na CLI do Azure. Especifique uma quantidade de dias entre 0 e 365 para manter os manifestos não marcados. Se você não especificar uma quantidade de dias, o comando definirá um padrão de 7 dias. Após o período de retenção, todos os manifestos não marcados no registro são excluídos automaticamente.

O exemplo a seguir define uma política de retenção de 30 dias para manifestos não marcados no registro *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

O exemplo a seguir define uma política para excluir todos os manifestos no registro assim que eles foram desmarcados. Crie essa política definindo um período de retenção de 0 dias. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Validar uma política de retenção

Se você habilitar a política anterior com um período de retenção de 0 dias, poderá verificar rapidamente se os manifestos não marcados são excluídos:

1. Envie uma imagem de teste `hello-world:latest` para o registro ou substitua outra imagem de teste de sua escolha.
1. Desmarque imagem `hello-world:latest`, por exemplo, usando o comando [az acr repository untag][az-acr-repository-untag]. O manifesto não marcado permanece no registro.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Em alguns segundos, o manifesto não marcado será excluído. Verifique a exclusão listando os manifestos no repositório, por exemplo, usando o comando [az acr repository show-manifests][az-acr-repository-show-manifests]. Se a imagem de teste foi a única no repositório, o repositório em si será excluído.

### <a name="disable-a-retention-policy"></a>Desabilitar uma política de retenção

Para ver a política de retenção definida em um registro, execute o comando [az acr config retention show][az-acr-config-retention-show]:

```azurecli
az acr config retention show --registry myregistry
```

Para desabilitar uma política de retenção em um registro, execute o comando [az acr config retention update][az-acr-config-retention-update] e defina `--status disabled`:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Definir uma política de retenção - portal

Também é possível definir a política de retenção de um registro no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como usar o portal para definir uma política de retenção para manifestos não marcados em um registro.

### <a name="enable-a-retention-policy"></a>Habilitar uma política de retenção

1. Navegue até o registro de contêiner do Azure. Em **Políticas**, selecione **Retenção** (versão prévia).
1. Em **Status**, selecione **Habilitado**.
1. Selecione uma quantidade de dias entre 0 e 365 para manter os manifestos não marcados. Clique em **Salvar**.

![Habilitar uma política de retenção no portal do Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Desabilitar uma política de retenção

1. Navegue até o registro de contêiner do Azure. Em **Políticas**, selecione **Retenção** (versão prévia).
1. Em **Status**, selecione **Desabilitado**. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as opções para [excluir imagens e repositórios](container-registry-delete.md) no Registro de Contêiner do Azure

* Saiba como [limpar automaticamente](container-registry-auto-purge.md) imagens e manifestos selecionados de um registro

* Saiba mais sobre as opções para [bloquear imagens e manifestos](container-registry-image-lock.md) em um registro

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
