---
title: Arquitetura de rede do Azure
description: Este artigo fornece uma descrição geral da rede de produção Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: 48a7e52d4284e5c2db1d77d24d91fd4701aad8d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587132"
---
# <a name="azure-network-architecture"></a>Arquitetura de rede do Azure
A arquitetura de rede do Azure segue uma versão modificada do modelo de núcleo/distribuição/acesso padrão do setor, com camadas de hardware distintas. As camadas incluem:

- Núcleo (roteadores de datacenter)
- Distribuição (roteadores de acesso e agregação de L2). A camada de distribuição separa o roteamento de L3 da comutação de L2.
- Acesso (opções de host de L2)

A arquitetura de rede tem dois níveis de opções de camada 2. Uma camada agrega o tráfego da outra camada. A segunda camada faz um loop para incorporar redundância. A arquitetura oferece uma superfície de VLAN mais flexível e melhora o dimensionamento da porta. A arquitetura mantém L2 e L3 distintas, que permite o uso de hardware em cada uma das camadas distintas na rede e minimiza a falha em uma camada não afetem a outra camada (s). Permite o uso de troncos para compartilhamento de recursos, como a conectividade com a infraestrutura de L3.

## <a name="network-configuration"></a>Configuração de rede
A arquitetura de rede de um cluster do Azure em um data center consiste nos seguintes dispositivos:

- Roteadores (datacenter, roteador de acesso e roteadores de folha de borda)
- Comutadores (comutadores de agregação e topo de rack)
- Digi CMs
- Unidades de distribuição de energia

O Azure tem duas arquiteturas separadas. Alguns clientes do Azure existentes e os serviços compartilhados residem na DLA (arquitetura de LAN) padrão, enquanto novas regiões e clientes virtuais residem na arquitetura do Quantum 10 (Q10). A arquitetura do DLA é um projeto de árvore tradicional com roteadores de acesso ativos/passivos e ACLs (listas de controle de acesso) de segurança aplicadas aos roteadores de acesso. A arquitetura do Quantum 10 é um design Close/malha de roteadores, em que as ACLs não são aplicadas nos roteadores. Em vez disso, as ACLs são aplicadas abaixo o roteamento, por meio de SLB (Balanceamento de Carga de Software) ou VLANs definida por software.

O diagrama a seguir apresenta uma visão geral da arquitetura de rede dentro de um cluster do Azure:

![Diagrama da rede do Azure][1]

### <a name="quantum-10-devices"></a>Dispositivos Quantum 10
O design do Quantum 10 conduz a comutação de camada 3 distribuída em vários dispositivos em um design Clos/malha. As vantagens do design P10 incluem maior funcionalidade e maior capacidade de dimensionar a infraestrutura de rede existente. O design utiliza roteadores de folha de borda, comutadores de lombada e roteadores de topo do rack para passar tráfego a clusters entre várias rotas, permitindo tolerância a falhas. Balanceamento de carga de software, em vez de dispositivos de hardware, lida com serviços de segurança, como tradução de endereço de rede.

### <a name="access-routers"></a>Roteadores de acesso
Os roteadores de acesso/distribuição L3 (ARs) executam a funcionalidade de roteamento principal para as camadas de acesso e distribuição. Esses dispositivos são implantados como um par e são o gateway padrão para as sub-redes. Cada par de RA pode dar suporte a vários pares de comutador de agregação de L2, dependendo da capacidade. O número máximo depende na capacidade do dispositivo, bem como os domínios de falha. Um número típico é três pares de comutador de agregação de L2 por par de RA.

### <a name="l2-aggregation-switches"></a>Comutadores de agregação de L2  
Esses dispositivos servem como um ponto de agregação para o tráfego de L2. Eles são a camada de distribuição para a malha de L2 e podem lidar com grandes quantidades de tráfego. Como esses dispositivos agregam tráfego, eles exigem a funcionalidade 802.1q, além de tecnologias de alta largura de banda, como agregação de porta e 10GE.

### <a name="l2-host-switches"></a>Comutadores de host de L2
Hosts se conecta diretamente a essas opções. Eles podem ser comutadores montados em rack ou implantações de chassi. O padrão de 802.1q permite a designação de uma VLAN como uma VLAN nativa, tratando VLAN como normal quadros Ethernet (sem marcas). Em circunstâncias normais, os quadros na VLAN nativa são transmitidos e recebidos sem marcas em um 802.1q porta de tronco. Esse recurso foi projetado para a migração para 802.1q e compatibilidade com non-802.1q dispositivos habilitados. Nessa arquitetura, apenas a infraestrutura de rede utiliza a VLAN nativa.

Essa arquitetura especifica um padrão para a seleção de VLAN nativa. O padrão garante que, sempre que possível, os dispositivos de RA tenham uma VLAN nativa exclusiva para cada tronco e a L2Aggregation para troncos de L2Aggregation. O L2Aggregation para troncos L2Host Switch tiver uma VLAN nativa de não-padrão.

### <a name="link-aggregation-8023ad"></a>Agregação de link (802.3ad)
A agregação de link permite que vários links individuais sejam agrupados e tratados como um único link lógico. Para facilitar a depuração operacional, o número usado para designar as interfaces entre porta e canal deve ser padronizado. O restante da rede usa o mesmo número em ambas as extremidades de uma porta e canal.

Os números especificados para o L2Agg ao comutador L2Host são os números de porta de canal usados no lado do L2Agg. Como o intervalo de números é mais limitado no lado do L2Host, o padrão é usar números de 1 e 2 no lado do L2Host. Eles fazem referência à porta e canal indo para o lado "a" e para o lado "b", respectivamente.

### <a name="vlans"></a>VLANs
A arquitetura de rede usa VLANs para servidores de grupo juntos em um único domínio de difusão. Os números da VLAN estão em conformidade com padrões 802.1q, que oferecem suporte a VLANs numeradas de 1 a 4094.

### <a name="customer-vlans"></a>VLANS do cliente
Você tem várias opções de implementação de VLAN para implantar no portal do Azure para atender às necessidades de separação e arquitetura da sua solução. Você implanta essas soluções por meio de máquinas virtuais. Para exemplos de arquitetura de referência do cliente, veja [Arquiteturas de referência do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Arquitetura de borda
Data centers do Azure baseiam-se a infraestruturas de rede bem provisionada e altamente redundantes. A Microsoft implementa redes nos datacenters do Azure com arquiteturas de redundância "necessidade mais de um" (N + 1) ou melhor. Os recursos completos de failover dentro e entre os datacenters ajudam a garantir a disponibilidade da rede e do serviço. Externamente, os datacenters são atendidos por circuitos de rede dedicados de alta largura de banda. Esses circuitos conectam de modo redundante propriedades com mais de 1.200 provedores de serviços de Internet globalmente em vários pontos de emparelhamento. Isso fornece mais de 2.000 Gbps de capacidade de borda em potencial em toda a rede.

Os roteadores de filtragem na camada de acesso e na borda da rede do Azure fornecem segurança bem estabelecida no nível de pacote e ajudam a impedir tentativas não autorizadas para se conectar ao Azure. Os roteadores ajudam a garantir que o conteúdo real dos pacotes inclua dados no formato esperado e esteja em conformidade com o esquema de comunicação cliente/servidor esperado. O Azure implementa uma arquitetura em camadas que consiste nos seguintes componentes de controle de acesso e segregação de rede:

- **Roteadores de borda.** Segregam o ambiente de aplicativo da Internet. Os roteadores de borda são projetados para fornecer proteção contra falsificação e limitar o acesso usando ACLs.
- **Roteadores de distribuição (acesso).** Eles permitem somente endereços IP aprovados pela Microsoft, fornecem recurso contra falsificação e estabelecem conexões usando ACLs.

### <a name="ddos-mitigation"></a>Mitigação de DDOS
Ataques de negação de serviço distribuído (DDoS) continuam a impor uma ameaça real à confiabilidade dos serviços online. Conforme os ataques se tornam mais direcionados e sofisticados, e conforme os serviços que a Microsoft fornece se tornam mais geograficamente diversificados, identificar e minimizar o impacto desses ataques é uma alta prioridade.

O [Padrão de Proteção contra DDoS do Azure](../virtual-network/ddos-protection-overview.md) oferece uma defesa contra ataques de DDoS. Consulte [Proteção contra DDoS do Azure: práticas recomendadas e arquiteturas de referência](azure-ddos-best-practices.md) para saber mais.

> [!NOTE]
> A Microsoft fornece proteção contra DDoS por padrão para todos os clientes do Azure.
>
>

## <a name="network-connection-rules"></a>Regras de conexão de rede
Na sua rede, o Azure implanta roteadores de borda para fornecer segurança no nível de pacote para impedir tentativas não autorizadas de conexão ao Azure. Os roteadores de borda garantem que o conteúdo real dos pacotes contenha dados no formato esperado e estejam em conformidade com o esquema de comunicação de cliente/servidor esperado.

Roteadores de borda segregam o ambiente de aplicativo da Internet. Esses roteadores são projetados para fornecer proteção contra falsificação, e limitam o acesso por meio de ACLs. A Microsoft configura roteadores de borda usando uma abordagem de ACL em camadas para limitar os protocolos de rede que têm permissão para transitar em roteadores de borda e roteadores de acesso.

A Microsoft posiciona os dispositivos de rede nos locais de acesso e de borda para que atuem como pontos de limite nos quais os filtros de entrada ou saída são aplicados.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
