---
title: Recursos de segurança do Banco de Dados SQL do Azure
description: Este artigo fornece uma descrição geral de como o Banco de Dados SQL do Azure protege os dados do cliente no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84021850"
---
# <a name="azure-sql-database-security-features"></a>Recursos de segurança do Banco de Dados SQL do Azure    
O Banco de Dados SQL do Azure fornece um serviço de banco de dados relacional no Azure. Para proteger os dados do cliente e fornecer recursos de segurança fortes que eles esperam de um serviço de banco de dados relacional, o Banco de Dados SQL tem seus próprios conjuntos de recursos de segurança. Esses recursos se baseiam nos controles que são herdados do Azure.

## <a name="security-capabilities"></a>Funcionalidades de segurança

### <a name="usage-of-the-tds-protocol"></a>Uso do protocolo TDS
O Banco de Dados SQL do Azure oferece suporte apenas ao protocolo de fluxo de dados tabulares (TDS), que exige que o banco de dados seja acessível somente pela porta padrão do TCP/1433.

### <a name="azure-sql-database-firewall"></a>Firewall do Banco de Dados SQL do Azure
Para ajudar a proteger os dados do cliente, o banco de dado SQL do Azure inclui uma funcionalidade de firewall, que, por padrão, impede todo o acesso ao SQL Database, como mostrado abaixo.

![Firewall do Banco de Dados SQL do Azure](./media/infrastructure-sql/sql-database-firewall.png)

O firewall do gateway pode limitar endereços, o que permite que o controle granular dos clientes especifique intervalos de endereços IP aceitáveis. O firewall concede acesso com base no endereço IP de origem de cada solicitação.

Os clientes podem obter a configuração de firewall usando um portal de gerenciamento ou programaticamente usando a API REST do Banco de Dados SQL do Azure Management. O Firewall do gateway do banco de dados SQL do Azure, por padrão, impede o acesso de TDS ao banco de dados SQL do Azure. Os clientes devem configurar o acesso usando listas de controle de acesso (ACLs) para permitir conexões do banco de dados SQL do Azure por endereços de Internet, protocolos e números de porta de origem e de destino.

### <a name="dosguard"></a>DoSGuard
Ataques de negação de serviço (DoS) são reduzidos por um serviço de gateway de banco de dados SQL chamado DoSGuard. O DoSGuard controla ativamente os logons com falha de endereços IP. Se houver vários logons com falha de um endereço IP específico dentro de um período de tempo, o endereço IP é impedido de acessar todos os recursos no serviço por um período de tempo predefinido.

Além disso, o gateway do Banco de Dados SQL do Azure executa:

- Negociações de capacidade de canal seguro para implementar conexões criptografadas validadas pelo TDS FIPS 140-2 quando ele se conecta aos servidores de banco de dados.
- Inspeção de pacote TDS com estado enquanto aceita conexões de clientes. O gateway valida as informações de conexão e passa os pacotes TDS para o servidor físico apropriado com base no nome do banco de dados especificado na cadeia de conexão.

O principal princípio para a segurança de rede da oferta do Banco de Dados SQL do Azure é permitir apenas a conexão e a comunicação necessárias para permitir que o serviço funcione. Todas as outras portas, protocolos e conexões são bloqueados por padrão. Redes locais virtuais (VLANs) e ACLs são usadas para restringir as comunicações de rede por redes de origem e destino, protocolos e números de porta.

Mecanismos aprovados para implementar ACLs baseadas em rede incluem ACLs em roteadores e balanceadores de carga. Esses mecanismos são gerenciados pelo sistema de rede do Azure, pelo firewall de convidado da VM e pelas regras de firewall do gateway do Banco de Dados SQL do Azure, que são configuradas pelo cliente.

## <a name="data-segregation-and-customer-isolation"></a>Segregação de dados e isolamento do cliente
A rede de produção do Azure é estruturada de forma que os componentes do sistema acessíveis publicamente sejam segregados dos recursos internos. Existem limites físicos e lógicos entre os servidores da Web que fornecem acesso ao portal do Azure voltado ao público e à infraestrutura virtual subjacente do Azure, onde residem as instâncias do aplicativo do cliente e os dados do cliente.

Todas as informações acessíveis publicamente são gerenciadas na rede de produção do Azure. A rede de produção está sujeita a mecanismos de proteção de limite e autenticação de dois fatores, usa o firewall e o conjunto de recursos de segurança descritos na seção anterior e usa funções de isolamento de dados, conforme observado nas próximas seções.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Sistemas não autorizados e isolamento do FC
Como o controlador de malha (FC) é o orquestrador central da malha do Azure, controles significativos estão em vigor para mitigar as ameaças, especialmente de FAs potencialmente comprometidos em aplicativos de clientes. O FC não reconhece nenhum hardware cujas informações do dispositivo (por exemplo, endereço MAC) não sejam pré-carregadas no FC. Os servidores DHCP no FC configuraram as listas de endereços MAC de nós que estão devem ser reinicializados. Mesmo se sistemas não autorizados estiverem conectados, eles não são incorporados ao inventário de malha e, portanto, não estão conectados ou autorizados a se comunicar com qualquer sistema dentro do inventário de malha. Isso reduz o risco de comunicação não autorizada de sistemas com o FC e acesso à VLAN e ao Azure.

### <a name="vlan-isolation"></a>Isolamento de VLAN
A rede de produção do Azure é separada logicamente em três VLANs primárias:

- A principal VLAN: Interconecta nós de clientes não confiáveis.
- O FC VLAN: Contém FCs confiáveis e sistemas de suporte.
- A VLAN do dispositivo: contém rede confiável e outros dispositivos de infraestrutura.

### <a name="packet-filtering"></a>Filtragem de pacotes
O IPFilter e os firewalls de software que são implementados no sistema operacional raiz e sistema operacional convidado de nós impõem restrições de conectividade e impedir que o tráfego não autorizado entre as VMs.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hiper visor, sistema operacional raiz e VMs convidadas
O isolamento da raiz do sistema operacional de VMs convidadas e as VMs convidadas umas das outras é gerenciado pelo hiper visor e o sistema operacional raiz.

### <a name="types-of-rules-on-firewalls"></a>Tipos de regras de firewalls
Uma regra é definida como:

{Src IP, porta src, IP de destino, porta de destino, protocolo de destino, entrada/saída, com estado/sem estado, tempo limite de fluxo com estado}.

Os pacotes de caracteres ociosos síncronos (SYN) só são permitidos dentro ou fora se qualquer uma das regras permitir. Para TCP, o Azure usa regras sem estado em que o princípio é que ele permite que apenas todos os pacotes não-SYN entrem ou saiam da VM. A premissa de segurança é que qualquer pilha de host seja resiliente de ignorar um não SYN se ele não tiver sido visto como um pacote SYN anteriormente. O protocolo TCP em si é stateful e, em combinação com a regra baseada em SYN sem estado, atinge um comportamento geral de uma implementação com estado.

Para User Datagram Protocol (UDP), o Azure usa uma regra stateful. Sempre que um pacote UDP corresponde a uma regra, um fluxo inverso é criado na outra direção. Esse fluxo tem um tempo limite interno.

Os clientes são responsáveis por configurar seus próprios firewalls além do que o Azure fornece. Aqui, os clientes são capazes de definir as regras de tráfego de entrada e saída.

### <a name="production-configuration-management"></a>Gerenciamento de configuração de produção
As configurações seguras padrão são mantidas pelas equipes de operações respectivas no Banco de Dados SQL do Azure e do Azure. Todas as alterações de configuração em sistemas de produção são documentadas e controladas por meio de um sistema de controle central. Alterações de software e hardware são controladas por meio do sistema central de rastreamento. As alterações de rede relacionadas à ACL são rastreadas usando um serviço de gerenciamento de ACL.

Todas as alterações de configuração no Azure são desenvolvidas e testadas no ambiente de preparação e, em seguida, são implantadas no ambiente de produção. Builds de software são examinados como parte dos testes. Verificações de segurança e privacidade são analisadas como parte dos critérios de lista de verificação de entrada. As alterações são implantadas em intervalos programados pela respectiva equipe de implantação. Versões são analisadas e aprovados pela respectiva equipe de implantação antes de serem implantadas em produção.

As alterações são monitoradas para o sucesso. Em um cenário de falha, a alteração é revertida para seu estado anterior ou um hotfix é implantado para resolver a falha com a aprovação do pessoal designado. O Source Depot, o Git, o TFS, o MDS (Master Data Services), os corredores, o monitoramento de segurança do Azure, o FC e a plataforma WinFabric são usados para gerenciar, aplicar e verificar as configurações no ambiente virtual do Azure.

Da mesma forma, as alterações de hardware e de rede têm estabelecido etapas de validação para avaliar sua aderência aos requisitos de build. As liberações são revisadas e autorizadas por meio de um painel consultivo de mudança coordenada (CAB) dos respectivos grupos em toda a pilha.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](physical-security.md)
- [Disponibilidade da infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Gerenciamento e operações de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](protection-customer-data.md)
