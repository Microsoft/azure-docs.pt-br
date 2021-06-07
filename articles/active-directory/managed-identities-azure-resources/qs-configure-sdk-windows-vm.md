---
title: Use o SDK para configurar identidades gerenciadas em uma VM – Azure AD
description: Instruções passo a passo para configurar e usar identidades gerenciadas para recursos do Azure em uma VM do Azure, usando um SDK do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93359656"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configurar uma VM com identidades gerenciadas para recursos do Azure usando um SDK do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende como habilitar e remover identidades gerenciadas para recursos do Azure para uma VM do Azure usando um SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDKs do Azure com identidades gerenciadas para suporte a recursos do Azure 

O Azure dá suporte a várias plataformas de programação por meio de uma série de [SDKs do Azure](https://azure.microsoft.com/downloads). Vários deles foram atualizados para dar suporte a identidades gerenciadas para recursos do Azure e fornecer exemplos correspondentes para demonstrar o uso. Esta lista é atualizada conforme suporte adicional é adicionado:

| . | Amostra |
| --- | ------ | 
| .NET   | [Gerenciar recursos de uma VM habilitada com identidades gerenciadas para recursos do Azure habilitados](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerenciar o armazenamento de uma VM habilitada com identidades gerenciadas para recursos do Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Criar uma VM com identidade gerenciada atribuída ao sistema habilitada](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Criar uma VM com identidade gerenciada atribuída ao sistema habilitada](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar uma VM do Azure com uma identidade atribuída ao sistema habilitada](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Próximas etapas

- Consulte os artigos relacionados em **Configurar identidade para uma VM do Azure**, para saber como também é possível usar o portal do Azure, o PowerShell, a CLI e os modelos de recursos.
