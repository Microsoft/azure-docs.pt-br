---
title: Alta disponibilidade – Servidor único do Banco de Dados do Azure para PostgreSQL
description: Este artigo fornece informações sobre alta disponibilidade em Servidor Único do Banco de Dados do Azure para PostgreSQL
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92485436"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Alta disponibilidade em Servidor único do Banco de Dados do Azure para PostgreSQL
O serviço de Servidor único do Banco de Dados do Azure para PostgreSQL fornece um alto nível de disponibilidade garantido com o SLA (contrato de nível de serviço) com suporte financeiro de [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql) de tempo de atividade. O Banco de Dados do Azure para PostgreSQL fornece alta disponibilidade durante eventos planejados, como a operação de computação dimensionada iniciada pelo usuário, e também quando ocorrem eventos não planejados, como falhas de rede de hardware ou software subjacentes. O Banco de Dados do Azure para PostgreSQL pode se recuperar rapidamente das circunstâncias mais críticas, garantindo praticamente nenhum tempo de inatividade do aplicativo ao usar esse serviço.

O BD do Azure para PostgreSQL é adequado para a execução de dados de missão crítica que exigem alto tempo de atividade. Baseado na arquitetura do Azure, o serviço tem recursos inerentes de alta disponibilidade, redundância e resiliência para reduzir o tempo de inatividade do banco de dados de interrupções planejadas e não planejadas, sem a necessidade de configurar outro componente adicional. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Componentes no Servidor único do Banco de Dados do Azure para PostgreSQL

| **Componente** | **Descrição**|
| ------------ | ----------- |
| <b>Servidor de Banco de Dados PostgreSQL | O Banco de Dados do Azure para PostgreSQL fornece segurança, isolamento, proteções de recursos e capacidade de reinicialização rápida para servidores de banco de dados. Esses recursos facilitam operações como o dimensionamento e a operação de recuperação do servidor de banco de dados após ocorrer uma interrupção em segundos. <br/> Normalmente, as modificações nos servidores de dados ocorrem durante uma transação de um banco de dados. Todas as alterações de banco de dados são gravadas de forma síncrona na maneira de WAL (logs write-ahead) no Armazenamento do Microsoft Azure – que é anexado ao servidor de banco de dados. Durante o processo de [ponto de verificação](https://www.postgresql.org/docs/11/sql-checkpoint.html) do banco de dados, as páginas de dados da memória do servidor de banco de dados também são liberadas para o armazenamento. |
| <b>Armazenamento Remoto | Todos os arquivos de dados físicos do PostgreSQL e os arquivos WAL são armazenados no Armazenamento do Microsoft Azure, que é arquitetado para armazenar três cópias de dados em uma região para garantir a redundância, a disponibilidade e a confiabilidade dos dados. A camada de armazenamento também é independente do servidor de banco de dados. Ela pode ser desanexada de um servidor de banco de dados com falha e reanexada em um novo servidor de banco de dados em questão de segundos. Além disso, o Armazenamento do Microsoft Azure monitora continuamente as falhas de armazenamento. Se uma corrupção de bloco for detectada, ela será automaticamente corrigida instanciando uma nova cópia de armazenamento. |
| <b>Gateway | O gateway atua como um proxy de banco de dados roteando todas as conexões de cliente para o servidor de banco de dados. |

## <a name="planned-downtime-mitigation"></a>Mitigação de tempo de inatividade planejada
O Banco de Dados do Azure para PostgreSQL é projetado para fornecer alta disponibilidade durante operações de tempo de inatividade planejadas. 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text="exibição do Dimensionamento Elástico no PostgreSQL do Azure":::

1. Aumentar e reduzir os servidores de banco de dados do PostgreSQL em segundos
2. O gateway que atua como um proxy para rotear o cliente se conecta ao servidor de banco de dados apropriado
3. O aumento do armazenamento pode ser realizado sem nenhum tempo de inatividade. O armazenamento remoto permite desanexar/reanexar rapidamente após o failover.
Estes são alguns cenários de manutenção planejada:

| **Cenário** | **Descrição**|
| ------------ | ----------- |
| <b>Dimensionar/reduzir verticalmente a computação | Quando o usuário executa a operação de dimensionar/reduzir verticalmente, um novo servidor de banco de dados é provisionado usando a configuração de computação dimensionada. No servidor de banco de dados antigo, os pontos de verificação ativos têm permissão para serem concluídos, as conexões de cliente são descarregadas, todas as transações não confirmadas são canceladas e desligadas. Então o armazenamento é desanexado do servidor de banco de dados antigo e anexado ao servidor de banco de dados novo. Quando o aplicativo cliente tenta se reconectar ou tenta fazer uma nova conexão, o gateway direciona a solicitação de conexão para o servidor de banco de dados novo.|
| <b>Dimensionamento do armazenamento | Dimensionar o armazenamento é uma operação online e não interrompe o servidor de banco de dados.|
| <b>Implantação de software novo (Azure) | Novos recursos de distribuição ou correções de bugs ocorrem automaticamente como parte da manutenção planejada do serviço. Para saber mais, consulte a [documentação](./concepts-monitoring.md#planned-maintenance-notification)e também verifique o [portal](https://aka.ms/servicehealthpm).|
| <b>Atualizações secundárias de versão | O Banco de Dados do Azure para PostgreSQL corrige automaticamente os servidores de banco de dados para a versão secundária determinada pelo Azure. Isso acontece como parte da manutenção planejada do serviço. Isso causará em alguns segundos de tempo de inatividade e o servidor de banco de dados será reiniciado automaticamente com a nova versão secundária. Para saber mais, consulte a [documentação](./concepts-monitoring.md#planned-maintenance-notification)e também verifique o [portal](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Mitigação de tempo de inatividade não planejada

O tempo de inatividade não planejado pode ocorrer como resultado de falhas imprevistas, incluindo falhas de hardware subjacentes, problemas de rede e bugs de software. Se o servidor de banco de dados ficar inativo inesperadamente, um novo servidor de banco de dados será provisionado automaticamente em segundos. O armazenamento remoto é anexado automaticamente ao novo servidor de banco de dados. O mecanismo do PostgreSQL executa a operação de recuperação usando WAL e arquivos de banco de dados abrindo o servidor de banco de dados para permitir que os clientes se conectem. As transações não confirmadas são perdidas e precisam ser repetidas pelo aplicativo. Embora não seja possível evitar um tempo de inatividade não planejado, o Banco de Dados do Azure para PostgreSQL reduz o tempo de inatividade realizando automaticamente operações de recuperação no servidor de banco de dados e nas camadas de armazenamento sem a necessidade de intervenção humana. 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="exibição de Alta Disponibilidade no PostgreSQL do Azure":::

1. Servidores PostgreSQL do Azure com recursos de dimensionamento rápido.
2. O gateway que atua como um proxy para rotear as conexões do cliente para o servidor de banco de dados apropriado
3. Armazenamento do Azure com três cópias para confiabilidade, disponibilidade e redundância.
4. O armazenamento remoto permite desanexar/reanexar rapidamente após o failover do servidor.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planejado: cenários de falha e recuperação de serviço
Aqui estão alguns cenários de falha e como o Banco de Dados do Azure para PostgreSQL se recupera automaticamente:

| **Cenário** | **Recuperação automática** |
| ---------- | ---------- |
| <B>Falha no servidor de banco de dados | Se o servidor de banco de dados estiver inoperante devido a alguma falha de hardware subjacente, as conexões ativas serão removidas e todas as transações em andamento serão anuladas. Um novo servidor de banco de dados é implantado automaticamente, e o armazenamento remoto é anexado ao novo servidor de banco de dado. Depois que a recuperação do banco de dados for concluída, os clientes podem se conectar ao novo servidor de banco de dados por meio do gateway. <br /> <br /> O RTO (tempo de recuperação) depende de vários fatores, incluindo a atividade no momento da falha, como transação grande e a quantidade de recuperação a ser executada durante o processo de inicialização do servidor de banco de dados. <br /> <br /> Os aplicativos que usam os bancos de dados do PostgreSQL precisam ser criados para detectar e repetir as conexões e transações que falharam.  Quando o aplicativo repete a operação, o gateway redireciona a conexão de forma transparente para o servidor de banco de dados recém-criado. |
| <B>Falha de armazenamento | Os aplicativos não detectam o impacto de problemas relacionados ao armazenamento, como uma falha de disco ou um corrompimento de bloco físico. Como os dados são armazenados em 3 cópias, a cópia dos dados é atendida pelo armazenamento remanescente. As corrupções de bloco são corrigidas automaticamente. Se uma cópia dos dados for perdida, uma nova cópia dos dados será criada automaticamente. |

Aqui estão alguns cenários de falha que necessitam da ação do usuário para serem recuperados:

| **Cenário** | **Plano de recuperação** |
| ---------- | ---------- |
| <b> Falha de região | A falha de uma região é um evento raro. No entanto, se precisar de proteção de uma falha de região, você poderá configurar uma ou mais réplicas de leitura em outras regiões para DR (recuperação de desastre). (Consulte [este artigo](./howto-read-replicas-portal.md), sobre como criar e gerenciar réplicas de leitura, para mais detalhes). No caso de uma falha em nível de região, você pode promover manualmente a réplica de leitura configurada na outra região para ser seu servidor de banco de dados de produção. |
| <b>Erros de usuário/lógicos | A recuperação de erros do usuário, como tabelas removidas por acidente ou dados atualizados incorretamente, envolve a execução de uma PITR ([recuperação pontual](./concepts-backup.md)), que restaura e recupera os dados até o momento anterior da ocorrência do erro.<br> <br>  Se você quiser restaurar apenas um subconjunto de bancos de dados ou tabelas específicas, em vez de todos os bancos de dados no servidor de banco de dados, poderá restaurar o servidor de banco de dados em uma nova instância, exportar as tabelas por meio de [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html) e usar [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) para restaurar essas tabelas em seu banco de dados. |



## <a name="summary"></a>Resumo

O Banco de Dados do Azure para PostgreSQL fornece recursos para reiniciar rapidamente servidores de banco de dados, armazenamento redundante e roteamento eficiente do gateway. Para proteção de dados adicional, você pode configurar os backups para serem replicados geograficamente e também implantar uma ou mais réplicas de leitura em outras regiões. Com os recursos inerentes de alta disponibilidade, o Banco de Dados do Azure para PostgreSQL protege seus bancos de dados contra as interrupções mais comuns e oferece um SLA de tempo de atividade, com suporte financeiro potencial do setor, de [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql). Todos esses recursos de disponibilidade e confiabilidade permitem que o Azure seja a plataforma ideal para executar aplicativos críticos.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Regiões do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre [tratamento de erros de conectividade transitórios](concepts-connectivity.md)
- Saiba como [replicar seus dados com réplicas de leitura](howto-read-replicas-portal.md)