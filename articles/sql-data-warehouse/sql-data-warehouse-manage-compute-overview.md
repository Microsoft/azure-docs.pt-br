---
title: "Gerenciar potência de computação no SQL Data Warehouse do Azure (Visão geral) | Microsoft Docs"
description: "Funcionalidades de escala horizontal de desempenho no SQL Data Warehouse do Azure. Escale horizontalmente por meio de ajuste de DWUs ou, para economizar custos, pause e retome os recursos de computação."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2017
ms.author: elbutter
ms.openlocfilehash: abe22f542a79714f6e894870872ee6b76ffe7633
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gerenciar poder de computação no SQL Data Warehouse do Azure (Visão Geral)
> [!div class="op_single_selector"]
> * [Visão geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

A arquitetura do SQL Data Warehouse separa armazenamento e computação, permitindo que cada um seja dimensionado independentemente. Como resultado, a computação pode ser dimensionada para atender às demandas de desempenho independentemente da quantidade de dados. Uma consequência natural dessa arquitetura é que a [cobrança][billed] pela computação e pelo armazenamento é separada. 

Esta visão geral descreve como o escalonamento horizontal funciona com o SQL Data Warehouse e como utilizar os recursos de pausar, retomar e dimensionar do SQL Data Warehouse. Consulte a página [DWUs (unidades do Data Warehouse)][data warehouse units (DWUs)] para saber como as DWUs e o desempenho estão relacionados. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Como as operações de gerenciamento de computação funcionam no SQL Data Warehouse
A arquitetura para o SQL Data Warehouse consiste em um nó de controle, nós de computação e a camada de armazenamento espalhados por 60 distribuições. 

Durante uma sessão ativa normal no SQL Data Warehouse, o nó principal do sistema que gerencia os metadados e contém o otimizador de consulta distribuída. Sob esse nó principal estão os nós de computação e a camada de armazenamento. Para um DWU 400, seu sistema tem um nó principal, quatro nós de computação e a camada de armazenamento, consistindo de 60 distribuições. 

Quando você passar por uma escala ou operação de pausa, o sistema primeiro interrompe todas as consultas de entrada e, em seguida, reverte as transações para assegurar um estado consistente. Para operações de escala, o dimensionamento só ocorrerá após a conclusão dessa reversão transacional. Para uma operação de escalonamento vertical, o sistema provisiona o número desejado de nós de computação adicionais desejado e começa a reconectar os nós de computação à camada de armazenamento. Para uma operação de redução de escala, os nós desnecessários são liberados e os demais nós de computação reconectam-se ao número adequado de distribuições. Para uma operação de pausa, todos os nós de computação são liberados e o sistema passará por uma variedade de operações de metadados para deixar o sistema final em um estado estável.

| DWU  | \# de nós de computação | \# de distribuições por nó |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1.200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

As três funções principais para o gerenciamento de computação são:

1. Pausar
2. Continuar
3. Escala

Cada uma dessas operações pode levar vários minutos para ser concluída. Se você estiver dimensionando/pausando/retomando automaticamente, talvez você queira implementar a lógica para assegurar que determinadas operações tenham sido concluídas antes de prosseguir com outra ação. 

Verificar o estado do banco de dados por meio de vários pontos de extremidade permitirá que você implemente corretamente a automação dessas operações. O portal fornecerá uma notificação após a conclusão de uma operação e o estado atual do bancos de dados, mas não permite a verificação de estado programática. 

>  [!NOTE]
>
>  A funcionalidade de gerenciamento de computação não existe em todos os pontos de extremidade.
>
>  

|              | Pausar/Retomar | Escala | Verificar estado do banco de dados |
| ------------ | ------------ | ----- | -------------------- |
| Portal do Azure | Sim          | Sim   | **Não**               |
| PowerShell   | Sim          | Sim   | Sim                  |
| API REST     | Sim          | Sim   | Sim                  |
| T-SQL        | **Não**       | Sim   | Sim                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Computação de escala

O desempenho no SQL Data Warehouse é medido em [DWUs (unidades do data warehouse)][data warehouse units (DWUs)], que é uma medida abstrata de recursos de computação como CPU, memória e E/S de largura de banda. Um usuário que deseja dimensionar o desempenho do seu sistema pode fazer isso de várias maneiras, por exemplo, por meio do portal, T-SQL e APIs REST. 

### <a name="how-do-i-scale-compute"></a>Como eu dimensiono a computação?
O poder de computação é gerenciado pelo seu SQL Data Warehouse alterando a configuração de DWU. O desempenho aumenta [linearmente][linearly] conforme você adiciona mais DWUs para determinadas operações.  Temos ofertas de DWU que asseguram que o desempenho será alterado visivelmente quando você escalar ou reduzir verticalmente seu sistema. 

Para ajustar DWUs, você pode usar qualquer um destes métodos individuais.

* [Dimensionar a potência de computação com o Portal do Azure][Scale compute power with Azure portal]
* [Dimensionar a potência de computação com o PowerShell][Scale compute power with PowerShell]
* [Dimensionar a potência de computação com APIs REST][Scale compute power with REST APIs]
* [Dimensionar a potência de computação com o TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>Quantas DWUs devo usar?

Para entender qual é o valor ideal de DWU é, tente escalar verticalmente, para cima e para baixo, e executar algumas consultas após carregar os dados. Como o dimensionamento é rápido, você pode experimentar vários níveis de desempenho diferentes durante uma hora ou menos. 

> [!Note] 
> O SQL Data Warehouse é projetado para processar grandes quantidades de dados. Para ver suas verdadeiras capacidades de dimensionamento, especialmente com DWUs maiores, é melhor você usar um grande conjunto de dados que se aproxime de 1 TB ou ultrapasse esse tamanho.

Recomendações para encontrar a melhor DWU para sua carga de trabalho:

1. Para um data warehouse em desenvolvimento, comece selecionando um nível de desempenho de DWU menor.  Um bom ponto de partida é DW400 ou DW200.
2. Monitore o desempenho do seu aplicativo, observando o número de DWUs selecionadas comparado ao desempenho que você observar.
3. Determine quão rápido ou lento o desempenho deve ser para você obter o nível de desempenho ideal para seus requisitos, presumindo uma escala linear.
4. Aumente ou diminua o número de DWUs proporcionalmente à velocidade desejada do desempenho de sua carga de trabalho. 
5. Continue fazendo ajustes até alcançar um nível de desempenho ideal para seus requisitos de negócios.

> [!NOTE]
>
> O desempenho de consulta só aumentará com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se você achar que o dimensionamento não está alterando seu desempenho, verifique nossos artigos sobre ajuste de desempenho para verificar se os dados são distribuídos sem uniformidade ou se você está implantando uma grande quantidade de movimentação de dados. 

### <a name="when-should-i-scale-dwus"></a>Quando devo dimensionar as DWUs?
O dimensionamento de DWUs altera os seguintes cenários importantes:

1. Alteração linear do desempenho do sistema para verificações, agregações e instruções de CTAS
2. Aumento do número de leitores e gravadores ao carregar com o PolyBase
3. Número máximo de consultas simultâneas e slots de simultaneidade

Recomendações para quando dimensionar DWUs:

1. Antes de executar uma operação de transformação ou carregamento de dados pesados, escale verticalmente as DWUs para que os dados fiquem disponíveis mais rapidamente.
2. Durante o horário comercial de pico, dimensione para acomodar um número maior de consultas simultâneas. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausar computação
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use qualquer um destes métodos individuais.

* [Pausar a computação com o Portal do Azure][Pause compute with Azure portal]
* [Pausar a computação com o PowerShell][Pause compute with PowerShell]
* [Pausar a computação com APIs REST][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar a computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar um banco de dados, use qualquer um destes métodos individuais.

* [Retomar a computação com o Portal do Azure][Resume compute with Azure portal]
* [Retomar a computação com o PowerShell][Resume compute with PowerShell]
* [Retomar a computação com APIs REST][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Verificar estado do banco de dados 

Para retomar um banco de dados, use qualquer um destes métodos individuais.

- [Verificar estado do banco de dados com o T-SQL][Check database state with T-SQL]
- [Verificar estado do banco de dados com o PowerShell][Check database state with PowerShell]
- [Verificar estado do banco de dados com o APIs REST][Check database state with REST APIs]

## <a name="permissions"></a>Permissões

Dimensionar o banco de dados exige as permissões descritas em [ALTERAR BANCO DE DADOS][ALTER DATABASE].  Pausar e Retomar exigirá a permissão [Colaborador do BD SQL][SQL DB Contributor], especificamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para ajudar a entender alguns dos principais conceitos de desempenho adicionais:

* [Gerenciamento da carga de trabalho e simultaneidade][Workload and concurrency management]
* [Visão geral do design da tabela][Table design overview]
* [Distribuição de tabelas][Table distribution]
* [Indexação de tabelas][Table indexing]
* [Particionamento de tabelas][Table partitioning]
* [Estatísticas de tabelas][Table statistics]
* [Práticas recomendadas][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[billed]: https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/
[linearly]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
