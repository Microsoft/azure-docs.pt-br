---
title: Solução do Azure VMware por CloudSimple-configure a alta disponibilidade do gateway de VPN local para CloudSimple
description: Descreve como configurar uma conexão de alta disponibilidade do seu ambiente local para um gateway de VPN CloudSimple habilitado para alta disponibilidade
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895692"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Configurar uma conexão de alta disponibilidade do gateway de VPN local para CloudSimple

Os administradores de rede podem configurar uma conexão VPN site a site IPsec de alta disponibilidade a partir de seu ambiente local para um gateway de VPN CloudSimple.

Este guia apresenta as etapas para configurar um firewall local para uma conexão de alta disponibilidade VPN site a site IPsec. As etapas detalhadas são específicas para o tipo de firewall local. Como exemplos, este guia apresenta as etapas para dois tipos de firewalls: redes Cisco ASA e Palo Alto.

## <a name="before-you-begin"></a>Antes de começar

Conclua as tarefas a seguir antes de configurar o firewall local.

1. Verifique se sua organização [provisionou](create-nodes.md) os nós necessários e criou pelo menos uma nuvem privada CloudSimple.
2. [Configure um gateway de VPN site a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre sua rede local e sua nuvem privada do CloudSimple.

Consulte [visão geral de gateways de VPN](cloudsimple-vpn-gateways.md) para obter as propostas da fase 1 e da fase 2 com suporte.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Configurar o Firewall do Cisco ASA local

As instruções nesta seção se aplicam ao Cisco ASA versão 8,4 e posterior. No exemplo de configuração, a versão 9,10 do software Cisco Adaptive Security Appliance é implantada e configurada no modo IKEv1.

Para que a VPN site a site funcione, você deve permitir UDP 500/4500 e ESP (protocolo IP 50) do IP público CloudSimple primário e secundário (IP de mesmo nível) na interface externa do gateway de VPN Cisco ASA local.

### <a name="1-configure-phase-1-ikev1"></a>1. configurar a fase 1 (IKEv1)

Para habilitar a fase 1 (IKEv1) na interface externa, insira o comando da CLI a seguir no firewall do Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. criar uma política IKEv1

Crie uma política IKEv1 que define os algoritmos e métodos a serem usados para hash, autenticação, Diffie-Hellman grupo, tempo de vida e criptografia.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. criar um grupo de túnel

Crie um grupo de túnel sob os atributos IPsec. Configure o endereço IP do par e a chave pré-compartilhada do túnel, que você define ao [configurar seu gateway de VPN site a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. configurar a fase 2 (IPsec)

Para configurar a fase 2 (IPsec), crie uma ACL (lista de controle de acesso) que define o tráfego a ser criptografado e encapsulado. No exemplo a seguir, o tráfego de interesse é do túnel que é originado da sub-rede local (10.16.1.0/24) para a sub-rede remota da nuvem privada (192.168.0.0/24). A ACL pode conter várias entradas se houver várias sub-redes entre os sites.

No Cisco ASA versões 8,4 e posteriores, objetos ou grupos de objetos podem ser criados para servir como contêineres para redes, sub-redes, endereços IP de host ou vários objetos. Crie um objeto para o local e um objeto para as sub-redes remotas e use-os para a ACL de criptografia e as instruções de NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definir uma sub-rede local como um objeto

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definir a sub-rede remota CloudSimple como um objeto

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Configurar uma lista de acesso para o tráfego de interesse

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. configurar o conjunto de transformação

Configure o conjunto de transformação (TS), que deve envolver a palavra-chave ```ikev1``` . Os atributos de criptografia e hash especificados no TS devem corresponder com os parâmetros listados em [configuração padrão para gateways de VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. configurar o mapa de criptografia

Configure o mapa de criptografia, que contém estes componentes:

* Endereço IP do par
* ACL definida que contém o tráfego de interesse
* Conjunto de transformação

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. aplicar o mapa de criptografia

Aplique o mapa de criptografia na interface externa:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. confirmar as regras NAT aplicáveis

A seguir está a regra NAT que é usada. Verifique se o tráfego VPN não está sujeito a nenhuma outra regra NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Exemplo de saída de VPN site a site IPsec estabelecida do Cisco ASA

Saída da fase 1:

![Saída da fase 1 para o Firewall do Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Saída da fase 2:

![Saída da fase 2 para o Firewall do Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Configurar o firewall de redes do Palo Alto local

As instruções nesta seção se aplicam ao Palo Alto Networks versão 7,1 e posterior. Neste exemplo de configuração, o Palo Alto Networks VM-Series software versão 8.1.0 é implantado e configurado no modo IKEv1.

Para que a VPN site a site funcione, você deve permitir UDP 500/4500 e ESP (protocolo IP 50) do IP público CloudSimple primário e secundário (IP de mesmo nível) na interface externa do gateway de redes Palo Alto local.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. criar interfaces de encapsulamento primário e secundário

Entre no Palo Alto firewall, selecione   >  **interface**  >  de rede **túnel**  >  **Adicionar**, configure os campos a seguir e clique em **OK**.

* Nome da interface. O primeiro campo é preenchido automaticamente com a palavra-chave ' Tunnel '. No campo adjacente, insira qualquer número entre 1 e 9999. Essa interface será usada como uma interface de túnel primária para transportar o tráfego site a site entre o datacenter local e a nuvem privada.
* Mente. Insira comentários para facilitar a identificação da finalidade do túnel
* Perfil do NetFlow. Mantenha o padrão.
* Configuração. Atribuir interface a: roteador virtual: selecione **padrão**. 
        Zona de segurança: selecione a zona para o tráfego de LAN confiável. Neste exemplo, o nome da zona para o tráfego de LAN é ' Trust '.
* IPv4. Clique em **Adicionar** e adicione qualquer endereço IP não usado/32 não sobreposto em seu ambiente, que será atribuído à interface de túnel primária e será usado para monitorar os túneis (explicados posteriormente).

Como essa configuração é para uma VPN de alta disponibilidade, são necessárias duas interfaces de túnel: uma primária e uma secundária. Repita as etapas anteriores para criar a interface de túnel secundária. Selecione uma ID de túnel diferente e um endereço IP não usado/32 diferente.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. configurar rotas estáticas para sub-redes de nuvem privada a serem acessadas pela VPN site a site

As rotas são necessárias para que as sub-redes locais alcancem sub-redes de nuvem privada do CloudSimple.

Selecione   >  **roteadores virtuais** de rede  >    >  **rotas estáticas** padrão  >  **Adicionar**, configure os campos a seguir e clique em **OK**.

* Nome. Insira qualquer nome para facilitar a identificação da finalidade da rota.
* Destino. Especifique as sub-redes da nuvem privada do CloudSimple a serem acessadas pelas interfaces de túnel S2S do local
* Interface. Selecione a interface de túnel primária criada na etapa 1 (seção-2) na lista suspensa. Neste exemplo, é Tunnel. 20.
* Próximo salto. Selecione **Nenhum**.
* Distância do administrador. Mantenha o padrão.
* Medidas. Insira qualquer valor de 1 a 65535. A chave é inserir uma métrica menor para a rota correspondente à interface de túnel primária em comparação com a interface de túnel secundária correspondente que torna a rota anterior preferida. Se Tunnel. 20 tiver um valor de métrica de 20, em oposição a um valor de métrica de 30 para Tunnel. 30, o Tunnel. 20 é preferencial.
* Tabela de rotas. Mantenha o padrão.
* Perfil de BFD. Mantenha o padrão.
* Monitoramento de caminho. Deixe essa opção desmarcada.

Repita as etapas anteriores para criar outra rota para sub-redes de nuvem privada para usar como uma rota secundária/de backup por meio da interface de encapsulamento secundário. Desta vez, selecione ID de túnel diferente e uma métrica mais alta do que para a rota primária.

### <a name="3-define-the-cryptographic-profile"></a>3. definir o perfil criptográfico

Defina um perfil criptográfico que especifique os protocolos e algoritmos para identificação, autenticação e criptografia a serem usados para configurar túneis de VPN na fase 1 IKEv1.

Selecione **rede**  >  **expandir rede perfis**  >  **Ike criptografia**  >  **Adicionar**, configure os campos a seguir e clique em **OK**.

* Nome. Insira qualquer nome do perfil de criptografia IKE.
* Grupo DH. Clique em **Adicionar** e selecione o grupo DH apropriado.
* Criptografia. Clique em **Adicionar** e selecione o método de criptografia apropriado.
* Autenticação. Clique em **Adicionar** e selecione o método de autenticação apropriado.
* Tempo de vida da chave. Mantenha o padrão.
* Autenticação IKEv2 múltipla. Mantenha o padrão.

### <a name="4-define-ike-gateways"></a>4. definir gateways IKE

Defina os gateways IKE para estabelecer a comunicação entre os pares em cada extremidade do túnel VPN.

Selecione **rede**  >  **expandir redes perfis**  >  **Ike gateways**  >  **Adicionar**, configure os campos a seguir e clique em **OK**.

Guia Geral:

* Nome. Insira o nome do gateway IKE a ser emparelhado com o par VPN primário CloudSimple.
* Versão. Selecione o **modo somente IKEv1**.
* Tipo de endereço. Selecione **IPv4**.
* Interface. Selecione a interface voltada para o público ou para o exterior.
* Endereço IP local. Mantenha o padrão.
* Tipo de endereço IP do par. Selecione **IP**.
* Endereço de par. Insira o endereço IP do par VPN CloudSimple primário.
* Autenticação. Selecione a **chave pré-compartilhada**.
* Chave pré-compartilhada/confirmar chave pré-compartilhada. Insira a chave pré-compartilhada para corresponder à chave de gateway de VPN CloudSimple.
* Identificação local. Insira o endereço IP público do firewall do Palo Alto local.
* Identificação de par. Insira o endereço IP do par VPN CloudSimple primário.

Guia Opções avançadas:

* Habilitar o modo passivo. Deixe essa opção desmarcada.
* Habilite a passagem NAT. Deixe desmarcado se o Firewall do Palo Alto local não estiver atrás de nenhum dispositivo NAT. Caso contrário, marque a caixa de seleção.

IKEv1

* Modo do Exchange. Selecione **principal**.
* Perfil de criptografia IKE. Selecione o perfil de criptografia IKE que você criou anteriormente. Deixe a caixa habilitar fragmentação desmarcada.
* Detecção de pares inativos. Deixe a caixa desmarcada.

Repita as etapas anteriores para criar o gateway IKE secundário.

### <a name="5-define-ipsec-crypto-profiles"></a>5. definir perfis de criptografia IPSEC

Selecione **rede**  >  **expandir perfis de rede**  >  **criptografia IPSec**  >  **Adicionar**, configure os campos a seguir e clique em **OK**.

* Nome. Insira um nome para o perfil de criptografia IPsec.
* Protocolo IPsec. Selecione **ESP**.
* Criptografia. Clique em **Adicionar** e selecione o método de criptografia apropriado.
* Autenticação. Clique em **Adicionar** e selecione o método de autenticação apropriado.
* Grupo DH. Selecione **no-PFS**.
* Existência. Defina como 30 minutos.
* Habilitar. Deixe a caixa desmarcada.

Repita as etapas anteriores para criar outro perfil de criptografia IPsec, que será usado como o par de VPN CloudSimple secundário. O mesmo perfil de criptografia IPSEC também pode ser usado para os túneis IPsec primário e secundário (consulte o procedimento a seguir).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. definir perfis de monitor para monitoramento de túnel

Selecione **rede**  >  **expandir rede perfis**  >  **Monitor**  >  **Adicionar**, configure os campos a seguir e clique em **OK**.

* Nome. Insira qualquer nome do perfil de monitor a ser usado para o monitoramento de túnel para a reação proativa para a falha.
* Action. Selecione **fazer failover.**
* Intervalo. Insira o valor **3**.
* Limite. Insira o valor **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Configure túneis IPsec primários e secundários.

Selecione   >  **túneis IPsec**  >  de rede **Adicionar**, configure os campos a seguir e clique em **OK**.

Guia Geral:

* Nome. Insira qualquer nome para o túnel IPSEC primário a ser emparelhado com o par VPN primário CloudSimple.
* Interface de túnel. Selecione a interface de túnel primária.
* Digite. Mantenha o padrão.
* Tipo de endereço. Selecione **IPv4**.
* Gateway IKE. Selecione o gateway IKE primário.
* Perfil de criptografia IPsec. Selecione o perfil IPsec primário. Selecione **Mostrar opções avançadas**.
* Habilite a proteção de repetição. Mantenha o padrão.
* Copie o cabeçalho do TOS. Deixe a caixa desmarcada.
* Monitor de túnel. Marque a caixa.
* IP de destino. Insira qualquer endereço IP que pertença à sub-rede da nuvem privada do CloudSimple que é permitida pela conexão site a site. Verifique se as interfaces de túnel (como Tunnel. 20-10.64.5.2/32 e Tunnel. 30-10.64.6.2/32) no Palo Alto têm permissão para alcançar o endereço IP da nuvem privada CloudSimple pela VPN site a site. Consulte a configuração a seguir para obter as IDs de proxy.
* Criar. Selecione o perfil do monitor.

Guia IDs de proxy: clique em **IPv4**  >  **Adicionar** e configure o seguinte:

* ID do proxy. Insira qualquer nome para o tráfego interessante. Pode haver várias IDs de proxy transportadas dentro de um túnel IPsec.
* Local. Especifique as sub-redes locais no local que têm permissão para se comunicar com sub-redes de nuvem privada na VPN site a site.
* Controle. Especifique as sub-redes remotas da nuvem privada que têm permissão para se comunicar com as sub-redes locais.
* Protocolo. Selecione **qualquer**.

Repita as etapas anteriores para criar outro túnel IPsec a ser usado para o par de VPN CloudSimple secundário.

## <a name="references"></a>Referências

Configurando o NAT no Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Guia de configuração do Cisco ASA 5500 Series</a>

Atributos IKEv1 e IKEv2 com suporte no Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Guia de configuração da CLI da Cisco ASA Series</a>

Configurando VPN site a site do IPsec no Cisco ASA com a versão 8,4 e posteriores:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Configurar túneis de site a site IKEv1 IPsec com o ASDM ou a CLI no ASA</a>

Configurando o Cisco Adaptive Security Appliance virtual (ASAv) no Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Guia de início rápido do ASAv (dispositivo virtual de segurança adaptável) da Cisco</a>

Configurando a VPN site a site com IDs de proxy no Palo Alto:

[Configurar VPN site a site](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Configurando o monitor de túnel:

[Configurar o monitoramento do túnel](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operações de túnel de IPsec ou gateway IKE:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Habilitar/desabilitar, atualizar ou reiniciar um gateway IKE ou túnel IPsec</a>
