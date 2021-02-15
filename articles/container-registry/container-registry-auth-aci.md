---
title: Acesso de instâncias de contêiner
description: Saiba como fornecer acesso a imagens em seu registro de contêiner particular das Instâncias de Contêiner do Azure usando uma entidade de serviço do Azure Active Directory.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74456512"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar no Registro de Contêiner do Azure por meio das Instâncias de Contêiner do Azure

Use uma entidade de serviço do Azure AD (Azure Active Directory) para fornecer acesso aos seus registros de contêineres particulares no Registro de Contêiner do Azure.

Neste artigo, você aprende a criar e configurar uma entidade de serviço do Azure AD com permissões de *pull* no registro. Em seguida, você inicia um contêiner no ACI (Instâncias de Contêiner do Azure), que efetua pull de sua imagem por meio de seu registro particular, usando a entidade de serviço para autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para a autenticação no ACI em **cenários “sem periféricos”**, como em aplicativos ou serviços que criam instâncias de contêiner de maneira automatizada ou autônoma.

Por exemplo, se você tem um script automatizado que é executado à noite e que cria uma [instância de contêiner baseada em tarefa](../container-instances/container-instances-restart-policy.md) para processar alguns dados, ele pode usar uma entidade de serviço com permissões somente pull para se autenticar no registro. Em seguida, você pode girar as credenciais da entidade de serviço ou revogar o acesso por completo, sem afetar outros serviços e aplicativos.

As entidades de serviço também devem ser usadas quando o registro [usuário administrador](container-registry-authentication.md#admin-account) está desabilitado.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autenticar usando a entidade de serviço

Para iniciar um contêiner em Instâncias de Contêiner do Azure usando uma entidade de serviço, especifique sua ID para `--registry-username` e sua senha para `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Scripts de exemplo

Você pode encontrar os scripts de exemplo anteriores para a CLI do Azure no GitHub, bem como versões do Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [PowerShell do Azure][acr-scripts-psh]

## <a name="next-steps"></a>Próximas etapas

Os seguintes artigos contêm mais detalhes sobre como trabalhar com entidades de serviço e o ACR:

* [Autenticação do Registro de Contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md)
* [Autenticar com o Registro de Contêiner do Azure do Serviço de Kubernetes do Azure (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
