---
title: Solução VMware da CloudSimple no Azure – transferir uma rede de camada 2 local para a nuvem privada
description: Descreve como configurar uma VPN de camada 2 entre o NSX-T em uma nuvem privada do CloudSimple e um cliente NSX Edge autônomo local
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06446b6c36e36466fe891d7327d8151603cdecd2
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899364"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrar cargas de trabalho usando redes ampliadas da camada 2

Nesse guia, você aprenderá a usar a L2VPN (VPN de camada 2) para transferir uma rede de camada 2 do seu ambiente local para sua nuvem privada do CloudSimple. Essa solução permite migrar cargas de trabalho em execução em seu ambiente VMware local para a nuvem privada no Azure no mesmo espaço de endereço de sub-rede sem a necessidade de reutilizar o IP de suas cargas de trabalho.

A transferência baseada em L2VPN das redes de camada 2 pode trabalhar com ou sem redes baseadas no NSX em seu ambiente VMware local. Caso não tenha redes baseadas no NSX para cargas de trabalho locais, você poderá usar um Gateway de Serviços do NSX Edge autônomo.

> [!NOTE]
> Esse guia aborda o cenário em que os data centers local e de nuvem privada estão conectados via VPN site a site.

## <a name="deployment-scenario"></a>Cenário de implantação

Para transferir sua rede local usando a L2VPN, você deve configurar um servidor L2VPN (com destino ao roteador do NSX-T de Nível 0) e um cliente L2VPN (cliente autônomo de origem).  

Nesse cenário de implantação, sua nuvem privada é conectada ao seu ambiente local por meio de um túnel VPN site a site que permite que o gerenciamento e as sub-redes do vMotion locais se comuniquem com o gerenciamento e as sub-redes do vMotion de nuvem privada. Essa disposição é necessária para o vCenter vMotion (xVC-vMotion) cruzado. Um roteador do NSX-T de Nível 0 é implantado como um servidor L2VPN na nuvem privada.

O NSX Edge autônomo é implantado em seu ambiente local como um cliente L2VPN e, em seguida, é emparelhado ao servidor L2VPN. Um ponto de extremidade de túnel GRE é criado em cada lado e configurado para “transferir” a rede de camada 2 local para sua nuvem privada. Essa configuração é mostrada na figura a seguir.

![Cenário de implantação](media/l2vpn-deployment-scenario.png)

Para saber mais sobre a migração usando a L2VPN, consulte [Redes virtuais privadas](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação do VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Pré-requisitos para implantar a solução

Verifique se os itens a seguir estão ativos antes de implantar e configurar a solução:

* A versão do vSphere local é 6.7U1+ ou 6.5P03+.
* A licença local do vSphere está no nível Enterprise Plus (para o vSphere Distributed Switch).
* Identifique a rede de camada 2 da carga de trabalho a ser transferida para sua nuvem privada.
* Identifique uma rede de camada 2 em seu ambiente local para implantar seu dispositivo cliente L2VPN.
* [Já há uma nuvem privada criada](create-private-cloud.md).
* A versão do dispositivo NSX-T Edge autônomo é compatível com a versão do Gerenciador de NSX-T (NSX-T 2.3.0) usada em seu ambiente de nuvem privada.
* Um grupo de portas do tronco foi criado no vCenter local com transmissões forjadas habilitadas.
* Um endereço IP público foi reservado para ser usado para o endereço IP de uplink do cliente NSX-T autônomo, e 1:1 NAT está ativo para a tradução entre os dois endereços.
* O encaminhamento do DNS é definido nos servidores DNS locais para o domínio az.cloudsimple.io para apontar para os servidores DNS de nuvem privada.
* A latência de RTT é menor ou igual a 150 ms, conforme necessário para que o vMotion funcione nos dois sites.

## <a name="limitations-and-considerations"></a>Limitações e considerações

A tabela a seguir lista as versões do vSphere e os tipos de adaptadores de rede com suporte.  

| Versão do vSphere | Tipo de vSwitch de origem | Driver da NIC virtual | Tipo de vSwitch de destino | Com suporte? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Todos | DVS | Todos | DVS | Sim |
| 6\.7UI ou superior do vSphere, 6.5P03 ou superior | DVS | VMXNET3 | N-VDS | Sim |
| 6\.7UI ou superior do vSphere, 6.5P03 ou superior | DVS | E1000 | N-VDS | [Sem suporte pelo VWware](https://kb.vmware.com/s/article/56991) |
| 6\.7UI ou 6.5P03 do vSphere, NSX-V ou versões abaixo do NSX-T 2.2, 6.5P03 ou superior | Todos | Todos | N-VDS | [Sem suporte pelo VWware](https://kb.vmware.com/s/article/56991) |

A partir da versão do VMware NSX-T 2.3:

* O comutador lógico no lado da nuvem privada que é transferida para o local pela L2VPN não pode ser roteado ao mesmo tempo. O comutador lógico transferido não pode ser conectado a um roteador lógico.
* As VPNs do IPSEC baseadas em rota e a L2VPN só podem ser configuradas usando chamadas à API.

Para obter mais informações, consulte [Redes virtuais privadas](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação do VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Endereçamento de implantação da L2VPN de amostra

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Rede local em que o ESG autônomo (cliente L2VPN) está implantado

| **Item** | **Valor** |
|------------|-----------------|
| Nome da rede | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Endereço IP do dispositivo Edge autônomo | 10.250.0.111 |
| Endereço IP da NAT do dispositivo Edge autônomo | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Rede local a ser transferida

| **Item** | **Valor** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Esquema de IP de nuvem privada para o roteador do NSX-T de Nível 0 (servidor L2VPN)

| **Item** | **Valor** |
|------------|-----------------|
| Interface de loopback | 192.168.254.254/32 |
| Interface de túnel | 5.5.5.1/29 |
| Comutador lógico (transferido) | Stretch_LS |
| Interface de loopback (endereço IP da NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Rede de nuvem privada a ser mapeada para a rede transferida

| **Item** | **Valor** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Buscar a ID do roteador lógico necessária para a L2VPN

As etapas a seguir mostram como buscar a ID do roteador lógico da instância do roteador lógico de DR de Nível 0 para os serviços IPsec e L2VPN. Posteriormente, a ID do roteador lógico será necessária ao implementar a L2VPN.

1. Entre no Gerenciador do NSX-T `https://*nsx-t-manager-ip-address*` e selecione **Rede** > **Roteadores** > **Provedor-LR** > **Visão geral**. Para o **Modo de alta disponibilidade**, selecione **Ativo–Em espera**. Essa ação abre uma janela pop-up que mostra a VM do Edge na qual o roteador de Nível 0 está ativo no momento.

    ![Selecionar Ativo–Em espera](media/l2vpn-fetch01.png)

2. Selecione **Malha** > **Nós** > **Edges**. Anote o endereço IP do gerenciamento da VM do Edge ativa (Edge VM1) identificada na etapa anterior.

    ![Anotar a IP de gerenciamento](media/l2vpn-fetch02.png)

3. Abra uma sessão SSH para o endereço IP de gerenciamento da VM do Edge. Execute o comando ```get logical-router``` usando o nome de usuário **admin** e a senha **CloudSimple 123!** .

    ![Captura de tela que mostra uma sessão SSH aberta.](media/l2vpn-fetch03.png)

4. Caso não esteja vendo uma entrada “DR-Provider-LR”, conclua as etapas a seguir.

5. Crie dois comutadores lógicos com retorno de sobreposição. Um comutador lógico é transferido para o local onde ficam as cargas de trabalho migradas. Um outro comutador lógico é um comutador fictício. Para obter instruções, consulte [Criar um comutador lógico](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) na documentação do VMware.

    ![Criar comutador lógico](media/l2vpn-fetch04.png)

6. Anexe o comutador fictício ao roteador Nível 1 com um endereço IP local de link ou qualquer sub-rede não sobreposta do local ou da sua nuvem privada. Consulte [Adicionar uma porta de downlink em um roteador lógico de Nível 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) na documentação do VMware.

    ![Anexar o comutador fictício](media/l2vpn-fetch05.png)

7. Execute o comando `get logical-router` novamente na sessão SSH da VM do Edge. A UUID do roteador lógico “DR-Provider-LR” é exibida. Anote a UUID, a qual é necessária ao configurar a L2VPN.

    ![Captura de tela que mostra o UUID do roteador lógico.](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Buscar a ID do comutador lógico necessária para a L2VPN

1. Entre no Gerenciador NSX-T (`https://nsx-t-manager-ip-address`).
2. Selecione **Rede** > **Comutação** > **Comutadores** >  **<\Comutador lógico\>**  > **Visão geral**.
3. Anote a UUID do comutador lógico de transferência, a qual é necessária ao configurar a L2VPN.

    ![Obter saída do roteador lógico](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Considerações de roteamento e segurança para a L2VPN

Para estabelecer uma VPN baseada em rotas IPsec entre o roteador do NSX-T de Nível 0 e o cliente NSX Edge autônomo, a interface de loopback do roteador do NSX-T de Nível 0 deve ser capaz de se comunicar com o endereço IP público do cliente autônomo do NSX local por UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Permitir a UDP 500/4500 para o IPsec

1. [Crie um endereço IP público](public-ips.md) para a interface de loopback do NSX-T de Nível 0 no portal do CloudSimple.

2. [Crie uma tabela de firewall](firewall.md) com regras com estado que permitam o tráfego de entrada da UDP 500/4500 e anexe a tabela de firewall à sub-rede HostTransport do NSX-T.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Anunciar o IP da interface de loopback para a rede subjacente

1. Crie uma rota nula para a rede de interface de loopback. Entre no Gerenciador do NSX-T e selecione **Rede** > **Roteamento** > **Roteadores** > **Provedor-LR** > **Roteamento** > **Rotas Estáticas**. Clique em **Adicionar**. Em **Rede**, insira o endereço IP da interface de loopback. Em **Próximos Saltos**, clique em **Adicionar**, especifique “Nulo” para o próximo salto e mantenha o padrão de 1 para Distância do administrador.

    ![Adicionar rota estática](media/l2vpn-routing-security01.png)

2. Crie uma lista de prefixos IP. Entre no Gerenciador do NSX-T e selecione **Rede** > **Roteamento** > **Roteadores** > **Provedor-LR** > **Roteamento** > **Listas de Prefixos IP**. Clique em **Adicionar**. Insira um nome para identificar a lista. Em **Prefixos**, clique duas vezes em **Adicionar**. Na primeira linha, insira “0.0.0.0/0” em **Rede** e “Negar” em **Ação**. Na segunda linha, selecione **Qualquer** em **Rede** e **Permitir** em **Ação**.
3. Anexe a lista de prefixos IP a ambos os vizinhos do BGP (TOR). Anexar a lista de prefixos IP ao vizinho do BGP impede que a rota padrão seja anunciada no BGP para os comutadores TOR. Porém, qualquer outra rota que inclua a rota nula anunciará o endereço IP da interface de loopback para os comutadores TOR.

    ![Criar uma lista de prefixos IP](media/l2vpn-routing-security02.png)

4. Entre no Gerenciador do NSX-T e selecione **Rede** > **Roteamento** > **Roteadores** > **Provedor-LR** > **Roteamento** > **BGP** > **Vizinhos**. Selecione o primeiro vizinho. Clique em **Editar** > **Famílias de Endereços**. Na família IPv4, edite a coluna **Filtro de saída** e selecione a lista de prefixos IP que você criou. Clique em **Save** (Salvar). Repita essa etapa no segundo vizinho.

    ![Anexar lista de prefixos IP 1](media/l2vpn-routing-security03.png) ![Anexar lista de prefixos IP 2](media/l2vpn-routing-security04.png)

5. Redistribua a rota estática nula no BGP. Para anunciar a rota de interface de loopback para a parte subjacente, você deve redistribuir a rota estática nula no BGP. Entre no Gerenciador do NSX-T e selecione **Rede** > **Roteamento** > **Roteadores** > **Provedor-LR** > **Roteamento** > **Redistribuição de Rota** > **Vizinhos**. Selecione **Provider-LR-Route_Redistribution** e clique em **Editar**. Marque a caixa de seleção **Estática** e clique em **Salvar**.

    ![Redistribuir a rota estática nula no BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Configurar uma VPN baseada em rotas no roteador do NSX-T de Nível 0

Use o modelo a seguir para preencher todos os detalhes de configuração de uma VPN baseada em rotas no roteador NSX-T de Nível 0. As UUIDs em cada chamada POST serão necessárias nas chamadas POST subsequentes. Os endereços IP das interfaces de loopback e de túnel para a L2VPN devem ser exclusivas e não devem se sobrepor às redes locais ou de nuvem privada.

Os endereços IP escolhidos para interfaces de loopback e de túnel usadas para a L2VPN devem ser exclusivas e não devem se sobrepor às redes locais ou de nuvem privada. A rede da interface de loopback deve ser sempre /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Em todas as chamadas de API a seguir, substitua o endereço IP pelo seu endereço IP do Gerenciador de NSX-T. Você pode executar todas essas chamadas a partir da API do cliente POSTMAN ou usando comandos `curl`.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Habilitar o serviço VPN do IPSec no roteador lógico

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Criar perfis: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Criar perfis: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Criar perfis: Túnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Criar um ponto de extremidade local

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Criar um ponto de extremidade de par

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Criar uma sessão de VPN baseada em rotas

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Configurar a L2VPN no roteador NSX-T de Nível 0

Preencha as informações a seguir depois de cada chamada POST. As IDs serão necessárias em chamadas POST subsequentes.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Criar o serviço de L2VPN

A saída do comando GET a seguir ficará em branco porque a configuração ainda não foi concluída.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

No comando POST a seguir, a ID do roteador lógico é a UUID do roteador lógico de Nível 0 de DR obtida anteriormente.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Criar a sessão de L2VPN

Para o comando POST a seguir, a ID do serviço da L2VPN é a ID que você acabou de obter, e a ID da sessão de VPN do IPsec é a ID obtida na seção anterior.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Essas chamadas criam um ponto de extremidade de túnel GRE. Para verificar o status, execute o comando a seguir.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Criar uma porta lógica com a ID de túnel especificada

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Obter o código de par da L2VPN no lado do NSX-T

Obtenha o código de par do ponto de extremidade do NSX-T. O código de par é necessário ao configurar o ponto de extremidade remoto. A <session-id> da L2VPN pode ser obtida na seção anterior. Para obter mais informações, consulte a [Guia da API do NSX-T 2.3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Implantar o cliente autônomo do NSX-T (local)

Antes da implantação, verifique se as regras de firewall local permitem o tráfego da UDP 500/4500 de entrada e saída de/para o endereço IP público da CloudSimple que foi reservado anteriormente para a interface de loopback do roteador do NSX-T T0. 

1. [Baixe o OVF do cliente NSX Edge autônomo](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) e extraia os arquivos do pacote baixado para uma pasta.

    ![Baixar o cliente NSX Edge autônomo](media/l2vpn-deploy-client01.png)

2. Acesse a pasta com todos os arquivos extraídos. Selecione todos os vmdks (NSX-l2t-client-large.mf e NSX-l2t-client-large.ovf para tamanho de dispositivo grande ou NSX-l2t-client-Xlarge.mf e NSX-l2t-client-Xlarge.ovf for para tamanho de dispositivo extragrande). Clique em **Próximo**.

    ![Selecione ](media/l2vpn-deploy-client02.png) ![ captura de tela do modelo que mostra os arquivos VMDKs selecionados.](media/l2vpn-deploy-client03.png)

3. Insira um nome para o cliente autônomo do NSX-T e clique em **Próximo**.

    ![Inserir o nome do modelo](media/l2vpn-deploy-client04.png)

4. Clique em **Próximo** quantas vezes forem necessárias até acessar as configurações de armazenamento de dados. Selecione o armazenamento de dados apropriado para o cliente autônomo do NSX-T e clique em **Próximo**.

    ![Selecionar um armazenamento de dados](media/l2vpn-deploy-client06.png)

5. Selecione os grupos de portas corretos do Tronco (PG do Tronco), Público (PG do Uplink) e a Interface de HA (PG do Uplink) para o cliente autônomo do NSX-T. Clique em **Próximo**.

    ![Selecionar grupos de portas](media/l2vpn-deploy-client07.png)

6. Preencha os detalhes a seguir na tela **Personalizar modelo** e clique em **Próximo**:

    Expandir L2T:

    * **Endereço de par**. Insira o endereço IP reservado no portal CloudSimple do Azure para a interface de loopback do NSX-T de Nível 0.
    * **Código de par**. Cole o código de par obtido na última etapa da implantação do servidor L2VPN.
    * **VLAN de subinterfaces (ID do túnel)** . Insira a ID da VLAN a ser transferida. Entre parênteses (), insira a ID do túnel que foi previamente configurada.

    Expanda a interface do uplink:

    * **Endereço IP do DNS**. Insira o endereço IP do DNS local.
    * **Gateway padrão**.  Insira o gateway padrão da VLAN, o qual atuará como um gateway padrão para esse cliente.
    * **Endereço IP**. Insira o endereço IP do uplink do cliente autônomo.
    * **Comprimento do prefixo**. Insira o comprimento do prefixo da VLAN/sub-rede do uplink.
    * **Senha de usuário de administrador/habilitação/raiz da CLI**. Defina a senha para a conta de administrador/habilitação/raiz.

      ![Personalizar modelo](media/l2vpn-deploy-client08.png)
      ![Personalizar modelo – mais](media/l2vpn-deploy-client09.png)

7. Revise as configurações e clique em **Concluir**.

    ![Concluir a configuração](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Configurar uma porta do coletor local

Caso um dos sites da VPN não tenha o NSX implantado, você poderá configurar uma L2VPN por meio da implantação de um NSX Edge autônomo no site. Um NSX Edge autônomo é implantado usando um arquivo OVF em um host que não seja gerenciado pelo NSX. Isso implanta um dispositivo de Gateway de Serviços do NSX Edge para atuar como um cliente VPN de L2.

Caso um vNIC de tronco de borda autônomo esteja conectado a um Comutador distribuído vSphere, serão necessários um modo promíscuo ou uma porta de coletor para a função da L2VPN. O uso do modo promíscuo pode levar à duplicação de pings e respostas. Por isso, use o modo de porta de coletor na configuração do NSX Edge autônomo da L2VPN. Consulte [Configurar uma porta de coletor](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) na documentação do VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Verificação do IPsec da VPN e da L2VPN

Use os comandos a seguir para verificar as sessões do IPsec e da L2VPN do NSX-T Edge autônomo.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Use os comandos a seguir para verificar as sessões do IPsec e da L2VPN do roteador do NSX-T de Nível 0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Use os comandos a seguir para verificar a porta de coletor no host ESXi em que a VM do cliente autônomo do NSX-T reside no ambiente local.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
