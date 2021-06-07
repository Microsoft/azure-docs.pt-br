---
title: 'Configurar a política IPsec personalizada para a WAN virtual do Azure: Portal | Microsoft Docs'
description: Saiba como configurar a política IPsec personalizada para a WAN virtual do Azure usando o Portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91851159"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configurar uma política IPsec personalizada para WAN virtual usando o portal

Você pode configurar uma política IPsec personalizada para uma conexão VPN de WAN virtual no portal do Azure. Políticas personalizadas são úteis quando você deseja que ambos os lados (gateway de VPN local e do Azure) usem as mesmas configurações para a fase 1 do IKE e a fase 2 do IKE.

## <a name="working-with-custom-policies"></a>Trabalhando com políticas personalizadas

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurar uma política

1. **Localize o Hub virtual**. Em um navegador, acesse o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e entre com sua conta do Azure. Navegue até o recurso de WAN virtual e localize o Hub virtual ao qual seu site de VPN está conectado.
2. **Selecione o site VPN**. Na página Visão geral do Hub, clique em **VPN (site a site)** e selecione o site VPN para o qual você deseja configurar uma política IPSec personalizada.

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Edite a conexão VPN**. No **menu de contexto** **...**, selecione **Editar conexão VPN**.

   ![editar](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Defina as configurações**. Na página **Editar conexão VPN** , altere a configuração de IPSec de padrão para personalizado e personalize a política IPSec. Selecione **salvar** para salvar suas configurações.

   ![configurar e salvar](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).