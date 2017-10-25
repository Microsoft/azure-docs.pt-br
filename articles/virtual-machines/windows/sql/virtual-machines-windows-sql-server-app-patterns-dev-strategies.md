---
title: "Padrões de aplicativo do SQL Server em VMs | Microsoft Docs"
description: "Este artigo aborda os padrões de aplicativo para o SQL Server em máquinas virtuais do Azure. Ele fornece aos desenvolvedores e arquitetos de soluções uma base para design e arquitetura de aplicativos de qualidade."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: bdc23573eca15df9c959d932a23707104266a47a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Resumo:
Determinar o padrão ou os padrões de aplicativos que serão utilizados em seus aplicativos baseados em SQL Server no ambiente do Azure é uma decisão de design importante, que requer uma compreensão sólida de como o SQL Server e cada componente de infraestrutura do Azure funcionam juntos. Com o SQL Server nos Serviços de Infraestrutura do Azure, você pode migrar, manter e monitorar com facilidade seus aplicativos de SQL Server existentes integrados ao Windows Server em máquinas virtuais no Azure.

O objetivo deste artigo é fornecer aos desenvolvedores e arquitetos de soluções a base para uma boa arquitetura e design de aplicativos que eles possam seguir ao migrar aplicativos existentes para o Azure, bem como ao desenvolver novos aplicativos no Azure.

Para cada padrão de aplicativo, você encontrará um cenário local, sua respectiva solução habilitada para nuvem e as recomendações técnicas relacionadas. Além disso, o artigo discute estratégias de desenvolvimento específicas do Azure para que você possa criar seus aplicativos corretamente. Devido à variedade de padrões de aplicativos, é recomendável que arquitetos e desenvolvedores escolham o padrão mais apropriado para seus aplicativos e usuários.

**Colaboradores técnicos:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman e Xin Jin

## <a name="introduction"></a>Introdução
Você pode desenvolver diversos tipos de aplicativos com n camadas, separando os componentes das diferentes camadas de aplicativo em máquinas diferentes, bem como em componentes separados. Por exemplo, você pode colocar os componentes de regras de negócios e aplicativo cliente em uma máquina, os componentes da camada web de front-end e da camada de acesso de dados em outra máquina e uma camada de banco de dados de back-end em outra máquina. Este tipo de estruturação ajuda a isolar as camadas entre si. Se alterar a origem dos dados, você não precisa alterar o aplicativo cliente ou Web, somente os componentes de camada de acesso dados.

Um aplicativo típico de *n camadas* inclui a camada de apresentação, a camada de negócios e a camada de dados:

| Camada | Descrição |
| --- | --- |
| **Apresentação** |A *camada de apresentação* (camada da Web, camada de front-end) é a camada na qual os usuários interagem com um aplicativo. |
| **Negócios** |A *camada de negócios* (camada intermediária) é a camada usada pela camada de apresentação e pela camada de dados para se comunicar e que inclui a principal funcionalidade do sistema. |
| **Dados** |A *camada de dados* é basicamente o servidor que armazena os dados de um aplicativo (por exemplo, um servidor que executa o SQL Server). |

Camadas de aplicativo descrevem os agrupamentos lógicos de funcionalidades e os componentes em um aplicativo, enquanto as camadas de modo geral descrevem a distribuição física das funcionalidades e dos componentes em servidores físicos, computadores, redes ou locais remotos separados. As camadas de aplicativo podem residir no mesmo computador físico (a mesma camada) ou podem ser distribuídas em computadores separados (n camadas), e os componentes de cada camada de aplicativo se comunicam com componentes de outras camadas por meio de interfaces bem definidas. Você pode considerar o termo camada, como uma referência a padrões de distribuição física, como de duas camadas, três camadas e n camadas. Um **padrão de aplicativo de duas camadas** contém duas camadas de aplicativo: o servidor de aplicativos e o servidor de banco de dados. A comunicação direta acontece entre o servidor de aplicativos e o servidor de banco de dados. O servidor de aplicativos contém componentes de camada da web e camada de negócios. Em um **padrão de aplicativo de três camadas**, há três camadas de aplicativo: servidor Web, servidor de aplicativos, que contém a camada da lógica de negócios e/ou os componentes de acesso de dados da camada de negócios, e o servidor de banco de dados. A comunicação entre o servidor Web e o servidor de banco de dados ocorre no servidor de aplicativos. Para obter informações detalhadas sobre as camadas de aplicativos, veja o [Guia de arquitetura de aplicativos da Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar a ler este artigo, você deve ter conhecimento dos conceitos básicos do SQL Server e do Azure. Para obter mais informações, confira [Manuais Online do SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve vários padrões de aplicativos que podem ser adequados para seus aplicativos simples, bem como para aplicativos corporativos altamente complexos. Antes de detalharmos cada padrão, recomendamos que você se familiarize com os serviços de armazenamento de dados disponíveis no Azure, como o [Armazenamento do Azure](../../../storage/common/storage-introduction.md), o [Banco de Dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md) e o [SQL Server em uma Máquina Virtual do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tomar as melhores decisões de design para seus aplicativos, entenda claramente quando usar cada serviço de armazenamento de dados.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolha o SQL Server em uma Máquina Virtual do Azure quando:
* Você precisa ter controle no SQL Server e no Windows. Por exemplo, isso pode incluir a versão do SQL Server, hotfixes especiais, configuração de desempenho etc.
* Você precisa de compatibilidade total com o SQL Server local e quer mover aplicativos existentes para o Azure como eles estão.
* Você quer aproveitar os recursos do ambiente do Azure, mas o Banco de Dados SQL do Azure não dá suporte a todos os recursos que seu aplicativo requer. Isso pode incluir as áreas a seguir:
  
  * **Tamanho do banco de dados**: no momento em que este artigo foi atualizado, o Banco de Dados SQL dá suporte a um banco de dados com até 1 TB de dados. Se seu aplicativo exigir mais de 1 TB de dados e você não quiser implementar soluções de fragmentação personalizadas, será recomendável usar o SQL Server em uma Máquina Virtual do Azure. Para obter as informações mais recentes, veja [Escalando horizontalmente Bancos de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx) e [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](../../../sql-database/sql-database-service-tiers.md).
  * **Conformidade com a HIPAA**: ISVs (Fornecedores de Software Independentes) e clientes do setor de serviços de saúde podem optar pelo [SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) em vez do [Banco de Dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md), pois o SQL Server em uma Máquina Virtual do Azure é coberto pelo BAA (Acordo de Parceiro Comercial) da HIPAA. Para obter informações sobre conformidade, veja [Central de Confiabilidade do Microsoft Azure: conformidade](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Recursos do nível de instância**: neste momento, o Banco de Dados SQL não dá suporte a recursos que residem fora do banco de dados (como Servidores Vinculados, trabalhos do Agente, FileStream, Service Broker, etc). Para obter mais informações, veja [Diretrizes e limitações do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>Uma camada (simples): máquina virtual única
Neste padrão de aplicativo, você implanta seu banco de dados e aplicativo do SQL Server em uma máquina virtual autônoma no Azure. A mesma máquina virtual contém seu aplicativo Web/cliente, componentes de negócios, camada de acesso de dados e o servidor de banco de dados. A apresentação, os negócios e o código de acesso a dados estão separados logicamente, mas estão localizados fisicamente em uma única máquina do servidor. A maioria dos clientes inicia com esse padrão de aplicativo e escala horizontalmente por meio do acréscimo de mais funções Web ou máquinas virtuais a seu sistema.

Esse padrão de aplicativo é útil quando:

* Você deseja executar uma migração simples para a plataforma do Azure para avaliar se a plataforma atende aos requisitos do aplicativo ou não.
* Você deseja manter todas as camadas do aplicativo hospedadas na mesma máquina virtual no mesmo data center do Azure para reduzir a latência entre camadas.
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo não deseja possuir e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um cenário local simples e como você pode implantar sua solução habilitada para nuvem em uma única máquina virtual no Azure.

![Padrão de aplicativo de uma camada](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implantar a camada de negócios (componentes de acesso de dados e lógica de negócios) na mesma camada física da camada de apresentação pode maximizar o desempenho do aplicativo, a menos que você precise usar uma camada separada devido a questões de dimensionamento ou segurança.

Como esse é um padrão muito comum para começar, você pode considerar útil o seguinte artigo sobre migração para mover seus dados para a VM do SQL Server: [Migrando um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>Três camadas (simples): várias máquinas virtuais
Nesse padrão de aplicativo, você implanta um aplicativo de três camadas no Azure colocando cada camada do aplicativo em uma máquina virtual diferente. Isso fornece um ambiente flexível para facilitar cenários de ampliação e redução. Quando uma máquina virtual contém seu aplicativo Web/cliente, a outra hospeda seus componentes de negócios e a terceira hospeda o servidor de banco de dados.

Esse padrão de aplicativo é útil quando:

* Você deseja executar uma migração de aplicativos de bancos de dados complexos para máquinas virtuais do Azure.
* Você quer que diferentes camadas do aplicativo sejam hospedadas em regiões diferentes. Por exemplo, você pode ter bancos de dados compartilhados implantados em várias regiões para fins de elaboração de relatórios.
* Você deseja mover aplicativos corporativos de plataformas locais virtualizadas para máquinas virtuais do Azure. Para ver uma discussão detalhada sobre aplicativos empresariais, confira [O que é um Aplicativo Empresarial](https://msdn.microsoft.com/library/aa267045.aspx).
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo não deseja possuir e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra como você pode colocar um aplicativo simples de três camadas no Azure colocando cada camada do aplicativo em uma máquina virtual diferente.

![padrão de aplicativo de três camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Neste padrão de aplicativo, há apenas uma VM (máquina virtual) em cada camada. Se você tiver várias VMs no Azure, recomendamos configurar uma rede virtual. [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) cria um limite de segurança confiável e também permite que as VMs se comuniquem por meio do endereço IP privado. Além disso, certifique-se de que todas as conexões de Internet vão apenas para a camada de apresentação. Quando seguir esse padrão de aplicativo, gerencie as regras de grupos de segurança de rede para controlar o acesso. Para obter mais informações, consulte [Permitir o acesso externo à sua VM usando o Portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

No diagrama, os protocolos de Internet podem ser TCP, UDP, HTTP ou HTTPS.

> [!NOTE]
> Configurar uma rede virtual no Azure é gratuito. No entanto, você é cobrado pelo gateway de VPN que se conecta ao local. A cobrança é feita com base na quantidade de tempo que a conexão é fornecida e fica disponível.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>Duas e três camadas com dimensionamento da camada de apresentação
Nesse padrão de aplicativo, você implanta um aplicativo de banco de dados de duas ou três camadas em máquinas virtuais do Azure colocando cada camada do aplicativo em uma máquina virtual diferente. Além disso, você escala horizontalmente a camada de apresentação devido ao maior volume de solicitações do cliente.

Esse padrão de aplicativo é útil quando:

* Você deseja mover aplicativos corporativos de plataformas locais virtualizadas para máquinas virtuais do Azure.
* Você deseja escalar horizontalmente a camada de apresentação devido ao maior volume de solicitações do cliente.
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo não deseja possuir e manter muitas máquinas físicas o tempo todo.
* Você deseja ter um ambiente de infraestrutura que possa escalar e reduzir verticalmente sob demanda.

O diagrama a seguir demonstra como você pode colocar as camadas do aplicativo em várias máquinas virtuais no Azure expandindo a camada de apresentação devido a um volume maior de solicitações de clientes. Como visto no diagrama, o Balanceador de Carga do Azure é responsável por distribuir o tráfego entre várias máquinas virtuais e por determinar a qual servidor Web se conectar. Ter várias instâncias dos servidores Web atrás de um balanceador de carga garante a alta disponibilidade da camada de apresentação.

![Padrão de aplicativo - escalamento horizontal da camada de apresentação](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Práticas recomendadas para padrões de duas, três ou n camadas que têm várias VMs em uma camada
É recomendável que você coloque as máquinas virtuais que pertencem à mesma camada no mesmo serviço de nuvem e no mesmo conjunto de disponibilidade. Por exemplo, coloque um conjunto de servidores Web em **CloudService1** e **AvailabilitySet1** e um conjunto de servidores de banco de dados em **CloudService2** e **AvailabilitySet2**. Um conjunto de disponibilidade no Azure permite que você coloque os nós de alta disponibilidade em domínios de falha e domínios de atualização separados.

Para aproveitar as várias instâncias de VM de uma camada, você precisa configurar o Balanceador de Carga do Azure entre as camadas do aplicativo. Para configurar o Balanceador de Carga em cada camada, crie um ponto de extremidade com balanceamento de carga nas VMs de cada camada separadamente. Para uma camada específica, primeiro crie VMs no mesmo serviço de nuvem. Isso garante que todos eles tenham o mesmo endereço IP virtual público. Em seguida, crie um ponto de extremidade em uma das máquinas virtuais nessa camada. Em seguida, atribua o mesmo ponto de extremidade às outras máquinas virtuais na camada para balanceamento de carga. Ao criar um conjunto com balanceamento de carga, você distribui o tráfego entre várias máquinas virtuais e também permite que o Balanceador de Carga determine qual nó conectar quando um nó de VM de back-end falhar. Por exemplo, ter várias instâncias dos servidores Web atrás de um balanceador de carga garante a alta disponibilidade da camada de apresentação.

Como prática recomendada, sempre certifique-se de que todas as conexões de Internet vão primeiro para a camada de apresentação. A camada de apresentação acessa a camada de negócios e, em seguida, a camada de negócios acessa a camada de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte [Permitir o acesso externo à sua VM usando o Portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Observe que o Balanceador de Carga no Azure funciona de forma semelhante a balanceadores de carga em um ambiente local. Para obter mais informações, veja [Balanceamento de carga para serviços de infraestrutura do Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Além disso, recomendamos que você configure uma rede privada para suas máquinas virtuais usando a Rede Virtual do Azure. Isso permite que elas se comuniquem por meio do endereço IP privado. Para obter mais informações, veja [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>Duas e três camadas com dimensionamento da camada de negócios
Nesse padrão de aplicativo, você implanta um aplicativo de banco de dados de duas ou três camadas em máquinas virtuais do Azure colocando cada camada do aplicativo em uma máquina virtual diferente. Além disso, talvez você queira distribuir os componentes do servidor de aplicativos em várias máquinas virtuais devido à complexidade do seu aplicativo.

Esse padrão de aplicativo é útil quando:

* Você deseja mover aplicativos corporativos de plataformas locais virtualizadas para máquinas virtuais do Azure.
* Você deseja distribuir os componentes do servidor de aplicativos em várias máquinas virtuais devido à complexidade do seu aplicativo.
* Você deseja mover aplicativos pesados de LOB (linha de negócios) locais de lógica de negócios para as Máquinas Virtuais do Azure. Aplicativos de LOB são um conjunto de aplicativos críticos de computador que são vitais para comandar uma empresa, como aplicativos de contabilidade, RH (recursos humanos), folha de pagamento, gerenciamento da cadeia de suprimentos e planejamento de recursos.
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo não deseja possuir e manter muitas máquinas físicas o tempo todo.
* Você deseja ter um ambiente de infraestrutura que possa escalar e reduzir verticalmente sob demanda.

O diagrama a seguir demonstra um cenário local e sua solução habilitada para nuvem. Nesse cenário, você coloca as camadas do aplicativo em várias máquinas virtuais no Azure por meio da expansão da camada de negócios, que contém os componentes de acesso de dados e a camada de lógica de negócios. Como visto no diagrama, o Balanceador de Carga do Azure é responsável por distribuir o tráfego entre várias máquinas virtuais e por determinar a qual servidor Web se conectar. Ter várias instâncias dos servidores de aplicativo atrás de um balanceador de carga garante a alta disponibilidade da camada de negócios. Para obter mais informações, veja [Práticas recomendadas para padrões de aplicativos de duas, três ou n camadas que têm várias máquinas virtuais em uma camada](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicativo com escalamento horizontal da camada de negócios](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>Duas e três camadas com expansão das camadas de negócios e apresentação e HADR
Nesse padrão de aplicativo, você implanta um aplicativo de banco de dados de duas ou três camadas em Máquinas Virtuais do Azure distribuindo a camada de apresentação (servidor Web) e os componentes da camada corporativa (servidor de aplicativos) em várias máquinas virtuais. Além disso, você deve implementar soluções de recuperação de desastres e alta disponibilidade em seus bancos de dados nas máquinas virtuais do Azure.

Esse padrão de aplicativo é útil quando:

* Você deseja mudar aplicativos corporativos de plataformas virtualizadas locais para o Azure implementando os recursos de alta disponibilidade e recuperação de dados do SQL Server.
* Você deseja escalar horizontalmente a camada de apresentação e a camada de negócios devido ao aumento no volume de solicitações de cliente e à complexidade do seu aplicativo.
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo não deseja possuir e manter muitas máquinas físicas o tempo todo.
* Você deseja ter um ambiente de infraestrutura que possa escalar e reduzir verticalmente sob demanda.

O diagrama a seguir demonstra um cenário local e sua solução habilitada para nuvem. Nesse cenário, você escala horizontalmente os componentes da camada de apresentação e da camada de negócios em várias máquinas virtuais no Azure. Além disso, você deve implementar técnicas de HADR (alta disponibilidade e recuperação de desastres) em seus bancos de dados do SQL Server.

Ao executar várias cópias de um aplicativo em diferentes VMs, certifique-se de estar balanceando a carga de solicitações entre elas. Quando tem várias máquinas virtuais, você precisa certificar-se de que todas as suas VMs estejam acessíveis e em execução em um determinado momento. Se você configurar o balanceamento de carga, o Azure Load Balancer vai monitorar a integridade das VMs e direcionar as chamadas de entrada para os nós de VM funcionando corretamente. Para obter informações sobre como configurar o balanceamento de carga das máquinas virtuais, veja [Balanceamento de carga para serviços de infraestrutura do Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ter várias instâncias dos servidores Web e de aplicativo atrás de um balanceador de carga garante a alta disponibilidade das camadas de negócios e apresentação.

![Escalamento horizontal e alta disponibilidade](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Práticas recomendadas para padrões de aplicativo que exigem HADR SQL
Quando você configura soluções de alta disponibilidade e recuperação de desastre do SQL Server nas Máquinas Virtuais do Azure, é obrigatório configurar uma rede virtual para suas máquinas virtuais usando a [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) .  Máquinas virtuais em uma Rede Virtual terão um endereço IP privado estável mesmo após um tempo de inatividade do serviço, assim você pode evitar o tempo de atualização necessário para resolução de nome DNS. Além disso, a rede virtual permite que você estenda sua rede local para o Azure e crie um limite de segurança confiável. Por exemplo, se o seu aplicativo tiver restrições de domínio corporativo (por exemplo, autenticação do Windows, Active Directory), é necessário configurar a [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) .

A maioria dos clientes que estão executando código de produção no Azure mantêm as réplicas primária e secundária no Azure.

Para obter informações abrangentes e tutoriais sobre técnicas de alta disponibilidade e recuperação de desastres, veja [Alta disponibilidade e recuperação de desastre para o SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>Duas e três camadas usando Serviços de Nuvem e VMs do Azure
Nesse padrão de aplicativo, você implanta um aplicativo de duas ou três camadas no Azure usando os [Serviços de Nuvem do Azure](../../../cloud-services/cloud-services-choose-me.md#tellmecs) (funções web e de trabalho – PaaS (Plataforma como serviço)) e as [Máquinas Virtuais do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (IaaS (Infraestrutura como serviço)). Usar os [Serviços de Nuvem do Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para a camada de negócios/camada de apresentação e o SQL Server nas [Máquinas Virtuais do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para a camada de dados é útil para a maioria dos aplicativos em execução no Azure. O motivo é que ter uma instância de computação em execução nos Serviços de Nuvem facilita o gerenciamento, a implantação, o monitoramento e o dimensionamento.

Com os Serviços de Nuvem, o Azure mantém a infraestrutura para você, executa a manutenção de rotina, aplica patches aos sistemas operacionais e tenta se recuperar de falhas do serviço e do hardware. Quando seu aplicativo precisa ser escala horizontalmente, há opções de escalamento horizontal automático e manual disponíveis para seu projeto de serviço de nuvem, aumentando ou diminuindo o número de instâncias ou máquinas virtuais que são usadas pelo aplicativo. Além disso, você pode usar o Visual Studio local para implantar o aplicativo em um projeto de serviço de nuvem no Azure.

Em resumo, se você não quiser ser responsável por extensas tarefas administrativas para as camadas de negócios/apresentação e seu aplicativo não precisar de nenhuma configuração complexa de software ou sistema operacional, use os Serviços de Nuvem do Azure. Se o Banco de Dados SQL do Azure não der suporte a todos os recursos que você está procurando, use o SQL Server em uma Máquina Virtual do Azure para a camada de dados. Executar um aplicativo nos Serviços de Nuvem do Azure e armazenar dados em Máquinas Virtuais do Azure combina os benefícios dos dois serviços. Para ver uma comparação detalhada, confira a seção neste tópico em [Comparando as estratégias de desenvolvimento no Azure](#comparing-web-development-strategies-in-azure).

Nesse padrão de aplicativo, a camada de apresentação inclui uma função web, que é um componente dos Serviços de Nuvem em execução no ambiente de execução do Azure e é personalizada para a programação de aplicativos Web com suporte do IIS e ASP.NET. A camada de negócios ou back-end inclui uma função de trabalho, que é um componente dos Serviços de Nuvem em execução no ambiente de execução do Azure e é útil para desenvolvimento de forma geral, e pode executar o processamento em segundo plano para uma função web. A camada de banco de dados reside em uma máquina virtual do SQL Server no Azure. A comunicação entre a camada de apresentação e a camada de banco de dados acontece diretamente ou por meio dos componentes da camada de negócios - função de trabalho.

Esse padrão de aplicativo é útil quando:

* Você deseja mudar aplicativos corporativos de plataformas virtualizadas locais para o Azure implementando os recursos de alta disponibilidade e recuperação de dados do SQL Server.
* Você deseja ter um ambiente de infraestrutura que possa escalar e reduzir verticalmente sob demanda.
* O Banco de Dados SQL do Azure não dá suporte a todos os recursos de que seu aplicativo ou banco de dados precisa.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo não deseja possuir e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um cenário local e sua solução habilitada para nuvem. Nesse cenário, você coloca a camada de apresentação em funções da Web e a camada de negócios em funções de trabalho, mas a camada de dados em máquinas virtuais no Azure. Executar várias cópias da camada de apresentação em diferentes funções da Web garante o balanceamento da carga de solicitações entre elas. Quando você combina os Serviços de Nuvem do Azure com as Máquinas Virtuais do Azure, recomendamos que você configure também a [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) . Com a [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md), você pode ter endereços IP privados estáveis e persistentes dentro do mesmo serviço de nuvem na nuvem. Depois de você definir uma rede virtual para suas máquinas virtuais e serviços de nuvem, eles poderão começar a se comunicar entre si por meio di endereço IP privado. Além disso, ter máquinas virtuais e funções web/de trabalho do Azure na mesma [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) proporciona baixa latência e uma conectividade mais segura. Para obter mais informações, veja [O que é um serviço de nuvem?](../../../cloud-services/cloud-services-choose-me.md)

Como visto no diagrama, o Balanceador de Carga do Azure distribui o tráfego entre várias máquinas virtuais e determina a qual servidor Web ou servidor de aplicativos se conectar. Ter várias instâncias dos servidores Web e de aplicativo atrás de um balanceador de carga garante a alta disponibilidade das camadas de negócios e de apresentação. Para obter mais informações, veja [Práticas recomendadas para padrões de aplicativos que exigem o HADR SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicativo com Serviços de Nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Outra abordagem para implementar esse padrão de aplicativo é usar uma função web consolidada que contém componentes de camada de apresentação e camada de negócios, conforme mostrado no diagrama a seguir. Esse padrão de aplicativo é útil para aplicativos que requerem um design com monitoração de estado. Como o Azure fornece nós de computação sem monitoração de estado em funções web e de trabalho, recomendamos que você implemente uma lógica para armazenar o estado de sessão usando uma das seguintes tecnologias: [Caching do Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Armazenamento de Tabelas do Azure](../../../cosmos-db/table-storage-how-to-use-dotnet.md) ou [Banco de Dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md).

![Padrões de aplicativo com Serviços de Nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com VMs do Azure, Banco de Dados SQL do Azure e Serviço de Aplicativo do Azure (Aplicativos Web)
O objetivo principal deste padrão de aplicativo é mostrar como combinar os componentes de IaaS (infraestrutura como serviço) do Azure com componentes de PaaS (plataforma como serviço) do Azure em sua solução. Este padrão tem como foco o Banco de Dados SQL do Azure para armazenamento de dados relacionais. Ele não inclui o SQL Server em uma máquina virtual do Azure, que faz parte da oferta de infraestrutura como serviço do Azure.

Nesse padrão de aplicativo, você implanta um aplicativo de banco de dados no Azure colocando as camadas de apresentação e negócios na mesma máquina virtual e acessando um banco de dados nos servidores do Banco de Dados SQL do Azure (Banco de Dados SQL). Você pode implementar a camada de apresentação usando soluções Web tradicionais baseadas no IIS. Ou você pode implementar uma camada combinada de apresentação e de negócios usando os [Aplicativos Web do Azure](https://azure.microsoft.com/documentation/services/app-service/web/).

Esse padrão de aplicativo é útil quando:

* Você já tem um servidor de Banco de Dados SQL existente configurado no Azure e deseja testar seu aplicativo rapidamente.
* Você deseja testar os recursos do ambiente do Azure.
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Os componentes de acesso de dados e lógica de negócios podem ser contidos de forma autônoma dentro de um aplicativo Web.

O diagrama a seguir demonstra um cenário local e sua solução habilitada para nuvem. Nesse cenário, você coloca as camadas de aplicativo em uma única máquina virtual no Azure e acessa dados no Banco de Dados SQL do Azure.

![Padrão de aplicativo misto](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se você optar por implementar uma camada Web e de aplicativo combinada usando os Aplicativos Web do Azure, recomendamos que você mantenha a camada intermediária ou camada de aplicativo como DLLs (bibliotecas de vínculo dinâmico) no contexto de um aplicativo Web.

Além disso, examine as recomendações apresentadas na seção [Comparando as estratégias de desenvolvimento da Web no Azure](#comparing-web-development-strategies-in-azure) no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicativo híbrido de n camadas
No padrão de aplicativo híbrido com n camadas, você implementa seu aplicativo em várias camadas distribuídas entre locais e o Azure. Portanto, você cria um sistema híbrido flexível e reutilizável que pode modificar ou adicionar uma camada específica sem alterar as outras camadas. Para estender sua rede corporativa para a nuvem, use o serviço da [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) .

Esse padrão de aplicativo híbrido é útil quando:

* Você deseja criar aplicativos que são executados parcialmente na nuvem e parcialmente localmente.
* Você deseja migrar alguns ou todos os elementos de um aplicativo local existente para a nuvem.
* Você deseja mover aplicativos corporativos de plataformas locais virtualizadas para o Azure.
* Você deseja ter um ambiente de infraestrutura que possa escalar e reduzir verticalmente sob demanda.
* Você deseja provisionar rapidamente ambientes de teste e desenvolvimento por curtos períodos de tempo.
* Você deseja ter uma forma econômica de fazer backups para aplicativos de bancos de dados corporativos.

O diagrama a seguir demonstra um padrão de aplicativo híbrido de n camadas que inclui o local e o Azure. Como mostrado no diagrama, a infraestrutura local inclui o controlador de domínio dos [Serviços de Domínio do Active Directory](https://technet.microsoft.com/library/hh831484.aspx) para dar suporte à autenticação e autorização do usuário. Observe que o diagrama demonstra um cenário em que algumas partes da camada de dados residem em um data center local, enquanto outras partes da camada residem no Azure. Dependendo das necessidades do seu aplicativo, você pode implementar vários outros cenários híbridos. Por exemplo, você pode manter a camada de apresentação e a camada de negócios em um ambiente local, mas a camada de dados no Azure.

![Padrão de aplicativo de n camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

No Azure, você pode usar o Active Directory como um diretório de nuvem autônomo para a sua organização ou também pode integrar o Active Directory local existente com o [Active Directory do Azure](https://azure.microsoft.com/documentation/services/active-directory/). Como visto no diagrama, os componentes da camada de negócios podem acessar várias fontes de dados, como o [SQL Server no Azure](virtual-machines-windows-sql-server-iaas-overview.md) por meio de um endereço IP interno privado, o SQL Server local por meio da [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) ou o [Banco de Dados SQL](../../../sql-database/sql-database-technical-overview.md) usando as tecnologias de provedor de dados do .NET Framework. Neste diagrama, o Banco de Dados SQL do Azure é um serviço de armazenamento de dados opcionais.

No padrão de aplicativo híbrido de n camadas, você pode implementar o fluxo de trabalho a seguir na ordem especificada:

1. Identificar aplicativos de banco de dados empresariais que precisam ser movidos para a nuvem usando o [MAP (Microsoft Assessment and Planning) Toolkit](http://microsoft.com/map). O MAP Toolkit reúne dados de desempenho e inventário de computadores que você está considerando para virtualização e faz recomendações sobre o planejamento de avaliação e a capacidade.
2. Planeje os recursos e a configuração necessários na plataforma do Azure, como as contas de armazenamento e máquinas virtuais.
3. Configure a conectividade de rede entre a rede corporativa local e a [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Para configurar a conexão entre a rede corporativa local e uma máquina virtual no Azure, use um dos dois métodos a seguir:
   
   1. Estabelecer uma conexão entre locais e o Azure por meio de pontos de extremidade públicos em uma máquina virtual no Azure. Esse método fornece uma configuração fácil e permite que você use a autenticação do SQL Server em sua máquina virtual. Além disso, configure as regras de grupo de segurança de rede para controlar o tráfego público para a VM. Para obter mais informações, consulte [Permitir o acesso externo à sua VM usando o Portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Estabelecer uma conexão entre locais e o Azure através do túnel da VPN (rede virtual privada) do Azure. Esse método permite a extensão de políticas de domínio a uma máquina virtual no Azure. Além disso, você pode configurar regras de firewall e usar a autenticação do Windows em sua máquina virtual. Atualmente, o Azure dá suporte a conexões seguras de VPN site a site e VPN site a ponto:
      
      * Com a conexão segura site a site, você pode estabelecer a conectividade de rede entre sua rede local e sua rede virtual no Azure. É recomendável para conectar o seu ambiente de data center local ao Azure.
      * Com a conexão segura ponto a site, você pode estabelecer a conectividade de rede entre sua rede virtual no Azure e seus computadores virtuais em execução em qualquer lugar. É recomendável principalmente para fins de desenvolvimento e teste.
      
      Para obter informações sobre como se conectar ao SQL Server no Azure, veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-sql-connect.md).
4. Configure trabalhos agendados e alertas que fazem backup de dados locais em um disco de máquina virtual no Azure. Para obter mais informações, veja [Backup e restauração do SQL Server com o Serviço de Armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [Backup e restauração do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).
5. Dependendo das necessidades do seu aplicativo, você pode implementar um dos três cenários comuns a seguir:
   
   1. Você pode manter seu servidor Web, servidor de aplicativos e dados não confidenciais em um servidor de banco de dados no Azure, enquanto mantém os dados confidenciais localmente.
   2. Você pode manter seu servidor Web e servidor de aplicativos localmente, enquanto mantém o servidor de banco de dados em uma máquina virtual no Azure.
   3. Você pode manter seu servidor de banco de dados, servidor Web e servidor de aplicativos localmente, enquanto mantém as réplicas de banco de dados em máquinas virtuais no Azure. Essa configuração permite que servidores Web locais ou aplicativos de geração de relatórios acessem as réplicas de banco de dados no Azure. Portanto, você pode reduzir a carga de trabalho em um banco de dados local. Nós recomendamos que você implemente este cenário para cargas de trabalho de leitura pesadas e para fins de desenvolvimento. Para obter informações sobre como criar réplicas de banco de dados no Azure, veja os Grupos de Disponibilidade AlwaysOn em [Alta disponibilidade e recuperação de desastre para o SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparando as estratégias de desenvolvimento da Web no Azure
Para implementar e implantar um aplicativo de várias camadas baseado no SQL Server no Azure, você pode usar um dos dois métodos programação a seguir:

* Configurar um servidor Web tradicional (IIS - Serviços de Informações da Internet) no Azure e acessar bancos de dados no SQL Server nas Máquinas Virtuais do Azure.
* Implemente e implante um serviço de nuvem no Azure. Em seguida, certifique-se de que esse serviço de nuvem pode acessar bancos de dados no SQL Server nas Máquinas Virtuais do Azure. Um serviço de nuvem pode incluir várias funções Web e de trabalho.

A tabela a seguir fornece uma comparação do desenvolvimento tradicional da Web com os Serviços de Nuvem do Azure e os Aplicativos Web do Azure em relação ao SQL Server nas Máquinas Virtuais do Azure. A tabela inclui os Aplicativos Web do Azure pois é possível usar o SQL Server em uma Máquina Virtual do Azure como fonte de dados para aplicativos Web do Azure por meio de seu endereço IP virtual público ou nome DNS.

|  | Desenvolvimento para a web tradicional em Máquinas Virtuais do Azure | Serviços de nuvem no Azure | Hospedagem na Web com Aplicativos Web do Azure |
| --- | --- | --- | --- |
| **Migração de aplicativos do local** |Aplicativos existentes como são. |Os aplicativos precisam de funções da web e do trabalhador. |Aplicativos existentes como são, mas adequados para aplicativos da web autocontidos e serviços Web que exigem escalabilidade rápida. |
| **Desenvolvimento e Implantação** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gerenciador do IIS, PowerShell. |Visual Studio, SDK do Azure, TFS, PowerShell. Cada serviço de nuvem tem dois ambientes nos quais você pode implantar seu pacote e configuração de serviço: preparo e produção. Você pode implantar um serviço de nuvem para p ambiente de preparo para testá-lo antes de promovê-lo para produção. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Implantação da Web, PowerShell. |
| **Administração e Configuração** |Você é responsável por tarefas administrativas no aplicativo, dados, regras de firewall, rede virtual e sistema operacional. |Você é responsável por tarefas administrativas no aplicativo, dados, regras de firewall e rede virtual. |Você é responsável por tarefas administrativas no aplicativo e somente dados. |
| **HADR (Alta Disponibilidade e Recuperação de Desastre)** |É recomendável que você coloque máquinas virtuais no mesmo conjunto de disponibilidade e no mesmo serviço de nuvem. Manter suas VMs no mesmo conjunto de disponibilidade permite que o Azure coloque os nós de alta disponibilidade em domínios de falha e domínios de atualização separados. Da mesma forma, manter suas VMs no mesmo serviço de nuvem permite o balanceamento de carga e as VMs podem se comunicar diretamente umas com as outras pela rede local dentro de um datacenter do Azure.<br/><br/>Você é responsável por implementar uma solução de alta disponibilidade e de recuperação de desastre para o SQL Server nas Máquinas Virtuais do Azure para evitar qualquer tempo de inatividade. Para conhecer as tecnologias de HADR com suporte, veja [Alta disponibilidade e recuperação de desastre para o SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Você é responsável pelo backup de seus dados e aplicativos.<br/><br/>O Azure pode mover suas máquinas virtuais se o computador host no datacenter falhar devido a problemas de hardware. Além disso, pode haver tempo de inatividade planejado de sua VM quando o computador host for atualizado devido a atualizações de software ou segurança. Portanto, recomendamos que você mantenha pelo menos duas VMs em cada camada de aplicativo para garantir a disponibilidade contínua. O Azure não fornece SLA para uma única máquina virtual. Para saber mais, confira as [Orientação técnica de resiliência do Azure](../../../resiliency/resiliency-technical-guidance.md). |O Azure gerencia as falhas resultantes do hardware subjacente ou do software de sistema operacional. É recomendável que você implemente várias instâncias de uma função de trabalho ou Web para garantir a alta disponibilidade do seu aplicativo. Para obter informações, consulte [Contrato de Nível de Serviço de Rede Virtual, Máquinas Virtuais e Serviços de Nuvem](http://www.microsoft.com/download/details.aspx?id=38427) e [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](../../../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Você é responsável pelo backup de seus dados e aplicativos.<br/><br/>Para bancos de dados que residem em um banco de dados do SQL Server em uma VM do Azure, você é responsável por implementar uma solução de recuperação de desastre e alta disponibilidade para evitar qualquer tempo de inatividade. Para conhecer as tecnologias de HADR com suporte, veja Alta disponibilidade e recuperação de desastre para o SQL Server nas Máquinas Virtuais do Azure.<br/><br/>**Espelhamento de banco de dados do SQL Server**: uso com os Serviços de Nuvem do Azure (funções web/de trabalho). Máquinas virtuais do SQL Server e um projeto de serviço de nuvem podem estar na mesma Rede Virtual do Azure. Se a VM do SQL Server não estiver na mesma Rede Virtual, você precisa criar um alias do SQL Server para encaminhar a comunicação para a instância do SQL Server. Além disso, o nome do alias deve corresponder ao nome do SQL Server. |A alta disponibilidade é herdada de funções do trabalhador do Azure, do armazenamento de blobs do Azure e do banco de dados SQL. Por exemplo, o Armazenamento do Azure mantém três réplicas de todos dados de blob, tabela e fila. A qualquer momento, o Banco de Dados SQL do Azure mantém três réplicas dos dados em execução — uma réplica primária e duas réplicas secundárias. Para saber mais, confira [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) e [Banco de Dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Ao usar o SQL Server na VM do Azure como uma fonte de dados para Aplicativos Web do Azure, tenha em mente de que os Aplicativos Web do Azure não dão suporte à Rede Virtual do Azure. Ou seja, todas as conexões dos Aplicativos Web do Azure com as VMs do SQL Server no Azure devem passar pelos pontos de extremidade públicos das máquinas virtuais. Isso pode causar algumas limitações para cenários de alta disponibilidade e recuperação de desastres. Por exemplo, o aplicativo cliente nos Aplicativos Web do Azure que se conectam à VM do SQL Server com espelhamento de banco de dados não seria capaz de se conectar ao novo servidor principal, pois o espelhamento de banco de dados exige que você configure a Rede Virtual do Azure entre as VMs do host do SQL Server no Azure. Portanto, atualmente não há suporte ao uso do **Espelhamento de banco de dados do SQL Server** com Aplicativos Web do Azure.<br/><br/>**Grupos de Disponibilidade AlwaysOn do SQL Server**: é possível configurar Grupos de Disponibilidade AlwaysOn ao usar os Aplicativos Web do Azure com as VMs do SQL Server no Azure. Mas, você precisa configurar o ouvinte de grupo de disponibilidade do AlwaysOn para rotear a comunicação para a réplica primária por meio de pontos de extremidade públicos com balanceamento de carga. |
| **Conectividade entre locais** |Você pode usar a Rede Virtual do Azure para se conectar ao local. |Você pode usar a Rede Virtual do Azure para se conectar ao local. |Há suporte para a Rede Virtual do Azure. Para saber mais, confira a [Integração da Rede Virtual de Aplicativos Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Escalabilidade** |A ampliação está disponível ao aumentar o tamanho da máquina virtual ou ao adicionar mais discos. Para obter mais informações sobre tamanhos de máquina virtual, consulte [Tamanhos de Máquina Virtual para o Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Para Servidor de Banco de Dados**: a expansão está disponível por meio de técnicas de particionamento do banco de dados e Grupos de disponibilidade AlwaysOn do SQL Server.<br/><br/>Para cargas de trabalho de leitura pesadas, você pode usar [Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como a replicação do SQL Server.<br/><br/>Para cargas de trabalho pesadas de gravação, você pode implementar o particionamento horizontal de dados entre vários servidores físicos para fornecer expansão do aplicativo.<br/><br/>Além disso, você pode implementar uma expansão usando [SQL Server com roteamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx). Com o DDR (roteamento dependente de dados), você precisa implementar o mecanismo de particionamento no aplicativo cliente, normalmente na camada de negócios, para rotear as solicitações de banco de dados para vários nós do SQL Server. A camada de negócios contém mapeamentos de como os dados são particionados e o nó que contém os dados.<br/><br/>Você pode escalar aplicativos que executam máquinas virtuais. Para saber mais, confira [Como dimensionar um aplicativo](../../../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Observação importante**: o recurso de **Dimensionamento Automático** no Azure permite aumentar ou diminuir automaticamente as Máquinas Virtuais usadas pelo seu aplicativo. Esse recurso garante que a experiência do usuário final não seja afetada negativamente durante períodos de pico e que as VMs sejam desativadas quando a demanda diminuir. É recomendável que você não defina a opção de Dimensionamento automático para seu serviço de nuvem se ele incluir VMs do SQL Server. O motivo é que o recurso de Dimensionamento automático permite que o Azure ative uma máquina virtual quando o uso da CPU da VM ultrapassar um limite e desative uma máquina virtual quando o uso da CPU for inferior ao limite. O recurso de Dimensionamento automático é útil para aplicativos sem monitoração de estado, como servidores Web, em que qualquer VM pode gerenciar a carga de trabalho sem quaisquer referências a qualquer estado anterior. No entanto, o recurso Dimensionamento Automático não é útil para aplicativos com monitoração de estado, como o SQL Server, nos quais apenas uma instância permite gravar no banco de dados. |A ampliação está disponível ao usar várias funções da web e do trabalhador. Para obter mais informações sobre tamanhos de máquina virtual para funções web e funções de trabalho, consulte [Configurar tamanhos para Serviços de Nuvem](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao usar os **Serviços de Nuvem**, é possível definir várias funções para distribuir o processamento e também alcançar a escala flexível do seu aplicativo. Cada serviço de nuvem inclui uma ou mais funções Web e/ou funções de trabalho, cada uma com seus próprios arquivos e configuração de aplicativo. Você pode ampliar um serviço de nuvem aumentando o número de instâncias de função (máquinas virtuais) implantadas para uma função e reduzir o serviço de nuvem diminuindo o número de instâncias de função. Para obter mais informações, consulte [Modelos de execução do Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>A expansão está disponível por meio do suporte interno de alta disponibilidade do Azure através do [Contrato de Nível de Serviço de Rede Virtual, Máquinas Virtuais e Serviços de Nuvem](http://www.microsoft.com/download/details.aspx?id=38427) e do Balanceador de carga.<br/><br/>Para um aplicativo de várias camadas, é recomendável que você conecte aplicativos de função Web/de trabalho a VMs do servidor de banco de dados na Rede Virtual do Azure. Além disso, o Azure fornece balanceamento de carga para VMs no mesmo serviço de nuvem, distribuindo as solicitações de usuários entre elas. As máquinas virtuais conectadas dessa maneira podem se comunicar diretamente entre si pela rede local em um datacenter do Azure.<br/><br/>É possível configurar o **Dimensionamento Automático** no portal do Azure, bem como os horários de agendamento. Para obter mais informações, consulte [Como configurar o dimensionamento automático para um Serviço de Nuvem no portal](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Escalar e reduzir verticalmente**: você pode aumentar/diminuir o tamanho da instância (VM) reservada para seu site da Web.<br/><br/>Escalar horizontalmente: você pode adicionar mais instâncias reservadas (VMs) para seu site da Web.<br/><br/>É possível configurar a **Dimensionamento Automático** no Portal do Azure, bem como os horários de agendamento. Para saber mais, confira [Como dimensionar aplicativos Web](../../../app-service/web-sites-scale.md). |

Para obter mais informações sobre como escolher entre esses métodos de programação, veja [Aplicativos Web, Serviços de Nuvem e VMs do Azure: quando usar qual?](../../../app-service/choose-web-site-cloud-service-vm.md)

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como executar o SQL Server nas Máquinas Virtuais do Azure, confira [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

