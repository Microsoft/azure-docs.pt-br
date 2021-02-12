---
title: Visão geral do ajuste automático
description: O banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure analisam a consulta SQL e se adaptam automaticamente à carga de trabalho do usuário.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/30/2020
ms.openlocfilehash: 4204254754307f8310d5ccfda19400de57381075
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500862"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Ajuste automático no banco de dados SQL do Azure e no SQL Instância Gerenciada do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada o ajuste automático fornece desempenho de pico e cargas de trabalho estáveis por meio do ajuste de desempenho contínuo com base no ia e aprendizado de máquina.

O ajuste automático é um serviço de desempenho inteligente totalmente gerenciado que usa inteligência interna para monitorar continuamente consultas executadas em um banco de dados e aprimora automaticamente o desempenho. Isso é obtido adaptando dinamicamente o banco de dados à mudança das cargas de trabalho e aplicando recomendações de ajuste. O ajuste automático aprende horizontalmente com todos os bancos de dados do Azure por meio de inteligência artificial e aprimora de modo dinâmico suas ações de ajustes. Quanto mais tempo um banco de dados for executado com o ajuste automático, melhor será o desempenho.

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada o ajuste automático podem ser um dos recursos mais importantes que você pode habilitar para fornecer cargas de trabalho de banco de dados de desempenho estável e de pico.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que o ajuste automático pode fazer por você

- Ajuste de desempenho automatizado de bancos de dados
- Verificação automatizada de ganhos de desempenho
- Autocorreção e reversão automática
- Histórico de ajuste
- Scripts de ação de ajuste do Transact-SQL (T-SQL) para implantações manuais
- Monitoramento do desempenho de carga de trabalho proativa
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, confiável e comprovado

As operações de ajuste aplicadas aos bancos de dados no banco de dados SQL do Azure são totalmente seguras para o desempenho de suas cargas de trabalho mais intensa. O sistema foi projetado com cuidado para não interferir nas cargas de trabalho do usuário. Recomendações de ajuste automatizadas são aplicadas somente nos horários de pouca utilização. O sistema também pode desabilitar temporariamente as operações de ajuste automático para proteger o desempenho da carga de trabalho. Nesse caso, a mensagem "desabilitada pelo sistema" será mostrada na portal do Azure. Ajuste automático considera cargas de trabalho com a prioridade mais alta de recurso.

Mecanismos de ajuste automático são desenvolvidos e foram aperfeiçoados em milhões de bancos de dados em execução no Azure. As operações de ajuste automatizado aplicadas são verificadas automaticamente para garantir que exista uma melhoria no desempenho da carga de trabalho. Recomendações de desempenho retornadas são detectadas dinamicamente e revertidas no mesmo momento. Por meio do histórico de ajuste registrado, existe um rastreamento claro de aprimoramentos de ajuste feitos em cada banco de dados no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada.

![Como funciona o trabalho de ajuste automático](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

O ajuste automático do banco de dados SQL do Azure está compartilhando sua lógica principal com o SQL Server recurso de ajuste automático no mecanismo de banco de dados. Para obter informações técnicas adicionais sobre o mecanismo interno de inteligência, consulte [Ajuste automático do SQL Server](/sql/relational-databases/automatic-tuning/automatic-tuning).

Para uma visão geral de como o ajuste automático funciona em cenários de uso típicos, assista ao vídeo inserido:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Habilitar o ajuste automático

- [Habilite o ajuste automático para o banco de dados SQL do Azure no portal do Azure](automatic-tuning-enable.md) ou usando a instrução T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) .
- Habilite o ajuste automático para o Azure SQL Instância Gerenciada usando a instrução T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) .

## <a name="automatic-tuning-options"></a>Opções de ajuste automático

As opções de ajuste automático disponíveis no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada são:

| Opção de ajuste automático | Suporte a banco de dados individual e banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Criar índice** – identifica índices que podem melhorar o desempenho de sua carga de trabalho, cria índices e verifica automaticamente se o desempenho das consultas foi melhorado. | Sim | Não |
| **Drop index** -identifica índices redundantes e duplicados diariamente, exceto índices exclusivos, e índices que não foram usados por um longo tempo (>de 90 dias). Observe que essa opção não é compatível com aplicativos que usam alternância de partição e dicas de índice. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico. | Sim | Não |
| **Forçar último plano bom** (correção de plano automática) – identifica consultas SQL do Azure usando um plano de execução que é mais lento do que o bom plano anterior e consultas usando o último plano bom conhecido em vez do plano regressivo. | Sim | Sim |

### <a name="automatic-tuning-for-sql-database"></a>Ajuste automático para o banco de dados SQL

O ajuste automático para o banco de dados SQL do Azure usa as recomendações **criar índice**, **drop index** e **forçar último plano** do banco de dados para otimizar o desempenho do banco de dados. Para obter mais informações, consulte [recomendações do Database Advisor no portal do Azure](database-advisor-find-recommendations-portal.md), no [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)e na [API REST](/rest/api/sql/serverautomatictuning).

Você pode aplicar manualmente as recomendações de ajuste usando o portal do Azure ou pode permitir que o ajuste automático aplique recomendações de ajuste de forma autônoma para você. Os benefícios de permitir que o sistema aplique recomendações de ajuste autonomamente para você é que ele valida automaticamente que existe um ganho positivo no desempenho da carga de trabalho e, se não houver nenhuma melhoria de desempenho significativa detectada, ele reverterá automaticamente a recomendação de ajuste. Observe que, no caso de consultas afetadas por recomendações de ajuste que não são executadas com frequência, a fase de validação pode levar até 72 horas por design.

Caso você esteja aplicando recomendações de ajuste por meio do T-SQL, os mecanismos validação de desempenho automático e reversão não estão disponíveis. As recomendações aplicadas de forma que permanecerão ativas e mostradas na lista de recomendações de ajuste para 24-48 horas. antes que o sistema as retire automaticamente. Se você quiser remover uma recomendação mais cedo, poderá descartá-la de portal do Azure.

As opções de ajuste automático podem ser habilitadas ou desabilitadas independentemente por banco de dados, ou podem ser configuradas no nível do servidor e aplicadas em cada banco de dados que herda as configurações do servidor. Os servidores podem herdar os padrões do Azure para configurações de ajuste automático. Atualmente, os padrões do Azure estão definidos como FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

> [!IMPORTANT]
> A partir de março, 2020 alterações nos padrões do Azure para o ajuste automático entrarão em vigor da seguinte maneira:
>
> - Os novos padrões do Azure serão FORCE_LAST_GOOD_PLAN = habilitado, CREATE_INDEX = desabilitado e DROP_INDEX = desabilitado.
> - Os servidores existentes sem preferências de ajuste automático configuradas serão automaticamente configurados para HERDAr os novos padrões do Azure. Isso se aplica a todos os clientes que atualmente têm configurações de servidor para ajuste automático em um estado indefinido.
> - Novos servidores criados serão automaticamente configurados para HERDAr os novos padrões do Azure (diferentemente do anterior quando a configuração de ajuste automático estava em um estado indefinido na criação do novo servidor).

Configurar opções de ajuste automático em um servidor e herdar configurações para bancos de dados pertencentes ao servidor pai é um método recomendado para configurar o ajuste automático, pois simplifica o gerenciamento de opções de ajuste automático para um grande número de bancos de dados.

Para saber mais sobre como criar notificações por email para recomendações de ajuste automático, consulte [notificações por email para ajuste automático](automatic-tuning-email-notifications-configure.md).

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Ajuste automático para o Azure SQL Instância Gerenciada

O ajuste automático para o SQL Instância Gerenciada dá suporte apenas ao **último plano de bom esforço**. Para obter mais informações sobre como configurar opções de ajuste automático por meio do T-SQL, consulte o [ajuste automático introduz a correção automática do plano](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e a [correção automática do plano](/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a inteligência interna usada no ajuste automático, confira [inteligência artificial ajusta o banco de dados SQL do Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Para saber como o ajuste automático funciona nos bastidores, consulte [indexação automática de milhões de bancos de dados em banco de dados SQL do Microsoft Azure](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).