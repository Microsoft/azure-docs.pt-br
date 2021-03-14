---
title: Configurar preferência de roteamento de rede
titleSuffix: Azure Storage
description: Configure a preferência de roteamento de rede para sua conta de armazenamento do Azure para especificar como o tráfego de rede é roteado para sua conta de clientes pela Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700774"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configurar a preferência de roteamento de rede para o armazenamento do Azure

Este artigo descreve como você pode configurar a preferência de roteamento de rede e os pontos de extremidade específicos de rota para sua conta de armazenamento. 

A preferência de roteamento de rede especifica como o tráfego de rede é roteado para sua conta de clientes pela Internet. Os pontos de extremidade específicos de rota são novos pontos de extremidade que o armazenamento do Azure cria para sua conta de armazenamento. Esses pontos de extremidade roteiam o tráfego sobre um caminho desejado sem alterar a preferência de roteamento padrão. Para saber mais, consulte [preferência de roteamento de rede para o armazenamento do Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configurar a preferência de roteamento para o ponto de extremidade público padrão

Por padrão, a preferência de roteamento para o ponto de extremidade público da conta de armazenamento é definida como Microsoft Global Network. Você pode escolher entre a rede global da Microsoft e o roteamento da Internet como a preferência de roteamento padrão para o ponto de extremidade público da sua conta de armazenamento. Para saber mais sobre a diferença entre esses dois tipos de roteamento, consulte [preferência de roteamento de rede para o armazenamento do Azure](network-routing-preference.md). 

Para alterar sua preferência de roteamento para roteamento da Internet:

1. Navegue até sua conta de armazenamento no Portal.

2. Em **configurações**, escolha **rede**.

    > [!div class="mx-imgBorder"]
    > ![Opção de menu rede](./media/configure-network-routing-preference/networking-option.png)

3.  Na guia **firewalls e redes virtuais** , em **Roteamento de rede**, altere a configuração de **preferência de roteamento** para roteamento da **Internet**.

4.  Clique em **Save** (Salvar).

    > [!div class="mx-imgBorder"]
    > ![opção de roteamento da Internet](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Configurar um ponto de extremidade específico de rota

Você também pode configurar um ponto de extremidade específico de rota. Por exemplo, você pode definir a preferência de roteamento para o ponto de extremidade padrão para *Roteamento da Internet* e, em seguida, publicar um ponto de extremidade específico de rota que permite o tráfego entre clientes na Internet e sua conta de armazenamento para ser roteado por meio da rede global da Microsoft.

1.  Navegue até sua conta de armazenamento no Portal.

2.  Em **configurações**, escolha **rede**.

3.  Na guia **firewalls e redes virtuais** , em **publicar pontos de extremidade específicos da rota**, escolha a preferência de roteamento do ponto de extremidades específico de rota e clique em **salvar**. Essa preferência afeta apenas o ponto de extremidade específico da rota. Essa preferência não afeta a preferência de roteamento padrão.  

    A imagem a seguir mostra a opção de **Roteamento de rede da Microsoft** selecionada.

    > [!div class="mx-imgBorder"]
    > ![Opção de roteamento de rede da Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Localizar o nome do ponto de extremidade para um ponto de extremidade específico de rota

Se você configurou um ponto de extremidade específico de rota, poderá encontrar o ponto de extremidade nas propriedades de sua conta de armazenamento.

1.  Em **configurações**, escolha **Propriedades**.

    > [!div class="mx-imgBorder"]
    > ![opção de menu Propriedades](./media/configure-network-routing-preference/properties.png)

2.  O ponto de extremidade de **Roteamento de rede da Microsoft** é mostrado para cada serviço que dá suporte às preferências de roteamento. Essa imagem mostra o ponto de extremidade para os serviços BLOB e arquivo.

    > [!div class="mx-imgBorder"]
    > ![Opção de roteamento de rede da Microsoft para pontos de extremidade específicos de rota](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Confira também

- [Preferência de roteamento de rede](network-routing-preference.md)
- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
