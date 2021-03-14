---
title: Hospedagem de mainframe em máquinas virtuais do Azure
description: Rehospede suas cargas de trabalho de mainframe como sistemas IBM Z usando máquinas virtuais (VMs) em Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7ae20b484b4c22c19b856c237d85efe31dc01407
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553537"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Hospedagem de mainframe em máquinas virtuais do Azure

A migração de cargas de trabalho de ambientes de mainframe para a nuvem permite modernizar sua infraestrutura e, muitas vezes, economizar em custos. Muitas cargas de trabalho podem ser transferidas para o Azure com apenas pequenas alterações de código, como atualizar os nomes dos bancos de dados.

Em geral, o termo *mainframe* significa um sistema de computador grande. Especificamente, a grande maioria atualmente em uso são os servidores IBM System Z ou OS sistemas compatíveis com IBM plug-and-compatible que executam MVS, DOS, VSE, OS/390 ou Z/OS.

Uma VM (máquina virtual) do Azure é usada para isolar e gerenciar os recursos de um aplicativo específico em uma única instância do. Mainframes como IBM z/OS usam partições lógicas (LPARs) para essa finalidade. Um mainframe pode usar um LPAR para uma região CICS com programas COBOL associados e um LPAR separado para o banco de dados IBM DB2. Um [aplicativo típico de n camadas no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implanta VMs do Azure em uma rede virtual que pode ser segmentada em sub-redes para cada camada.

As VMs do Azure podem executar ambientes de emulação de mainframe e compiladores que dão suporte a cenários de comparação de precisão e deslocamento. O desenvolvimento e o teste geralmente estão entre as primeiras cargas de trabalho para migrar de um mainframe para um ambiente de desenvolvimento/teste do Azure. Os componentes comuns do servidor que você pode emular incluem OLTP (processo de transação online), lote e sistemas de ingestão de dados como mostra a figura a seguir.

![Os ambientes de emulação no Azure permitem que você execute sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho de mainframe podem ser migradas para o Azure com uma facilidade relativa, enquanto outras podem ser rehospedadas no Azure usando uma solução de parceiro. Para obter diretrizes detalhadas sobre como escolher uma solução de parceiro, o [centro de migração de mainframe do Azure](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="mainframe-migration"></a>Migração de mainframe

Rehospedar, recompilar, substituir ou desativar? IaaS ou PaaS? Para determinar a estratégia de migração certa para seu aplicativo de mainframe, consulte o guia de [migração do mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  na centro de arquitetura do Azure.

## <a name="micro-focus-rehosting-platform"></a>Plataforma de rehospedagem micro Focus

O micro Focus Enterprise Server é uma das maiores plataformas de Hospedagem de mainframes disponíveis. Você pode usá-lo para executar suas cargas de trabalho do z/OS em uma plataforma x86 menos dispendiosa no Azure.

Introdução:

- [Instalar o Enterprise Server e o Enterprise Developer no Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configurar o CICS BankDemo for Enterprise Developer no Azure](./microfocus/demo.md)
- [Executar o servidor corporativo em um contêiner do Docker no Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame no Azure

TmaxSoft OpenFrame é uma popular solução de rehospedagem de mainframe usada em cenários de comparação de precisão e deslocamento. Um ambiente OpenFrame no Azure é adequado para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção.

Introdução:

- [Introdução ao TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Baixe o ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12,0

O IBM Z Development and Test Environment (IBM zD&T) configura um ambiente de não produção no Azure que você pode usar para desenvolvimento, teste e demonstrações de aplicativos baseados em Z/OS.

O ambiente de emulação no Azure pode hospedar diferentes tipos de instâncias Z por meio de ADCDs (distribuições controladas por desenvolvedores de aplicativos). Você pode executar o zD&T Personal Edition, o zD&T Parallel Sysplex e o zD&T Enterprise Edition no Azure e Azure Stack.

Introdução:

- [Configurar o IBM zD&T 12,0 no Azure](./ibm/install-ibm-z-environment.md)
- [Configurar ADCD em zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale no Azure

O ambiente IBM DB2 pureScale fornece um cluster de banco de dados para o Azure. Ele não é idêntico ao ambiente original, mas oferece disponibilidade e escala semelhantes à medida que o IBM DB2 para z/OS está sendo executado em uma configuração de sysplex paralelo.

Para começar, consulte [IBM DB2 pureScale no Azure](.//ibm/ibm-db2-purescale-azure.md).

## <a name="considerations"></a>Considerações

Ao migrar cargas de trabalho de mainframe para o IaaS (infraestrutura como serviço) do Azure, você pode escolher entre vários tipos de recursos de computação escalonáveis e sob demanda, incluindo VMs do Azure. O Azure oferece uma variedade de VMs [Linux](../../linux/overview.md) e [Windows](../../windows/overview.md) .

### <a name="compute"></a>Computação

A potência de computação do Azure se compara favorável à capacidade de um mainframe. Se você estiver pensando em mover uma carga de trabalho de mainframe para o Azure, compare a métrica de mainframe de 1 milhão instruções por segundo (MIPS) para CPUs virtuais. 

Saiba como [mover a computação do mainframe para o Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Failover e alta disponibilidade

O Azure oferece SLAs (contratos de nível de serviço) baseados em compromisso. A disponibilidade de vários noves é o padrão, e os SLAs podem ser otimizados com a replicação local ou baseada em geografia dos serviços. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Com o IaaS do Azure, como uma VM, as funções específicas do sistema fornecem suporte a failover — por exemplo, instâncias de clustering de failover e conjuntos de disponibilidade. Quando você usa recursos de PaaS (plataforma como serviço) do Azure, a plataforma manipula o failover automaticamente. Os exemplos incluem [banco de dados SQL do Azure](../../../azure-sql/database/sql-database-paas-overview.md) e [Azure Cosmos DB](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Escalabilidade

Os mainframes normalmente aumentam, enquanto os ambientes de nuvem são expandidos. O Azure oferece uma variedade de tamanhos do [Linux](../../sizes.md) e do [Windows](../../sizes.md) para atender às suas necessidades. A nuvem também é dimensionada para cima ou para baixo para corresponder às especificações exatas do usuário. A capacidade de computação, o armazenamento e os serviços são [dimensionados](/azure/architecture/best-practices/auto-scaling) sob demanda em um modelo de cobrança baseado em uso.

### <a name="storage"></a>Armazenamento

Na nuvem, você tem uma variedade de opções de armazenamento flexíveis e escalonáveis e paga apenas pelo que precisa. O [Armazenamento do Azure](../../../storage/common/storage-introduction.md) oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, armazenamento de mensagens confiáveis e armazenamento de NoSQL. Para VMs, discos gerenciados e discos não gerenciados fornecem armazenamento em disco persistente e seguro.

Saiba como [mover o armazenamento de mainframe para o Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup e descoberta

Manter seu próprio site de recuperação de desastre pode ser uma proposta cara. O Azure tem opções fáceis de implementar e econômicas para [backup](../../../backup/backup-overview.md), [recuperação](../../../site-recovery/site-recovery-overview.md)e [redundância](../../../storage/common/storage-redundancy.md) em níveis locais ou regionais, ou por meio de redundância geográfica.

## <a name="azure-government-for-mainframe-migrations"></a>Azure governamental para migrações de mainframe

Muitas entidades do setor público adorariam mover seus aplicativos de mainframe para uma plataforma mais moderna e flexível. Microsoft Azure Governamental é uma instância fisicamente separada da plataforma global Microsoft Azure, empacotada para sistemas governamentais federais, estaduais e locais. Ele fornece segurança de nível mundial, proteção e serviços de conformidade especificamente para Estados Unidos agências governamentais e seus parceiros.

O Azure governamental ganhou uma autoridade provisória para operar (P-ATO) para um alto impacto FedRAMP para sistemas que precisam desse tipo de ambiente.

Para começar, baixe [Microsoft Azure governamental Cloud para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Próximas etapas

Peça aos nossos [parceiros](partner-workloads.md) para ajudá-lo a migrar ou rehospedar seus aplicativos de mainframe. 

Consulte também:

- [White papers sobre tópicos de mainframe](mainframe-white-papers.md)
- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Solução de problemas](../../troubleshooting/index.yml)
- [Desmistificando a migração do mainframe para o Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md