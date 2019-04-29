---
title: Rede de produção do Azure
description: Este artigo fornece uma descrição geral da rede de produção do Microsoft Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: afae7cc6390ea4cd8c18c687e9d99400c8da9da4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611354"
---
# <a name="the-azure-production-network"></a>Rede de produção do Microsoft Azure
Os usuários da rede de produção do Microsoft Azure incluem ambos os clientes externos que acessam seus próprios aplicativos do Microsoft Azure e o pessoal de suporte do Microsoft Azure interno que gerenciam a rede de produção. Este artigo discute a segurança e os mecanismos de proteção para estabelecer conexões com a rede de produção do Microsofot Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Roteamento de internet e tolerância a falhas
Uma infraestrutura do Serviço de Nomes de Domínio (DNS) do Microsoft Azure interna e externa redundante, combinada com vários cluster de servidor DNS primários e secundários, fornece tolerância a falhas. Ao mesmo tempo, os controles adicionais de segurança de rede do Microsoft Azure, como NetScaler, são usados para impedir são usados para evitar ataque de negação de serviço distribuído (DDoS) e proteger a integridade dos serviços de DNS do Azure.                                                  

Os servidores DNS do Azure estão localizados em várias instalações de datacenter. A implementação do DNS do Azure incorpora uma hierarquia de servidores DNS secundários e primários para resolver publicamente os nomes de domínio do cliente do Microsoft Azure. Os nomes de domínio geralmente são resolvidos para um endereço CloudApp.net, que envolve o endereço IP virtual (VIP) para o serviço do cliente. Exclusivo para o Microsoft Azure, o VIP correspondente ao endereço IP Dedicado interno (DIP) da tradução do locatário é feito pelos balanceadores de carga da Microsoft responsáveis por esse VIP.

O Microsoft Azure é hospedado em datacenters do Azure distribuídos geograficamente nos EUA e é construído em plataformas de roteamento de última geração que implementam padrões arquitetônicos robustos e escalonáveis. Entre alguns dos recursos notáveis:

- Comutação de rótulo multiprotocolo (MPLS) com base em engenharia de tráfego, que fornece a utilização eficiente de link e redução gradual do serviço, se houver uma interrupção.
- As redes são implementadas com "necessidade de mais de um" (N + 1) as arquiteturas de redundância ou melhor.
- Externamente, os datacenters são servidos por circuitos de rede de alta largura de banda dedicados que conectam de forma redundante propriedades com mais de 1.200 provedores de serviços de Internet globalmente em múltiplos pontos de emparelhamento. Essa conexão fornece mais de 2.000 gigabytes por segundo (GBps) de capacidade de borda.

Como a Microsoft possui seus próprios circuitos de rede entre datacenters, esses atributos ajudam a oferta do Azure a atingir 99,9+ de disponibilidade de rede sem a necessidade de provedores tradicionais de serviços de Internet de terceiros.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Conexão à rede de produção e Firewalls associados
A política de fluxo de tráfego de Internet da rede do Microsoft Azure direciona o tráfego para a rede de produção do Azure localizado no datacenter regional mais próximo dentro dos Estados Unidos. Como os data centers de produção do Microsoft Azure mantêm arquitetura e hardware de rede consistentes, a descrição do fluxo de tráfego abaixo se aplica de maneira consistente a todos os datacenters.

Depois que o tráfego de Internet para o Azure é roteado para o datacenter mais próximo, uma conexão é estabelecida para os roteadores de acesso. Esses roteadores de acesso servem para isolar o tráfego entre nós do Azure e VMs de uma instância do cliente. Dispositivos de infraestrutura de rede nos locais de acesso e de borda são os pontos de limite em que os filtros de entrada e saída são aplicados. Esses roteadores são configurados por meio de uma lista de controle de acesso (ACL) em camadas para filtrar o tráfego de rede indesejado e aplicar limites de taxa de tráfego, se necessário. O tráfego é permitido por ACL é roteado para os balanceadores de carga. Os roteadores de distribuição são projetados para permitir somente endereços IP aprovados pela Microsoft, fornecer conexões falsas antifalsificação e estabelecidas que usam ACLs.

Os dispositivos de Balanceamento de Carga Externo estão localizados atrás dos roteadores de acesso para executar a Conversão de Endereço de Rede (Network Address Translation - NAT) de IPs roteáveis pela Internet para IPs internos do Microsoft Azure. Os dispositivos também encaminham pacotes para IPs e portas internas de produção válidos e atuam como um mecanismo de proteção para limitar a exposição do espaço de endereço interno da rede de produção.

Por padrão, a Microsoft aplica HTTPS (Hypertext Transfer Protocol Secure) a todo o tráfego transmitido aos navegadores da Web do cliente, incluindo o login e todo o tráfego posterior. O uso de TLS v1.2 habilita um túnel seguro para o qual o tráfego flua. As ACLs em roteadores de acesso e core Certifique-se que a origem do tráfego é consistente com o que é esperado.

Uma distinção importante nessa arquitetura, em comparação com a arquitetura de segurança tradicional, é que não há firewalls de hardware dedicados, dispositivos especializados de detecção/prevenção de invasão ou outros dispositivos de segurança normalmente esperados antes que as conexões sejam feitas no ambiente de produção do Microsoft Azure. Os clientes esperam normalmente esses dispositivos de firewall de hardware na rede do Microsoft Azure. No entanto, não há nenhum empregado dentro do Microsoft Azure. Quase exclusivamente, esses recursos de segurança são incorporados ao software que executa o ambiente do Azure para fornecer mecanismos robustos de segurança em várias camadas, incluindo recursos de firewall. Além disso, o escopo do limite e a expansão associada de dispositivos de segurança críticos é mais fácil de gerenciar e inventariar, conforme mostrado na ilustração anterior, porque é gerenciado pelo software que executa o Azure.

## <a name="core-security-and-firewall-features"></a>Principais recursos de segurança e firewall
O Azure implementa recursos robustos de segurança e firewall de software em vários níveis para reforçar os recursos de segurança normalmente esperados em um ambiente tradicional para proteger o limite principal de Autorização de Segurança.

### <a name="azure-security-features"></a>Recursos de segurança do Azure
O Azure implementa firewalls de software baseado em host dentro da rede de produção. Várias seguranças principais e recursos de firewall residem dentro do núcleo de ambiente do Azure. Esses recursos de segurança refletem uma estratégia de defesa em profundidade no ambiente do Azure. Dados do cliente no Microsoft Azure são protegidos pelos firewalls a seguir:

**Firewall de hipervisor (filtro de pacote)**: este firewall é implementado no hipervisor e configurado por um agente controlador de malha (FC). Esse firewall protege o locatário em execução dentro da VM contra acesso não autorizado. Por padrão, quando uma VM é criada, todo o tráfego é bloqueado e o agente FC adicionar exceções no filtro a fim de permitir o tráfego autorizado.

Há duas categorias de regras que são programadas aqui:

- **Regras de configuração do computador ou de infraestrutura**: por padrão, toda a comunicação é bloqueada. Há exceções para permitir que uma VM envie e receba comunicações do Protocolo de Configuração Dinâmica de Hosts (DHCP), informações de DNS, envie tráfego para a Internet “pública”, de saída para outras VMs no cluster FC e no servidor de Ativação do SO. Como a lista permitida de destinos de saída das VMs não inclui sub-redes de roteadores do Microsoft Azure e outras propriedades da Microsoft, as regras agem como uma camada de defesa para elas.
- **Regras do arquivo de configuração de função**: define as ACLs de entrada com base no modelo de serviço dos locatários. Por exemplo, se um locatário tiver um front-end da web na porta 80 em uma determinada VM, em seguida, a porta 80 é aberta para todos os endereços IP. Se a VM tiver uma função ou de trabalho em execução, a função de trabalho somente abre para a VM que esteja no mesmo locatário.

**Firewall host nativo**: a malha e o Armazenamento do Azure são executados em um sistema operacional nativo sem hipervisores e, portanto, o firewall do Windows está configurado com os dois conjuntos de regras anteriores.

**Firewall do host**: o firewall do host existe para proteger a partição do host que executa o hipervisor. As regras são programadas para permitir que as jump boxes conversem com a partição do host em uma porta específica. As outras exceções existem para permitir a resposta DHCP e Respostas DNS. O Microsoft Azure usa um arquivo de configuração de computador que contém o modelo de regras de firewall para a partição do host. Há também uma exceção de firewall de host que permite que as VMs se comuniquem com os componentes do host, o servidor de rede e o servidor de metadados, por meio de protocolos/portas específicos.

**Firewall do convidado**: a parte do Firewall do Windows do SO convidado, que pode ser configurado pelo cliente em VMs de cliente e armazenamento.

Recursos de segurança adicionais que são integrados aos recursos do Microsoft Azure incluem:

- Os componentes de infraestrutura que são atribuídos endereços IP que são de DIPs. Um invasor na Internet não pode resolver o tráfego para esses endereços porque eles não seriam chegam à Microsoft. Roteadores de Gateway de Internet filtram pacotes endereçados exclusivamente para endereços internos, portanto, não realizaria a rede de produção. Os únicos componentes que aceitam o tráfego direcionado para VIPs são balanceadores de carga.
- Os firewalls implementados em todos os nós internos têm três considerações de arquitetura de segurança principal para qualquer cenário dado:

   - Os firewalls são colocados atrás de balanceador de carga (LB) e aceitam pacotes em qualquer lugar. Esses pacotes se destinam a ser exposto externamente e correspondem para abrir as portas em um firewall de perímetro tradicional.
   - Os firewalls aceitam somente pacotes de um conjunto limitado de endereços. Essa consideração é parte da estratégia detalhada defensiva contra ataques de DDoS. Essas conexões são autenticadas criptograficamente.
   - Os Firewalls podem ser acessados somente nós internos selecionados. Eles aceitam pacotes apenas de uma lista enumerada de endereços IP de origem, sendo todos DIPs na rede do Azure. Por exemplo, um ataque à rede corporativa poderia direcionar solicitações para esses endereços, mas eles seriam bloqueados, a menos que o endereço de origem do pacote fosse um na lista enumerada na rede do Microsoft Azure.
     - O roteador de acesso no perímetro bloqueia pacotes de saída endereçados a um endereço que está dentro da rede do Azure devido a suas rotas estáticas configuradas.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)
