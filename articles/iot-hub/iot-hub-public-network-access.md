---
title: Gerenciando o acesso à rede pública para o Hub IoT do Azure
description: Documentação sobre como desabilitar e habilitar o acesso à rede pública para o Hub IoT
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: 27552b2c39f2d1e5d9cc1719a9cc2944e088773a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517120"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gerenciando o acesso à rede pública para o Hub IoT

Para restringir o acesso somente ao [ponto de extremidade privado para o Hub IOT em sua VNet](virtual-network-support.md), desabilite o acesso à rede pública. Para fazer isso, use portal do Azure ou a `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Desligar o acesso à rede pública usando portal do Azure

1. Visite [portal do Azure](https://portal.azure.com)
2. Navegue até o seu Hub IoT.
3. Selecione **rede** no menu do lado esquerdo.
4. Em "permitir acesso de rede pública a", selecione **desabilitado**
5. Selecione **Salvar**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Imagem mostrando portal do Azure onde desativar o acesso à rede pública" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Para ativar o acesso à rede pública, selecione **todas as redes** e, em seguida, **salvar**.

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Ponto de extremidade do Hub IoT, endereço IP e portas depois de desabilitar o acesso à rede pública

O Hub IoT é uma PaaS (plataforma como serviço) multilocatário, para que clientes diferentes compartilhem o mesmo pool de recursos de hardware de computação, rede e armazenamento. Os nomes de host do Hub IoT são mapeados para um ponto de extremidade público com um endereço IP roteável publicamente pela Internet. Diferentes clientes compartilham esse ponto de extremidade público do Hub IoT e dispositivos IoT em redes de longa distância e redes locais podem acessá-lo. 

A desabilitação do acesso à rede pública é imposta em um recurso de Hub IoT específico, garantindo o isolamento. Para manter o serviço ativo para outros recursos do cliente usando o caminho público, seu ponto de extremidade público permanece resolvido, os endereços IP são detectáveis e as portas permanecem abertas. Isso não é uma causa de preocupação, pois a Microsoft integra várias camadas de segurança para garantir o isolamento completo entre os locatários. Para saber mais, confira [isolamento na nuvem pública do Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtro IP 

Se o acesso à rede pública estiver desabilitado, todas as regras de [filtro IP](iot-hub-ip-filtering.md) serão ignoradas. Isso ocorre porque todos os IPs da Internet pública estão bloqueados. Para usar o filtro IP, use a opção **intervalos de IP selecionados** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Correção de bug com ponto de extremidade compatível com o Hub de eventos interno

Há um bug com o Hub IoT em que o [ponto de extremidade compatível com o Hub de eventos interno](iot-hub-devguide-messages-read-builtin.md) continua a ser acessado por meio da Internet pública quando o acesso à rede pública ao Hub IOT é desabilitado. Para saber mais e entre em contato conosco sobre esse bug, consulte [desabilitar o acesso à rede pública para o Hub IOT desabilita o acesso ao ponto de extremidade do hub de eventos interno](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
