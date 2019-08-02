---
title: Usar a WAN Virtual do Azure para criar uma conexão site a site para o Azure | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN site a site para o Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e9be7ef5c4f37c66f7cbf2c6226936438b367108
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515163"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão site a site usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar aos recursos do Azure em uma conexão de VPN IPsec/IKE (IKEv1 e IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

> [!NOTE]
> Se você tem vários sites, normalmente usaria um [parceiro de WAN Virtual](https://aka.ms/virtualwan) para criar esta configuração. No entanto, você poderá criar essa configuração por conta própria se estiver familiarizado com a rede e tiver domínio sobre a configuração de seu próprio dispositivo VPN.
>

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um site
> * Criar um hub
> * Conectar um hub a um site
> * Criar uma rede virtual compatível (se você ainda não tiver uma)
> * Conectar uma VNET a um hub
> * Baixar e aplicar a configuração do dispositivo VPN
> * Exibir a WAN virtual
> * Exibir a integridade dos recursos
> * Monitorar uma conexão

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Criar uma WAN virtual

Em um navegador, acesse o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e entre com sua conta do Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>2. Criar um site

Crie quantos sites forem necessários para corresponder a seus locais físicos. Por exemplo, se você tiver uma filial em Nova York, uma filial em Londres e uma filial em Los Angeles, crie três sites separados. Esses sites contêm seus pontos de extremidade do dispositivo VPN local. Neste momento, você pode especificar apenas um espaço de endereço privado para o site.

1. Clique na WAN que você criou. Na página da WAN, em **Arquitetura da WAN**, clique em **Sites VPN** para abrir a página de sites VPN.
2. Na página **Sites VPN**, clique em **+Criar site**.
3. Na página **Criar site**, preencha os seguintes campos:

   * **Nome**: o nome que você deseja dar ao site local.
   * **Endereço IP público:** : o endereço IP público do dispositivo de VPN local.
   * **Espaço de endereço privado:** espaço de endereço localizado no local. O tráfego destinado a esse espaço de endereço é roteado para o site local.
   * **Assinatura**: verifique a assinatura.
   * **Grupo de Recursos:** o grupo de recursos que você deseja usar.
   * **Localidade**
4. Clique em **Mostrar opções avançadas** para exibir as configurações adicionais. 

   Selecione **BGP** para habilitar o BGP, o que habilitará essa funcionalidade em todas as conexões criadas para o site no Azure. Configurar o BGP em uma WAN Virtual é equivalente a configurar o BGP em um gateway de VPN do Azure. Seu endereço de par no nível de protocolo BGP local *não deve* ser o mesmo que o endereço IP público do VPN para dispositivo ou o espaço de endereço da VNet do site VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, ele *não pode* ser um endereço APIPA (169.254.*x*.*x*). Especifique esse endereço no Gateway de Rede Local correspondente que representa o local. Para saber os pré-requisitos do BGP, confira [Sobre o BGP com o Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

   Você também pode inserir **Informações do dispositivo** (campos opcionais). Isso pode ajudar a equipe do Azure a entender melhor seu ambiente para possível e futuramente adicionar otimizações ou para ajudá-lo a solucionar problemas.
   
5. Clique em **Confirmar**.
6. Depois de clicar em **Confirmar**, veja o status na página sites VPN. O site mudará de **Provisionando** para **Provisionado**.

## <a name="hub"></a>3. Criar um hub

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>4. Associar os sites ao hub

Os hubs devem ser geralmente associados a sites que estão na mesma região da rede virtual.

1. Na página **Sites VPN**, selecione o site ou sites que você deseja associar ao hub, clique em **+ Nova associação de hub**.
2. Na página **Associar sites a um ou mais hubs**, selecione um hub na lista suspensa. Você pode associar um site a hubs adicionais clicando **+Adicionar uma associação**.
3. Você também pode adicionar um **PSK** específico aqui, ou usar o padrão.
4. Clique em **Confirmar**.
5. Veja o status da conexão na página **Sites VPN**.

## <a name="vnet"></a>5. Criar uma rede virtual

Se você ainda não tem uma rede virtual, crie uma rapidamente usando o PowerShell ou o portal do Azure. Se você já tiver uma VNET, verifique se ela atende aos critérios necessários e não tem um gateway de rede virtual.

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="vnet"></a>6. Conectar sua VNET a um hub

Nesta etapa, você pode criar a conexão de emparelhamento entre uma VNET e seu hub. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão de emparelhamento.

## <a name="device"></a>7. Baixar configuração de VPN

Use a configuração do dispositivo VPN para configurar seu dispositivo VPN local.

1. Na página de sua WAN virtual, clique em **Visão Geral**.
2. Na parte superior da página de visão geral, clique em **Baixar configuração de VPN**. O Azure cria uma conta de armazenamento no grupo de recursos 'microsoft-network-[local]', em que local é o local da rede remota. Depois de aplicar a configuração a seus dispositivos VPN, você poderá excluir essa conta de armazenamento.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Aplique a configuração ao dispositivo VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Entender o arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections**: essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** dos hubs virtuais da VNet<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** das VNets conectadas ao hub<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
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
                  "10.30.0.0/16"
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
* Uma nova WAN Virtual pode dar suporte a IKEv1 e IKEv2.
* A WAN Virtual só pode usar dispositivos VPN baseados em rota e instruções do dispositivo.

## <a name="viewwan"></a>8. Exibir a WAN virtual

1. Navegue até a WAN virtual.
2. Na página de visão geral, cada ponto do mapa representa um hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do hub.
3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="viewhealth"></a>9. Exibir a integridade do recurso

1. Navegue até a WAN.
2. Na página de sua WAN, na seção **SUPORTE + Solução de problemas**, clique em **Integridade** e exiba seu recurso.

## <a name="connectmon"></a>10. Monitorar uma conexão

Crie uma conexão para monitorar a comunicação entre uma VM do Azure e um site remoto. Para obter informações sobre como configurar um monitor de conexão, consulte [Monitorar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="cleanup"></a>11. Limpar recursos

Quando esses recursos não forem mais necessários, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um site
> * Criar um hub
> * Conectar um hub a um site
> * Conectar uma VNET a um hub
> * Baixar e aplicar a configuração do dispositivo VPN
> * Exibir a WAN virtual
> * Exibir a integridade dos recursos
> * Monitorar uma conexão

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
