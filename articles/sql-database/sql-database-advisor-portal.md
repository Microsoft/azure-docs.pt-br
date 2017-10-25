---
title: "Aplicar recomendações de desempenho - Banco de Dados SQL do Azure | Microsoft Docs"
description: "Você pode usar o Portal do Azure para encontrar recomendações de desempenho que podem otimizar o desempenho de seu Banco de Dados SQL do Azure ou corrigir algum problema identificado na carga de trabalho."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 018afaa8b08bd001e55693390e80c8e2c4f33a30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-and-apply-performance-recommendations"></a>Localizar e aplicar recomendações de desempenho

Você pode usar o Portal do Azure para encontrar recomendações de desempenho que podem otimizar o desempenho de seu Banco de Dados SQL do Azure ou corrigir algum problema identificado na carga de trabalho. A página **Recomendação de desempenho** no Portal do Azure permite que você localize as principais recomendações com base no possível impacto. 

## <a name="viewing-recommendations"></a>Exibindo recomendações

Para exibir e aplicar as recomendações de desempenho, você precisa das permissões corretas ao [controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) no Azure. As permissões de **Leitor**, **Contribuidor do DB SQL** são necessárias para exibir as recomendações, enquanto as permissões de **Proprietário**, **Contribuidor do DB SQL** são necessárias para executar todas as ações: criar ou remover índices e cancelar a criação de índices.

Use as etapas a seguir para localizar as recomendações de desempenho no Portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Acesse **Mais serviços** > **Bancos de Dados SQL** e selecione seu banco de dados.
3. Navegue até **Recomendação de desempenho** para exibir as recomendações disponíveis para o banco de dados selecionado.

As recomendações de desempenho são exibidas em um tabela semelhante à exibida na figura a seguir:

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

As recomendações são classificadas de acordo com seu impacto em potencial no desempenho nas seguintes categorias:

| Impacto | Descrição |
|:--- |:--- |
| Alto |Recomendações de alto impacto devem fornecer o impacto mais significativo no desempenho. |
| Média |Recomendações de médio impacto devem melhorar o desempenho, mas não substancialmente. |
| Baixo |Recomendações de baixo impacto devem fornecer um desempenho melhor do que seria obtido sem elas, mas as melhorias podem não ser significativas. |


> [!NOTE]
> O Banco de Dados SQL do Azure precisa monitorar as atividades de pelo menos um dia para identificar algumas recomendações. O Banco de Dados SQL do Azure pode otimizar com mais facilidade os padrões de consulta consistentes do que intermitências aleatórias e irregulares de atividade. Se não houver recomendações no momento, a página **Recomendação de desempenho** deverá fornecer uma mensagem explicando o motivo.
> 

Você também pode exibir o status das operações do histórico. Selecione um status ou recomendação para ver seus detalhes.

Confira um exemplo de recomendação "Criar índice" no Portal do Azure.

![Criar índice](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicando recomendações
O Banco de Dados SQL do Azure concede a você controle total sobre como as recomendações são habilitadas usando qualquer uma das três opções a seguir: 

* Aplicar uma recomendação individual de cada vez.
* Habilitar o Ajuste automático para aplicar automaticamente as recomendações.
* Para implementar uma recomendação manualmente, execute o script T-SQL recomendado no banco de dados.

Selecione qualquer recomendação para exibir seus detalhes e clique em **Exibir script** para examinar os detalhes exatos de como a recomendação é criada.

O banco de dados permanece online enquanto a recomendação é aplicada – o uso da recomendação de desempenho ou ajuste automático nunca desativa um banco de dados.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual
Você pode examinar e aceitar uma recomendação de cada vez.

1. Na folha **Recomendações**, selecione uma recomendação.
2. Na folha **Detalhes**, clique no botão **Aplicar**.
   
    ![Aplicar recomendação](./media/sql-database-advisor-portal/apply.png)

A recomendação selecionada será aplicada no banco de dados.

### <a name="removing-recommendations-from-the-list"></a>Removendo recomendações da lista
Se sua lista de recomendações contiver itens que você deseja remover da lista, você poderá descartar a recomendação:

1. Selecione uma recomendação na lista de **Recomendações** para abrir os detalhes.
2. Clique em **Descartar** na folha **Detalhes**.

Se desejar, você poderá adicionar itens descartados de volta para a lista de **Recomendações** :

1. Na folha **Recomendações**, clique em **Exibir descartados**.
2. Selecione um item descartado na lista para exibir seus detalhes.
3. Outra alternativa é clicar em **Desfazer Descarte** para adicionar o índice de volta à lista principal de **Recomendações**.


### <a name="enable-automatic-tuning"></a>Habilitar o ajuste automático
Você pode definir o Banco de Dados SQL do Azure para implementar as recomendações automaticamente. Conforme as recomendações são disponibilizadas, elas serão aplicadas automaticamente. Assim como em todas as recomendações gerenciadas pelo serviço, se o impacto de desempenho for negativo, a recomendação será revertida.

1. Na folha **Recomendações**, clique em **Automatizar**:
   
    ![Configurações do supervisor](./media/sql-database-advisor-portal/settings.png)
2. Selecione as ações para automatização:
   
    ![Índices recomendados](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Executar manualmente o script T-SQL recomendado
Selecione qualquer recomendação e clique em **Exibir script**. Execute este script em seu banco de dados para aplicar manualmente a recomendação.

*Os índices que são executados manualmente não são monitorados e validados quanto ao impacto no desempenho pelo serviço* , portanto, é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustá-los ou excluí-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [CRIAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Cancelando recomendações
Recomendações que estão com status **Pendente**, **Verificando** ou **Sucesso** podem ser canceladas. Recomendações com status **Executando** não podem ser canceladas.

1. Selecione uma recomendação na área **Histórico de Ajustes** para abrir a folha **detalhes da recomendação**.
2. Clique em **Cancelar** para anular o processo de aplicação da recomendação.

## <a name="monitoring-operations"></a>Monitoramento de operações
A aplicação de uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o status da recomendação. Um índice pode estar em um dos estados a seguir:

| Status | Descrição |
|:--- |:--- |
| Pendente |O comando Aplicar recomendação foi recebido e está programado para execução. |
| Executando |A recomendação está sendo aplicada. |
| Verificando |A recomendação foi aplicada com êxito e o serviço está medindo os benefícios. |
| Sucesso |A recomendação foi aplicada com êxito e benefícios foram calculados. |
| Erro |Ocorreu um erro durante o processo de aplicação da recomendação. Este pode ser um problema temporário ou, possivelmente, uma alteração de esquema na tabela, tornando o script inválido. |
| Revertendo |A recomendação foi aplicada, mas foi considerada não funcional e está sendo revertida automaticamente. |
| Revertida |A recomendação foi revertida. |

Clique em uma recomendação no processo da lista para ver mais detalhes:

![Índices recomendados](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Revertendo uma recomendação
Se você usou as recomendações de desempenho para aplicar a recomendação (ou seja, você não executou o script T-SQL manualmente), ele a reverterá automaticamente se o impacto de desempenho for negativo. Se, por algum motivo, você simplesmente desejar reverter uma recomendação, poderá fazer o seguinte:

1. Selecione uma recomendação aplicada com êxito na área **Histórico de ajustes** .
2. Clique em **Reverter** na folha **detalhes da recomendação**.

![Índices recomendados](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorando o impacto do desempenho de recomendações de índice
Depois que as recomendações forem implementadas com êxito (atualmente, apenas recomendações de operações de índice e de parametrização de consultas), clique em **Análise de Consultas** na folha detalhes da recomendação para abrir [Análise de Desempenho de Consultas](sql-database-query-performance.md) e ver o impacto no desempenho das principais consultas.

![Monitorar o impacto do desempenho](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumo
O Banco de Dados SQL do Azure fornece recomendações para aprimorar o desempenho do bancos de dados SQL. Ao fornecer scripts T-SQL, além de individuais e totalmente automáticos, você obtém uma assistência útil ao otimizar seu banco de dados e, como resultado final, melhorar o desempenho da consulta.

## <a name="next-steps"></a>Próximas etapas
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Banco de Dados SQL do Azure continuará a monitorar e fornecer recomendações que podem melhorar o desempenho de seu banco de dados. 

* Consulte [Ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático no Banco de Dados SQL do Azure.
* Consulte [Recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.

## <a name="additional-resources"></a>Recursos adicionais
* [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md)

