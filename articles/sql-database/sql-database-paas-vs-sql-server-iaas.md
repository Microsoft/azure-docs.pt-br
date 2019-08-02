---
title: Escolhendo entre as opções de SQL no Azure | Microsoft Docs
description: Saiba como escolher entre as opções de implantação dentro do Banco de Dados SQL do Azure e entre o Banco de Dados SQL do Azure e o SQL Server em máquinas virtuais do Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Nuvem do SQL Server, SQL Server na nuvem, banco de dados PaaS, SQL Server na nuvem, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: 9e95569ba3fe65ea5bce7d6a95a24324235e9a7f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447745"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Escolher a melhor opção do Microsoft SQL Server no Azure

No Azure, suas cargas de trabalho do SQL Server podem ser executadas em uma infraestrutura hospedada (IaaS) ou como um serviço hospedado ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). Em PaaS, você tem várias opções de implantação e camadas de serviço dentro de cada opção de implantação. A questão principal que você precisa perguntar ao decidir entre PaaS ou IaaS é se você deseja gerenciar seu banco de dados, aplicar patches, fazer backups ou deseja delegar essas operações para o Azure?
Dependendo da resposta, você tem as seguintes opções:

- [Banco de Dados SQL do Azure](sql-database-technical-overview.md): Um mecanismo de banco de dados SQL totalmente gerenciado, com base na Edição Enterprise mais estável do Microsoft SQL Server. Isso é um banco de dados relacional como serviço (DBaaS) hospedado na nuvem do Azure que se enquadra na categoria do setor *Platform-as-a-Service (PaaS)* . O Banco de Dados SQL tem várias opções de implantação, cada uma das quais se baseia no hardware e no software padronizados que são de propriedade, hospedados e mantidos pela Microsoft. Com o Banco de Dados SQL, você pode usar recursos e funcionalidades internos que exigem ampla configuração quando usados no SQL Server (localmente ou em uma máquina virtual do Azure). Quando você usa o Banco de Dados SQL, ele é pré-pago, com opções para escalar verticalmente ou horizontalmente, de modo a ter maior capacidade, sem interrupção. O Banco de Dados SQL tem recursos adicionais que não estão disponíveis no SQL Server, como alta disponibilidade, inteligência e gerenciamento internos. O Banco de Dados SQL do Azure oferece as seguintes opções de implantação:
  
  - Como um [banco de dados individual](sql-database-single-database.md) com seu próprio conjunto de recursos gerenciado por um servidor do Banco de Dados SQL. Um banco de dados individual é semelhante a [bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server. Essa opção é otimizada para o desenvolvimento moderno de aplicativo de novos aplicativos de nuvem.
  - Uma [pool Elástico](sql-database-elastic-pool.md), que é uma coleção de bancos de dados com um conjunto compartilhado de recursos gerenciados por meio de um servidor do Banco de Dados SQL. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico. Essa opção é otimizada para desenvolvimento de aplicativos modernos de novos aplicativos de nuvem usando o aplicativo SaaS multilocatário.
  - [A instância gerenciada](sql-database-managed-instance.md), que é uma coleção de bancos de dados do sistema e do usuário com um conjunto compartilhado de recursos. Uma instância gerenciada é semelhante a uma instância do [mecanismo de banco de dados do Microsoft SQL Server] que oferece recursos compartilhados para bancos de dados e recursos adicionais no escopo da instância. A instância gerenciada dá suporte à migração de banco de dados do local com nenhuma alteração no banco de dados ou com uma alteração mínima. Essa opção fornece todos os benefícios de PaaS do Banco de Dados SQL do Azure, mas adiciona recursos que, anteriormente, estavam disponíveis somente nas VMs do SQL. Isso inclui uma VNet (rede virtual) nativa e quase 100% de compatibilidade com o SQL Server local.
- [O SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) se enquadra na categoria do setor *IaaS (Infraestrutura como Serviço)* e permite executar o SQL Server em uma máquina virtual totalmente gerenciada na nuvem do Azure. As [máquinas virtuais do SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) também são executadas no hardware padronizado que pertence à Microsoft e é hospedado e mantido por ela. Ao usar o SQL Server em uma VM, você pode pagar previamente por uma licença do SQL Server já incluída em uma imagem do SQL Server ou usar facilmente uma licença existente. Você também pode parar ou retomar a VM conforme necessário. O SQL Server instalado e hospedado na nuvem em VMs (máquinas virtuais) do Windows Server ou do Linux, em execução no Azure, também conhecido como IaaS (infraestrutura como serviço). O SQL Server em máquinas virtuais do Azure é uma boa opção para migrar bancos de dados e aplicativos do SQL Server local sem nenhuma alteração no banco de dados. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação em uma máquina virtual de IaaS. A diferença mais significativa do Banco de Dados SQL é que as VMs do SQL Server permitem o controle total sobre o mecanismo de banco de dados. Você pode escolher quando a manutenção/aplicação de patches será iniciada, alterar o modelo de recuperação para simples ou bulk-logged para habilitar o carregamento mais rápido e menos log, pausar ou iniciar o mecanismo quando necessário e personalizar totalmente o mecanismo de banco de dados do SQL Server. Esse controle adicional traz a responsabilidade de gerenciar as máquinas virtuais.

As diferenças principais entre essas opções estão listadas na tabela a seguir:

| SQL Server em VM | A instância gerenciada no Banco de Dados SQL | Banco de dados individual/pool elástico no Banco de Dados SQL |
| --- | --- | --- |
|Você tem controle total sobre o mecanismo do SQL Server.<br/>Até 99,95% de disponibilidade.<br/>Paridade completa com a versão correspondente do SQL Server no local.<br/>Versão do mecanismo de banco de dados fixa e bem conhecida.<br/>Fácil migração do SQL Server local.<br/>Endereço IP privado na VNet do Azure.<br/>Você tem a capacidade de implantar o aplicativo ou serviços no host em que o SQL Server é colocado.| Alta compatibilidade com o SQL Server local.<br/>99,99% de disponibilidade garantida.<br/>Backups internos, aplicação de patch, recuperação.<br/>Última versão estável do Mecanismo de Banco de Dados do Microsoft SQL Server.<br/>Fácil migração do SQL Server local.<br/>Endereço IP privado na VNet do Azure.<br/>Inteligência avançada interna e segurança.<br/>Alteração online de recursos (CPU/armazenamento).|Recursos do SQL Server mais comumente usados estão disponíveis.<br/>99,99% de disponibilidade garantida.<br/>Backups internos, aplicação de patch, recuperação.<br/>Última versão estável do Mecanismo de Banco de Dados do Microsoft SQL Server.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) para bancos de dados individuais.<br/>Inteligência avançada interna e segurança.<br/>Alteração online de recursos (CPU/armazenamento).|
|Você precisa gerenciar seus backups e patches.<br>Você precisa implementar sua própria solução de alta disponibilidade.<br/>Há um tempo de inatividade durante a alteração de recursos (CPU/armazenamento)|Ainda há um número mínimo de recursos do SQL Server que não estão disponíveis.<br/>Não há tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade do banco de dados.|A migração do SQL Server pode ser difícil.<br/>Alguns recursos do SQL Server não estão disponíveis.<br/>Não há tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade do banco de dados.<br/>Não é possível atribuir o endereço IP privado (você pode limitar o acesso usando regras de firewall).|

Aprenda como cada opção de implantação se encaixa na plataforma de dados da Microsoft e obtenha ajuda que corresponda à opção correta aos requisitos de sua empresa. Se você priorizar economia ou administração mínima à frente de tudo, este artigo pode ajudá-lo a decidir qual abordagem cumpre os requisitos de negócios com os quais você se preocupa mais.

## <a name="microsofts-sql-data-platform"></a>Plataforma de dados SQL da Microsoft

Uma das primeiras coisas a compreender em qualquer discussão do Azure versus bancos de dados local do SQL Server é que você pode usá-lo. A Plataforma de Dados da Microsoft aproveita a tecnologia do SQL Server e o torna disponível em computadores locais físicos, ambientes de nuvem privada, ambientes de nuvem privada hospedados por terceiros e nuvem pública. O SQL Server nas máquinas virtuais do Azure permite atender a necessidades comerciais exclusivas e diversificadas por meio de uma combinação de implantações locais e hospedadas na nuvem, usando o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e experiência nesses ambientes.

   ![Opções de SQL Server de nuvem: SQL Server em IaaS ou Banco de Dados SQL de SaaS na nuvem.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como visto no diagrama, cada oferta pode ser caracterizada por nível de administração que você tem sobre a infraestrutura (no eixo X) e pelo grau de economia obtida com a consolidação de nível de banco de dados e automação (no eixo Y).

Ao criar um aplicativo, quatro opções básicas estão disponíveis para hospedar a parte do SQL Server do aplicativo:

- SQL Server em computadores físicos não virtualizados
- SQL Server em computadores virtualizados locais (nuvem privada)
- SQL Server na Máquina Virtual do Azure (nuvem pública da Microsoft)
- Banco de Dados SQL do Azure (nuvem pública da Microsoft)

Nas seções a seguir, você aprenderá sobre o SQL Server na nuvem pública da Microsoft: Banco de Dados SQL do Azure e SQL Server em VMs do Azure. Além disso, irá explorar os motivadores comerciais comuns para determinar qual opção funciona melhor para seu aplicativo.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Uma análise mais aprofundada do Banco de Dados SQL do Azure e do SQL Server em VMs do Azure

Em geral, essas duas opções de SQL são otimizadas para finalidades diferentes:

- **Banco de Dados SQL do Azure**

Otimizado para reduzir os custos gerais de gerenciamento para o mínimo para provisionar e gerenciar muitos bancos de dados. Ele reduz os custos de administração contínua porque você não precisa gerenciar máquinas virtuais, um sistema operacional nem um software de banco de dados. Você não precisa gerenciar as atualizações, a alta disponibilidade ou os [backups](sql-database-automated-backups.md). Em geral, o Banco de Dados SQL do Azure pode aumentar drasticamente o número de bancos de dados gerenciados por um único recurso de TI ou desenvolvimento. Os [Pools elásticos](sql-database-elastic-pool.md) também dão suporte a arquiteturas de aplicativo multilocatário de SaaS com recursos, como isolamento de locatários e a capacidade de dimensionar para reduzir os custos por meio do compartilhamento de recursos entre os bancos de dados. A [instância gerenciada](sql-database-managed-instance.md) é compatível com recursos com escopo na instância, facilitando a migração de aplicativos existentes e compartilhando recursos entre os bancos de dados.

- **SQL Server em execução em VMs do Azure**

Otimizado para migrar aplicativos existentes para o Azure ou estendendo aplicativos locais existentes para a nuvem em implantações híbridas. Além disso, você pode usar o SQL Server em uma máquina virtual para desenvolver e testar os aplicativos tradicionais do SQL Server. Com o SQL Server em VMs do Azure, você tem os direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada em nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Esses recursos permitem que você crie um sistema altamente personalizado para endereçar os requisitos específicos de desempenho e disponibilidade de seu aplicativo.

A seguinte tabela resume as principais características do Banco de Dados SQL e do SQL Server em VMs do Azure:

| | Pools elásticos e bancos de dados individuais do Banco de Dados SQL | Instâncias gerenciadas do Banco de Dados SQL |Máquinas virtuais do Azure com o SQL Server |
| --- | --- | --- |---|
| **Mais adequado para:** |Novos aplicativos projetados em nuvem que desejam usar os mais recentes recursos estáveis do SQL Server e têm restrições de tempo no desenvolvimento e no marketing. | Novos aplicativos ou aplicativos locais existentes que precisam usar os recursos estáveis mais recentes do SQL Server e que são migrados para a nuvem com o mínimo de alterações.  | Aplicativos existentes que requerem uma migração rápida para a nuvem com o mínimo de alterações ou nenhuma alteração. Cenários de rápido desenvolvimento e teste quando você não deseja comprar hardware do SQL Server local de não produção. |
|  | Equipes que precisam da atualização, recuperação de desastres e alta disponibilidade interna para o banco de dados. | Mesmo que os bancos de dados individuais e em pool do Banco de Dados SQL. | Equipes que podem configurar, ajustar, personalizar e gerenciar a alta disponibilidade, a recuperação de desastre e a aplicação de patch do SQL Server. Alguns recursos automatizados fornecidos simplificam muito isso. |
|  | Equipes que não desejam gerenciar o sistema operacional subjacente e definições de configuração. | Mesmo que os bancos de dados individuais e em pool do Banco de Dados SQL. | Você precisa de um ambiente personalizado com direitos administrativos completos. |
|  | Bancos de dados de até 100 TB. | Até 8 TB. | Instâncias do SQL Server com até 64 TB de armazenamento. A instância pode suportar quantos bancos de dados forem necessários. |
| **Compatibilidade** | É compatível com a maioria dos recursos locais no nível do banco de dados. | É compatível com quase todos os recursos locais no nível da instância e no nível do banco de dados. | É compatível com todos os recursos locais. |
| **Recursos:** | Você não deseja empregar recursos de TI para configuração e gerenciamento da infraestrutura subjacente, mas deseja se concentrar na camada de aplicativos. | Mesmo que os bancos de dados individuais e em pool do Banco de Dados SQL. | Você tem alguns recursos de TI para a configuração e o gerenciamento. Alguns recursos automatizados fornecidos simplificam muito isso. |
| **Custo total de propriedade:** | Elimina os custos de hardware e reduz os custos administrativos. | Mesmo que os bancos de dados individuais e em pool do Banco de Dados SQL. | Elimina os custos de hardware. |
| **Continuidade dos negócios:** |Além dos [recursos de infraestrutura de tolerância a falhas internos](sql-database-high-availability.md), o Banco de Dados SQL do Azure fornece recursos, como [backups automatizados](sql-database-automated-backups.md), [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore), [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore), [replicação geográfica ativa](sql-database-active-geo-replication.md), e [grupos de failover automático](sql-database-auto-failover-group.md) para aumentar a continuidade dos negócios. Para saber mais, confira a [Visão geral da continuidade de negócios do Banco de Dados SQL](sql-database-business-continuity.md). | Mesmo que bancos de dados em pool e individuais do Banco de Dados SQL, além de backups somente de cópia iniciados pelo usuário estão disponíveis. | O SQL Server nas VMs do Azure permite que você configure uma solução de alta disponibilidade e recuperação de desastres para as necessidades específicas de seu banco de dados. Portanto, você pode ter um sistema altamente otimizado para seu aplicativo. Você pode testar e executar failovers por conta própria, quando necessário. Para saber mais, consulte [Alta disponibilidade e recuperação de desastre para o SQL Server em máquinas virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Nuvem híbrida:** |Seu aplicativo local pode acessar dados no Banco de Dados SQL do Azure. | [Implementação de rede virtual nativa](sql-database-managed-instance-vnet-configuration.md) e conectividade com seu ambiente local usando o Azure ExpressRoute ou o Gateway de VPN. | Com o SQL Server em VMs do Azure você pode ter aplicativos que são executados parcialmente na nuvem e parcialmente no local. Por exemplo, você pode estender sua rede local e o Domínio do Active Directory para a nuvem por meio da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções de nuvem híbrida, veja [Estendendo soluções de dados locais para a nuvem](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Oferece suporte à [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como um assinante para replicar os dados. | A replicação tem suporte para a instância gerenciada como uma versão prévia do recurso. | É totalmente compatível com a [Replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), os [Grupos de Disponibilidade AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), os Integration Services e o envio de logs para replicar dados. Além disso, os backups tradicionais do SQL Server são totalmente suportados |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações de negócios para escolher o Banco de Dados SQL do Azure ou o SQL Server em VMs do Azure

Há vários fatores que podem influenciar sua decisão para escolher PaaS ou IaaS para hospedar os bancos de dados SQL:

- [Custo](#cost) - tanto a opção PaaS como IaaS incluem a base de dados de preço que abrange a infra-estrutura subjacente e licenciamento. No entanto, com a opção de IaaS você precisa investir mais tempo e recursos para gerenciar o banco de dados, enquanto no PaaS, você obtém esses recursos de administração incluídos no preço. A opção IaaS permite que você encerre seus recursos enquanto não estiver usando-os para reduzir o custo, enquanto a versão PaaS está sempre em execução, a menos que você descarte e recrie seus recursos quando forem necessários.
- [Administração](#administration) - as opções de PaaS reduzem a quantidade de tempo que você precisa investir para administrar o banco de dados. No entanto, ela também limita o número de tarefas de administração personalizada e scripts que você pode executar ou executar. Por exemplo, o CLR não é compatível com bancos de dados individual ou em pool, mas há suporte para uma instância gerenciada. Além disso, não há opções de implantação no PaaS suportam o uso de sinalizadores de rastreamento.
- [Contrato de Nível de Serviço](#service-level-agreement-sla) - tanto IaaS como PaaS fornecem SLA de padrão alto do setor. A opção PaaS garante SLA de 99,99%, enquanto IaaS, garante SLA de 99,95% para a infraestrutura, o que significa que você precisa implementar mecanismos adicionais para garantir a disponibilidade de seus bancos de dados. Em casos extremos, se você quiser implementar soluções de alta disponibilidade que corresponda a PaaS, talvez seja necessário criar adicionais do SQL Server na VM e configurar grupos de disponibilidade AlwaysOn, o que pode ser duas vezes o custo de seu banco de dados.
- [Hora de migrar para o Azure](#market) – o SQL Server na VM do Azure é a correspondência exata do ambiente, portanto, a migração do local para a VM do SQL Azure não é diferente de mover os bancos de dados de um servidor local para outro. A instância gerenciada também permite migração extremamente fácil. No entanto, pode haver algumas alterações que você precisa aplicar antes de migrar para a instância gerenciada.

Esses fatores serão discutidos mais detalhadamente nas seções a seguir.

### <a name="cost"></a>Custo

Seja você uma startup sem recursos financeiros ou uma equipe em uma empresa estabelecida que opera com restrições de orçamento apertado, financiamento limitado costuma ser o principal motivador ao decidir como hospedar os bancos de dados. Nesta seção, você aprenderá sobre as noções básicas de cobrança e licenciamento no Azure com relação a estas duas opções de banco de dados relacional: Banco de Dados SQL e SQL Server em VMs do Azure. Você também aprenderá sobre como calcular o custo total do aplicativo.

#### <a name="billing-and-licensing-basics"></a>Noções básicas de licenciamento e cobrança

No momento, o **Banco de Dados SQL** é vendido como um serviço e está disponível com várias opções de implantação e em várias camadas de serviço com preços diferentes para os recursos. Os recursos são cobrados por hora a uma taxa fixa com base na camada de serviço e no tamanho da computação escolhido. Para obter as informações mais recentes sobre as camadas de serviço, tamanhos da computação e quantidades de armazenamento atuais com suporte, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

- Com o banco de dados individual do Banco de Dados SQL, você pode escolher uma camada de serviço que atenda às suas necessidades de uma ampla variedade de preços começando em US$ 5/mês para a camada básica.
- Você pode criar [pools elásticos](sql-database-elastic-pool.md) para compartilhar recursos entre as instâncias de banco de dados para reduzir os custos e acomodar os picos de uso.
- Com a instância gerenciada do Banco de Dados SQL, você também pode usar sua própria licença. Para obter mais informações sobre como utilizar seu próprio licenciamento, consulte [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/) ou use a [calculadora do Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) para ver como **economizar até 40%** .

Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares. Você ajustar as camadas de serviço e os tamanhos da computação dinamicamente para atender à variação das necessidades de produtividade do aplicativo.

Com o **Banco de Dados SQL**, o software de banco de dados é automaticamente configurado, corrigido e atualizado pela Microsoft, o que reduz os custos de administração. Além disso, seus recursos de [backup interno](sql-database-automated-backups.md) o ajudam a obter economia significativa, principalmente quando você tem um grande número de bancos de dados.

Com o **SQL Server nas VMs do Azure**, você pode usar qualquer imagem do SQL Server fornecida pela plataforma (que inclui uma licença) ou utilizar sua licença do SQL Server. Todas as versões com suporte do SQL Server (2008R2, 2012, 2014, 2016) e as edições (Developer, Express, Web, Standard e Enterprise) estão disponíveis. Além disso, as versões BYOL (Traga sua Própria Licença) das imagens estão disponíveis. Ao usar as imagens fornecidas pelo Azure, os custos operacionais dependem do tamanho da VM e da edição do SQL Server escolhida. Independentemente do tamanho da VM ou da edição do SQL Server, você paga o custo de licenciamento por minuto do SQL Server e do Linux ou do Windows Server, juntamente com o custo de Armazenamento do Azure para os discos de VM. A opção de cobrança por minuto permite que você use o SQL Server pelo tempo necessário, sem adquirir licenças adicionais do SQL Server. Se você usar sua própria licença do SQL Server no Azure, serão cobrados apenas os custos de armazenamento e de servidor. Para obter mais informações sobre como utilizar seu próprio licenciamento, consulte [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares.

#### <a name="calculating-the-total-application-cost"></a>Calculando o custo total do aplicativo

Quando você começar a usar uma plataforma de nuvem, o custo de execução do aplicativo incluirá os custos do novo desenvolvimento e da administração contínua, mais os custos de serviço da plataforma de nuvem pública.

**Ao usar o Banco de Dados SQL do Azure:**

- Custos de administração altamente minimizados
- Custos de desenvolvimento limitados para aplicativos migrados (instâncias gerenciadas)
- Custos de serviço do Banco de Dados SQL
- Não há custos de compra de hardware

**Ao usar o SQL Server em VMs do Azure:**

- Custos de administração mais altos
- Limitado à ausência de custos de desenvolvimento para aplicativos migrados
- Custos de serviço de máquina virtual
- Não há custos de compra de hardware

Para obter mais informações sobre preços, consulte os recursos a seguir:

- [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço de nuvem se refere à redução da complexidade de administração, além de seu custo. Com IaaS e PaaS, a Microsoft administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastre, configura e atualiza o software do banco de dados, gerencia o balanceamento de carga e faz o failover transparente quando há uma falha do servidor em um data center.

- Com o **Banco de Dados SQL do Azure**, você pode continuar a administrar o banco de dados, mas não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional do servidor nem o hardware. Alguns exemplos de itens que você pode continuar a administrar são bancos de dados e logons, ajuste de índice e consulta e auditoria e segurança. Além disso, a configuração da alta disponibilidade para outro data center requer o mínimo de administração e configuração.
- Com o **SQL Server nas VMs do Azure**, você tem controle total sobre o sistema operacional e a configuração da instância do SQL Server. Em uma VM, cabe a você decidir quando atualizar o sistema operacional e o software do banco de dados, e quando instalar qualquer software adicional, como um antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Além disso, você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. O Azure permite mudar o tamanho de uma VM quando necessário. Para obter informações, consulte [Tamanhos de Máquinas Virtuais e Serviço de Nuvem do Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

Para vários departamentos de TI, atender às obrigações de tempo de atividade de um SLA (Contrato de Nível de Serviço) é uma grande prioridade. Nesta seção, vamos examinar qual SLA se aplica a cada opção de hospedagem de banco de dados.

Para o **Banco de Dados SQL**, a Microsoft fornece um SLA de 99,99% de disponibilidade. Para obter as últimas informações, consulte [Contrato de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/sql-database/).

Para o **SQL Server em execução em VMs do Azure**, a Microsoft fornece um SLA de disponibilidade de 99,95% que abrange apenas a Máquina Virtual. Esse SLA não abrange os processos (como o SQL Server) em execução na VM e exige que você hospede pelo menos duas instâncias de VM em um conjunto de disponibilidade. Para obter as últimas informações, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para HA (alta disponibilidade) de banco de dados em VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como os [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional, mas permite que você atinja > 99,99% de disponibilidade do banco de dados.

### <a name="market"></a>Tempo para mover para o Azure

**Os bancos de dados individuais ou os pools elásticos do Banco de Dados SQL** são a solução certa para aplicativos projetados em nuvem, quando a produtividade do desenvolvedor e um tempo de entrega rápido para novas soluções são fatores críticos. Com a funcionalidade de programação como DBA, ele é perfeito para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes.

**A instância gerenciada do Banco de Dados SQL** simplifica muito a migração de aplicativos existentes para o Banco de Dados SQL do Azure, permitindo que você leve aplicativos de banco de dados migrados ao mercado no Azure rapidamente.

**O SQL Server executado em VMs do Azure** é perfeito se seus aplicativos existentes ou novos exigirem bancos de dados grandes ou acesso a todos os recursos do SQL Server ou Windows / Linux e você quiser evitar o tempo e a despesa de adquirir novos hardware das instalações. Ele também é uma boa opção quando você deseja migrar aplicativos e bancos de dados locais existentes para o Azure no estado em que se encontram, nos casos em que a instância gerenciada do Banco de Dados SQL do Azure não é uma boa opção. Como você não precisa alterar as camadas de apresentação, aplicativo e dados, economiza tempo e orçamento na nova arquitetura da solução existente. Em vez disso, você pode se concentrar em migrar todas as soluções para o Azure e fazer algumas otimizações de desempenho que possam ser necessárias para a plataforma Microsoft Azure. Para obter mais informações, veja [Práticas Recomendadas de Desempenho para o SQL Server em Máquinas Virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Próximos passos

- Consulte [Seu primeiro Banco de Dados SQL do Azure](sql-database-single-database-get-started.md) para uma introdução ao Banco de Dados SQL.
- Confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Confira [Provisionar uma máquina virtual do SQL Server no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) para obter uma introdução ao SQL Server em VMs do Azure.
- [Identificar o Azure SQL banco de dados/gerenciado instância SKU certa para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).