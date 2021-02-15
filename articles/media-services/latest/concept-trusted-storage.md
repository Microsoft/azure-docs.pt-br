---
title: Armazenamento confiável para os serviços de mídia
description: A autenticação de identidades gerenciadas permite que os serviços de mídia acessem a conta de armazenamento que foi configurada com um firewall ou uma restrição de VNet por meio de acesso de armazenamento confiável.
keywords: armazenamento confiável, identidades gerenciadas
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 96031642efb1248fb3b1acabeb9d7381177a2c70
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098071"
---
# <a name="trusted-storage-for-media-services"></a>Armazenamento confiável para os serviços de mídia

Ao criar uma conta de serviços de mídia, você deve associá-la a uma conta de armazenamento. Os serviços de mídia podem acessar essa conta de armazenamento usando a autenticação de sistema ou a autenticação de identidade gerenciada. Os serviços de mídia validam que a conta dos serviços de mídia e a conta de armazenamento estão na mesma assinatura e valida que o usuário que está adicionando a associação tem acesso à conta de armazenamento com o Azure Resource Manager RBAC.

## <a name="trusted-storage-with-a-firewall"></a>Armazenamento confiável com um firewall

No entanto, se você quiser usar um firewall para proteger sua conta de armazenamento e habilitar o armazenamento confiável, a autenticação de [identidades gerenciadas](concept-managed-identities.md) será a opção preferida. Ele permite que os serviços de mídia acessem a conta de armazenamento que foi configurada com um firewall ou uma restrição de VNet por meio de acesso de armazenamento confiável.

## <a name="tutorial"></a>Tutorial

Você pode saber mais sobre como habilitar o armazenamento confiável com o tutorial de [armazenamento confiável dos serviços de mídia](tutorial-trusted-storage-rest.md) .

> [!NOTE]
> Você precisa conceder o acesso de colaborador de dados de blob de armazenamento de identidade gerenciada do AMS para que os serviços de mídia possam ler e gravar na conta de armazenamento.  A concessão da função de colaborador genérico não funcionará, pois não habilita as permissões corretas no plano de dados.

## <a name="further-reading"></a>Leitura adicional

Para entender os métodos de criação de armazenamento confiável com identidades gerenciadas, leia [identidades gerenciadas e serviços de mídia](concept-managed-identities.md).

Para obter mais informações sobre serviços confiáveis da Microsoft, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o que as identidades gerenciadas podem fazer por você e seus aplicativos do Azure, confira [identidades gerenciadas do Azure ad](../../active-directory/managed-identities-azure-resources/overview.md).
