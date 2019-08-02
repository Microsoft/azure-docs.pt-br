---
title: Autenticar no Registro de Contêiner do Azure por meio das Instâncias de Contêiner do Azure
description: Saiba como fornecer acesso a imagens em seu registro de contêiner particular das Instâncias de Contêiner do Azure usando uma entidade de serviço do Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: d2099de9ad909b23b79a92a831d7730b1cf126e3
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311632"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar no Registro de Contêiner do Azure por meio das Instâncias de Contêiner do Azure

Use uma entidade de serviço do Azure AD (Azure Active Directory) para fornecer acesso aos seus registros de contêineres particulares no Registro de Contêiner do Azure.

Neste artigo, você aprende a criar e configurar uma entidade de serviço do Azure AD com permissões de *pull* no registro. Em seguida, você inicia um contêiner no ACI (Instâncias de Contêiner do Azure), que efetua pull de sua imagem por meio de seu registro particular, usando a entidade de serviço para autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para a autenticação no ACI em **cenários “sem periféricos”** , como em aplicativos ou serviços que criam instâncias de contêiner de maneira automatizada ou autônoma.

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
* [Autenticar com o Registro de Contêiner do Azure do Serviço de Kubernetes do Azure (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
