---
title: Configurar o controle de acesso à rede
titleSuffix: Azure SignalR Service
description: Configure o controle de acesso à rede para seu serviço de Signaler do Azure.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143218"
---
# <a name="configure-network-access-control"></a>Configurar o controle de acesso à rede

O serviço de Signaler do Azure permite que você proteja e controle o nível de acesso ao ponto de extremidade de serviço, com base no tipo de solicitação e no subconjunto de redes usadas. Quando as regras de rede são configuradas, somente os aplicativos que solicitam dados no conjunto especificado de redes podem acessar o serviço de Signaler do Azure.

O serviço de sinalizador do Azure tem um ponto de extremidade público que pode ser acessado pela Internet. Você também pode criar [pontos de extremidade privados para o serviço de signaler do Azure](howto-private-endpoints.md). O ponto de extremidade privado atribui um endereço IP privado de sua VNet ao serviço de Signaler do Azure e protege todo o tráfego entre a VNet e o serviço de Signaler do Azure por meio de um link privado. O controle de acesso de rede do serviço de Signaler do Azure fornece controle de acesso para pontos de extremidade públicos e privados.

Opcionalmente, você pode optar por permitir ou negar determinados tipos de solicitações para o ponto de extremidade público e cada ponto de extremidade privado. Por exemplo, você pode bloquear todas as [conexões de servidor](signalr-concept-internals.md#server-connections) do ponto de extremidade público e certificar-se de que elas se originam apenas de uma VNet específica.

Um aplicativo que acessa um serviço de sinalizador do Azure quando as regras de controle de acesso à rede estão em vigor ainda requer autorização adequada para a solicitação.

## <a name="scenario-a---no-public-traffic"></a>Cenário A – sem tráfego público

Para negar completamente todo o tráfego público, primeiro você deve configurar a regra de rede pública para não permitir nenhum tipo de solicitação. Em seguida, você deve configurar regras que permitem acesso ao tráfego de VNets específicas. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Cenário B-somente conexões de cliente da rede pública

Nesse cenário, você pode configurar a regra de rede pública para permitir somente [conexões de cliente](signalr-concept-internals.md#client-connections) da rede pública. Em seguida, você pode configurar regras de rede privada para permitir que outros tipos de solicitações provenientes de uma VNet específica. Essa configuração oculta os servidores de aplicativos da rede pública e estabelece conexões seguras entre os servidores de aplicativos e o serviço de Signaler do Azure.

## <a name="managing-network-access-control"></a>Gerenciando o controle de acesso à rede

Você pode gerenciar o controle de acesso à rede para o serviço de Signaler do Azure por meio do portal do Azure.

### <a name="azure-portal"></a>Portal do Azure

1. Vá para o serviço de Signalr do Azure que você deseja proteger.

1. Clique no menu configurações chamado **controle de acesso à rede**.

    ![ACL de rede no portal](media/howto-network-access-control/portal.png)

1. Para editar a ação padrão, alterne o botão **permitir/negar** .

    > [!TIP]
    > A ação padrão é a ação que executamos quando não há correspondências de regras de ACL. Por exemplo, se a ação padrão for **Deny**, os tipos de solicitação que não são explicitamente aprovados abaixo serão negados.

1. Para editar a regra de rede pública, selecione tipos permitidos de solicitações em **rede pública**.

    ![Editar ACL de rede pública no portal ](media/howto-network-access-control/portal-public-network.png)

1. Para editar as regras de rede do ponto de extremidade privado, selecione tipos permitidos de solicitações em cada linha em **conexões de ponto de extremidade privado**.

    ![Editar ACL de ponto de extremidade privado no portal ](media/howto-network-access-control/portal-private-endpoint.png)

1. Clique em **Salvar** para aplicar suas alterações.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-overview.md).