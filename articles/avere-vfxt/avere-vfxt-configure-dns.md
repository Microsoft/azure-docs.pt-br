---
title: DNS do Avere vFXT – Azure
description: Configurar um servidor DNS para balanceamento de carga com round robin com o Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009017"
---
# <a name="avere-cluster-dns-configuration"></a>Configuração de DNS do cluster do Avere

Esta seção contém noções básicas sobre a configuração de um sistema DNS para o balanceamento de carga de seu cluster do Avere vFXT.

Este documento *não inclui* instruções para configurar e gerenciar um servidor DNS no ambiente do Azure.

Em vez de usar o DNS com round robin para balancear a carga de um cluster do vFXT no Azure, considere usar métodos manuais para atribuir endereços IP de maneira uniforme entre os clientes quando eles estiverem montados. Vários métodos são descritos em [Montar o cluster do Avere](avere-vfxt-mount-clients.md).

Tenha em mente as informações a seguir ao decidir se deseja ou não usar um servidor DNS:

* Se o sistema é acessado apenas por clientes NFS, não é necessário usar DNS – é possível especificar todos os endereços de rede usando endereços IP numéricos.

* Se o sistema dá suporte ao acesso de SMB (CIFS), o DNS é necessário, pois você precisa especificar um domínio DNS para o servidor do Active Directory.

* O DNS é necessário quando você quer usar autenticação com Kerberos.

## <a name="load-balancing"></a>Balanceamento de carga

Para distribuir a carga geral, configure o domínio DNS para usar a distribuição de carga round robin para endereços IP voltados para o cliente.

## <a name="configuration-details"></a>Detalhes da configuração

Quando os clientes acessam o cluster, o RRDNS equilibra automaticamente suas solicitações entre todas as interfaces disponíveis.

Para atingir o desempenho ideal, configure seu servidor DNS para lidar com endereços de cluster voltados para o cliente da maneira mostrada no diagrama a seguir.

Um vserver do cluster é mostrado à esquerda e os endereços IP aparecem no centro e à direita. Configure cada ponto de acesso do cliente com registros e ponteiros A conforme ilustrado.

![Diagrama do DNS com round robin no cluster do Avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Cada endereço IP voltado ao cliente deve ter um nome exclusivo para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são chamados de vs1-client-IP-* para maior clareza, mas na produção você provavelmente deve usar algo mais conciso, como cliente*.)

Os clientes montam o cluster usando o nome do vserver como argumento do servidor.

Modifique o arquivo ``named.conf`` do servidor DNS para definir a ordem cíclica para as consultas em seu vserver. Essa opção garante que todos os valores disponíveis sejam percorridos. Adicione uma instrução semelhante à seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os comandos ``nsupdate`` a seguir mostram um exemplo de como configurar o DNS corretamente:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Configurações de DNS do cluster

Especifique o servidor DNS que o cluster do vFXT usa na página de configuração **Cluster** > **Rede Administrativa**. As configurações na página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa DNS

Leia [Configurações de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no Guia de Configuração de Cluster do Avere para obter mais detalhes sobre como usar essa página.
