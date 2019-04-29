---
title: Mainframe rehosting em máquinas virtuais do Azure
description: Hospede novamente suas cargas de trabalho de mainframe como sistemas com base em IBM Z usando máquinas virtuais (VMs) no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487437"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting em máquinas virtuais do Azure

Migrar cargas de trabalho de mainframe ambientes na nuvem permite que você modernize sua infraestrutura e muitas vezes economizar nos custos. Muitas cargas de trabalho podem ser transferidas para o Azure com apenas pequenas alterações de código, como atualizar os nomes dos bancos de dados.

Em termos gerais, o termo *mainframe* significa que um sistema de computador grande. Especificamente, a grande maioria em uso no momento são servidores de sistema do IBM Z ou IBM systems plug-compatible que executam MVS, DOS, VSE, OS/390 ou z/OS.

Uma máquina virtual do Azure (VM) é usada para isolar e gerenciar os recursos para um aplicativo específico em uma única instância. Mainframes, como IBM z/OS usam partições lógicas (LPARS) para essa finalidade. Um mainframe poderia usar um LPAR para uma região do CICS com programas associados do COBOL e um LPAR separado para o banco de dados IBM Db2. Um típico [aplicativo de n camadas no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implanta as VMs do Azure em uma rede virtual que pode ser segmentada em sub-redes de cada camada.

As VMs do Azure podem executar ambientes de emulação de mainframe e compiladores que dão suporte a cenários de lift-and-shift. Desenvolvimento e teste estão geralmente entre as cargas de trabalho primeiro migrar de um mainframe para um ambiente de desenvolvimento/teste do Azure. Componentes de servidor comuns que você pode emular incluem o processo de transações online (OLTP), lotes e sistemas de ingestão de dados, como mostra a figura a seguir.

![Ambientes de emulação no Azure que você possa executar sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho de mainframe podem ser migradas para o Azure com relativa facilidade, enquanto outros podem ser hospedado novamente no Azure usando uma solução de parceiro. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, o [Centro de migração de Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="mainframe-migration"></a>Migração de mainframe

Novo host, recompilar, substituir ou desativar? IaaS ou PaaS? Para determinar a estratégia de migração certa para seu aplicativo de mainframe, consulte o [migração de Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) guia no Centro de arquitetura do Azure.

## <a name="micro-focus-rehosting-platform"></a>Micro plataforma rehosting de foco

Micro Focus Enterprise Server é um dos maior mainframe rehosting plataformas disponíveis. Você pode usá-lo executar suas cargas de trabalho do z/OS em um x86 mais barato plataforma no Azure.

Introdução:

- [Instalar o servidor Enterprise e Developer Enterprise no Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configurar o CICS BankDemo para desenvolvedor empresarial no Azure](./microfocus/demo.md)
- [Execute Enterprise Server em um contêiner do Docker no Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame no Azure

TmaxSoft OpenFrame é uma solução de rehosting de mainframe popular usada em cenários de lift-and-shift. Um ambiente OpenFrame no Azure é adequado para desenvolvimento, demonstrações, teste ou cargas de trabalho de produção.

Introdução:

- [Introdução ao TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Baixe o livro eletrônico](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD & T 12.0

IBM Z Development e ambiente de teste (IBM zD & T) define um ambiente de não produção no Azure que você pode usar para desenvolvimento, testes e demonstrações de aplicativos baseados em z/OS.

O ambiente de emulação do Azure pode hospedar diferentes tipos de instâncias de Z por meio de distribuições de desenvolvedores de aplicativo controlado (ADCDs). Você pode executar zD T Personal Edition, zD Sysplex paralelo de T e zD & T Enterprise Edition no Azure e o Azure Stack.

Introdução:

- [Configurar o IBM zD & 12.0 T no Azure](./ibm/install-ibm-z-environment.md)
- [Configurar ADCD em zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale no Azure

O ambiente do IBM DB2 pureScale fornece um cluster de banco de dados para o Azure. Não é idêntico ao ambiente original, mas ele fornece disponibilidade e escala semelhantes como IBM DB2 para z/OS em execução em uma configuração de Sysplex paralelo.

Para começar, consulte [pureScale IBM DB2 no Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerações

Quando você migra cargas de trabalho de mainframe para a infraestrutura do Azure como um serviço (IaaS), você pode escolher entre vários tipos e sob demanda escalonáveis recursos de computação, incluindo as VMs do Azure. O Azure oferece uma variedade de [Linux](/azure/virtual-machines/linux/overview) e [Windows](/azure/virtual-machines/windows/overview) VMs.

### <a name="compute"></a>Computação

Potência de computação do Azure favorável compara a capacidade do mainframe. Se você está pensando em mudar uma carga de trabalho de mainframe no Azure, compare a métrica de mainframe de um milhão de instruções por segundo (MIPS) para CPUs virtuais. 

Saiba como [mover a computação de mainframe para o Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Failover e alta disponibilidade

O Azure oferece o compromisso com base em contratos de nível de serviço (SLAs). Disponibilidade de vários noves é o padrão e SLAs podem ser otimizados com a replicação local ou com base geográfica dos serviços. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Com a IaaS do Azure como uma VM, funções de sistema específica oferecem suporte a failover — por exemplo, instâncias de cluster de failover e [conjuntos de disponibilidade](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Quando você usa a plataforma Windows Azure como um serviço (PaaS) de recursos, a plataforma lida com failover automaticamente. Os exemplos incluem [banco de dados SQL](/azure/sql-database/sql-database-technical-overview) e [do Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Escalabilidade

Mainframes normalmente se expandem, enquanto os ambientes de nuvem se expandem. O Azure oferece uma variedade de [Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) tamanhos para atender às suas necessidades. A nuvem também pode ser dimensionado para cima ou para baixo até as especificações exatas do usuário de correspondência. Potência de computação, armazenamento e serviços [escala](/azure/architecture/best-practices/auto-scaling) sob demanda em um modelo de cobrança baseada em uso.

### <a name="storage"></a>Armazenamento

Na nuvem, você tem uma variedade de opções de armazenamento flexível e escalonável, e você paga apenas pelo que precisa. O [Armazenamento do Azure](/azure/storage/common/storage-introduction) oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, armazenamento de mensagens confiáveis e armazenamento de NoSQL. Para VMs, discos gerenciados e discos não gerenciados fornecem armazenamento em disco persistente e seguro.

Saiba como [mover o armazenamento de mainframe para Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup e recuperação

Manter seu próprio site de recuperação de desastre pode ser uma proposição cara. Opções de fácil de implementar e econômicas para o Azure tem [backup](/azure/backup/backup-introduction-to-azure-backup), [recuperação](/azure/site-recovery/site-recovery-overview), e [redundância](/azure/storage/common/storage-redundancy) nos níveis locais ou regionais ou por meio de redundância geográfica.

## <a name="azure-government-for-mainframe-migrations"></a>O Azure governamental para migrações de mainframe

Muitas entidades do setor público adoraria mover seus aplicativos de mainframe para uma plataforma mais moderna e flexível. Microsoft Azure Government é uma instância fisicamente separada da plataforma global do Microsoft Azure — empacotados para sistemas do governo federal, estadual e local. Ele fornece segurança de classe mundial, proteção e serviços de conformidade especificamente para agências governamentais dos Estados Unidos e seus parceiros.

O Azure governamental ganhos uma autoridade provisória de funcionamento (P-ATO) para FedRAMP alto impacto para os sistemas que precisam desse tipo de ambiente.

Para começar, baixe [nuvem do Microsoft Azure governamental para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Próximas etapas

Peça ao nosso [parceiros](partner-workloads.md) para ajudá-lo a migrar ou hospedar novamente seus aplicativos de mainframe. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte o [plataforma modernização Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) site.

Consulte também:

- [White papers sobre tópicos de mainframe](mainframe-white-papers.md)
- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Solução de problemas](/azure/virtual-machines/troubleshooting/)
- [Desmistificando mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
