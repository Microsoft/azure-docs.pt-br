---
title: Excluir recursos de imagem
description: Detalhes sobre como gerenciar efetivamente o tamanho do registro excluindo dados de imagem de contêiner usando comandos CLI do Azure.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78403340"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Excluir imagens de contêiner no registro de contêiner do Azure usando o CLI do Azure

Para manter o tamanho do registro do contêiner do Azure, exclua periodicamente os dados da imagem obsoletos. Embora algumas imagens de contêiner implantadas na produção possam exigir armazenamento de longo prazo, outras normalmente podem ser excluídas mais rapidamente. Por exemplo, em um cenário de criação e teste automatizado, o registro pode ser preenchido rapidamente com imagens que talvez nunca sejam implementadas e podem ser eliminadas logo após a conclusão da passagem de teste e de compilação.

Como você pode excluir dados de imagem de várias maneiras diferentes, é importante entender como cada operação de exclusão afeta o uso do armazenamento. Este artigo aborda vários métodos para excluir dados de imagem:

* Exclua um [repositório](#delete-repository): Exclui todas as imagens e todas as camadas exclusivas do repositório.
* Excluir pela [tag](#delete-by-tag): exclui uma imagem, a tag, todas as camadas exclusivas mencionadas pela imagem e todas as outras tags associadas à imagem.
* Exclua [digest manifesto](#delete-by-manifest-digest): exclui uma imagem, todas as camadas exclusivas referenciadas pela imagem e todas as marcas associadas à imagem.

Os scripts de exemplo são fornecidos para ajudar a automatizar operações de exclusão.

Para obter uma introdução a esses conceitos, consulte [sobre registros, repositórios e imagens](container-registry-concepts.md).

## <a name="delete-repository"></a>Excluir repositório

A exclusão de um repositório exclui todas as imagens no repositório, incluindo todas as tags, camadas exclusivas e manifestos. Ao excluir um repositório, você recupera o espaço de armazenamento usado pelas imagens que fazem referência a camadas exclusivas nesse repositório.

O seguinte comando da CLI do Azure exclui o repositório "acr-helloworld" e todas as tags e manifestos no repositório. Se as camadas referenciadas pelos manifestos excluídos não forem referenciadas por nenhuma outra imagem no registro, seus dados de camada também serão excluídos, recuperando o espaço de armazenamento.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Excluir por marca

Você pode excluir imagens individuais de um repositório especificando o nome e a marca do repositório na operação de exclusão. Quando você exclui por marca, recupera o espaço de armazenamento usado por qualquer camada exclusiva na imagem (camadas não compartilhadas por outras imagens no registro).

Para excluir por marca, use [exclusão de repositório de acr az][az-acr-repository-delete] e especifique o nome da imagem no`--image` parâmetro. Todas as camadas exclusivas da imagem e quaisquer outras tags associadas à imagem são excluídas.

Por exemplo, excluindo a imagem "acr-helloworld: latest" do registro "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> A exclusão de *pela tag* não deve ser confundida com a exclusão de uma tag (desmarcação). Você pode excluir uma marca com o comando da CLI do Azure [repositório do acr az desmarcar][az-acr-repository-untag]. Nenhum espaço é liberado ao se desmarcar uma imagem porque seu [manifesto](container-registry-concepts.md#manifest) e os dados da camada permanecem no registro. Somente a referência de marca em si é excluída.

## <a name="delete-by-manifest-digest"></a>Excluir pelo manifesto digest

Um [resumo do manifesto](container-registry-concepts.md#manifest-digest) pode ser associado a uma, nenhuma ou várias tags. Quando você exclui por digest, todas as tags referenciadas pelo manifesto são excluídas, assim como os dados da camada para qualquer camada exclusiva da imagem. Compartilhado a camada de dados não são excluídos.

Para excluir, digest, a primeira lista o manifesto resumos para o repositório que contém as imagens que você deseja excluir. Por exemplo:

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
  }
]
```

Em seguida, especifique o resumo da mensagem que deseja excluir na [exclusão de repositório de acr az][az-acr-repository-delete] comando. O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo, para excluir o último manifesto listado na saída anterior (com a tag "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

A `acr-helloworld:v2` imagem é excluída do registro, assim como qualquer dado de camada exclusivo para essa imagem. Se um manifesto é associado com várias marcas, todas as marcas associadas também são excluídas.

## <a name="delete-digests-by-timestamp"></a>Excluir resumos por carimbo de data/hora

Para manter o tamanho de um repositório ou registro, talvez seja necessário excluir periodicamente resumos de manifesto com mais de uma determinada data.

O comando CLI do Azure a seguir lista todos os resumos de manifesto em um repositório com mais de um carimbo de data/hora especificado, em ordem crescente. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente. O timestamp pode ser uma expressão de data e hora completa ou uma data, como neste exemplo.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Depois de identificar resumos de manifesto obsoletos, você pode executar o script bash a seguir para excluir resumos de manifesto mais antigos que um carimbo de data/hora especificado. Ele requer a CLI do Azure e **xargs**. Por padrão, o script não realiza nenhuma exclusão. Altere o `ENABLE_DELETE` valor para `true` para ativar a exclusão da imagem.

> [!WARNING]
> Use o exemplo de script a seguir com cuidado--os dados de imagem excluídos são irrecuperáveis. Se você tiver sistemas que extraem imagens por Resumo do manifesto (em oposição ao nome da imagem), você não deve executar esses scripts. Excluir os resumos do manifesto impedirá que esses sistemas extraiam as imagens do registro. Em vez de extrair pelo manifesto, considere a adoção de um esquema de *marcação exclusiva* esquema, uma [melhor prática recomendada](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Excluir imagens sem marcas

Conforme mencionado na [manifesto digest](container-registry-concepts.md#manifest-digest) seção, enviar por push uma imagem modificada usando uma marca existente **untags** a imagem anteriormente enviada, resultando em uma imagem órfão (ou "pendentes"). A imagem anteriormente enviada de manifesto – e seus dados de camada-- permanece no registro. Considere a seguinte sequência de eventos:

1. Enviar imagem por push *acr helloworld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr helloworld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modifique *acr helloworld* Dockerfile
1. Enviar imagem por push *acr helloworld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr helloworld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Como você pode ver na saída da última etapa na sequência, agora há um manifesto órfão cuja `"tags"` propriedade é uma lista vazia. Esse manifesto ainda existe no registro, junto com todos os dados de camada exclusivos que ele referencia. **Para excluir tais órfão imagens e seus dados de camada, você deve excluir digest manifesto**.

## <a name="delete-all-untagged-images"></a>Excluir todas as imagens não marcas

Você pode listar todas as imagens não identificadas em seu repositório usando o seguinte comando da CLI do Azure. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Usando esse comando em um script, você pode excluir todas as imagens não marcadas em um repositório.

> [!WARNING]
> Use os scripts de exemplo a seguir com cuidado – excluído dados de imagem são IRRECUPERÁVEL. Se você tiver sistemas que extraem imagens por Resumo do manifesto (em oposição ao nome da imagem), você não deve executar esses scripts. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de extrair pelo manifesto, considere a adoção de um esquema de *marcação exclusiva* esquema, uma [melhor prática recomendada](container-registry-image-tag-version.md).

**CLI do Azure no Bash**

O script Bash a seguir exclui todas as imagens não marcadas de um repositório. Ele requer a CLI do Azure e **xargs**. Por padrão, o script não realiza nenhuma exclusão. Altere o `ENABLE_DELETE` valor para `true` para ativar a exclusão da imagem.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**CLI do Azure no PowerShell**

O seguinte script do PowerShell exclui todas as imagens não identificadas de um repositório. Ele requer o PowerShell e a CLI do Azure. Por padrão, o script não realiza nenhuma exclusão. Altere o `$enableDelete` valor para `$TRUE` para ativar a exclusão da imagem.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Limpar automaticamente as marcas e os manifestos (versão prévia)

Como uma alternativa ao script CLI do Azure comandos, execute uma tarefa de ACR sob demanda ou agendada para excluir todas as marcas que são mais antigas do que uma determinada duração ou que correspondam a um filtro de nome especificado. Para obter mais informações, consulte [limpar automaticamente imagens de um registro de contêiner do Azure](container-registry-auto-purge.md).

Opcionalmente, defina uma [política de retenção](container-registry-retention-policy.md) para cada registro, para gerenciar os manifestos não marcados. Quando você habilita uma política de retenção, os manifestos de imagem no registro que não têm nenhuma marca associada e os dados de camada subjacentes são excluídos automaticamente após um período definido.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o armazenamento de imagens no Azure Container Registry, consulte [Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
