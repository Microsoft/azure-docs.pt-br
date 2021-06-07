---
title: Conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute – solução do Azure VMware por CloudSimple
description: Descreve como obter informações de emparelhamento para uma conexão entre a rede virtual do Azure e seu ambiente CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3fedfbe55fd8ea3d2b4cc910df631e40bc74e210
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899058"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute

Você pode estender sua rede de nuvem privada para sua rede virtual do Azure e recursos do Azure. Uma conexão do ExpressRoute permite que você acesse recursos em execução na sua assinatura do Azure de sua nuvem privada.

## <a name="request-authorization-key"></a>Solicitar chave de autorização

Uma chave de autorização é necessária para a conexão de ExpressRoute entre sua nuvem privada e a rede virtual do Azure. Para obter uma chave, arquivo de um tíquete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a>.  Use as seguintes informações na solicitação:

* Tipo de problema: **técnico**
* Assinatura: **Selecione a assinatura em que o serviço CloudSimple está implantado**
* Serviço: **solução VMware por CloudSimple**
* Tipo de problema: **solicitação de serviço**
* Subtipo de problema: **chave de autorização para conexão de VNET do Azure**
* Assunto: **solicitação de chave de autorização para conexão de VNET do Azure**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Obter informações de emparelhamento do portal do CloudSimple

Para configurar a conexão, você deve estabelecer uma conexão entre a rede virtual do Azure e o ambiente do CloudSimple.  Como parte do procedimento, você deve fornecer o URI do circuito par e a chave de autorização. Obtenha o URI e a chave de autorização do [portal do CloudSimple](access-cloudsimple-portal.md).  Selecione **rede** no menu lateral e, em seguida, selecione **conexão de rede do Azure**. Ou selecione **conta** no menu lateral e, em seguida, selecione **conexão de rede do Azure**.

Copie o URI do circuito par e a chave de autorização para cada uma das regiões usando o ícone de *cópia* . Para cada região do CloudSimple que você deseja conectar:

1. Clique em **copiar** para copiar o URI. Cole-o em um arquivo no qual ele pode estar disponível para ser adicionado ao portal do Azure.  
2. Clique em **copiar** para copiar a chave de autorização e cole-a no arquivo também.

Copie a chave de autorização e o URI do circuito par que está no estado **disponível** .  Status **usado** indica que a chave já foi usada para criar uma conexão de rede virtual.

![Página conexão de rede virtual](media/virtual-network-connection.png)

Para obter detalhes sobre como configurar a rede virtual do Azure para o link do CloudSimple, consulte [conectar seu ambiente de nuvem privada do CloudSimple à rede virtual do Azure usando o ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Próximas etapas

* [Conexão de rede virtual do Azure para nuvem privada](azure-expressroute-connection.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
