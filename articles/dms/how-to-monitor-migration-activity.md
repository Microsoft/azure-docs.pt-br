---
title: Monitorar a atividade de migração-serviço de migração de banco de dados do Azure
description: Aprenda a usar o Serviço de Migração de Banco de Dados do Azure para monitorar a atividade de migração.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297463"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorar a atividade de migração usando o serviço de migração de banco de dados do Azure
Neste artigo, você aprenderá a monitorar o progresso de uma migração em nível de banco de dados e em nível de tabela.

## <a name="monitor-at-the-database-level"></a>Monitorar em nível de banco de dados
Para monitorar a atividade em nível de banco de dados, exiba a folha de nível de banco de dados:

![Folha de nível de banco de dados](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> A seleção do hiperlink do banco de dados mostrará a lista de tabelas e o andamento da migração.

A tabela a seguir lista os campos na folha de nível de banco de dados e descreve os vários valores de status associados a cada um.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nome do campo</strong></th>
      <th><strong>Substatus do campo</strong></th>
      <th><strong>Descrição</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Status da atividade</strong></td>
      <td>Executando</td>
      <td>A atividade de migração está em execução.</td>
    </tr>
    <tr>
      <td>Com sucesso</td>
      <td>A atividade de migração foi bem-sucedida sem problemas.</td>
    </tr>
    <tr>
      <td>Com falha</td>
      <td>Falha na migração. Selecione o link 'Ver detalhes do erro' nos detalhes da migração para obter a mensagem de erro completa.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Inicializando</td>
      <td>O DMS está configurando o pipeline de migração.</td>
    </tr>
    <tr>
      <td>Executando</td>
      <td>O pipeline do DMS está em execução e executando a migração.</td>
    </tr>
    <tr>
      <td>Concluir</td>
      <td>Migração concluída.</td>
    </tr>
    <tr>
      <td>Com falha</td>
      <td>Falha na migração. Clique nos detalhes da migração para ver os erros de migração.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Detalhes da migração</strong></td>
      <td>Iniciando o pipeline de migração</td>
      <td>O DMS está configurando o pipeline de migração.</td>
    </tr>
    <tr>
      <td>Carregamento de dados completo em andamento</td>
      <td>O DMS está executando o carregamento inicial.</td>
    </tr>
    <tr>
      <td>Pronto para substituição</td>
      <td>Após a conclusão do carregamento inicial, o DMS marcará o banco de dados como pronto para substituição. O usuário deve verificar se os dados foram capturados na sincronização contínua.</td>
    </tr>
    <tr>
      <td>Todas as alterações aplicadas</td>
      <td>O carregamento inicial e a sincronização contínua foram concluídos. Este status também ocorre depois que o banco de dados é substituído com êxito.</td>
    </tr>
    <tr>
      <td>Confira os detalhes do erro</td>
      <td>Clique no link para mostrar detalhes do erro.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duration</strong></td>
      <td>N/D</td>
      <td>Tempo total desde a inicialização da atividade de migração até a conclusão da migração ou a falha de migração.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitorar em nível de tabela – Resumo rápido
Para monitorar a atividade em nível de tabela, exiba a folha de nível de tabela. A parte superior da folha mostra o número detalhado de linhas migradas no carregamento completo e nas atualizações incrementais. 

A parte inferior da folha lista as tabelas e mostra um resumo rápido do andamento da migração.

![Folha de nível de tabela – Resumo rápido](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

A tabela a seguir descreve os campos mostrados nos detalhes de nível de tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Carregamento completo concluído**      | Número de tabelas com o carregamento de dados completo concluído. |
| **Carregamento completo na fila**      | Número de tabelas que estão sendo enfileiradas para o carregamento completo.      |
| **Carregamento completo carregando** | Número de tabelas com falha.      |
| **Atualizações incrementais**      | Número atualizações na CDA (captura de dados de alterações) nas linhas aplicadas ao destino. |
| **Inserções incrementais**      | Número de inserções da CDA nas linhas aplicadas ao destino.      |
| **Exclusões incrementais** | Número de exclusões da CDA nas linhas aplicadas ao destino.      |
| **Alterações pendentes**      | Número de CDA nas linhas que ainda estão esperando para ser aplicadas ao destino. |
| **Alterações aplicadas**      | Total de atualizações, inserções e exclusões da CDA nas linhas aplicadas ao destino.      |
| **Tabelas no estado de erro** | Número de tabelas que estão no estado 'erro' durante a migração. Alguns exemplos em que as tabelas podem entrar no estado de erro são quando há duplicatas identificadas no destino ou dados que não são compatíveis sendo carregados na tabela de destino.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitorar em nível de tabela – resumo detalhado
Há duas guias que mostram o progresso da migração no carregamento completo e na sincronização de dados incrementais.
    
![Guia de carregamento completo](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Guia de sincronização de dados incrementais](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

A tabela a seguir descreve os campos mostrados no andamento da migração em nível de tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Status – sincronizando**      | A sincronização contínua está em execução. |
| **Inserção**      | Número de inserções da CDA nas linhas aplicadas ao destino.      |
| **Atualizar** | O número de atualizações da CDA nas linhas aplicadas ao destino.      |
| **Excluir**      | Número de exclusões da CDA nas linhas aplicadas ao destino. |
| **Total Aplicado**      | Total de atualizações, inserções e exclusões da CDA nas linhas aplicadas ao destino. |
| **Erros de dados** | O número de erros de dados ocorridos nesta tabela. Alguns exemplos dos erros são *511: não é possível criar uma linha do tamanho %d que é maior que o tamanho de linha máximo permitido de %d, 8114: erro ao converter o tipo de dados %ls em %ls.*  O cliente deve consultar os detalhes do erro na tabela dms_apply_exceptions no destino do Azure.    |

> [!NOTE]
> Os valores da CDA de Inserção, Atualização, Exclusão e Total Aplicado poderão diminuir quando o banco de dados for substituído ou a migração for reiniciada.

## <a name="next-steps"></a>Próximas etapas
- Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).