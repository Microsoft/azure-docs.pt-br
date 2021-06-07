---
title: O que é o SQL Azure?
description: 'Conheça as diferentes opções na família de serviços do SQL do Azure: Banco de Dados SQL do Azure, Instância Gerenciada de SQL do Azure e SQL Server em VM do Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: Nuvem do SQL Server, SQL Server na nuvem, banco de dados PaaS, SQL Server na nuvem, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: ea097119111d5dbd5eba3c11aba549d201186e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592176"
---
# <a name="what-is-azure-sql"></a>O que é o SQL Azure? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

O SQL do Azure é uma família de produtos gerenciados, seguros e inteligentes que usam o mecanismo de banco de dados do SQL Server na nuvem do Azure.

- **Banco de Dados SQL do Azure**: Suporte a aplicativos de nuvem modernos em um serviço de banco de dados inteligente e gerenciado, que inclui computação sem servidor. 
- **Instância Gerenciada de SQL do Azure**: modernize seus aplicativos SQL Server existentes em escala com uma instância como serviço inteligente totalmente gerenciada, com quase 100% de paridade de recursos com o mecanismo de banco de dados do SQL Server. A melhor para a maior parte das migrações para a nuvem.
- **SQL Server em VMs do Azure**: faça lift-and-shift de suas cargas de trabalho do SQL Server com facilidade e mantenha 100% de compatibilidade com o SQL Server e acesso no nível do sistema operacional. 
 
O SQL do Azure é criado com base no já conhecido mecanismo do SQL Server, para que você possa migrar aplicativos com facilidade e continuar usando as ferramentas, as linguagens e os recursos que você conhece. Suas habilidades e sua experiência são transferidas para a nuvem. Assim, você pode fazer ainda mais com o que você já tem. 

Saiba como cada produto se encaixa na plataforma de dados do SQL do Azure da Microsoft para encontrar a opção certa para seus requisitos de negócios. Se você prioriza a economia ou a administração mínima, este artigo pode ajudar você a decidir qual abordagem cumpre os requisitos de negócios com os quais você se preocupa mais.

Se você não está familiarizado com o SQL do Azure, confira o vídeo *O que é o SQL do Azure* da nossa [série detalhada de vídeos do SQL do Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Como podemos aprimorar o SQL do Azure? [Responda à pesquisa](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Visão geral

No mundo atual controlado por dados, a condução da transformação digital depende cada vez mais da nossa capacidade de gerenciar grandes quantidades de dados e de aproveitar o potencial deles. No entanto, os acervos de dados atuais são cada vez mais complexos, com os dados hospedados localmente, na nuvem ou na borda da rede. Os desenvolvedores que estão criando aplicativos inteligentes e de imersão podem se encontrar restritos por limitações que podem, por fim, afetar a experiência deles. Limitações decorrentes de plataformas incompatíveis, segurança de dados inadequada, recursos insuficientes e barreiras de preço-desempenho criam uma complexidade que pode inibir a modernização e o desenvolvimento do aplicativo. 

Uma das primeiras coisas a compreender em qualquer discussão do Azure versus bancos de dados local do SQL Server é que você pode usá-lo. A plataforma de dados da Microsoft aproveita a tecnologia do SQL Server e o torna disponível em computadores locais físicos, ambientes de nuvem privada, ambientes de nuvem privada hospedados por terceiros e nuvem pública. 


### <a name="fully-managed-and-always-up-to-date"></a>Totalmente gerenciado e sempre atualizado 

Gaste mais tempo inovação e menos tempo aplicando patches, atualizando e fazendo backup de seus bancos de dados. O Azure é a única nuvem com SQL evergreen que aplica automaticamente as atualizações e patches mais recentes para que seus bancos de dados estejam sempre atualizados – eliminando os problemas de fim de suporte. Até mesmo tarefas complexas como ajuste de desempenho, alta disponibilidade, recuperação de desastre e backups são automatizadas, liberando você para se concentrar em aplicativos.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Proteja seus dados com segurança inteligente interna 

O Azure monitora constantemente seus dados em busca de ameaças. Com o SQL do Azure, você pode:

- Corrigir ameaças em potencial em tempo real com [detecção avançada de ameaças](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) inteligente e alertas de avaliação proativa de vulnerabilidades. 
- Obter a proteção de várias camadas e líder do setor com [controles de segurança internos](https://azure.microsoft.com/overview/security/), incluindo T-SQL, autenticação, rede e gerenciamento de chaves. 
- Aproveitar a mais abrangente cobertura de [conformidade](https://azure.microsoft.com/overview/trusted-cloud/compliance/) de qualquer serviço de banco de dados de nuvem. 


### <a name="business-motivations"></a>Motivações de negócios

Há vários fatores que podem influenciar sua decisão de optar entre diferentes ofertas de dados:

- [Custo](#cost): tanto a opção de PaaS quanto de IaaS incluem a base de dados de preço que abrange a infraestrutura subjacente e o licenciamento. No entanto, com a opção de IaaS você precisa investir mais tempo e recursos para gerenciar o banco de dados, enquanto no PaaS, você obtém esses recursos de administração incluídos no preço. O IaaS permite que você desligue seus recursos enquanto não os estiver usando para reduzir o custo, enquanto o PaaS está sempre em execução, a menos que você descarte e recrie seus recursos quando forem necessários.
- [Administração](#administration): as opções de PaaS reduzem a quantidade de tempo que você precisa investir para administrar o banco de dados. No entanto, ela também limita o intervalo de tarefas e scripts de administração personalizados que você pode realizar ou executar. Por exemplo, o CLR não é compatível com o Banco de Dados SQL, mas é compatível com uma Instância Gerenciada de SQL. Além disso, nenhuma opção de implantação no PaaS é compatível com o uso de sinalizadores de rastreamento.
- [Contrato de nível de serviço](#service-level-agreement-sla): tanto o IaaS quanto o PaaS fornecem um SLA de padrão alto do setor. A opção PaaS garante SLA de 99,99%, enquanto IaaS, garante SLA de 99,95% para a infraestrutura, o que significa que você precisa implementar mecanismos adicionais para garantir a disponibilidade de seus bancos de dados. Você pode obter um SLA de 99,99% criando uma máquina virtual SQL adicional e implementando a solução de alta disponibilidade do grupo de disponibilidade Always On do SQL Server. 
- [Hora da migração para o Azure](#market): o SQL Server na VM do Azure é a correspondência exata do ambiente, portanto, a migração do local para a VM do Azure não é diferente de mover os bancos de dados de um servidor local para outro. A Instância Gerenciada de SQL também permite a migração fácil; contudo, pode haver algumas alterações que você precisa aplicar antes da sua migração. 


## <a name="service-comparison"></a>Comparação de serviço

   ![Opções de SQL Server de nuvem: SQL Server no IaaS ou Banco de Dados SQL de SaaS na nuvem.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como visto no diagrama, cada oferta de serviço pode ser caracterizada pelo nível de administração que você tem sobre a infraestrutura e pelo grau de economia.

No Azure, suas cargas de trabalho do SQL Server podem ser executadas em um serviço hospedado ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) ou uma infraestrutura hospedada ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). No PaaS, você tem várias opções de produto e camadas de serviço em cada opção. A principal pergunta que você precisa fazer ao decidir entre PaaS ou IaaS é se deseja gerenciar seu banco de dados, aplicar patches, fazer backups ou se deseja delegar essas operações para o Azure?

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O [Banco de Dados SQL do Azure](database/sql-database-paas-overview.md) é um DBaaS (banco de dados relacional como serviço) hospedado no Azure que se encaixa na categoria do setor de *PaaS (plataforma como serviço)* . 
- Melhor para aplicativos de nuvem modernos que desejam usar os mais recentes recursos estáveis do SQL Server e têm restrições de tempo no desenvolvimento e no marketing. 
- Um mecanismo de banco de dados do SQL Server totalmente gerenciado, com base na Edição Enterprise mais estável do SQL Server. O Banco de Dados SQL tem duas opções de implantação baseadas no hardware e no software padronizados que são de propriedade, hospedados e mantidos pela Microsoft. 

Com o SQL Server, você pode usar recursos e funcionalidades internos que exigem ampla configuração (localmente ou em uma máquina virtual do Azure). Quando você usa o Banco de Dados SQL, ele é pré-pago, com opções para escalar verticalmente ou horizontalmente, de modo a ter maior capacidade, sem interrupção. O Banco de Dados SQL tem alguns recursos adicionais que não estão disponíveis no SQL Server, como alta disponibilidade, inteligência e gerenciamento internos.


O Banco de Dados SQL do Azure oferece as seguintes opções de implantação:
  - Como um [*banco de dados individual*](database/single-database-overview.md) com o próprio conjunto de recursos gerenciados por meio de um [servidor SQL lógico](database/logical-servers.md). Um banco de dados individual é semelhante a um [banco de dado independente](/sql/relational-databases/databases/contained-databases) no SQL Server. Essa opção é otimizada para o desenvolvimento moderno de aplicativo de novos aplicativos de nuvem. As opções de [hiperescala](database/service-tier-hyperscale.md) e [sem servidor](database/serverless-tier-overview.md) estão disponíveis.
  - Um [*pool elástico*](database/elastic-pool-overview.md), que é uma coleção de bancos de dados com um conjunto compartilhado de recursos gerenciados por meio de um [servidor SQL lógico](database/logical-servers.md). Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico. Essa opção é otimizada para desenvolvimento de aplicativos modernos de novos aplicativos de nuvem usando o padrão de aplicativo SaaS multilocatário. Os pools elásticos fornecem uma solução econômica para gerenciar o desempenho de vários bancos de dados com padrões de uso variáveis.

### <a name="azure-sql-managed-instance"></a>Instância Gerenciada do Azure SQL

A [Instância Gerenciada de SQL do Azure](managed-instance/sql-managed-instance-paas-overview.md) se enquadra na categoria do setor de *PaaS (Plataforma como Serviço)* e é melhor para a maioria das migrações para a nuvem. A Instância Gerenciada de SQL é uma coleção de bancos de dados do sistema e de usuário com um conjunto compartilhado de recursos prontos para lift-and-shift.  
- Melhor para novos aplicativos ou aplicativos locais existentes que querem usar os recursos estáveis mais recentes do SQL Server e que são migrados para a nuvem com o mínimo de alterações. Uma instância da Instância Gerenciada de SQL é semelhante a uma instância do [mecanismo de banco de dados do Microsoft SQL Server](/sql/database-engine/sql-server-database-engine-overview), oferecendo recursos compartilhados para bancos de dados e recursos adicionais no escopo da instância. 
- A Instância Gerenciada de SQL dá suporte à migração de banco de dados do local com pouca ou nenhuma alteração do banco de dados. Essa opção fornece todos os benefícios de PaaS do Banco de Dados SQL do Azure, mas adiciona recursos que, anteriormente, estavam disponíveis somente nas VMs do SQL Server. Isso inclui uma rede virtual nativa e com quase 100% de compatibilidade com o SQL Server local. Instâncias da Instância Gerenciada de SQL fornecem acesso total ao SQL Server e compatibilidade de recursos para migrar servidores SQL para o Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server na VM do Azure

O [SQL Server na VM do Azure](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) se enquadra na categoria do setor *IaaS (Infraestrutura como Serviço)* e permite executar o SQL Server em uma VM (máquina virtual) totalmente gerenciada no Azure. 
- O melhor para migrações e aplicativos que exigem acesso no nível do sistema operacional. As máquinas virtuais SQL no Azure estão prontas para lift-and-shift em aplicativos existentes que exigem migração rápida para a nuvem com pouca ou nenhuma alteração. Máquinas virtuais de SQL oferecem controle administrativo total sobre a instância do SQL Server e o SO subjacente para migração para o Azure. 
- Cenários de rápido desenvolvimento e teste quando você não deseja comprar hardware do SQL Server local de não produção. As máquinas virtuais SQL também são executadas no hardware padronizado que pertence à Microsoft e é hospedado e mantido por ela. Ao usar máquinas virtuais SQL, você pode pagar uma licença do SQL Server conforme o uso já incluída em uma imagem do SQL Server ou usar facilmente uma licença existente. Você também pode interromper ou retomar a VM conforme o necessário. 
- O SQL Server instalado e hospedado na nuvem é executado em máquinas virtuais do Windows Server ou do Linux em execução no Azure, também conhecido como IaaS (infraestrutura como serviço). As máquinas virtuais SQL são uma boa opção para migrar bancos de dados e aplicativos do SQL Server local sem nenhuma alteração no banco de dados. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação em uma máquina virtual de IaaS. 

    A diferença mais significativa do Banco de Dados SQL e da Instância Gerenciada de SQL é que o SQL Server em Máquinas Virtuais do Azure permite o controle total sobre o mecanismo de banco de dados. Você pode escolher quando deseja iniciar a manutenção/aplicação, alterar o modelo de recuperação para simples ou bulk-logged e pausar ou iniciar o serviço quando necessário. Além disso, também pode personalizar completamente o mecanismo de banco de dados do SQL Server. Esse controle adicional traz a responsabilidade agregada de gerenciar a máquina virtual.
- Otimizado para migrar aplicativos existentes para o Azure ou estendendo aplicativos locais existentes para a nuvem em implantações híbridas. Além disso, você pode usar o SQL Server em uma máquina virtual para desenvolver e testar os aplicativos tradicionais do SQL Server. Com as máquinas virtuais SQL, você tem os direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada em nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Esses recursos permitem que você crie um sistema altamente personalizado para endereçar os requisitos específicos de desempenho e disponibilidade de seu aplicativo.

Outras diferenças são listadas na tabela a seguir, mas *o Banco de Dados SQL e a Instância Gerenciada de SQL são otimizados para reduzir os custos gerais de gerenciamento ao mínimo para provisionar e gerenciar muitos bancos de dados.* Custos de administração contínua são reduzidos porque você não precisa gerenciar máquinas virtuais, um sistema operacional nem um software de banco de dados. Você não precisa gerenciar as atualizações, a alta disponibilidade ou os [backups](database/automated-backups-overview.md). 

Em geral, o Banco de Dados SQL e a Instância Gerenciada de SQL podem aumentar drasticamente o número de bancos de dados gerenciados por um recurso de TI ou de desenvolvimento. Os [Pools elásticos](database/elastic-pool-overview.md) também dão suporte a arquiteturas de aplicativo multilocatário de SaaS com recursos, como isolamento de locatários e a capacidade de dimensionar para reduzir os custos por meio do compartilhamento de recursos entre os bancos de dados. A [Instância Gerenciada de SQL](managed-instance/sql-managed-instance-paas-overview.md) é compatível com recursos com escopo na instância, facilitando a migração de aplicativos existentes e compartilhando recursos entre os bancos de dados.

### <a name="comparison-table"></a>Tabela de comparação

| Banco de Dados SQL do Azure | Instância Gerenciada do Azure SQL | SQL Server na VM do Azure |
| :--- | :--- | :--- |
|É compatível com a maioria dos recursos locais no nível do banco de dados. Recursos do SQL Server mais comumente usados estão disponíveis.<br/>Disponibilidade garantida de 99,995%.<br/>Backups internos, aplicação de patch, recuperação.<br/>Última versão estável do Mecanismo de Banco de Dados do Microsoft SQL Server.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) para bancos de dados individuais.<br/>Inteligência avançada interna e segurança.<br/>Alteração online de recursos (CPU/armazenamento).| É compatível com quase todos os recursos locais no nível da instância e no nível do banco de dados. Alta compatibilidade com o SQL Server.<br/>99,99% de disponibilidade garantida.<br/>Backups internos, aplicação de patch, recuperação.<br/>Última versão estável do Mecanismo de Banco de Dados do Microsoft SQL Server.<br/>Fácil migração do SQL Server local.<br/>Endereço IP privado na Rede Virtual do Azure.<br/>Inteligência avançada interna e segurança.<br/>Alteração online de recursos (CPU/armazenamento).| Você tem controle total sobre o mecanismo do SQL Server. É compatível com todos os recursos locais.<br/>Até 99,99% de disponibilidade.<br/>Paridade completa com a versão correspondente do SQL Server no local.<br/>Versão do Mecanismo de Banco de Dados fixa e bem conhecida.<br/>Fácil migração do SQL Server local.<br/>Endereço IP privado na Rede Virtual do Azure.<br/>Você tem a capacidade de implantar o aplicativo ou serviços no host em que o SQL Server é colocado.|
|A migração do SQL Server pode ser desafiadora.<br/>Alguns recursos do SQL Server não estão disponíveis.<br/>Não há tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade do banco de dados.<br/>Suporte ao endereço IP privado com o [Link Privado do Azure](database/private-endpoint-overview.md).|Ainda há um número mínimo de recursos do SQL Server que não estão disponíveis.<br/>Não há tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade do banco de dados.|Você precisa gerenciar seus backups e patches.<br>Você precisa implementar sua própria solução de alta disponibilidade.<br/>Há um tempo de inatividade durante a alteração de recursos (CPU/armazenamento)|
| Bancos de dados de até 100 TB. | Até 8 TB. | Instâncias do SQL Server com até 256 TB de armazenamento. A instância pode suportar quantos bancos de dados forem necessários. |
| O aplicativo local pode acessar dados no Banco de Dados SQL do Azure. | [Implementação de rede virtual nativa](managed-instance/vnet-existing-add-subnet.md) e conectividade com seu ambiente local usando o Azure ExpressRoute ou o Gateway de VPN. | Com as máquinas virtuais SQL, você pode ter aplicativos que são executados parcialmente na nuvem e parcialmente no local. Por exemplo, você pode estender sua rede local e o Domínio do Active Directory para a nuvem por meio da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções de nuvem híbrida, veja [Estendendo soluções de dados locais para a nuvem](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Cost

Seja você uma startup sem recursos financeiros ou uma equipe em uma empresa estabelecida que opera com restrições de orçamento apertado, financiamento limitado costuma ser o principal motivador ao decidir como hospedar os bancos de dados. Nesta seção, você conhecerá as noções básicas de cobrança e licenciamento no Azure associadas à família de serviços SQL do Azure.  Você também aprenderá sobre como calcular o custo total do aplicativo.

### <a name="billing-and-licensing-basics"></a>Noções básicas de licenciamento e cobrança

No momento, o **Banco de Dados SQL** e a **Instância Gerenciada de SQL do Azure** são vendidas como um serviço e estão disponíveis com várias opções e em várias camadas de serviço com preços diferentes para os recursos. Todos os recursos são cobrados por hora a uma taxa fixa com base na camada de serviço e no tamanho da computação escolhidos. Para obter as informações mais recentes sobre as camadas de serviço, tamanhos da computação e quantidades de armazenamento atuais com suporte, confira [Modelo de compra baseado em DTU para o Banco de Dados SQL](database/service-tiers-dtu.md) e [Modelo de compra baseado em vCore para o Banco de Dados SQL e a Instância Gerenciada de SQL](database/service-tiers-vcore.md).

- Com o Banco de Dados SQL, você pode escolher uma camada de serviço adequada às suas necessidades de uma ampla gama de preços que começam em US$ 5/mês para a camada básica. Além disso, você pode criar [pools elásticos](database/elastic-pool-overview.md) para compartilhar recursos entre bancos de dados para reduzir custos e acomodar picos de uso.
- Com a Instância Gerenciada de SQL, você também pode trazer a própria licença. Para obter mais informações sobre como utilizar o próprio licenciamento, confira [Mobilidade de Licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/) ou use a [calculadora do Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) para ver como **economizar até 40%** .

Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares. Você ajustar as camadas de serviço e os tamanhos da computação dinamicamente para atender à variação das necessidades de produtividade do aplicativo.

Com o **Banco de Dados SQL** e a **Instância Gerenciada de SQL**, o software de banco de dados é automaticamente configurado, corrigido e atualizado pelo Azure, o que reduz os custos de administração. Além disso, seus recursos de [backup interno](database/automated-backups-overview.md) o ajudam a obter economia significativa, principalmente quando você tem um grande número de bancos de dados.

Com o **SQL nas VMs do Azure**, você pode usar imagens do SQL Server fornecidas pela plataforma (que incluem uma licença) ou utilizar sua licença do SQL Server. Todas as versões com suporte do SQL Server (2008R2, 2012, 2014, 2016, 2017, 2019) e as edições (Developer, Express, Web, Standard e Enterprise) estão disponíveis. Além disso, as versões BYOL (Traga sua Própria Licença) das imagens estão disponíveis. Ao usar as imagens fornecidas pelo Azure, os custos operacionais dependem do tamanho da VM e da edição do SQL Server escolhida. Independentemente do tamanho da VM ou da edição do SQL Server, você paga o custo de licenciamento por minuto do SQL Server e do Linux ou do Windows Server, juntamente com o custo de Armazenamento do Azure para os discos de VM. A opção de cobrança por minuto permite que você use o SQL Server pelo tempo necessário, sem adquirir licenças adicionais do SQL Server. Se você usar sua própria licença do SQL Server no Azure, serão cobrados apenas os custos de armazenamento e de servidor. Para obter mais informações sobre como utilizar seu próprio licenciamento, consulte [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares.

#### <a name="calculating-the-total-application-cost"></a>Calculando o custo total do aplicativo

Quando você começar a usar uma plataforma de nuvem, o custo de execução do aplicativo incluirá os custos do novo desenvolvimento e da administração contínua, mais os custos de serviço da plataforma de nuvem pública.

Para obter mais informações sobre preços, consulte os recursos a seguir:

- [Preços do Banco de Dados SQL e da Instância Gerenciada de SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço de nuvem se refere à redução da complexidade de administração, além de seu custo. Com o IaaS e PaaS, o Azure administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastre, configura e atualiza o software do banco de dados, gerencia o balanceamento de carga e faz o failover transparente quando há uma falha do servidor em um data center.

- Com o **Banco de Dados SQL** e a **Instância Gerenciada de SQL**, você pode continuar administrando o banco de dados, mas não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional do servidor nem o hardware. Alguns exemplos de itens que você pode continuar a administrar são bancos de dados e logons, ajuste de índice e consulta e auditoria e segurança. Além disso, a configuração da alta disponibilidade para outro data center requer o mínimo de administração e configuração.
- Com o **SQL na VM do Azure**, você tem controle total sobre o sistema operacional e a configuração da instância do SQL Server. Com uma VM, cabe a você decidir quando atualizar o sistema operacional e o software do banco de dados e quando instalar qualquer software adicional, como um antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Além disso, você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. O Azure permite mudar o tamanho de uma VM quando necessário. Para obter informações, consulte [Tamanhos de Máquinas Virtuais e Serviço de Nuvem do Azure](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>SLA (Contrato de Nível de Serviço)

Para vários departamentos de TI, atender às obrigações de tempo de atividade de um SLA (contrato de nível de serviço) é uma grande prioridade. Nesta seção, vamos examinar qual SLA se aplica a cada opção de hospedagem de banco de dados.

Para o **Banco de Dados SQL do Azure** e a **Instância Gerenciada de SQL do Azure**, a Microsoft fornece um SLA de disponibilidade de 99,99%. Para obter as últimas informações, confira [Contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/sql-database/).

Para o **SQL na VM do Azure**, a Microsoft fornece um SLA de disponibilidade de 99,95% que abrange apenas a máquina virtual. Esse SLA não abrange os processos (como o SQL Server) em execução na VM e exige que você hospede pelo menos duas instâncias de VM em um conjunto de disponibilidade. Para obter as últimas informações, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para HA (alta disponibilidade) de banco de dados em VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como os [Grupos de disponibilidade Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional, mas permite que você atinja > 99,99% de disponibilidade do banco de dados.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Tempo para mover para o Azure

O **Banco de Dados SQL do Azure** é a solução adequada para aplicativos projetados em nuvem quando a produtividade do desenvolvedor e um tempo de colocação no mercado rápido para novas soluções são fatores críticos. Com a funcionalidade de programação como DBA, ele é perfeito para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes.

A **Instância Gerenciada de SQL do Azure** simplifica muito a migração de aplicativos existentes para o Azure, permitindo que você leve aplicativos de banco de dados migrados ao mercado no Azure rapidamente.

O **SQL na VM do Azure** é perfeito se seus aplicativos existentes ou novos exigem bancos de dados grandes ou acesso a todos os recursos do SQL Server ou Windows/Linux e você quer evitar o tempo e a despesa de adquirir um novo hardware local. Ele também é uma boa opção quando você deseja migrar aplicativos e bancos de dados locais existentes para o Azure no estado em que se encontram, nos casos em que o Banco de Dados SQL ou a Instância Gerenciada de SQL não é uma boa opção. Como você não precisa alterar as camadas de apresentação, aplicativo e dados, economiza tempo e orçamento na nova arquitetura da solução existente. Em vez disso, você pode se concentrar em migrar todas as soluções para o Azure e fazer algumas otimizações de desempenho que possam ser necessárias para a plataforma Microsoft Azure. Para obter mais informações, veja [Práticas Recomendadas de Desempenho para o SQL Server em Máquinas Virtuais do Azure](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [Seu primeiro Banco de Dados SQL do Azure](database/single-database-create-quickstart.md) para uma introdução ao Banco de Dados SQL.
- Confira [Sua primeira Instância Gerenciada de SQL do Azure](managed-instance/instance-create-quickstart.md) para começar a usar a Instância Gerenciada de SQL. 
- Confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Confira [Provisionar uma máquina virtual do SQL Server no Azure](virtual-machines/windows/create-sql-vm-portal.md) para obter uma introdução ao SQL Server em VMs do Azure.
- [Identificar o Banco de Dados SQL ou o SKU da Instância Gerenciada de SQL correto para o seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).