---
title: Montar um volume gitRepo em Instâncias de Contêiner do Azure
description: Saiba como montar um volume gitRepo para clonar um repositório Git em suas instâncias de contêiner
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: e8afa9e14941920cdcfb984e6660bdc666240716
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325434"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montar um volume gitRepo em Instâncias de Contêiner do Azure

Saiba como montar um volume *gitRepo* para clonar um repositório Git em suas instâncias de contêiner.

> [!NOTE]
> A montagem de um volume *gitRepo* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>Volume gitRepo

O volume *gitRepo* monta um diretório e clona para dentro dele o repositório Git especificado, na inicialização do contêiner. Usando um volume *gitRepo* em suas instâncias de contêiner, você pode evitar adicionar o código para fazer isso em seus aplicativos.

Quando você monta um volume *gitRepo*, você pode definir três propriedades para configurar o volume:

| Propriedade | Obrigatório | DESCRIÇÃO |
| -------- | -------- | ----------- |
| `repository` | Sim | A URL completa, incluindo `http://` ou `https://`, do repositório Git a ser clonado.|
| `directory` | Não | O diretório no qual o repositório deve ser clonado. O caminho não deve conter nem começar com "`..`".  Se você especificar "`.`", o repositório será clonado no diretório do volume. Caso contrário, o repositório do Git será clonado em um subdiretório com o nome fornecido, dentro do diretório do volume. |
| `revision` | Não | O hash de confirmação da revisão a ser clonada. Se não for especificado, a revisão `HEAD` será clonada. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Montar o volume gitRepo: CLI do Azure

Para montar um volume gitRepo ao implantar instâncias de contêiner com o [CLI do Azure](/cli/azure), forneça os `--gitrepo-url` parâmetros `--gitrepo-mount-path` e para o comando [AZ container Create][az-container-create] . Você pode opcionalmente especificar o diretório dentro do volume para clonar em (`--gitrepo-dir`) e o hash de confirmação da revisão a ser clonado (`--gitrepo-revision`).

Este exemplo de comando clona o aplicativo `/mnt/aci-helloworld` de exemplo Microsoft [ACI-HelloWorld][aci-helloworld] na instância de contêiner:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Para verificar se o volume gitRepo foi montado, inicie um shell no contêiner com [AZ container exec][az-container-exec] e liste o diretório:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montar o volume gitRepo: Gerenciador de Recursos

Para montar um volume gitRepo quando você implanta instâncias de contêiner com uma [modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), primeiro preencha a seção da `volumes` matriz no grupo de contêiner `properties` do modelo. Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar o volume *gitRepo*, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em um único contêiner. O contêiner clona dois repositórios do GitHub especificados pelos blocos de volume do *gitRepo*. O segundo volume inclui propriedades adicionais, especificando um diretório para o qual clonar e também o hash de confirmação de uma revisão específica a ser clonada.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A estrutura de diretório resultante dos dois repositórios clonados definida no modelo anterior é:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver um exemplo de implantação de instância de contêiner com um modelo do Azure Resource Manager, consulte [Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Autenticação do repositório Git privado

Para montar um volume gitRepo para um repositório Git privado, especifique as credenciais na URL do repositório. Normalmente, as credenciais estão na forma de um nome de usuário e um PAT (token de acesso pessoal) que concedem acesso com escopo ao repositório.

Por exemplo, o parâmetro `--gitrepo-url` da CLI do Azure para um repositório do GitHub privado deve assemelhar-se ao seguinte (em que "gituser" é o nome de usuário do GitHub e "abcdef1234fdsa4321abcdef" é o token de acesso pessoal do usuário):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Para um repositório do Azure Repos Git, especifique qualquer nome de usuário (você pode usar "azurereposuser" como no exemplo a seguir) em combinação com um PAT válido:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Para obter mais informações sobre tokens de acesso pessoal para o GitHub e o Azure Repos, consulte o seguinte:

GitHub [Criando um token de acesso pessoal para a linha de comando][pat-github]

Azure Repos: [Criar tokens de acesso pessoal para autenticar o acesso][pat-repos]

## <a name="next-steps"></a>Próximas etapas

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em Instâncias de Contêiner do Azure](container-instances-volume-emptydir.md)
* [Montar um volume secreto em Instâncias de Contêiner do Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
