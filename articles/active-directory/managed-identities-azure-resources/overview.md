---
title: Identidades gerenciadas dos recursos do Azure
description: Uma visão geral das identidades gerenciadas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/05/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c49ded056c642fa91331b7cc98d18da34d9c73a6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504338"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>O que são identidades gerenciadas para recursos do Azure?

Um desafio comum para desenvolvedores é o gerenciamento de segredos e credenciais usadas para proteger a comunicação entre os diferentes componentes que formam uma solução. As identidades gerenciadas eliminam a necessidade de os desenvolvedores gerenciarem credenciais. As identidades gerenciadas fornecem uma identidade para os aplicativos usarem ao se conectarem a recursos que dão suporte à autenticação do Azure AD (Azure Active Directory). Os aplicativos podem usar a identidade gerenciada para obter tokens do Azure AD. Por exemplo, um aplicativo pode usar uma identidade gerenciada para acessar recursos como [Azure Key Vault](../../key-vault/general/overview.md), em que os desenvolvedores podem armazenar credenciais de maneira segura ou acessar contas de armazenamento.

Para que uma identidade gerenciada pode ser usada?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Aqui estão alguns dos benefícios de usar identidades gerenciadas:

- Você não precisa gerenciar credenciais. As credenciais nem sequer estão acessíveis para você.
- Você pode usar identidades gerenciadas para se autenticar em qualquer recurso que dê suporte à [autenticação do Azure Active Directory](../authentication/overview-authentication.md), incluindo seus próprios aplicativos.
- Identidades gerenciadas podem ser usadas sem nenhum custo adicional.

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="managed-identity-types"></a>Tipos de identidade gerenciada

Há dois tipos de identidades gerenciadas:

- **Atribuídas pelo sistema** Alguns serviços do Azure permitem que você habilite uma identidade gerenciada diretamente em uma instância de serviço. Quando você habilita uma identidade gerenciada atribuída pelo sistema, uma identidade é criada no Azure AD que está vinculada ao ciclo de vida dessa instância de serviço. Assim, quando o recurso é excluído, o Azure exclui automaticamente a identidade para você. Por design, somente o recurso do Azure pode usar essa identidade para solicitar tokens do Azure AD.
- **Atribuídas pelo usuário** Você também pode criar uma identidade gerenciada como um recurso autônomo do Azure. Você pode [criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-portal.md) e atribuí-la a uma ou mais instâncias de um serviço do Azure. No caso de identidades gerenciadas atribuídas pelo usuário, a identidade é gerenciada separadamente dos recursos que a usam. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

A tabela abaixo mostra as diferenças entre os dois tipos de identidades gerenciadas.

|  Propriedade    | Identidade gerenciada atribuída pelo sistema | Identidade gerenciada atribuída pelo usuário |
|------|----------------------------------|--------------------------------|
| Criação |  Criada como parte de um recurso do Azure (por exemplo, uma máquina virtual do Azure ou o Serviço de Aplicativo do Azure) | Criada como um recurso independente do Azure |
| Ciclo de vida | Ciclo de vida compartilhado com o recurso do Azure com o qual a identidade gerenciada é criada. <br/> Quando o recurso pai é excluído, a identidade gerenciada também é excluída. | Ciclo de vida independente. <br/> Precisa ser explicitamente excluída. |
| Compartilhamento entre recursos do Azure | Não pode ser compartilhada. <br/> Só pode ser associada a um único recurso do Azure. | Pode ser compartilhada <br/> A mesma identidade gerenciada atribuída pelo usuário pode ser associada a mais de um recurso do Azure. |
| Casos de uso comuns | Cargas de trabalho que estão contidas em um único recurso do Azure <br/> Cargas de trabalho para as quais você precisa de identidades independentes. <br/> Por exemplo, um aplicativo que é executado em uma única máquina virtual | Cargas de trabalho que são executadas em vários recursos e que podem compartilhar uma única identidade. <br/> Cargas de trabalho que precisam de pré-autorização para um recurso seguro como parte de um fluxo de provisionamento. <br/> Cargas de trabalho em que os recursos são reciclados com frequência, mas as permissões devem permanecer consistentes. <br/> Por exemplo, uma carga de trabalho em que várias máquinas virtuais precisam acessar o mesmo recurso |

>[!IMPORTANT]
>Independentemente do tipo de identidade escolhida, a identidade gerenciada é uma entidade de serviço de um tipo especial que só pode ser usado com recursos do Azure. Quando a identidade gerenciada é excluída, a entidade de serviço correspondente é removida automaticamente.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Como usar identidades gerenciadas para recursos do Azure?

![Alguns exemplos de como um desenvolvedor pode usar identidades gerenciadas para obter acesso a recursos do código deles sem gerenciar informações de autenticação](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>O recurso tem suporte de quais serviços do Azure?<a name="which-azure-services-support-managed-identity"></a>

As identidades gerenciadas para recursos do Azure podem ser usadas para autenticar os serviços que são compatíveis com a autenticação do Azure AD. Para obter uma lista de serviços do Azure que dão suporte para identidades gerenciadas para a funcionalidade de recursos do Azure, veja [Serviços que dão suporte a identidades gerenciadas para recursos do Azure](./services-support-managed-identities.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Resource Manager](tutorial-linux-vm-access-arm.md)
* [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../../app-service/overview-managed-identity.md)
* [Como usar identidades gerenciadas com Instâncias de Contêiner do Azure](../../container-instances/container-instances-managed-identity.md)
* [Como implementar identidades gerenciadas para recursos do Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).