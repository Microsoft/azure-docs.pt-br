---
title: Suporte do DPS (serviço de provisionamento de dispositivos) do Azure IoT para redes virtuais
description: Como usar o padrão de conectividade de redes virtuais com o serviço de provisionamento de dispositivos IoT do Azure (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959774"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Suporte do DPS (serviço de provisionamento de dispositivos) do Hub IoT do Azure para redes virtuais

Este artigo apresenta o padrão de conectividade de rede virtual (VNET) para o provisionamento de dispositivos IoT com hubs IoT usando o DPS. Esse padrão fornece conectividade privada entre os dispositivos, o DPS e o Hub IoT dentro de uma VNET do Azure de Propriedade do cliente. 

Na maioria dos cenários em que o DPS é configurado com uma VNET, o Hub IoT também será configurado na mesma VNET. Para obter informações mais específicas sobre suporte VNET e configuração para hubs IoT, consulte [suporte à rede virtual do Hub IOT](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Introdução

Por padrão, os nomes de host de DPS são mapeados para um ponto de extremidade público com um endereço IP roteável publicamente pela Internet. Esse ponto de extremidade público é visível para todos os clientes. O acesso ao ponto de extremidade público pode ser tentado por dispositivos IoT em redes de longa distância, bem como redes locais.

Por vários motivos, os clientes podem querer restringir a conectividade aos recursos do Azure, como o DPS. Esses motivos incluem:

* Impeça a exposição da conexão pela Internet pública. A exposição pode ser reduzida com a introdução de camadas adicionais de segurança por meio do isolamento no nível da rede para o Hub IoT e os recursos do DPS

* A habilitação de uma experiência de conectividade privada de seus ativos de rede local, garantindo que seus dados e tráfego sejam transmitidos diretamente para a rede de backbone do Azure.

* Impedir ataques de vazamento de redes locais confidenciais. 

* Seguindo os padrões estabelecidos de conectividade de todo o Azure usando [pontos de extremidade privados](../private-link/private-endpoint-overview.md).

Abordagens comuns para restringir a conectividade incluem [regras de filtro IP de DPS](./iot-dps-ip-filtering.md) e rede virtual (VNET) com pontos de [extremidade privados](../private-link/private-endpoint-overview.md). O objetivo deste artigo é descrever a abordagem de VNET para o DPS usando pontos de extremidade privados. 

Os dispositivos que operam em redes locais podem usar a [VPN (rede virtual privada)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o emparelhamento privado do [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para se conectar a uma VNET no Azure e acessar os recursos de DPS por meio de pontos de extremidade privados. 

Um ponto de extremidade privado é um endereço IP privado alocado dentro de uma VNET de Propriedade do cliente pela qual um recurso do Azure está acessível. Ao ter um ponto de extremidade privado para seu recurso de DPS, você poderá permitir que os dispositivos que operam dentro de sua VNET solicitem provisionamento por seu recurso de DPS sem permitir o tráfego para o ponto de extremidade público.


## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, verifique se os seguintes pré-requisitos foram atendidos:

* Seu recurso de DPS já foi criado e vinculado aos seus hubs IoT. Para obter orientação sobre como configurar um novo recurso de DPS, consulte [Configurar o serviço de provisionamento de dispositivos no Hub IOT com o portal do Azure](./quick-setup-auto-provision.md)

* Você provisionou uma VNET do Azure com uma sub-rede na qual o ponto de extremidade privado será criado. Para obter mais informações, consulte [criar uma rede virtual usando CLI do Azure](../virtual-network/quick-create-cli.md).

* Para dispositivos que operam dentro de redes locais, configure a [VPN (rede virtual privada)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o emparelhamento privado do [EXPRESSROUTE](https://azure.microsoft.com/services/expressroute/) em sua VNET do Azure.

## <a name="private-endpoint-limitations"></a>Limitações do ponto de extremidade privado

Observe as seguintes limitações atuais para o DPS ao usar pontos de extremidade privados:

* Os pontos de extremidade privados não funcionarão com o DPS quando o recurso de DPS e o Hub vinculado estiverem em nuvens diferentes. Por exemplo, [Azure governamental e global Azure](../azure-government/documentation-government-welcome.md).

* Atualmente, [as políticas de alocação personalizadas com Azure Functions](how-to-use-custom-allocation-policies.md) para o DPS não funcionarão quando a função do Azure for bloqueada para uma VNET e pontos de extremidade privados. 

* O suporte a VNET do DPS atual é para entrada de dados somente no DPS. A saída de dados, que é o tráfego do DPS para o Hub IoT, usa um mecanismo de serviço a serviço interno em vez de uma VNET dedicada. O suporte para o bloqueio de saída completo baseado em VNET entre o DPS e o Hub IoT não está disponível no momento.

* A diretiva de alocação de latência mais baixa é usada para atribuir um dispositivo ao Hub IoT com a menor latência. Essa política de alocação não é confiável em um ambiente de rede virtual. 

## <a name="set-up-a-private-endpoint"></a>Configurar um ponto de extremidade privado

Para configurar um ponto de extremidade privado, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o recurso de DPS e clique na guia **rede** . Clique em **conexões de ponto de extremidade privado** e **+ ponto de extremidade privado**.

    ![Adicionar um novo ponto de extremidade privado para o DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Na página Criar noções básicas do _ponto de extremidade privado_ , insira as informações mencionadas na tabela a seguir.

    ![Crie noções básicas de pontos de extremidade privados](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Campo | Valor |
    | :---- | :-----|
    | **Assinatura** | Escolha a assinatura do Azure desejada para conter o ponto de extremidade privado.  |
    | **Grupo de recursos** | Escolha ou crie um grupo de recursos para conter o ponto de extremidade privado |
    | **Nome**       | Insira um nome para seu ponto de extremidade privado |
    | **Região**     | A região escolhida deve ser a mesma que a região que contém a VNET, mas não precisa ser a mesma do recurso de DPS. |

    Clique em **Avançar: recurso** para configurar o recurso para o qual o ponto de extremidade privado apontará.

3. Na página _criar um recurso de ponto de extremidade privado_ , insira as informações mencionadas na tabela a seguir.

    ![Criar recurso de ponto de extremidade privado](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Campo | Valor |
    | :---- | :-----|
    | **Assinatura**        | Escolha a assinatura do Azure que contém o recurso de DPS ao qual seu ponto de extremidade privado apontará.  |
    | **Tipo de recurso**       | Escolha **Microsoft. Devices/ProvisioningServices**. |
    | **Recurso**            | Selecione o recurso de DPS para o qual o ponto de extremidade privado será mapeado. |
    | **Sub-recurso de destino** | Selecione **iotDps**. |

    > [!TIP]
    > As informações sobre a configuração **conectar a um recurso do Azure por ID de recurso ou alias** são fornecidas na seção [solicitar um ponto de extremidade privado](#request-a-private-endpoint) neste artigo.


    Clique em **Avançar: configuração** para configurar a VNET para o ponto de extremidade privado.

4. Na página _criar uma configuração de ponto de extremidade privado_ , escolha sua rede virtual e sub-rede para criar o ponto de extremidade privado.
 
    Clique em **Avançar: marcas** e, opcionalmente, forneça todas as marcas para o recurso.

    ![Configurar ponto de extremidade privado](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Clique em **revisar + criar** e em **criar** para criar seu recurso de ponto de extremidade privado.


## <a name="request-a-private-endpoint"></a>Solicitar um ponto de extremidade privado

Você pode solicitar um ponto de extremidade privado para um recurso de DPS por ID de recurso. Para fazer essa solicitação, você precisa do proprietário do recurso para fornecer a ID do recurso. 

1. A ID do recurso é fornecida para a guia Propriedades do recurso de DPS, conforme mostrado abaixo.

    ![Guia Propriedades do DPS](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Lembre-se de que a ID do recurso contém a ID da assinatura. 

2. Depois de ter a ID do recurso, siga as etapas acima em [configurar um ponto de extremidade privado](#set-up-a-private-endpoint) para a etapa 3 na página _criar um recurso de ponto de extremidade privado_ . Clique em **conectar a um recurso do Azure por ID de recurso ou alias** e insira as informações na tabela a seguir. 

    | Campo | Valor |
    | :---- | :-----|
    | **ID do recurso ou alias** | Insira a ID de recurso para o recurso de DPS. |
    | **Sub-recurso de destino** | Insira **iotDps** |
    | **Mensagem de solicitação** | Insira uma mensagem de solicitação para o proprietário do recurso de DPS.<br>Por exemplo, <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Clique em **Avançar: configuração** para configurar a VNET para o ponto de extremidade privado.

3. Na página _criar uma configuração de ponto de extremidade privado_ , escolha a rede virtual e a sub-rede para criar o ponto de extremidade privado.
 
    Clique em **Avançar: marcas** e, opcionalmente, forneça todas as marcas para o recurso.

4. Clique em **revisar + criar** e em **criar** para criar sua solicitação de ponto de extremidade particular.

5. O proprietário do DPS verá a solicitação de ponto de extremidade privado na lista de **conexões do ponto de extremidade privado** na guia rede do DPS. Nessa página, o proprietário pode **aprovar** ou **rejeitar** a solicitação de ponto de extremidade particular, conforme mostrado abaixo.

    ![Aprovação de DPS](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Pontos de extremidade privados de preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Próximas etapas

Use os links abaixo para saber mais sobre os recursos de segurança do DPS:

* [Segurança](./concepts-service.md#attestation-mechanism)
* [Suporte a TLS 1,2](tls-support.md)