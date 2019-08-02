---
title: Criar solução CloudSimple - serviço do VMware do Azure
description: Descreve como criar o serviço CloudSimple no portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0ccce6f298270b2751307868fdf85697cb7e8ee
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154953"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Criar a solução VMware do Azure por CloudSimple - serviço

Para começar com a solução de VMware do Azure por CloudSimple, crie a solução de VMware do Azure pelo serviço CloudSimple no portal do Azure.

> [!NOTE]
> Antes de criar o serviço CloudSimple, você deve registrar o provedor de recursos Microsoft.VMwareCloudSimple em sua assinatura do Azure. Siga as etapas em [habilitar o provedor de recursos em sua assinatura do Azure Microsoft.VMwareCloudSimple](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.

2. Pesquise **CloudSimple serviços**.

    ![Serviço de pesquisa de CloudSimple](media/create-cloudsimple-service-search.png)

3. Selecione **CloudSimple serviços**.

4. Clique em **adicionar** para criar um novo serviço.

    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a assinatura onde você deseja criar o serviço CloudSimple.

6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.

7. Insira um nome para identificar o serviço.

8. Insira o CIDR para o gateway do serviço. Especifique uma de/28 sub-rede que não se sobrepõem com qualquer uma das suas sub-redes existentes.  Elas incluem sub-redes locais, subredes do Azure, ou qualquer planejado CloudSimple sub-redes. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
