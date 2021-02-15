---
title: Visão geral da continuidade de negócios com o banco de dados do Azure para PostgreSQL – servidor flexível
description: Saiba mais sobre os conceitos de continuidade de negócios com o banco de dados do Azure para PostgreSQL – servidor flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 341d3c6f0a2d84c2b7b725959db9f7e5dd80d3ff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542957"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Visão geral da continuidade de negócios com o banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

**Continuidade de negócios** no banco de dados do Azure para PostgreSQL – o servidor flexível refere-se aos mecanismos, políticas e procedimentos que permitem que sua empresa continue a operar diante de interrupções, especialmente em sua infraestrutura computacional. Na maioria dos casos, o servidor flexível manipulará os eventos de interrupção que podem ocorrer no ambiente de nuvem e manterá seus aplicativos e processos de negócios em execução. No entanto, há alguns eventos que não podem ser manipulados automaticamente, como:

- O usuário exclui acidentalmente ou atualiza uma linha em uma tabela.
- O terremoto causa uma queda de energia e o temporário desabilita um datacenter ou uma zona de disponibilidade.
- Aplicação de patch de banco de dados necessária para corrigir um problema de erro ou de segurança.

O servidor flexível fornece recursos que protegem dados e reduzem o tempo de inatividade para seus bancos de dado de missão crítica no caso de eventos de tempo de inatividade planejados e não planejados. Criado com base na infraestrutura do Azure que já oferece resiliência e disponibilidade robustas, o servidor flexível tem recursos de continuidade de negócios que fornecem proteção de falha adicional, atendem aos requisitos de tempo de recuperação e reduzem a exposição à perda de dados. Ao arquitetar seus aplicativos, você deve considerar a tolerância a tempo de inatividade, que é o RTO (objetivo de horário de recuperação) e a exposição à perda de dados, que é o RPO (objetivo de ponto de recuperação). Por exemplo, seu banco de dados crítico para os negócios requer requisitos de tempo de atividade muito mais estritos em comparação com um banco de dados de teste.  

> [!IMPORTANT]
> O% SLA (contrato de nível de serviço) de tempo de atividade não é oferecido durante a versão prévia. 

A tabela a seguir ilustra os recursos que o servidor flexível oferece.


| **Recurso** | **Descrição** | **Considerações** |
| ---------- | ----------- | ------------ |
| **Backups automáticos** | O servidor flexível executa automaticamente backups diários de seus arquivos de banco de dados e faz backup contínuo dos logs de transações. Os backups podem ser mantidos de 7 dias até 35 dias. Você poderá restaurar o servidor de banco de dados para qualquer ponto no tempo dentro do período de retenção do backup. O RTO depende do tamanho dos dados a serem restaurados + o tempo para executar a recuperação de log. Pode ser de alguns minutos até 12 horas. Para obter mais detalhes, consulte [conceitos-backup e restauração](./concepts-backup-restore.md). |Os dados de backup permanecem dentro da região. |
| **Alta disponibilidade com redundância de zona** | O servidor flexível pode ser implantado com a configuração de alta disponibilidade com redundância de zona em que os servidores primário e em espera são implantados em duas zonas de disponibilidade diferentes dentro de uma região. Essa configuração de HA protege seus bancos de dados contra falhas no nível da zona e também ajuda a reduzir o tempo de inatividade do aplicativo durante eventos de tempo de inatividade planejados e não planejados. Os dados do servidor primário são replicados para a réplica em espera no modo síncrono. No caso de qualquer interrupção no servidor primário, o servidor passará por failover automaticamente na réplica em espera. O RTO na maioria dos casos deve estar dentro de 60s-120s. O RPO deve ser zero (sem perda de dados). Para obter mais informações, consulte [conceitos – alta disponibilidade](./concepts-high-availability.md). | Com suporte nas camadas de computação de uso geral e otimizado para memória. Disponível somente em regiões em que várias zonas estão disponíveis. |
| **Premium – discos gerenciados** | Os arquivos de banco de dados são armazenados em um armazenamento gerenciado Premium altamente durável e confiável. Isso fornece redundância de dados com três cópias de réplicas armazenadas em uma zona de disponibilidade com recursos de recuperação automática de dados. Para obter mais informações, consulte a [documentação de Managed disks](../../virtual-machines/managed-disks-overview.md). | Dados armazenados em uma zona de disponibilidade. |
| **Backup com redundância de zona** | Os backups de servidor flexíveis são armazenados de forma automática e segura em um armazenamento com redundância de zona em uma região. Durante uma falha no nível de zona em que o servidor é provisionado e, se o servidor não estiver configurado com redundância de zona, você ainda poderá restaurar seu banco de dados usando o ponto de restauração mais recente em uma zona diferente. Para obter mais informações, consulte [conceitos-backup e restauração](./concepts-backup-restore.md).| Aplicável somente em regiões em que várias zonas estão disponíveis.|


## <a name="planned-downtime-events"></a>Eventos de tempo de inatividade planejados
Abaixo estão alguns cenários de manutenção planejada. Normalmente, esses eventos incorrem até alguns minutos de inatividade e sem perda de dados.

| **Cenário** | **Processo**|
| ------------------- | ----------- | 
| <b>Escala de computação (iniciada pelo usuário)| Durante a operação de dimensionamento de computação, pontos de verificação ativos podem ser concluídos, as conexões de cliente são descarregadas, todas as transações não confirmadas são canceladas, o armazenamento é desanexado e, em seguida, é desligado. Um novo servidor flexível com o mesmo nome de servidor de banco de dados é provisionado com a configuração de computação dimensionada. O armazenamento é então anexado ao novo servidor e o banco de dados é iniciado, o que executa a recuperação, se necessário, antes de aceitar conexões de cliente. |
| <b>Dimensionamento do armazenamento (iniciado pelo usuário) | Quando uma operação de armazenamento de expansão é iniciada, os pontos de verificação ativos têm permissão para serem concluídos, as conexões de cliente são descarregadas, todas as transações não confirmadas são canceladas e, em seguida, desligadas. O armazenamento é dimensionado para o tamanho desejado e, em seguida, anexado ao novo servidor. Uma recuperação é executada, se necessário, antes de aceitar conexões de cliente. Observe que não há suporte para o dimensionamento vertical do tamanho do armazenamento. |
| <b>Nova implantação de software (iniciada pelo Azure) | Novos recursos de distribuição ou correções de bugs ocorrem automaticamente como parte da manutenção planejada do serviço, e você pode agendar quando essas atividades ocorrerem. Para obter mais informações, verifique o [portal](https://aka.ms/servicehealthpm). | 
| <b>Atualizações de versão secundárias (iniciadas pelo Azure) | O banco de dados do Azure para PostgreSQL corrige automaticamente os servidores de banco de dados para a versão secundária determinada pelo Azure. Isso acontece como parte da manutenção planejada do serviço. O servidor de banco de dados é reiniciado automaticamente com a nova versão secundária. Para obter mais informações, consulte a [documentação](../concepts-monitoring.md#planned-maintenance-notification). Você também pode verificar seu [portal](https://aka.ms/servicehealthpm).| 

 Quando o servidor flexível é configurado com **alta disponibilidade com redundância de zona** , o servidor flexível executa o dimensionamento e as operações de manutenção no servidor em espera primeiro. Para obter mais informações, consulte [conceitos – alta disponibilidade](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Mitigação de tempo de inatividade não planejada

Os tempos de inatividade não planejados podem ocorrer como resultado de interrupções imprevisíveis, como falha de hardware subjacente, problemas de rede e bugs de software. Se o servidor de banco de dados configurado com alta disponibilidade falhar inesperadamente, a réplica em espera será ativada e os clientes poderão retomar suas operações. Se não estiver configurado com alta disponibilidade (HA), se a tentativa de reinicialização falhar, um novo servidor de banco de dados será provisionado automaticamente. Embora um tempo de inatividade não planejado não possa ser evitado, o servidor flexível ajuda a reduzir o tempo de inatividade realizando automaticamente operações de recuperação sem exigir intervenção humana. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planejado: cenários de falha e recuperação de serviço
Abaixo estão alguns cenários de falha não planejado e o processo de recuperação. 

| **Cenário** | **Processo de recuperação [não HA]** | **Processo de recuperação [HA]** |
| ---------- | ---------- | ------- |
| <B>Falha no servidor de banco de dados | Se o servidor de banco de dados estiver inoperante, o Azure tentará reiniciar o servidor de banco de dados. Se isso falhar, o servidor de banco de dados será reiniciado em outro nó físico.  <br /> <br /> O RTO (tempo de recuperação) depende de vários fatores, incluindo a atividade no momento da falha, como transação grande e o volume de recuperação a ser executado durante o processo de inicialização do servidor de banco de dados. <br /> <br /> Os aplicativos que usam os bancos de dados PostgreSQL precisam ser criados de forma que detectem e repitam as conexões e as transações com falha. | Se a falha do servidor de banco de dados for detectada, o servidor passará por failover para o servidor em espera, reduzindo assim o tempo de inatividade. Para obter mais informações, consulte a [página de conceitos de ha](./concepts-high-availability.md). O RTO deve ser de 60 120s, com perda de dados zero. |
| <B>Falha de armazenamento | Os aplicativos não veem nenhum impacto para problemas relacionados ao armazenamento, como uma falha de disco ou uma corrupção de bloco físico. À medida que os dados são armazenados em três cópias, a cópia dos dados é servida pelo armazenamento sobrevivente. O bloco de dados corrompido é automaticamente reparado e uma nova cópia dos dados é criada automaticamente. | Para qualquer erro raro e não recuperável, como o armazenamento inteiro está inacessível, o servidor flexível faz failover para a réplica em espera para reduzir o tempo de inatividade. Para obter mais informações, consulte a [página de conceitos de ha](./concepts-high-availability.md). |
| <b> Erros lógicos/de usuário | Para se recuperar de erros do usuário, como tabelas descartadas acidentalmente ou dados atualizados incorretamente, você precisa executar uma PITR ( [recuperação pontual](../concepts-backup.md) ). Ao executar a operação de restauração, você especifica o ponto de restauração personalizado, que é a hora certa antes do erro.<br> <br>  Se você quiser restaurar apenas um subconjunto de bancos de dados ou tabelas específicas, em vez de todos os bancos de dados no servidor de banco, poderá restaurar o servidor de banco de dados em uma nova instância, exportar as tabelas via [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)e, em seguida, usar [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) para restaurar essas tabelas em seu banco de dados. | Esses erros de usuário não são protegidos com alta disponibilidade, pois todas as alterações são replicadas para a réplica em espera de forma síncrona. Você precisa executar a restauração pontual para recuperar esses erros. |
| <b> Falha na zona de disponibilidade | Para recuperar-se de uma falha no nível de zona, você pode executar uma restauração pontual usando o backup e escolhendo um ponto de restauração personalizado com o tempo mais recente para restaurar os dados mais recentes. Um novo servidor flexível será implantado em outra zona não afetada. O tempo necessário para a restauração depende do backup anterior e do volume de logs de transações a serem recuperados. | O servidor flexível faz failover automaticamente para o servidor em espera em 60 120s sem perda de dados. Para obter mais informações, consulte a [página de conceitos de ha](./concepts-high-availability.md). | 
| <b> Falha de região | A réplica de leitura entre regiões e a restauração geográfica dos recursos de backup ainda não têm suporte na versão prévia. | |


> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Use o [bloqueio de recursos do Azure](../../azure-resource-manager/management/lock-resources.md) para ajudar a evitar a exclusão acidental do seu servidor.


## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
-   Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)