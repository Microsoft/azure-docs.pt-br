---
title: Alta disponibilidade e recuperação de desastre do SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Estabeleça a alta disponibilidade e planeje a recuperação de desastre do SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 078ce7e2acd93ecab6b37407f460672d4acb1853
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707335"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Alta disponibilidade e recuperação de desastre de instâncias grandes do SAP HANA no Azure 

>[!IMPORTANT]
>Esta documentação não substitui a documentação de administração do SAP HANA ou as Notas do SAP. Espera-se que o leitor tenha um sólido reconhecimento e experiência em administração e operações do SAP HANA, especialmente com os tópicos de backup, restauração, alta disponibilidade e recuperação de desastre.

É importante que você execute as etapas e os processos realizados em seu ambiente e com as liberações e versões do HANA. Alguns processos descritos nesta documentação foram simplificados para um melhor entendimento geral e não devem ser usados como etapas detalhadas para eventuais manuais de operação. Se quiser criar manuais de operação para suas configurações, será necessário testar e executar os processos e documentar os processos relacionados às suas configurações específicas. 


Alta disponibilidade e recuperação de desastre (DR) são aspectos críticos da execução do SAP HANA no servidor do Azure (Instâncias Grandes). É importante trabalhar com o SAP, seu integrador de sistemas ou com a Microsoft para arquitetar e implementar adequadamente as estratégias corretas de alta disponibilidade e recuperação de desastre. Também é importante considerar o RPO (objetivo do ponto de recuperação) e o objetivo do tempo de recuperação, específicos para o ambiente.

A Microsoft dá suporte a alguns recursos de alta disponibilidade do SAP HANA com instâncias grandes do HANA. Esses recursos incluem:

- **Replicação de armazenamento**: Capacidade do sistema de armazenamento replicar todos os dados para outro selo de instância grande do HANA em outra região do Azure. O SAP HANA funciona independentemente desse método. Essa funcionalidade é o mecanismo de recuperação de desastre padrão oferecido para o SAP HANA em Instâncias Grandes.
- **Replicação do sistema HANA**: O [replicação de todos os dados no SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) para um sistema SAP HANA separado. O objetivo de tempo de recuperação é minimizado por meio da replicação de dados em intervalos regulares. O SAP HANA dá suporte a modos síncronos, síncronos na memória e assíncronos. O modo síncrono é utilizado apenas para sistemas SAP HANA que estão dentro do mesmo datacenter ou a menos de 100 km de distância. Com o design atual dos carimbos do SAP HANA em Instâncias Grandes, a replicação do sistema HANA pode ser utilizada para alta disponibilidade apenas dentro de uma região. A replicação do sistema HANA requer um componente roteamento ou de proxy reverso de terceiros para usar as configurações de recuperação de desastre em outra região do Azure. 
- **Failover automático de host**: Uma solução de recuperação de falhas local para o SAP HANA que é uma alternativa à replicação de sistema do HANA. Se o nó mestre ficar não disponível, configure um ou mais nós SAP HANA em espera no modo de escala horizontal e o SAP HANA fará automaticamente o failover para um nó em espera.

O SAP HANA (Instâncias Grandes) do Azure é oferecido em duas regiões do Azure, em quatro áreas geopolíticas (EUA, Austrália, Europa e Japão). Duas regiões dentro de uma área geopolítica que hospedam os carimbos do SAP HANA em Instâncias Grandes são conectadas a circuitos de rede dedicados separados. Eles são utilizados para replicar instantâneos de armazenamento para fornecer métodos de recuperação de desastre. A replicação não é estabelecida por padrão, mas é configurada para clientes que solicitam a funcionalidade de recuperação de desastre. A replicação de armazenamento depende do uso de instantâneos de armazenamento para instâncias grandes HANA. Não será possível escolher uma região do Azure como uma região de DR se estiver em uma área geopolítica diferente. 

A tabela a seguir mostra os métodos e combinações de alta disponibilidade e recuperação de desastre atualmente com suporte:

| Cenário com suporte em Instâncias Grandes HANA | Opção de alta disponibilidade | Opção de recuperação de desastre | Comentários |
| --- | --- | --- | --- |
| Nó único | Não disponível. | Instalação da recuperação de desastre dedicada.<br /> Instalação da recuperação de desastre multipropósito. | |
| Failover automático do host: Expansão (com ou sem estado de espera)<br /> incluindo 1 + 1 | Possível com em espera usando a função ativa.<br /> O HANA controla a opção de função. | Instalação da recuperação de desastre dedicada.<br /> Instalação da recuperação de desastre multipropósito.<br /> Sincronização da recuperação de desastre usando a replicação de armazenamento. | Conjuntos de volumes do HANA são conectados a todos os nós.<br /> O local de recuperação de desastre deve ter o mesmo número de nós. |
| Replicação do sistema HANA | Possível com a instalação primária ou secundária.<br /> A secundária move para a função primária em um caso de failover.<br /> Replicação do sistema HANA e controle de failover do SO. | Instalação da recuperação de desastre dedicada.<br /> Instalação da recuperação de desastre multipropósito.<br /> Sincronização da recuperação de desastre usando a replicação de armazenamento.<br /> A recuperação de desastre usando replicação do sistema HANA ainda não é possível sem componentes de terceiros. | Conjunto separado de volumes de disco são anexados a cada nó.<br /> Somente os volumes de disco de réplica secundária no local de produção são replicados para o local da recuperação de desastre.<br /> Um conjunto de volumes é necessário no local da recuperação de desastre. | 

É em uma configuração de recuperação de desastre dedicada que a unidade da Instância Grande do HANA no local da recuperação de desastre não é usada para executar nenhuma outra carga de trabalho ou sistema de não produção. A unidade é passiva e é implantada apenas se um failover de desastre é executado. No entanto, essa configuração não é uma opção preferencial para muitos clientes.

Consulte os [ cenários suportados pelo HLI ](hana-supported-scenario.md) para aprender o layout de armazenamento e os detalhes de ethernet para sua arquitetura.

> [!NOTE]
> [Implantações do SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (várias instâncias do HANA em uma unidade) como cenários de sobreposição funcionam com os métodos de alta disponibilidade e recuperação de desastres listados na tabela. Uma exceção é o uso da Replicação do Sistema do HANA com um cluster de failover automático baseado no Pacemaker. Esse caso permite somente uma instância do HANA por unidade. Para implantações de [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000), somente métodos de HA e DR não baseados em armazenamento funcionarão se mais de um locatário for implantado. Com um locatário implantado, todos os métodos listados são válidos.  

É em uma configuração de recuperação de desastre multipropósito que a unidade de Instância Grande do HANA no local da recuperação de desastre executa uma carga de trabalho de não produção. Em caso de desastre, desligue o sistema de não produção, monte os conjuntos de volume de armazenamento replicado (adicional) e, em seguida, inicie a instância do HANA de produção. A maioria dos clientes que usam a funcionalidade de recuperação de desastre do SAP HANA em Instâncias Grandes utiliza essa configuração. 


É possível encontrar mais informações sobre a alta disponibilidade do SAP HANA nos seguintes artigos do SAP: 

- [Whitepaper de Alta Disponibilidade do SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo do SAP HANA Academy sobre Replicação do Sistema SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Note de Suporte nº 1999880 – perguntas frequentes sobre replicação do sistema SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Note de Suporte SAP nº 2165547 – Fazer backup do SAP HANA e restaurá-lo no ambiente de replicação do sistema SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Note de Suporte SAP nº 1984882 – uso da replicação do sistema SAP HANA para troca de hardware com tempo de inatividade mínimo/zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para recuperação de desastre com Instâncias Grandes HANA

Para aproveitar a funcionalidade da recuperação de desastre do SAP HANA em Instâncias Grandes, é necessário começar a projetar a conectividade de rede para as duas regiões do Azure. É necessária uma conexão de circuito do Azure ExpressRoute dos locais em sua região principal do Azure e outra conexão de circuito com os locais em sua região de recuperação de desastre. Essa medida abrange uma situação na qual há um problema em uma região do Azure, incluindo um local do MSEE (Microsoft Enterprise Edge Router).

Como segunda medida, é possível conectar todas as redes virtuais do Azure que conectam-se ao SAP HANA (Instâncias Grandes) do Azure em uma região a um circuito de ExpressRoute que conecta o SAP HANA em Instâncias Grandes na outra região. Com essa *conexão cruzada* os serviços em execução em uma rede virtual do Azure na Região 1 podem conectar-se a unidades do SAP HANA em Instâncias Grandes na Região 2 e vice-versa. Essa medida resolve um caso em que apenas um dos locais do MSEE conecta-se a um ponto local com o Azure estando offline.

O gráfico a seguir ilustra uma configuração flexível para casos de recuperação de desastre:

![Configuração ideal para a recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos com replicação de armazenamento do SAP HANA em Instâncias Grandes para recuperação de desastre

Além dos requisitos anteriores para uma configuração de recuperação de desastre com o SAP HANA em Instâncias Grandes, é necessário:

- Ordenar SKUs do SAP HANA (Instâncias Grandes) do Azure do mesmo tamanho que SKUs de produção e implantá-las na região de recuperação de desastre. Nas implantações atuais do cliente, essas instâncias são usadas para executar instâncias HANA de não produção. Essas configurações são chamadas de *configurações de recuperação de desastres multipropósito*.   
- Ordenar armazenamento adicional no site de DR para cada uma das SKUs do SAP HANA (Instâncias Grandes) do Azure que você deseja recuperar no site de recuperação de desastre. Comprar armazenamento adicional permite que você aloque os volumes de armazenamento. É possível alocar os volumes que são o destino da replicação de armazenamento da sua região do Azure de produção para a região de recuperação de desastre do Azure.
- No caso de ter configurado HSR primário e configurar a replicação baseada em armazenamento para o site de DR, você deverá adquirir armazenamento adicional no site de DR para que os dados de nós primários e secundários sejam replicados para o site de DR.

  **Próximas etapas**
- Veja [Backup e restauração](hana-backup-restore.md).













