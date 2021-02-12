---
title: Identidades gerenciadas para o Azure
description: Saiba mais sobre como usar identidades gerenciadas para o Azure com Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881713"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Usando identidades gerenciadas para o Azure com Service Fabric

Um desafio comum ao criar aplicativos em nuvem é como gerenciar com segurança as credenciais em seu código para autenticação em vários serviços sem salvá-los localmente em uma estação de trabalho do desenvolvedor ou no controle do código-fonte. *Identidades gerenciadas para o Azure* resolvem esse problema para todos os seus recursos no Azure Active Directory (AD do Azure) fornecendo-lhes identidades gerenciadas automaticamente no Azure AD. Você pode usar a identidade de um serviço para se autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial armazenada em seu código.

*Identidades gerenciadas para recursos do Azure* são gratuitas com o Azure ad para assinaturas do Azure. Não há nenhum custo adicional.

> [!NOTE]
> *Identidades gerenciadas para o Azure* é o novo nome para o serviço anteriormente conhecido como identidade de serviço gerenciada (MSI).

## <a name="concepts"></a>Conceitos

Identidades gerenciadas para o Azure baseiam-se em vários conceitos principais:

- **ID do cliente** – um identificador exclusivo gerado pelo Azure AD que está vinculado a um aplicativo e uma entidade de serviço durante seu provisionamento inicial (também veja a [ID do aplicativo](../active-directory/develop/developer-glossary.md#application-id-client-id)).

- **ID da entidade de segurança** – a ID de objeto do objeto de entidade de serviço para sua identidade gerenciada que é usada para conceder acesso baseado em função a um recurso do Azure.

- **Entidade de serviço** -um objeto Azure Active Directory, que representa a projeção de um aplicativo AAD em um determinado locatário (também consulte a [entidade de serviço](../active-directory/develop/developer-glossary.md#service-principal-object).)

Há dois tipos de identidades gerenciadas:

- Uma **identidade gerenciada atribuída pelo sistema** é habilitada diretamente em uma instância de serviço do Azure.  O ciclo de vida de uma identidade atribuída pelo sistema é exclusivo para a instância de serviço do Azure em que está habilitada.
- Uma **identidade gerenciada atribuída pelo usuário** é criada como um recurso autônomo do Azure. A identidade pode ser atribuída a uma ou mais instâncias de serviço do Azure e é gerenciada separadamente dos ciclos de vida dessas instâncias.

Para entender ainda mais a diferença entre os tipos de identidade gerenciada, consulte [como funcionam as identidades gerenciadas dos recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

## <a name="supported-scenarios-for-service-fabric-applications"></a>Cenários com suporte para aplicativos Service Fabric

As identidades gerenciadas para Service Fabric só têm suporte em clusters de Service Fabric implantados pelo Azure e somente para aplicativos implantados como recursos do Azure; um aplicativo que não é implantado como um recurso do Azure não pode ser atribuído a uma identidade. Falando conceitualmente, o suporte para identidades gerenciadas em um cluster de Service Fabric do Azure consiste em duas fases:

1. Atribuir uma ou mais identidades gerenciadas ao recurso de aplicativo; um aplicativo pode receber uma única identidade atribuída pelo sistema e/ou até 32 identidades atribuídas pelo usuário, respectivamente.

2. Na definição do aplicativo, mapeie uma das identidades atribuídas ao aplicativo para qualquer serviço individual que compõe o aplicativo.

A identidade atribuída pelo sistema de um aplicativo é exclusiva para esse aplicativo; uma identidade atribuída pelo usuário é um recurso autônomo, que pode ser atribuído a vários aplicativos. Dentro de um aplicativo, uma única identidade (se atribuído pelo sistema ou atribuído pelo usuário) pode ser atribuída a vários serviços do aplicativo, mas cada serviço pode ser atribuído apenas a uma identidade. Por fim, um serviço deve ser atribuído explicitamente a uma identidade para ter acesso a esse recurso. Na verdade, o mapeamento das identidades de um aplicativo para seus serviços constituintes permite o isolamento no aplicativo — um serviço pode usar apenas a identidade mapeada para ele.  

Atualmente, os seguintes cenários têm suporte para esse recurso:

- Implantar um novo aplicativo com um ou mais serviços e uma ou mais identidades atribuídas

- Atribuir uma ou mais identidades gerenciadas a um aplicativo existente (implantado pelo Azure) para acessar os recursos do Azure

Os cenários a seguir não têm suporte ou não são recomendados; Observe que essas ações podem não ser bloqueadas, mas podem levar a interrupções em seus aplicativos:

- Remover ou alterar as identidades atribuídas a um aplicativo; Se você precisar fazer alterações, envie implantações separadas para primeiro adicionar uma nova atribuição de identidade e, em seguida, para remover uma anteriormente atribuída. A remoção de uma identidade de um aplicativo existente pode ter efeitos indesejáveis, incluindo deixar seu aplicativo em um estado que não seja atualizável. É seguro excluir o aplicativo completamente se a remoção de uma identidade for necessária; Observação isso excluirá a identidade atribuída pelo sistema (se for definida) associada ao aplicativo e removerá todas as associações com as identidades atribuídas ao usuário atribuídas ao aplicativo.

- Service Fabric suporte para identidades gerenciadas não está integrado no momento ao [o azureservicetokenprovider](/dotnet/api/overview/azure/service-to-service-authentication).

## <a name="next-steps"></a>Próximas etapas

- [Implantar um novo cluster de Service Fabric do Azure com suporte de identidade gerenciada](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Habilitar o suporte de identidade gerenciada em um cluster existente do Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
- [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)
- [Declarando e usando os segredos do aplicativo como KeyVaultReferences](./service-fabric-keyvault-references.md)