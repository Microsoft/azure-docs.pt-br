---
title: Alta disponibilidade-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo fornece informações sobre alta disponibilidade no banco de dados do Azure para PostgreSQL-servidor único
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485436"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Alta disponibilidade no banco de dados do Azure para PostgreSQL – servidor único
O banco de dados do Azure para PostgreSQL – serviço de servidor único fornece um alto nível de disponibilidade garantido com o SLA (contrato de nível de serviço) com suporte financeiro de [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql) de tempo de atividade. O banco de dados do Azure para PostgreSQL fornece alta disponibilidade durante eventos planejados, como a operação de computação de escala iniciada pelo do usuário, e também quando ocorrem eventos não planejados, como hardware subjacente, software ou falhas de rede. O banco de dados do Azure para PostgreSQL pode se recuperar rapidamente das circunstâncias mais críticas, garantindo praticamente nenhum tempo de inatividade do aplicativo ao usar esse serviço.

O banco de dados do Azure para PostgreSQL é adequado para a execução de dados de missão crítica que exigem alto tempo de atividade. Baseado na arquitetura do Azure, o serviço tem recursos inerentes de alta disponibilidade, redundância e resiliência para reduzir o tempo de inatividade do banco de dados de interrupções planejadas e não planejadas, sem a necessidade de configurar nenhum componente adicional. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Componentes no banco de dados do Azure para PostgreSQL – servidor único

| **Componente** | **Descrição**|
| ------------ | ----------- |
| <b>Servidor de banco de dados PostgreSQL | O banco de dados do Azure para PostgreSQL fornece segurança, isolamento, proteções de recursos e capacidade de reinicialização rápida para servidores de banco de dados. Esses recursos facilitam operações como o dimensionamento e a operação de recuperação do servidor de banco de dados após uma interrupção a ocorrer em segundos. <br/> Normalmente, as modificações de dados no servidor de banco de dados ocorrem no contexto de uma transação de banco de dado. Todas as alterações de banco de dados são gravadas de forma síncrona na maneira de logs write-ahead (WAL) no armazenamento do Azure – que é anexado ao servidor de banco de dados. Durante o processo de [ponto de verificação](https://www.postgresql.org/docs/11/sql-checkpoint.html) , as páginas de dados da memória do servidor de banco de dado também são liberadas para o armazenamento. |
| <b>Armazenamento Remoto | Todos os arquivos de dados físicos do PostgreSQL e os arquivos WAL são armazenados no armazenamento do Azure, que é arquitetado para armazenar três cópias de dados em uma região para garantir a redundância, a disponibilidade e a confiabilidade dos dados. A camada de armazenamento também é independente do servidor de banco de dados. Ele pode ser desanexado de um servidor de banco de dados com falha e reanexado a um novo servidor de banco de dados dentro de alguns segundos. Além disso, o armazenamento do Azure monitora continuamente as falhas de armazenamento. Se uma corrupção de bloco for detectada, ela será automaticamente corrigida instanciando uma nova cópia de armazenamento. |
| <b>Gateway | O gateway atua como um proxy de banco de dados, roteia todas as conexões de cliente para o servidor de banco de dados. |

## <a name="planned-downtime-mitigation"></a>Mitigação de tempo de inatividade planejada
O banco de dados do Azure para PostgreSQL é projetado para fornecer alta disponibilidade durante operações de tempo de inatividade planejadas. 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text="exibição do dimensionamento elástico no PostgreSQL do Azure":::

1. Aumentar e reduzir os servidores de banco de dados PostgreSQL em segundos
2. O gateway que atua como um proxy para rotear o cliente se conecta ao servidor de banco de dados apropriado
3. O dimensionamento do armazenamento pode ser realizado sem nenhum tempo de inatividade. O armazenamento remoto permite desanexar/reanexar rapidamente após o failover.
Aqui estão alguns cenários de manutenção planejada:

| **Cenário** | **Descrição**|
| ------------ | ----------- |
| <b>Computar escala para cima/para baixo | Quando o usuário executa a operação de expansão/redução de computação, um novo servidor de banco de dados é provisionado usando a configuração de computação dimensionada. No servidor de banco de dados antigo, os pontos de verificação ativos têm permissão para serem concluídos, as conexões de cliente são descarregadas, todas as transações não confirmadas são canceladas e, em seguida, desligadas. O armazenamento é então desanexado do servidor de banco de dados antigo e anexado ao novo servidor de banco de dados. Quando o aplicativo cliente tenta novamente a conexão ou tenta fazer uma nova conexão, o gateway direciona a solicitação de conexão para o novo servidor de banco de dados.|
| <b>Dimensionando o armazenamento | Escalar verticalmente o armazenamento é uma operação online e não interrompe o servidor de banco de dados.|
| <b>Nova implantação de software (Azure) | Novos recursos de distribuição ou correções de bugs ocorrem automaticamente como parte da manutenção planejada do serviço. Para obter mais informações, consulte a [documentação](./concepts-monitoring.md#planned-maintenance-notification)e também Verifique seu [portal](https://aka.ms/servicehealthpm).|
| <b>Atualizações de versão secundárias | O banco de dados do Azure para PostgreSQL corrige automaticamente os servidores de banco de dados para a versão secundária determinada pelo Azure. Isso acontece como parte da manutenção planejada do serviço. Isso incorrerá em um breve tempo de inatividade em termos de segundos e o servidor de banco de dados será reiniciado automaticamente com a nova versão secundária. Para obter mais informações, consulte a [documentação](./concepts-monitoring.md#planned-maintenance-notification)e também Verifique seu [portal](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Mitigação de tempo de inatividade não planejada

O tempo de inatividade não planejado pode ocorrer como resultado de falhas imprevistas, incluindo falhas de hardware subjacentes, problemas de rede e bugs de software. Se o servidor de banco de dados ficar inativo inesperadamente, um novo servidor de banco de dados será provisionado automaticamente em segundos. O armazenamento remoto é anexado automaticamente ao novo servidor de banco de dados. O mecanismo PostgreSQL executa a operação de recuperação usando WAL e arquivos de banco de dados e abre o servidor de banco de dados para permitir que os clientes se conectem. As transações não confirmadas são perdidas e precisam ser repetidas pelo aplicativo. Embora não seja possível evitar um tempo de inatividade não planejado, o banco de dados do Azure para PostgreSQL reduz o tempo de inatividade realizando automaticamente operações de recuperação no servidor de banco de dados e nas camadas de armazenamento sem exigir intervenção humana. 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="exibição do dimensionamento elástico no PostgreSQL do Azure":::

1. Servidores PostgreSQL do Azure com recursos de dimensionamento rápido.
2. Gateway que atua como um proxy para rotear conexões de cliente para o servidor de banco de dados apropriado
3. Armazenamento do Azure com três cópias de confiabilidade, disponibilidade e redundância.
4. O armazenamento remoto também permite desanexar/reanexar rapidamente após o failover do servidor.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planejado: cenários de falha e recuperação de serviço
Aqui estão alguns cenários de falha e como o banco de dados do Azure para PostgreSQL recupera automaticamente:

| **Cenário** | **Recuperação automática** |
| ---------- | ---------- |
| <B>Falha no servidor de banco de dados | Se o servidor de banco de dados estiver inoperante devido a alguma falha de hardware subjacente, as conexões ativas serão descartadas e todas as transações em andamentodas serão anuladas. Um novo servidor de banco de dados é implantado automaticamente, e o armazenamento remoto é anexado ao novo servidor de banco de dado. Após a conclusão da recuperação do banco de dados, os clientes podem se conectar ao novo servidor de banco de dados por meio do gateway. <br /> <br /> O RTO (tempo de recuperação) depende de vários fatores, incluindo a atividade no momento da falha, como transação grande e a quantidade de recuperação a ser executada durante o processo de inicialização do servidor de banco de dados. <br /> <br /> Os aplicativos que usam os bancos de dados PostgreSQL precisam ser criados de forma que detectem e repitam as conexões e as transações com falha.  Quando o aplicativo tenta novamente, o gateway redireciona de forma transparente a conexão para o servidor de banco de dados recém-criado. |
| <B>Falha de armazenamento | Os aplicativos não veem nenhum impacto para problemas relacionados ao armazenamento, como uma falha de disco ou uma corrupção de bloco físico. À medida que os dados são armazenados em 3 cópias, a cópia dos dados é servida pelo armazenamento sobrevivente. Os danos de bloco são corrigidos automaticamente. Se uma cópia dos dados for perdida, uma nova cópia dos dados será criada automaticamente. |

Aqui estão alguns cenários de falha que exigem a recuperação da ação do usuário:

| **Cenário** | **Plano de recuperação** |
| ---------- | ---------- |
| <b> Falha de região | A falha de uma região é um evento raro. No entanto, se você precisar de proteção de uma falha de região, poderá configurar uma ou mais réplicas de leitura em outras regiões para recuperação de desastre (DR). (Consulte [Este artigo](./howto-read-replicas-portal.md) sobre como criar e gerenciar réplicas de leitura para obter detalhes). No caso de uma falha de nível de região, você pode promover manualmente a réplica de leitura configurada na outra região para ser seu servidor de banco de dados de produção. |
| <b> Erros lógicos/de usuário | A recuperação de erros do usuário, como tabelas descartadas acidentalmente ou dados atualizados incorretamente, envolve a execução de uma PITR ( [recuperação pontual](./concepts-backup.md) ), restaurando e recuperando os dados até o momento anterior da ocorrência do erro.<br> <br>  Se você quiser restaurar apenas um subconjunto de bancos de dados ou tabelas específicas, em vez de todos os bancos de dados no servidor de banco, poderá restaurar o servidor de banco de dados em uma nova instância, exportar as tabelas via [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)e, em seguida, usar [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) para restaurar essas tabelas em seu banco de dados. |



## <a name="summary"></a>Resumo

O banco de dados do Azure para PostgreSQL fornece recursos de reinício rápido de servidores de banco de dados, armazenamento redundante e roteamento eficiente do gateway. Para proteção de dados adicional, você pode configurar os backups para serem replicados geograficamente e também implantar uma ou mais réplicas de leitura em outras regiões. Com os recursos inerentes de alta disponibilidade, o banco de dados do Azure para PostgreSQL protege seus bancos de dados contra interrupções mais comuns e oferece um setor de SLA de tempo de atividade, com suporte financeiro, de [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql). Todos esses recursos de disponibilidade e confiabilidade permitem que o Azure seja a plataforma ideal para executar seus aplicativos de missão crítica.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [regiões do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre [tratamento de erros de conectividade transitórios](concepts-connectivity.md)
- Saiba como [replicar seus dados com réplicas de leitura](howto-read-replicas-portal.md)