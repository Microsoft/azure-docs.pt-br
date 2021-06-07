---
title: Aplicar recomendações do desempenho
description: Use o portal do Azure para encontrar recomendações de desempenho que podem otimizar o desempenho do seu banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 748ac448ad8bf5c06e5be8b7a4a8b00a9b7af84b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500879"
---
# <a name="find-and-apply-performance-recommendations"></a>Localizar e aplicar recomendações de desempenho
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Você pode usar o portal do Azure para encontrar recomendações de desempenho que podem otimizar o desempenho do banco de dados no banco de dados SQL do Azure ou corrigir algum problema identificado na carga de trabalho. A página **recomendação de desempenho** no portal do Azure permite que você encontre as principais recomendações com base em seu impacto potencial.

## <a name="viewing-recommendations"></a>Exibindo recomendações

Para exibir e aplicar recomendações de desempenho, você precisa das permissões corretas do Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) do Azure no Azure. As permissões de **Leitor**, **Contribuidor do DB SQL** são necessárias para exibir as recomendações, enquanto as permissões de **Proprietário**, **Contribuidor do DB SQL** são necessárias para executar todas as ações: criar ou remover índices e cancelar a criação de índices.

Use as etapas a seguir para encontrar recomendações de desempenho no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Vá para **todos os serviços**  >  **bancos** de dados SQL e selecione seu banco de dados.
3. Navegue até **Recomendação de desempenho** para exibir as recomendações disponíveis para o banco de dados selecionado.

As recomendações de desempenho são exibidas na tabela semelhante à exibida na figura a seguir:

![Captura de tela mostra as recomendações de desempenho em uma tabela com a descrição de ação e recomendação.](./media/database-advisor-find-recommendations-portal/recommendations.png)

As recomendações são classificadas de acordo com seu impacto em potencial no desempenho nas seguintes categorias:

| Impacto | Descrição |
|:--- |:--- |
| Alta |Recomendações de alto impacto devem fornecer o impacto mais significativo no desempenho. |
| Médio |Recomendações de médio impacto devem melhorar o desempenho, mas não substancialmente. |
| Baixo |Recomendações de baixo impacto devem fornecer um desempenho melhor do que seria obtido sem elas, mas as melhorias podem não ser significativas. |

> [!NOTE]
> O Banco de Dados SQL do Azure precisa monitorar as atividades de pelo menos um dia para identificar algumas recomendações. O Banco de Dados SQL do Azure pode otimizar com mais facilidade os padrões de consulta consistentes do que intermitências aleatórias e irregulares de atividade. Se não houver recomendações disponíveis no momento, a página **Recomendação de desempenho** apresentará uma mensagem explicando o motivo.

Você também pode exibir o status das operações do histórico. Selecione uma recomendação ou status para ver mais informações.

Aqui está um exemplo da recomendação "criar índice" no portal do Azure.

![Criar índice](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicando recomendações

O Banco de Dados SQL do Azure concede a você controle total sobre como as recomendações são habilitadas usando qualquer uma das três opções a seguir:

* Aplicar uma recomendação individual de cada vez.
* Habilitar o Ajuste automático para aplicar automaticamente as recomendações.
* Para implementar uma recomendação manualmente, execute o script T-SQL recomendado no banco de dados.

Selecione qualquer recomendação para exibir seus detalhes e clique em **Exibir script** para examinar os detalhes exatos de como a recomendação é criada.

O banco de dados permanece online enquanto a recomendação é aplicada – o uso da recomendação de desempenho ou ajuste automático nunca desativa um banco de dados.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual

Você pode examinar e aceitar uma recomendação de cada vez.

1. Na página **Recomendações**, selecione uma recomendação.
2. Na página **detalhes** , clique no botão **aplicar** .

   ![Aplicar recomendação](./media/database-advisor-find-recommendations-portal/apply.png)

As recomendações selecionadas são aplicadas no banco de dados.

### <a name="removing-recommendations-from-the-list"></a>Removendo recomendações da lista

Se sua lista de recomendações contiver itens que você deseja remover da lista, você poderá descartar a recomendação:

1. Selecione uma recomendação na lista de **Recomendações** para abrir os detalhes.
2. Clique em **Descartar** na página **Detalhes**.

Se desejar, você poderá adicionar itens descartados de volta para a lista de **Recomendações** :

1. Na página **Recomendações**, clique em **Exibir descartados**.
2. Selecione um item descartado na lista para exibir seus detalhes.
3. Outra alternativa é clicar em **Desfazer Descarte** para adicionar o índice de volta à lista principal de **Recomendações**.

> [!NOTE]
> Observe que se o [ajuste automático](automatic-tuning-overview.md) do Banco de Dados SQL estiver habilitado, e se você descartou manualmente uma recomendação na lista, essa recomendação nunca será aplicada automaticamente. Descartar uma recomendação é uma maneira útil para que os usuários tenham o ajuste automático habilitado em casos ao exigir que uma recomendação específica não deve ser aplicada.
> Você pode reverter esse comportamento adicionando recomendações descartadas à lista de recomendações selecionando a opção de Desfazer Descartar.

### <a name="enable-automatic-tuning"></a>Habilitar o ajuste automático

Você pode definir seu banco de dados para implementar recomendações automaticamente. Conforme as recomendações são disponibilizadas, elas são aplicadas automaticamente. Assim como em todas as recomendações gerenciadas pelo serviço, se o impacto ao desempenho for negativo, a recomendação será revertida.

1. Na página **Recomendações**, clique em **Automatizar**:

   ![Configurações do supervisor](./media/database-advisor-find-recommendations-portal/settings.png)
2. Selecione as ações para automatização:

   ![Captura de tela que mostra onde selecionar as ações a serem automatizadas.](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> Observe que a opção **DROP_INDEX** atualmente não é compatível com aplicativos usando as dicas de índice e de alternância de partição.

Depois de selecionar a configuração desejada, clique em Aplicar.

### <a name="manually-apply-recommendations-through-t-sql"></a>Aplicar recomendações manualmente por meio do T-SQL

Selecione qualquer recomendação e clique em **Exibir script**. Execute este script em seu banco de dados para aplicar manualmente a recomendação.

*Os índices que são executados manualmente não são monitorados e validados quanto ao impacto no desempenho pelo serviço* , portanto, é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustá-los ou excluí-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [CRIAR ÍNDICE (Transact-SQL)](/sql/t-sql/statements/create-index-transact-sql). Além disso, as recomendações aplicadas manualmente permanecerão ativas e exibidas na lista de recomendações de 24-48 horas. antes que o sistema as retire automaticamente. Se você quiser remover uma recomendação mais cedo, poderá descartá-la manualmente.

### <a name="canceling-recommendations"></a>Cancelando recomendações

Recomendações que estão com status **Pendente**, **Validando** ou **Êxito** podem ser canceladas. Recomendações com status **Executando** não podem ser canceladas.

1. Selecione uma recomendação na área **Histórico de Ajustes** para abrir a página **detalhes da recomendação**.
2. Clique em **Cancelar** para anular o processo de aplicação da recomendação.

## <a name="monitoring-operations"></a>Monitoramento de operações

A aplicação de uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o status da recomendação. Um índice pode estar em um dos estados a seguir:

| Status | Descrição |
|:--- |:--- |
| Pendente |O comando Aplicar recomendação foi recebido e está programado para execução. |
| Executando |A recomendação está sendo aplicada. |
| Validando |A recomendação foi aplicada com êxito e o serviço está medindo os benefícios. |
| Êxito |A recomendação foi aplicada com êxito e benefícios foram calculados. |
| Erro |Ocorreu um erro durante o processo de aplicação da recomendação. Este pode ser um problema temporário ou, possivelmente, uma alteração de esquema na tabela, tornando o script inválido. |
| Revertendo |A recomendação foi aplicada, mas foi considerada não funcional e está sendo revertida automaticamente. |
| Revertida |A recomendação foi revertida. |

Clique em uma recomendação em processo da lista para ver mais informações:

![Captura de tela que mostra a lista de recomendações em processo.](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Revertendo uma recomendação

Se você tiver usado as recomendações de desempenho para aplicar a recomendação (ou seja, não executou o script T-SQL manualmente), ela será revertida automaticamente se o impacto ao desempenho for negativo. Se, por algum motivo, você simplesmente desejar reverter uma recomendação, poderá fazer o seguinte:

1. Selecione uma recomendação aplicada com êxito na área **Histórico de ajustes** .
2. Clique em **Reverter** na página **detalhes da recomendação**.

![Índices recomendados](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorando o impacto do desempenho de recomendações de índice

Depois que as recomendações forem implementadas com êxito (atualmente, apenas recomendações de operações de índice e de parametrização de consultas), clique em **Análise de Consultas** na página detalhes da recomendação para abrir [Análise de Desempenho de Consultas](query-performance-insight-use.md) e ver o impacto no desempenho das principais consultas.

![Monitorar o impacto do desempenho](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Resumo

O banco de dados SQL do Azure fornece recomendações para melhorar o desempenho do banco de dados. Ao fornecer scripts T-SQL, você obtém assistência na otimização do seu banco de dados e, por fim, na melhora do desempenho da consulta.

## <a name="next-steps"></a>Próximas etapas

Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Banco de Dados SQL do Azure continua a monitorar e a fornecer recomendações que podem melhorar o desempenho do seu banco de dados.

* Consulte [Ajuste automático](automatic-tuning-overview.md) para saber mais sobre o ajuste automático no Banco de Dados SQL do Azure.
* Consulte [Recomendações de desempenho](database-advisor-implement-performance-recommendations.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
* Consulte [Análise de desempenho de consultas](query-performance-insight-use.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.

## <a name="additional-resources"></a>Recursos adicionais

* [Repositório de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [CREATE INDEX](/sql/t-sql/statements/create-index-transact-sql)
* [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md)