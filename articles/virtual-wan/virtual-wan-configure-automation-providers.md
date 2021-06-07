---
title: Diretrizes de automação de parceiros de WAN virtual do Azure | Microsoft Docs
description: Configure um ambiente de automação para conectar e configurar uma VPN local ou SD-WAN CPE, ou dispositivo de ramificação, para a WAN virtual do Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566462"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Diretrizes de automação para parceiros da WAN Virtual

Este artigo ajuda a entender como configurar o ambiente de automação para conectar e configurar um dispositivo de branch (um dispositivo de VPN local do cliente ou CPE SDWAN) para WAN Virtual do Azure. Se você é um provedor que fornece dispositivos de branch que podem acomodar conectividade de VPN sobre IPsec/IKEv2 ou IPsec/IKEv1, este artigo é para você.

Um dispositivo de branch (um dispositivo VPN local do cliente ou CPE SDWAN) normalmente usa um painel de dispositivo/controlador para ser provisionado. Com frequência, os administradores de solução SD-WAN podem usar um console de gerenciamento para pré-provisionar um dispositivo antes de conectá-lo à rede. Esse dispositivo compatível com VPN obtém sua lógica de plano de controle de um controlador. O dispositivo VPN ou controlador SD-WAN pode usar as APIs do Azure para automatizar a conectividade para a WAN Virtual do Azure. Esse tipo de conexão exige que o dispositivo local tenha um endereço IP público voltado para o exterior atribuído.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Antes de começar a automação

* Verifique se o dispositivo dá suporte ao IPsec IKEv1/IKEv2. Confira [políticas padrão](#default).
* Exiba as [APIs REST](#additional) que você usa para automatizar a conectividade com a WAN virtual do Azure.
* Experimente a experiência do portal de WAN Virtual do Azure.
* Depois, decida qual parte das etapas de conectividade você quer automatizar. Recomendamos automatizar no mínimo o seguinte:

  * Controle de acesso
  * Upload de informações de dispositivo de branch na WAN Virtual do Azure
  * Download da configuração do Azure e configuração da conectividade do dispositivo de branch na WAN Virtual do Azure

### <a name="additional-information"></a><a name ="additional"></a>Informações adicionais

* [API REST](/rest/api/virtualwan/virtualhubs) para automatizar a criação do Hub virtual
* [API REST](/rest/api/virtualwan/vpngateways) para automatizar o gateway de VPN do Azure para WAN virtual
* [API REST](/rest/api/virtualwan/vpnconnections) para conectar um VPNSite a um hub de VPN do Azure
* [Políticas IPsec padrão](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Experiência do usuário

Entenda a experiência esperada do cliente em conjunto com a WAN Virtual do Azure.

  1. Normalmente, um usuário de WAN virtual começará o processo criando um recurso de WAN Virtual.
  2. O usuário configurará o acesso ao grupo de recursos com base em uma entidade de serviço para o sistema local (seu controlador de branch ou software de provisionamento de dispositivo VPN) para gravar informações sobre o branch na WAN Virtual do Azure.
  3. Nesse momento, o usuário poderá decidir fazer logon em sua interface do usuário e configurar as credenciais da entidade de serviço. Assim que isso estiver concluído, o controlador poderá carregar informações do branch com a automação fornecida por você. O equivalente manual disso no lado do Azure é "Criar Site".
  4. Depois que as informações de site (dispositivo de ramificação) estiverem disponíveis no Azure, o usuário irá conectar o site a um Hub. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). O hub é o núcleo da sua rede em uma região. Pode haver apenas um hub por região do Azure, e o ponto de extremidade de vpn (vpngateway) dentro dele é criado durante esse processo. O gateway de VPN é um gateway escalonável que dimensiona apropriadamente com base nas necessidades de largura de banda e conexão. Você pode optar por automatizar o hub virtual e a criação de vpngateway no painel do controlador de dispositivo de seu branch.
  5. Após a associação do Hub virtual ao site, um arquivo de configuração será gerado para download manual do usuário. É aqui que sua automação entre em cena e torna a experiência do usuário perfeita. Em vez de o usuário precisar baixar e configurar manualmente o dispositivo de branch, você pode definir a automação e fornecer uma experiência mínima de cliques em sua interface do usuário, atenuando problemas comuns de conectividade, como a incompatibilidade de chave compartilhada, do parâmetro de IPSec, legibilidade do arquivo de configuração etc.
  6. No final desta etapa em sua solução, o usuário terá uma conexão site a site perfeita entre o dispositivo de branch e um hub virtual. Você também pode configurar conexões adicionais em outros hubs. Cada conexão é um túnel ativo-ativo. Seu cliente pode optar por usar um ISP diferente para cada um dos links para o túnel.
  7. Considere fornecer recursos de monitoramento e solução de problemas na interface de gerenciamento do CPE. Os cenários típicos incluem "o cliente não pode acessar os recursos do Azure devido a um problema de CPE", "mostrar parâmetros de IPsec no lado da CPE" etc.

## <a name="automation-details"></a><a name ="understand"></a>Detalhes da automação

###  <a name="access-control"></a><a name="access"></a>Controle de acesso

Os clientes devem ser capazes de configurar o controle de acesso apropriado para WAN Virtual na interface do usuário do dispositivo. Para isso, é recomendável usar uma Entidade de Serviço do Azure. O acesso baseado em entidade de serviço fornece a autenticação apropriada do controlador de dispositivo para carregar informações de branch. Para saber mais, confira [Criar entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Embora essa funcionalidade esteja fora da oferta de WAN Virtual do Azure, listamos abaixo as etapas típicas para configurar o acesso no Azure, após as quais os detalhes relevantes são inseridos no painel de gerenciamento do dispositivo

* Criar um aplicativo do Azure Active Directory para o controlador de dispositivo local.
* Obter chave de autenticação e ID do aplicativo
* Obter a ID do locatário
* Atribuir ao aplicativo a função de "Colaborador"

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Fazer upload de informações do dispositivo de branch

Você deve projetar a experiência do usuário para carregar informações de Branch (site local) no Azure. Você pode usar [APIs REST](/rest/api/virtualwan/vpnsites) para VPNSite para criar as informações do site na WAN virtual. É possível fornecer todos os dispositivos VPN/SDWAN de branch ou selecionar personalizações de dispositivo, conforme apropriado.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Conectividade e download da configuração do dispositivo

Esta etapa envolve o download da configuração do Azure e configuração da conectividade do dispositivo de branch na WAN Virtual do Azure. Nesta etapa, um cliente que não estivesse usando um provedor, baixaria manualmente a configuração do Azure e a aplicaria ao dispositivo VPN/SDWAN local. Como provedor, é necessário automatizar esta etapa. Exiba as [APIs REST](/rest/api/virtualwan/vpnsitesconfiguration/download) de download para obter informações adicionais. O controlador de dispositivo pode chamar a API REST ' GetVpnConfiguration ' para baixar a configuração do Azure.

**Notas de configuração**

  * Se VNets do Azure estiverem anexadas ao hub virtual, elas aparecerão como ConnectedSubnets.
  * A conectividade VPN usa a configuração baseada em rota e dá suporte aos protocolos IKEv1 e IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Arquivo de configuração do dispositivo

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections -** Essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** do(s) hub(s) virtual(is) da VNet.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** das VNets conectadas ao hub.<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Endereços IP** do hub virtual vpngateway. Como o vpngateway tem cada conexão composta por 2 túneis na configuração ativa-ativa, você verá os dois endereços IP listados neste arquivo. Neste exemplo, você vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes da configuração da conexão do Vpngateway** como BGP, chave pré-compartilhada, etc. O PSK é a chave pré-compartilhada gerada automaticamente para você. Você sempre pode editar a conexão na página Visão Geral de um PSK personalizado.
  
**Exemplo de arquivo de configuração de dispositivo**

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

## <a name="connectivity-details"></a><a name="default"></a>Detalhes de conectividade

O dispositivo VPN/SDWAN local ou a configuração SD-WAN devem corresponder ou conter os algoritmos e parâmetros a seguir, especificados na política de IPsec/IKE do Azure.

* Algoritmo de criptografia IKE
* Algoritmo de integridade de IKE
* Grupo DH
* Algoritmo de criptografia IPsec
* Algoritmo de integridade de IPsec
* Grupo PFS

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Políticas padrão para conectividade IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Políticas personalizadas para conectividade IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre WAN Virtual, consulte [Sobre a WAN Virtual do Azure](virtual-wan-about.md) e [Perguntas frequentes sobre a WAN Virtual do Azure](virtual-wan-faq.md).

Para saber mais, envie um email para <azurevirtualwan@microsoft.com>. Inclua nome da sua empresa entre "[ ]" na linha do assunto.