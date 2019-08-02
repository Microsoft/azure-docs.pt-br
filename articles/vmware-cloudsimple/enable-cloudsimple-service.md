---
title: Habilitar a solução de VMware do Azure pelo serviço CloudSimple
description: Descreve como habilitar o serviço de CloudSimple em uma assinatura do Azure e, em seguida, registrar o provedor de recursos CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154865"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrar o provedor de recursos Microsoft.VMwareCloudSimple em sua assinatura do Azure

O serviço CloudSimple permite consumir solução CloudSimple do VMware do Azure. Você pode registrar o serviço Microsoft.VMwareCloudSimple como seu provedor de recursos.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Todos os serviços**.

3. Pesquise e selecione **assinaturas**.

    ![Selecione assinaturas](media/cloudsimple-service-select-subscriptions.png)

4. Selecione a assinatura na qual você deseja habilitar o serviço CloudSimple.

5. Clique em **provedores de recursos** para a assinatura.

6. Use **Microsoft.VMwareCloudSimple** para filtrar o provedor de recursos.

7. Selecione o provedor de recursos e clique em **registrar**.

    ![Registrar provedor de recursos](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um serviço CloudSimple](create-cloudsimple-service.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
