---
title: Visão geral da continuidade dos negócios – servidor flexível do banco de dados do Azure para MySQL
description: Saiba mais sobre os conceitos de continuidade de negócios com o servidor flexível do banco de dados do Azure para MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4f9cc8321d5d1d19dbcb8294ad6205b01337ee72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715047"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Visão geral da continuidade de negócios com o banco de dados do Azure para MySQL – servidor flexível (versão prévia)

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

O banco de dados do Azure para MySQL servidor flexível permite recursos de continuidade de negócios que protegem seus bancos de dados no caso de uma interrupção planejada e não planejada. Recursos como backups automatizados e alta disponibilidade resolvem diferentes níveis de proteção contra falhas com diferentes exposições de tempo de recuperação e perda de dados. Ao arquitetar seu aplicativo para proteger contra falhas, você deve considerar o RTO (objetivo de tempo de recuperação) e o RPO (objetivo de ponto de recuperação) para cada aplicativo. O RTO é a tolerância a tempo de inatividade e o RPO é a tolerância à perda de dados após uma interrupção no serviço de banco de dado.

A tabela a seguir ilustra os recursos que o servidor flexível oferece.

| **Recurso** | **Descrição** | **Restrições** |
| ---------- | ----------- | ------------ |
| **Recuperação de & de backup** | O servidor flexível executa automaticamente backups diários de seus arquivos de banco de dados e faz backup contínuo dos logs de transações. Os backups podem ser retidos por qualquer período entre 1 e 35 dias. Você poderá restaurar o servidor de banco de dados para qualquer ponto no tempo dentro do período de retenção do backup. O tempo de recuperação dependerá do tamanho dos dados a serem restaurados + o tempo para executar a recuperação de log. Consulte [conceitos – backup e restauração](./concepts-backup-restore.md) para obter mais detalhes. |Os dados de backup permanecem dentro da região |
| **Backup redundante local** | Os backups de servidor flexíveis são armazenados de forma automática e segura em um armazenamento redundante local dentro de uma região e na mesma zona de disponibilidade. Os backups com redundância local replicam os arquivos de dados de backup do servidor três vezes em um único local físico na região primária. O armazenamento de backup com redundância local fornece pelo menos 99,999999999% (11 noves) de durabilidade de objetos em um determinado ano. Consulte [conceitos – backup e restauração](./concepts-backup-restore.md) para obter mais detalhes.| Aplicável em todas as regiões |
| **Alta disponibilidade com redundância de zona** | O servidor flexível pode ser implantado no modo de alta disponibilidade, que implanta servidores primários e em espera em duas zonas de disponibilidade diferentes em uma região. Isso protege contra falhas no nível de zona e também ajuda a reduzir o tempo de inatividade do aplicativo durante eventos de tempo de inatividade planejados e não planejados. Os dados do servidor primário são replicados de forma síncrona para a réplica em espera. Durante qualquer evento de tempo de inatividade, o servidor de banco de dados faz failover automaticamente para a réplica em espera. Consulte [conceitos – alta disponibilidade](./concepts-high-availability.md) para obter mais detalhes. | Com suporte nas camadas de computação de uso geral e otimizado para memória. Disponível somente em regiões em que várias zonas estão disponíveis.|
| **Compartilhamentos de arquivos Premium** | Os arquivos de banco de dados são armazenados em compartilhamentos de arquivos do Azure Premium altamente duráveis e confiáveis que fornecem redundância de dados com três cópias de réplica armazenadas em uma zona de disponibilidade com recursos de recuperação automática de dados. Consulte [compartilhamentos de arquivos Premium](../../storage/files/storage-how-to-create-file-share.md) para obter mais detalhes. | Dados armazenados em uma zona de disponibilidade |

> [!IMPORTANT]
> Nenhum SLA de tempo de atividade, RTO e RPO é oferecido durante o período de versão prévia. Detalhes fornecidos nesta página apenas para fins de planejamento e informações.

## <a name="planned-downtime-mitigation"></a>Mitigação de tempo de inatividade planejada

Aqui estão alguns cenários de manutenção planejada que incorrem em tempo de inatividade:

| **Cenário** | **Processo**|
| :------------ | :----------- |
| **Escala de computação (usuário)**| Quando você executa a operação de dimensionamento de computação, um novo servidor flexível é provisionado usando a configuração de computação dimensionada. No servidor de banco de dados existente, os pontos de verificação ativos têm permissão para serem concluídos, as conexões de cliente são descarregadas, todas as transações não confirmadas são canceladas e, em seguida, desligadas. O armazenamento é então anexado ao novo servidor e o banco de dados é iniciado, o que executa a recuperação, se necessário, antes de aceitar conexões de cliente. |
| **Nova implantação de software (Azure)** | Novos recursos de distribuição ou correções de bugs ocorrem automaticamente como parte da manutenção planejada do serviço, e você pode agendar quando essas atividades ocorrerem. Para obter mais informações, consulte a [documentação](https://aka.ms/servicehealthpm)e também Verifique seu [portal](https://aka.ms/servicehealthpm) |
| **Atualizações de versão secundárias (Azure)** | O banco de dados do Azure para MySQL corrige automaticamente os servidores de banco de dados para a versão secundária determinada pelo Azure. Isso acontece como parte da manutenção planejada do serviço. Isso incorrerá em um breve tempo de inatividade em termos de segundos e o servidor de banco de dados será reiniciado automaticamente com a nova versão secundária. Para obter mais informações, consulte a [documentação](../concepts-monitoring.md#planned-maintenance-notification)do e também Verifique o [portal](https://aka.ms/servicehealthpm).|

Quando o servidor flexível é configurado com **alta disponibilidade com redundância de zona**, o servidor flexível executa operações no servidor em espera primeiro e, em seguida, no servidor primário sem um failover. Consulte [conceitos – alta disponibilidade](./concepts-high-availability.md) para obter mais detalhes.

## <a name="unplanned-downtime-mitigation"></a>Mitigação de tempo de inatividade não planejada

Os tempos de inatividade não planejados podem ocorrer como resultado de falhas imprevistas, incluindo falhas de hardware subjacentes, problemas de rede e bugs de software. Se o servidor de banco de dados ficar inativo inesperadamente, se configurado com alta disponibilidade [HA], a réplica em espera será ativada. Caso contrário, um novo servidor de banco de dados será provisionado automaticamente. Embora um tempo de inatividade não planejado não possa ser evitado, o servidor flexível reduz o tempo de inatividade realizando automaticamente operações de recuperação no servidor de banco de dados e nas camadas de armazenamento sem exigir intervenção humana.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planejado: cenários de falha e recuperação de serviço

Aqui estão alguns cenários de falha não planejados e o processo de recuperação:

| **Cenário** | **Processo de recuperação [não HA]** | **Processo de recuperação [HA]** |
| :---------- | ---------- | ------- |
| **Falha no servidor de banco de dados** | Se o servidor de banco de dados estiver inoperante devido a alguma falha de hardware subjacente, as conexões ativas serão descartadas e todas as transações em andamentodas serão anuladas. O Azure tentará reiniciar o servidor de banco de dados. Se isso tiver sucesso, a recuperação do banco de dados será executada. Se a reinicialização falhar, o servidor de banco de dados será tentado reiniciar em outro nó físico.  <br /> <br /> O RTO (tempo de recuperação) depende de vários fatores, incluindo a atividade no momento da falha, como transação grande e a quantidade de recuperação a ser executada durante o processo de inicialização do servidor de banco de dados. <br /> <br /> Os aplicativos que usam os bancos de dados MySQL precisam ser criados de forma que detectem e repitam as conexões e as transações com falha.  Quando o aplicativo tenta novamente, as conexões são direcionadas para o servidor de banco de dados recém-criado. | Se a falha do servidor de banco de dados for detectada, o servidor de banco de dados em espera será ativado, reduzindo assim o tempo de Consulte a [página de conceitos de alta disponibilidade](concepts-high-availability.md) para obter mais detalhes. Espera-se que o RTO seja 60-120 s, com RPO = 0 |
| **Falha de armazenamento** | Os aplicativos não veem nenhum impacto para problemas relacionados ao armazenamento, como uma falha de disco ou uma corrupção de bloco físico. À medida que os dados são armazenados em 3 cópias, a cópia dos dados é servida pelo armazenamento sobrevivente. Os danos de bloco são corrigidos automaticamente. Se uma cópia dos dados for perdida, uma nova cópia dos dados será criada automaticamente. | Para erros não recuperáveis, o servidor flexível faz failover para a réplica em espera para reduzir o tempo de inatividade. Consulte a [página de conceitos de alta disponibilidade](./concepts-high-availability.md) para obter mais detalhes. |
| **Erros lógicos/de usuário** | A recuperação de erros do usuário, como tabelas descartadas acidentalmente ou dados atualizados incorretamente, envolve a execução de uma PITR ( [recuperação pontual](concepts-backup-restore.md) ), restaurando e recuperando os dados até o momento anterior da ocorrência do erro.<br> <br>  Se você quiser restaurar apenas um subconjunto de bancos de dados ou tabelas específicas, em vez de todos os bancos de dados no servidor de banco, poderá restaurar o servidor de banco de dados em uma nova instância, exportar as tabelas via [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html)e, em seguida, usar [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) para restaurar essas tabelas em seu banco de dados. | Esses erros de usuário não são protegidos com alta disponibilidade devido ao fato de que todas as operações de usuário são replicadas para o estado em espera também. |
| **Falha na zona de disponibilidade** | Embora seja um evento raro, se você quiser se recuperar de uma falha no nível da zona, poderá executar uma recuperação pontual usando o backup e escolhendo o ponto de restauração personalizado para obter os dados mais recentes. Um novo servidor flexível será implantado em outra zona. O tempo necessário para a restauração depende do backup anterior e do número de logs de transações a serem recuperados. | O servidor flexível executa o failover automático para o site em espera. Consulte a [página de conceitos de alta disponibilidade](./concepts-high-availability.md) para obter mais detalhes. |
| **Falha de região** | A réplica entre regiões e os recursos de restauração geográfica ainda não têm suporte na versão prévia. | |

> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Use o [bloqueio de recursos do Azure](../../azure-resource-manager/management/lock-resources.md) para ajudar a evitar a exclusão acidental do seu servidor.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
- Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)