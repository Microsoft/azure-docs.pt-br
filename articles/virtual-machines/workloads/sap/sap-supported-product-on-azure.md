---
title: 'SAP no Azure: Qual software SAP tem suporte no Azure'
description: Explica o que o software SAP tem suporte para ser implantado no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673531"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Quais programas de software SAP são compatíveis com as implantações do Azure
Este artigo descreve como você pode descobrir qual software SAP tem suporte para implantações do Azure e quais são as versões do sistema operacional ou as versões do DBMS necessárias.

Avaliando, se o seu software SAP atual tem suporte e quais versões do so e do DBMS têm suporte com seu software SAP no Azure, você precisará de acesso a:

- Notas de suporte do SAP
- Matriz de disponibilidade de produto SAP



## <a name="general-restrictions-for-sap-workload"></a>Restrições gerais para carga de trabalho do SAP
Os serviços IaaS do Azure que podem ser usados para a carga de trabalho do SAP são limitados ao hardware x86-64 ou x64. Não há ofertas de CPU ou de energia baseadas em SPARC que se aplicam à carga de trabalho do SAP. Clientes que executam seus aplicativos em sistemas operacionais de propriedade de arquiteturas de hardware como IBM Mainframe ou AS400, ou onde os sistemas operacionais HP-UX, Solaris ou AIX estão em uso, precisam alterar seus aplicativos SAP, incluindo DBMS para um dos seguintes sistemas operacionais:

- Windows Server 64 bits para a plataforma x86-64
- SUSE Linux de 64 bits para a plataforma x86-64
- Red Hat Linux de 64 bits para a plataforma x86-64
- Oracle Linux de 64 bits para a plataforma x86-64

Em combinação com o software SAP, não há suporte para versões do so ou distribuições do Linux. Os detalhes exatos sobre versões e casos específicos são documentados posteriormente no documento.


## <a name="you-start-here"></a>Comece aqui
O ponto de partida para você é o [#1928533 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1928533). Ao percorrer essa anotação do SAP de cima para baixo, várias áreas de software e VMs com suporte são mostradas

A primeira seção lista os requisitos mínimos para as versões operacionais com suporte no software SAP em VMs do Azure em geral. Se você não estiver atingindo esses requisitos mínimos e executar versões mais antigas desses sistemas operacionais, precisará atualizar sua versão do sistema operacional para uma versão mínima ou até mesmo versões mais recentes. Está correto que o Azure em geral ofereceria suporte a versões mais antigas de alguns desses sistemas operacionais. Mas as restrições ou as versões mínimas, conforme listadas, são baseadas em testes e qualificações executadas e não serão estendidas mais de volta. 


> [!NOTE]
>Há alguns tipos específicos de VM, as grandes instâncias do HANA ou cargas de trabalho do SAP que irão exigir versões mais recentes do sistema operacional. Casos como esse serão mencionados em todo o documento. Casos como esses são claramente documentados no SAP Notes ou em outras publicações do SAP.

A seção a seguir lista as plataformas gerais do SAP que têm suporte com as versões com suporte e mais importantes para os kernels do SAP com suporte. Ele lista as pilhas do NetWeaver/ABAP ou do Java com suporte e, que precisam de versões mínimas do kernel. As pilhas ABAP mais recentes têm suporte no Azure, mas não precisam de versões mínimas do kernel, pois as alterações do Azure foram implementadas desde o início do desenvolvimento das pilhas mais recentes

Você precisa verificar:

- Se os aplicativos SAP que você está executando, são cobertos pelas versões mínimas declaradas. Caso contrário, você precisa definir uma nova versão de destino, fazer check-in da matriz de disponibilidade de produto SAP, quais sistemas operacionais e combinações de DBMS têm suporte com a nova versão de destino. Portanto, você pode escolher a versão correta do sistema operacional e a versão do DBMS
- Se você precisa atualizar seus kernels do SAP em uma mudança para o Azure
- Se você precisa atualizar os pacotes de suporte do SAP. Pacotes de suporte especialmente base que podem ser necessários para casos em que você precisa mudar para uma versão mais recente do DBMS


A próxima seção apresenta mais detalhes sobre outros produtos SAP e versões de DBMS com suporte do SAP no Azure para Windows e Linux. 

> [!NOTE]
> As versões mínimas do DBMS diferente são cuidadosamente escolhidas e nem sempre podem refletir todo o espectro do DBMS, os diferentes fornecedores de DBMS dão suporte ao Azure em geral. Muitas considerações relacionadas à carga de trabalho do SAP foram levadas em conta para definir essas versões mínimas. Não há nenhum esforço para testar e qualificar versões mais antigas do DBMS. 

> [!NOTE]
> As versões mínimas listadas representam a versão mais antiga dos sistemas operacionais e das versões do banco de dados. É altamente recomendável usar versões mais recentes do sistema operacional e versões do banco de dados. Em muitos casos, as versões mais recentes do sistema operacional e do banco de dados levaram o caso de uso da execução na nuvem pública em consideração e adaptamos o código para otimizar a execução na nuvem pública ou, mais especificamente, o Azure

## <a name="oracle-dbms-support"></a>Suporte a Oracle DBMS
O sistema operacional, as versões do Oracle DBMS e as funcionalidades do Oracle com suporte no Azure estão listadas especificamente na [Observação de suporte do SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). A essência dessa observação pode ser resumida como:

- A versão mínima do Oracle com suporte em VMs do Azure que são certificadas para NetWeaver é Oracle 11g versão 2 patchset 3 (11.2.0.4)
- Como sistemas operacionais convidados, somente Windows e Oracle Linux qualificados. As versões exatas do sistema operacional e as versões mínimas relacionadas do DBMS são listadas na observação
- O suporte do Oracle Linux se estende ao cliente Oracle DBMS também. Isso significa que todos os componentes SAP, como instâncias de caixa de diálogo do ABAP ou da pilha Java, precisam ser executados em Oracle Linux também. Somente os componentes SAP dentro de um sistema SAP que não se conectariam ao DBMS da Oracle teriam permissão para executar um sistema operacional Linux diferente
- Não há suporte para o Oracle RAC 
- O Oracle ASM tem suporte para alguns dos casos. Os detalhes são listados na observação
- OS sistemas SAP não Unicode só têm suporte com servidores de aplicativos em execução com o sistema operacional convidado do Windows. O sistema operacional convidado do DBMS pode ser Oracle Linux ou Windows. O motivo para essa restrição é aparente ao verificar o PAM (matriz de disponibilidade de produto) do SAP. Por Oracle Linux, a SAP nunca lançou kernels SAP não-Unicode

Sabendo as versões do DBMS com suporte com a infraestrutura de destino do Azure, você precisa verificar a matriz de disponibilidade do produto SAP se as versões do sistema operacional e o DBMS necessários tiverem suporte com suas versões de produto SAP que você pretende executar. 

## <a name="oracle-linux"></a>Oracle Linux
A maioria das perguntas importantes sobre Oracle Linux é se o SAP dá suporte ao kernel Red Hat que é parte integrante do Oracle Linux também. Para obter detalhes, leia [#1565179 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>Outro banco de dados do que SAP HANA
O suporte a bancos de dados não HANA para carga de trabalho do SAP está documentado na [Observação de suporte do sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).


## <a name="sap-hana-support"></a>Suporte a SAP HANA
No Azure, há dois serviços, que podem ser usados para executar o banco de dados HANA:

- Máquinas Virtuais do Azure
- [Instâncias grandes do HANA](./hana-overview-architecture.md)

Para a execução de SAP HANA, o SAP tem uma infra-estrutura de condições mais rígida e mais forte para atender do que para executar o NetWeaver ou outros aplicativos SAP e DBMS. Como resultado, um número menor de VMs do Azure se qualifica para executar o SAP HANA DBMS. A lista de infraestruturas do Azure com suporte para SAP HANA pode ser encontrada no, por isso chamado de [SAP Hana diretório de hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> As unidades que começam com as letras ' são as unidades de [instâncias grandes do Hana](./hana-overview-architecture.md) . 

> [!NOTE]
> A SAP não tem certificação específica dependente do SAP HANA versões principais. Ao contrário da opinião comum, do **cenário de certificação** de coluna nas [plataformas IaaS certificadas do Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), a coluna **não faz nenhuma instrução sobre a versão principal ou secundária do Hana**. Você precisa assumir que todas as unidades listadas podem ser usadas para HANA 1,0 e HANA 2,0, desde que as versões certificadas do sistema operacional para as unidades específicas também tenham suporte das versões do HANA 1,0. 

Para o uso de SAP HANA, versões de sistema operacional mínimas diferentes podem ser aplicadas em casos de NetWeaver gerais. Você precisa verificar os sistemas operacionais com suporte para cada unidade individualmente, pois eles podem variar. Você faz isso clicando em cada unidade. Mais detalhes serão exibidos. Um dos detalhes listados são os diferentes sistemas operacionais com suporte para essa unidade específica.

> [!NOTE]
> As unidades de instância grande do HANA do Azure são mais restritivas com sistemas operacionais com suporte em comparação com as VMs do Azure. Por outro lado, as VMs do Azure podem impor versões operacionais mais recentes como versões mínimas. Isso é especialmente verdadeiro para algumas das unidades de VM maiores que exigiam alterações em kernels do Linux

Sabendo o sistema operacional com suporte para a infraestrutura do Azure, você precisa verificar a [Nota de suporte SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) para obter as versões exatas de SAP Hana e os níveis de patch que têm suporte com as unidades do Azure que você está direcionando. 

> [!IMPORTANT]
> A etapa de verificar as versões exatas de SAP HANA e os níveis de patches com suporte é muito importante. Em muitos casos, o suporte de uma determinada versão do sistema operacional depende de um nível de patch específico dos executáveis SAP HANA.

Como você conhece as versões específicas do HANA, você pode executar na infraestrutura de destino do Azure, você precisa verificar a matriz de disponibilidade do produto SAP para descobrir se há restrições com as versões do produto SAP que dão suporte às versões HANA que você filtrou


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>VMs do Azure certificadas e unidades de instância grande do HANA e taxa de transferência de transação de negócios
Além de avaliar as versões do sistema operacional com suporte, as versões do DBMS e o suporte dependente de versões de software SAP para unidades de infraestrutura do Azure, você tem a necessidade de qualificar essas unidades por taxa de transferência de transação de negócios, que é expressa na unidade ' SAP ' pela SAP. Todo o dimensionamento SAP depende dos cálculos de SAPS. Avaliando os sistemas SAP existentes, você geralmente pode, com a ajuda do seu provedor de infraestrutura, calcular os SAPS das unidades. Para a camada do DBMS, bem como para a camada de aplicativo. Em outros casos em que a nova funcionalidade é criada, um exercício de dimensionamento com o SAP pode revelar os números de SAPS necessários para a camada de aplicativo e a camada de DBMS. Como provedor de infraestrutura, a Microsoft é obrigado para fornecer a caracterização de taxa de transferência do SAP das diferentes unidades que são a NetWeaver e/ou o HANA certificado.

Para VMs do Azure, esses números de produtividade de SAPS são documentados no [#1928533 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1928533). Para as unidades do Azure HANA em instâncias grandes, os números de produtividade de SAPS estão documentados na [Nota de suporte SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Olhando para a [Nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533), os seguintes comentários se aplicam:

- **Para VMs do Azure da série M e Mv2-Series VMs do Azure, versões mínimas do sistema operacional diferentes se aplicam em outros tipos de VM do Azure**. O requisito para versões mais recentes do sistema operacional é baseado em alterações que os diferentes fornecedores de sistemas operacionais tinham que fornecer em suas versões de sistema operacional para habilitar seus sistemas operacionais em execução nos tipos específicos de VM do Azure ou otimizar o desempenho e a taxa de transferência da carga de trabalho do SAP nesses tipos de VM
- Há duas tabelas que especificam diferentes tipos de VM. A segunda tabela especifica a taxa de transferência de SAPS para tipos de VM do Azure que dão suporte apenas ao armazenamento standard do Azure. Não há suporte para a implantação de DBMS nas unidades especificadas na segunda tabela da observação


## <a name="other-sap-products-supported-on-azure"></a>Outros produtos SAP com suporte no Azure
Em geral, a suposição é que, com o estado de nuvens de hiperescala como o Azure, a maior parte do software SAP deve ser executada sem problemas funcionais no Azure. No entanto, e oposto à visualização de nuvem privada, o SAP ainda expressa o suporte para os diferentes produtos SAP explicitamente para os diferentes provedores de nuvem hyerpscale. Como resultado, há diferentes notas de suporte SAP indicando suporte para o Azure para diferentes produtos SAP. 

Para a plataforma de BI de objetos comerciais, a [Observação de suporte SAP #2145537](https://launchpad.support.sap.com/#/notes/2145537) fornece uma lista de produtos SAP Business Objects com suporte no Azure. Se houver perguntas sobre componentes ou combinações de versões de software e versões do sistema operacional que parecem não estar listadas ou suportadas e que são mais recentes do que as versões mínimas listadas, você precisará abrir uma solicitação de suporte do SAP em relação ao componente para o qual você consulta o suporte.

Para os serviços de dados de objetos de negócios, a [Observação de suporte sap #22288344](https://launchpad.support.sap.com/#/notes/2288344) explica o suporte mínimo do SAP Data Services em execução no Azure. 

> [!NOTE]
> Conforme indicado na nota de suporte do SAP, você precisa fazer check-in no PAM do SAP para identificar o nível de pacote de suporte correto para ter suporte no Azure

O suporte do SAP Datahub/Vora no AKS (serviços kubernetes do Azure) é detalhado na [Observação de suporte do sap #2464722](https://launchpad.support.sap.com/#/notes/2464722)

O suporte para SAP BPC 10,1 SP08 está descrito na [Observação de suporte do sap #2451795](https://launchpad.support.sap.com/#/notes/2451795)

O suporte para a plataforma SAP Hybris commerce no Azure é detalhado na [documentação do Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). A partir do DBMS com suporte para a plataforma SAP Hybris Commerce, ela lista como:

- SQL Server e Oracle na plataforma do sistema operacional Windows. As mesmas versões mínimas se aplicam como para SAP NetWeaver. Consulte [#1928533 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1928533) para obter detalhes
- SAP HANA no Red Hat e no SUSE Linux. SAP HANA tipos de VM certificados são necessários, conforme documentado anteriormente neste [documento](#sap-hana-support). A plataforma de comércio SAP (Hybris) é considerada carga de trabalho OLTP
- SQL Azure DB a partir da plataforma do SAP (Hybris) Commerce versão 1811




## <a name="next-steps"></a>Próximas etapas
Leia as próximas etapas no [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](./planning-guide.md)
