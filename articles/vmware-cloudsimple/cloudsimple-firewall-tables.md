---
title: Solução do Azure VMware por tabelas CloudSimple-firewall
description: Saiba mais sobre as tabelas de firewall e as regras de firewall de nuvem privada do CloudSimple, incluindo as regras padrão criadas em cada tabela de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140863"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego de rede de e para recursos de nuvem privada. Você pode aplicar tabelas de firewall a uma VLAN/sub-rede. As regras controlam o tráfego de rede entre uma rede de origem ou um endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras de firewall

A tabela a seguir descreve os parâmetros em uma regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra de firewall e sua finalidade. |
| **Prioridade** | Um número entre 100 e 4096, com 100 sendo a prioridade mais alta. As regras são processadas em ordem de prioridade. Quando o tráfego encontra uma correspondência de regra, o processamento da regra é interrompido. Como resultado, as regras com prioridades mais baixas que têm os mesmos atributos que as regras com prioridades mais altas não são processadas.  Tome cuidado para evitar regras conflitantes. |
| **Rastreamento de estado** | O rastreamento pode ser sem estado (nuvem privada, Internet ou VPN) ou com estado (IP público).  |
| **Protocolo** | As opções incluem any, TCP ou UDP. Se você precisar de ICMP, use any. |
| **Direção** | Se a regra se aplica ao tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar para o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, bloco CIDR (roteamento entre domínios sem classificação) (10.0.0.0/24, por exemplo) ou qualquer um.  Especificar um intervalo, uma marca de serviço ou um grupo de segurança de aplicativo permite que você crie menos regras de segurança. |
| **Porta de origem** | Porta da qual o tráfego de rede é originado.  Você pode especificar uma porta individual ou um intervalo de portas, como 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança. |
| **Destino** | Um endereço IP, bloco CIDR (roteamento entre domínios sem classificação) (10.0.0.0/24, por exemplo) ou qualquer um.  Especificar um intervalo, uma marca de serviço ou um grupo de segurança de aplicativo permite que você crie menos regras de segurança.  |
| **Porta de destino** | Porta para a qual o tráfego de rede flui.  Você pode especificar uma porta individual ou um intervalo de portas, como 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra sem estado só é semelhante a pacotes individuais e filtra-as com base na regra.  
Regras adicionais podem ser necessárias para o fluxo de tráfego na direção inversa.  Use regras sem estado para o tráfego entre os seguintes pontos:

* Sub-redes de nuvens privadas
* Sub-rede local e uma sub-rede de nuvem privada
* Tráfego de Internet das nuvens privadas

### <a name="stateful"></a>Com estado

 Uma regra com estado está ciente das conexões que passam por ela. Um registro de fluxo é criado para as conexões existentes. A comunicação é permitida ou negada com base no estado de conexão do registro de fluxo.  Use esse tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras padrão

As regras padrão a seguir são criadas em cada tabela de firewall.

|Prioridade|Nome|Rastreamento de estado|Direction|Tipo de Tráfego|Protocolo|Fonte|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-tudo para a Internet|Com estado|Saída|IP público ou tráfego de Internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Allow|
|65001|negar-tudo-da-Internet|Com estado|Entrada|IP público ou tráfego de Internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir-tudo para a intranet|Sem estado|Saída|Tráfego interno ou VPN da nuvem privada|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Allow|
|65003|permitir-tudo-da-intranet|Sem estado|Entrada|Tráfego interno ou VPN da nuvem privada|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Allow|

## <a name="next-steps"></a>Próximas etapas

* [Configurar regras e tabelas de firewall](firewall.md)
