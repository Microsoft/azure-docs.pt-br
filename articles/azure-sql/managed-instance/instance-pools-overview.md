---
title: O que é um pool de Instância Gerenciada do Azure SQL?
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre os pools do Azure SQL Instância Gerenciada (versão prévia), um recurso que fornece uma maneira conveniente e econômica de migrar bancos de dados menores SQL Server para a nuvem em escala e gerenciar várias instâncias gerenciadas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347057"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>O que é um pool de Instância Gerenciada do Azure SQL (versão prévia)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Os pools de instância no Azure SQL Instância Gerenciada fornecem uma maneira conveniente e econômica de migrar instâncias de SQL Server menores para a nuvem em escala.

Os pools de instância permitem pré-provisionar os recursos de computação de acordo com os requisitos de migração totais. Em seguida, você pode implantar várias instâncias gerenciadas individuais até seu nível de computação pré-provisionado. Por exemplo, se você pré-provisionar 8 vCores, poderá implantar a instância 2 2-vCore e 1 4-vCore e, em seguida, migrar bancos de dados para essas instâncias. Antes dos pools de instância estarem disponíveis, as cargas de trabalho menores e menos intensivas de computação geralmente teriam de ser consolidadas em uma instância gerenciada maior ao migrar para a nuvem. A necessidade de migrar grupos de bancos de dados para uma instância grande normalmente exigiu um planejamento de capacidade cuidadoso e governança de recursos, considerações de segurança adicionais e alguma consolidação de dados extra funciona no nível da instância.

Além disso, os pools de instância dão suporte à integração VNet nativa para que você possa implantar vários pools de instância e várias instâncias únicas na mesma sub-rede.

## <a name="key-capabilities"></a>Principais recursos

Os pools de instância oferecem os seguintes benefícios:

1. Capacidade de hospedar duas instâncias vCore. *\* Somente para instâncias nos pools de instância*.
2. Tempo de implantação previsível e de instância rápida (até 5 minutos).
3. Alocação mínima de endereço IP.

O diagrama a seguir ilustra um pool de instâncias com várias instâncias gerenciadas implantadas em uma sub-rede de rede virtual.

![pool de instâncias com várias instâncias](./media/instance-pools-overview/instance-pools1.png)

Os pools de instância habilitam a implantação de várias instâncias na mesma máquina virtual, em que o tamanho de computação da máquina virtual se baseia no número total de vCores alocadas para o pool. Essa arquitetura permite o *particionamento* da máquina virtual em várias instâncias, que pode ser qualquer tamanho com suporte, incluindo 2 vCores (as instâncias de 2 vcores só estão disponíveis para instâncias em pools).

Após a implantação inicial, as operações de gerenciamento em instâncias em um pool são muito mais rápidas. Isso ocorre porque a implantação ou a extensão de um [cluster virtual](connectivity-architecture-overview.md#high-level-connectivity-architecture) (conjunto dedicado de máquinas virtuais) não faz parte do provisionamento da instância gerenciada.

Como todas as instâncias em um pool compartilham a mesma máquina virtual, a alocação de IP total não depende do número de instâncias implantadas, o que é conveniente para a implantação em sub-redes com um intervalo de IP estreito.

Cada pool tem uma alocação de IP fixa de apenas nove endereços IP (não incluindo os cinco endereços IP na sub-rede que são reservados para suas próprias necessidades). Para obter detalhes, consulte os [requisitos de tamanho de sub-rede para instâncias únicas](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Cenários de aplicativos

A lista a seguir fornece os principais casos de uso em que os pools de instância devem ser considerados:

- Migração de *um grupo de instâncias de SQL Server* ao mesmo tempo, em que a maioria é um tamanho menor (por exemplo, 2 ou 4 vCores).
- Cenários em que a *criação ou o dimensionamento de instância curta e previsível* é importante. Por exemplo, a implantação de um novo locatário em um ambiente de aplicativo SaaS multilocatário que requer recursos de nível de instância.
- Cenários nos quais um limite de *custo fixo* ou de *gastos* é importante. Por exemplo, a execução de ambientes compartilhados de desenvolvimento/teste ou de demonstração de um tamanho fixo (ou raramente alterado), no qual você implanta periodicamente instâncias gerenciadas quando necessário.
- Cenários em que a *alocação de endereço IP mínima* em uma sub-rede VNet é importante. Todas as instâncias em um pool estão compartilhando uma máquina virtual, portanto, o número de endereços IP alocados é menor do que no caso de instâncias únicas.

## <a name="architecture"></a>Arquitetura

Os pools de instância têm uma arquitetura semelhante a instâncias gerenciadas regulares (*únicas*). Para dar suporte a [implantações em redes virtuais do Azure](../../virtual-network/virtual-network-for-azure-services.md) e para fornecer isolamento e segurança para clientes, os pools de instância também dependem de [clusters virtuais](connectivity-architecture-overview.md#high-level-connectivity-architecture). Os clusters virtuais representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede da rede virtual do cliente.

A principal diferença entre os dois modelos de implantação é que os pools de instância permitem várias implantações de processo de SQL Server no mesmo nó de máquina virtual, que são recursos controlados com o uso de [objetos de trabalho do Windows](/windows/desktop/ProcThread/job-objects), enquanto as instâncias únicas sempre são sozinhas em um nó de máquina virtual.

O diagrama a seguir mostra um pool de instâncias e duas instâncias individuais implantadas na mesma sub-rede e ilustra os principais detalhes de arquitetura para os dois modelos de implantação:

![Pool de instâncias e duas instâncias individuais](./media/instance-pools-overview/instance-pools2.png)

Cada pool de instâncias cria um cluster virtual separado abaixo. As instâncias em um pool e instâncias únicas implantadas na mesma sub-rede não compartilham recursos de computação alocados para SQL Server processos e componentes de gateway, o que garante a previsibilidade do desempenho.

## <a name="resource-limitations"></a>Limitações de recursos

Há várias limitações de recursos em relação a pools de instância e instâncias dentro de pools:

- Os pools de instância estão disponíveis somente no hardware Gen5.
- As instâncias gerenciadas em um pool têm CPU e RAM dedicados, portanto, o número agregado de vCores em todas as instâncias deve ser menor ou igual ao número de vCores alocados para o pool.
- Todos os [limites de nível de instância](resource-limits.md#service-tier-characteristics) se aplicam a instâncias criadas dentro de um pool.
- Além dos limites de nível de instância, há também dois limites impostos *no nível do pool de instâncias*:
  - Tamanho total do armazenamento por pool (8 TB).
  - Número total de bancos de dados de usuário por pool. Esse limite depende do valor de vCores do pool:
    - 8 vCores pool dá suporte a até 200 bancos de dados,
    - 16 pool de vCores dá suporte a até 400 bancos de dados,
    - 24 e vCores pools maiores dão suporte a até 500 bancos de dados.
- Não é possível definir o administrador do AAD para as instâncias implantadas dentro do pool de instâncias, portanto, a autenticação do AAD não pode ser usada.

A alocação de armazenamento total e o número de bancos de dados em todas as instâncias devem ser menores ou iguais aos limites expostos por pools de instância.

- Os pools de instâncias dão suporte a 8, 16, 24, 32, 40, 64 e 80 vCores.
- As instâncias gerenciadas dentro de pools dão suporte a 2, 4, 8, 16, 24, 32, 40, 64 e 80 vCores.
- Instâncias gerenciadas dentro de pools dão suporte a tamanhos de armazenamento entre 32 GB e 8 TB, exceto:
  - 2 as instâncias vCore dão suporte a tamanhos entre 32 GB e 640 GB,
  - 4 as instâncias vCore dão suporte a tamanhos entre 32 GB e 2 TB.
- Instâncias gerenciadas dentro de pools têm limite de até 100 bancos de dados de usuário por instância, exceto duas instâncias vCore que dão suporte a até 50 bancos de dados de usuário por instância.

A [propriedade da camada de serviço](resource-limits.md#service-tier-characteristics) é associada ao recurso de pool de instâncias, portanto, todas as instâncias em um pool devem ser da mesma camada de serviço que a camada de serviço do pool. Neste momento, somente a camada de serviço Uso Geral está disponível (consulte a seção a seguir sobre limitações na visualização atual).

### <a name="public-preview-limitations"></a>Limitações da visualização pública

A visualização pública tem as seguintes limitações:

- Atualmente, apenas a camada de serviço Uso Geral está disponível.
- Os pools de instâncias não podem ser dimensionados durante a visualização pública, portanto, o planejamento de capacidade cuidadoso antes da implantação é importante.
- Portal do Azure suporte para criação e configuração do pool de instâncias ainda não está disponível. Todas as operações em pools de instância têm suporte apenas por meio do PowerShell. A implantação de instância inicial em um pool pré-criado também tem suporte somente por meio do PowerShell. Uma vez implantado em um pool, as instâncias gerenciadas podem ser atualizadas usando o portal do Azure.
- Instâncias gerenciadas criadas fora do pool não podem ser movidas para um pool existente, e as instâncias criadas dentro de um pool não podem ser movidas para fora como uma única instância ou para outro pool.
- O preço da instância de [capacidade reservada](../database/reserved-capacity-overview.md) não está disponível.

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

As instâncias gerenciadas criadas em pools dão suporte aos mesmos [níveis de compatibilidade e recursos com suporte em instâncias gerenciadas únicas](sql-managed-instance-paas-overview.md#sql-features-supported).

Cada instância gerenciada implantada em um pool tem uma instância separada do SQL Agent.

Recursos opcionais ou recursos que exigem a escolha de valores específicos (como agrupamento em nível de instância, fuso horário, ponto de extremidade público para tráfego de dados, grupos de failover) são configurados no nível de instância e podem ser diferentes para cada instância em um pool.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Embora as instâncias gerenciadas nos pools tenham vCore e RAM dedicados, elas compartilham o disco local (para uso de tempdb) e os recursos de rede. Não é provável, mas é possível experimentar o efeito *vizinho ruidosa* se várias instâncias no pool tiverem alto consumo de recursos ao mesmo tempo. Se você observar esse comportamento, considere implantar essas instâncias em um pool maior ou como instâncias únicas.

## <a name="security-considerations"></a>Considerações de segurança

Como as instâncias implantadas em um pool compartilham a mesma máquina virtual, convém considerar a desabilitação de recursos que introduzem riscos mais altos de segurança ou a controlar firmemente as permissões de acesso a esses recursos. Por exemplo, integração CLR, backup e restauração nativos, email de banco de dados, etc.

## <a name="instance-pool-support-requests"></a>Solicitações de suporte do pool de instâncias

Crie e gerencie solicitações de suporte para pools de instância no [portal do Azure](https://portal.azure.com).

Se você estiver tendo problemas relacionados à implantação do pool de instâncias (criação ou exclusão), certifique-se de especificar **pools de instância** no campo **subtipo de problema** .

![Solicitação de suporte a pools de instâncias](./media/instance-pools-overview/support-request.png)

Se você estiver tendo problemas relacionados a uma única instância gerenciada ou banco de dados dentro de um pool, deverá criar um tíquete de suporte regular para o Azure SQL Instância Gerenciada.

Para criar implantações de Instância Gerenciada do SQL maiores (com ou sem pools de instância), talvez seja necessário obter uma cota regional maior. Para obter mais informações, consulte [aumentos de cota de solicitação para o banco de dados SQL do Azure](../database/quota-increase-request.md). A lógica de implantação para pools de instância compara o consumo de vCore total *no nível do pool* em relação à sua cota para determinar se você tem permissão para criar novos recursos sem aumentar ainda mais sua cota.

## <a name="instance-pool-billing"></a>Cobrança do pool de instâncias

Os pools de instância permitem o dimensionamento da computação e do armazenamento independentemente. Os clientes pagam pela computação associada ao recurso de pool medido em vCores e o armazenamento associado a cada instância medida em gigabytes (os primeiros 32 GB são gratuitos para cada instância).

o preço vCore de um pool é cobrado independentemente de quantas instâncias são implantadas nesse pool.

Para o preço de computação (medido em vCores), há duas opções de preço disponíveis:

  1. *Licença incluída*: o preço das licenças de SQL Server está incluído. Isso é para os clientes que optam por não aplicar as licenças de SQL Server existentes com o Software Assurance.
  2. *Benefício híbrido do Azure*: um preço reduzido que inclui Benefício Híbrido do Azure para SQL Server. Os clientes podem optar por esse preço usando suas licenças de SQL Server existentes com o Software Assurance. Para elegibilidade e outros detalhes, consulte [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

A definição de diferentes opções de preço não é possível para instâncias individuais em um pool. Todas as instâncias no pool pai devem estar no preço ou Benefício Híbrido do Azure preço incluído na licença. O modelo de licença para o pool pode ser alterado após a criação do pool.

> [!IMPORTANT]
> Se você especificar um modelo de licença para a instância diferente de no pool, o preço do pool será usado e o valor do nível da instância será ignorado.

Se você criar pools de instância em [assinaturas qualificadas para o benefício de desenvolvimento/teste](https://azure.microsoft.com/pricing/dev-test/), receberá automaticamente taxas com desconto de até 55% no Azure SQL instância gerenciada.

Para obter detalhes completos sobre o preço do pool de instâncias, consulte a seção *pools de instâncias* na [página de preços do SQL instância gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar os pools de instância, consulte [Guia de instruções sobre pools do SQL instância gerenciada](instance-pools-configure.md).
- Para saber como criar sua primeira instância gerenciada, confira o [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e comparação, consulte [recursos comuns do SQL do Azure](../database/features-comparison.md).
- Para obter mais informações sobre a configuração de VNet, confira [Configuração de VNet de Instância Gerenciada de SQL](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, confira [Criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o Serviço de Migração de Banco de Dados do Azure para migração, confira [Migração de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).
- Para obter o monitoramento avançado do desempenho de banco de dados da Instância Gerenciada de SQL com inteligência de solução de problemas interna, confira [Monitorar a Instância Gerenciada de SQL do Azure usando a Análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [preços do SQL instância gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).
