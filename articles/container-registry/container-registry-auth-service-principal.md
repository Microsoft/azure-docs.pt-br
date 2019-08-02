---
title: Autenticação do Registro de Contêiner do Azure com entidades de serviço
description: Fornecer acesso a imagens em seu registro de contêiner particular usando uma entidade de serviço do Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 97c45a009b155eea7bc61a9dd337090b9e3c1b42
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309956"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do Registro de Contêiner do Azure com entidades de serviço

Use uma entidade de serviço do Azure AD (Azure Active Directory) para fornecer à imagem de contêiner o acesso `docker push` e `pull` ao seu registro de contêiner. Usando uma entidade de serviço, você pode fornecer acesso a serviços e aplicativos “sem periféricos”.

## <a name="what-is-a-service-principal"></a>O que é uma entidade de serviço?

As *entidades de serviço* do Azure AD fornecem acesso aos recursos do Azure em sua assinatura. Você pode considerar uma entidade de serviço como uma identidade do usuário de um serviço, em que o “serviço” é qualquer aplicativo, serviço ou plataforma que precisa de acesso aos recursos. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas nos recursos especificados. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

No contexto do Registro de Contêiner do Azure, você pode criar uma entidade de serviço do Microsoft Azure Active Directory com permissões de pull, push e pull ou proprietário em seu Registro particular no Azure. Para obter uma lista completa de funções, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar uma entidade de serviço?

Usando uma entidade de serviço do Azure AD, você pode fornecer acesso com escopo ao seu registro de contêiner particular. Você pode criar entidades de serviço diferentes para cada um dos aplicativos ou serviços, cada uma com direitos de acesso adaptadas ao registro. Já que você pode evitar o compartilhamento de credenciais entre serviços e aplicativos, você pode girar as credenciais ou revogar o acesso somente à entidade de serviço (e, portanto, ao aplicativo) de sua escolha.

Por exemplo, o aplicativo Web pode usar uma entidade de serviço que fornece somente o acesso `pull` à imagem, enquanto o sistema de build pode usar uma entidade de serviço que fornece o acesso `push` e `pull`. Se o desenvolvimento de seu aplicativo for transferido para outra pessoa, você poderá girar as credenciais da entidade de serviço sem afetar o sistema de build.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para fornecer o acesso de registro em **cenários “sem periféricos”** . Em outras palavras, qualquer aplicativo, serviço ou script que precisa enviar por push ou efetuar pull de imagens de contêiner de maneira automatizada ou autônoma.

Para acesso individual a um registro, como quando você efetua pull manualmente de uma imagem de contêiner para sua estação de trabalho de desenvolvimento, você deve usar sua própria [identidade do Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) para acesso ao registro (por exemplo, com [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Scripts de exemplo

Você pode encontrar os scripts de exemplo anteriores para a CLI do Azure no GitHub, bem como versões do Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [PowerShell do Azure][acr-scripts-psh]

## <a name="next-steps"></a>Próximas etapas

Quando você tiver uma entidade de serviço para a qual você permitiu o acesso ao registro de contêiner, poderá usar suas credenciais nos aplicativos e nos serviços para interação remota com o registro. Use as credenciais da entidade de serviço de qualquer serviço do Azure que possa se autenticar em um registro de contêiner do Azure. Os exemplos incluem:

* [Autenticar com o Registro de Contêiner do Azure do Serviço de Kubernetes do Azure (AKS)](container-registry-auth-aks.md)
* [Autenticar no Registro de Contêiner do Azure por meio do ACI (Instâncias de Contêiner do Azure)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
