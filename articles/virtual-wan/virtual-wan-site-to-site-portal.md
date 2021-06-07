---
title: 'Tutorial: Usar a WAN Virtual do Azure para criar conexões site a site'
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN site a site para o Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 365952beca96b91d312eab209c5332fca2d4842b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061869"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão site a site usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar aos recursos do Azure em uma conexão de VPN IPsec/IKE (IKEv1 e IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma WAN virtual
> * Criar um hub
> * Criar um site
> * Conectar um site a um hub
> * Conectar um site VPN a um hub
> * Conectar uma VNET a um hub
> * Baixar um arquivo de configuração
> * Configurar seu gateway de VPN

> [!NOTE]
> Se você tem vários sites, normalmente usaria um [parceiro de WAN Virtual](https://aka.ms/virtualwan) para criar esta configuração. No entanto, você poderá criar essa configuração por conta própria se estiver familiarizado com a rede e tiver domínio sobre a configuração de seu próprio dispositivo VPN.
>

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Criar uma WAN Virtual

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Criar um hub

Um hub é uma rede virtual que pode conter gateways para funcionalidade site a site, ExpressRoute ou ponto a site. Depois que o hub é criado, você será cobrado por ele, mesmo se não anexar sites. Leva 30 minutos para criar o gateway de VPN site a site no hub virtual.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Criar um site

Nesta seção, você cria um site. Os sites correspondem aos seus locais físicos. Crie quantos sites forem necessários. Por exemplo, se você tiver uma filial em Nova York, uma filial em Londres e uma filial em Los Angeles, crie três sites separados. Esses sites contêm seus pontos de extremidade do dispositivo VPN local. Você pode criar até 1000 sites por hub virtual em uma WAN Virtual. No caso de vários hubs, você pode criar 1000 para cada um desses hubs. Se tiver um dispositivo CPE de parceiro de WAN Virtual, verifique com ele para saber mais sobre a automação no Azure. Normalmente, a automação implica uma experiência simples de clique para exportar informações de branch em grande escala para o Azure e configurar a conectividade do CPE com o gateway de VPN da WAN Virtual do Azure. Para obter mais informações, confira [Diretrizes de automação do Azure para os parceiros do CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Conectar o site VPN ao hub

Nesta etapa, você conecta o site VPN ao hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Conectar a VNet ao hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>Baixar configuração de VPN

Use a configuração do dispositivo VPN para configurar seu dispositivo VPN local.

1. Na página de sua WAN virtual, clique em **Visão Geral**.
2. Na parte superior da página **Hub ->VPNSite**, clique em **Baixar a Configuração de VPN**. O Azure cria uma conta de armazenamento no grupo de recursos 'microsoft-network-[local]', em que local é o local da rede remota. Depois de aplicar a configuração a seus dispositivos VPN, você poderá excluir essa conta de armazenamento.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Aplique a configuração ao dispositivo de VPN local.

### <a name="about-the-vpn-device-configuration-file"></a>Sobre o arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections**: essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** do(s) hub(s) virtual(is) da VNet.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** das VNets conectadas ao hub.<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Endereços IP** do hub virtual vpngateway. Como cada conexão do vpngateway é composta por dois túneis na configuração ativa-ativa, você vê os dois endereços IP listados neste arquivo. Neste exemplo, você vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes da configuração da conexão do Vpngateway** como BGP, chave pré-compartilhada, etc. O PSK é a chave pré-compartilhada gerada automaticamente para você. Você sempre pode editar a conexão na página Visão Geral de um PSK personalizado.
  
### <a name="example-device-configuration-file"></a>Exemplo de arquivo de configuração de dispositivo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

>[!NOTE]
> Se você estiver trabalhando com uma solução de parceiro de WAN Virtual, a configuração do dispositivo VPN ocorrerá automaticamente. O controlador do dispositivo obtém o arquivo de configuração do Azure e o aplica ao dispositivo para configurar a conexão com o Azure. Isso significa que você não precisa saber como configurar seu dispositivo VPN manualmente.
>

Se você precisar de instruções para configurar o dispositivo, poderá usar as instruções sobre a [página de scripts de configuração de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) com as seguintes condições:

* As instruções na página de dispositivos VPN não foram criadas para WAN Virtual, mas você pode usar os valores de WAN Virtual do arquivo de configuração para configurar seu dispositivo VPN manualmente. 
* Os scripts de configuração de dispositivo que podem ser baixados para o Gateway de VPN não funcionam para WAN Virtual, já que a configuração é diferente.
* Uma nova WAN Virtual pode oferecer suporte a IKEv1 e IKEv2.
* A WAN Virtual pode usar dispositivos VPN baseados em rota e baseados em política e instruções do dispositivo.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>Configurar seu gateway de VPN

Você pode ver e definir suas configurações de gateway de VPN a qualquer momento selecionando **Exibir/Configurar**.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Captura de tela que mostra a página 'VPN (site a site)' com uma seta apontando para a ação 'Exibir/Configurar'." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

Na página **Editar o Gateway de VPN**, você pode ver as seguintes configurações:

* Endereço IP Público do Gateway de VPN (atribuído pelo Azure)
* Endereço IP Privado do Gateway de VPN (atribuído pelo Azure)
* Endereço IP BGP Padrão do Gateway de VPN (atribuído pelo Azure)
* Opção de configuração para o Endereço IP BGP Personalizado: Esse campo é reservado para APIPA (Endereçamento IP Privado Automático). O Azure dá suporte a IP BGP nos intervalos 169.254.21.* e 169.254.22.*. O Azure aceita conexões BGP nesses intervalos, mas discará a conexão com o IP BGP padrão.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Exibir configuração" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando não precisar mais dos recursos que criou, exclua-os. Alguns dos recursos da WAN Virtual precisam ser excluídos em determinada ordem devido às dependências. A exclusão poderá levar cerca de 30 minutos para ser concluída.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Em seguida, para saber mais sobre a WAN Virtual, confira:

> [!div class="nextstepaction"]
> * [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md)
