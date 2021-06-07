---
title: Backup e restauração no banco de dados do Azure para servidor flexível MySQL
description: Saiba mais sobre os conceitos de backup e restauração com o servidor flexível do banco de dados do Azure para MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240759"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Backup e restauração no banco de dados do Azure para servidor flexível MySQL (versão prévia)

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Banco de dados do Azure para MySQL servidor flexível, cria automaticamente backups de servidor e os armazena com segurança no armazenamento redundante local dentro da região. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

## <a name="backup-overview"></a>Visão geral do backup

O servidor flexível faz backups instantâneos dos arquivos de dados e os armazena em um armazenamento redundante local. O servidor também executa backup de logs de transações e também os armazena em armazenamento redundante local. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Opcionalmente, você pode configurar o backup do banco de dados de 1 a 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits para os dados armazenados em repouso.

Esses arquivos de backup não podem ser exportados. Os backups só podem ser usados para operações de restauração no servidor flexível. Você também pode usar o [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) de um cliente MySQL para copiar um banco de dados.

## <a name="backup-frequency"></a>Frequência de backup

Os backups em servidores flexíveis são baseados em instantâneo. O primeiro backup de instantâneo completo é agendado imediatamente após a criação de um servidor. O primeiro backup de instantâneo completo é mantido como o backup base do servidor. Os backups de instantâneo subsequentes são apenas backups diferenciais.

Os backups de instantâneo diferenciais ocorrem pelo menos uma vez por dia. Os backups de instantâneo diferenciais não ocorrem em um agendamento fixo. Os backups de instantâneo diferenciais ocorrem a cada 24 horas, a menos que os logs binários no MySQL excedam 50 GB desde o último backup diferencial. Em um dia, são permitidos no máximo seis instantâneos diferenciais. Os backups de log de transações ocorrem a cada cinco minutos.

## <a name="backup-retention"></a>Retenção de backup

Os backups de banco de dados são armazenados em um armazenamento redundante local (LRS), que é armazenado em três cópias em uma região. Os backups são mantidos com base na configuração do período de retenção de backup no servidor. Você pode selecionar um período de retenção de 1 a 35 dias com um período de retenção padrão de sete dias. Você pode definir o período de retenção durante a criação do servidor ou posterior, atualizando a configuração de backup usando portal do Azure.

O período de retenção de backup controla o tempo de retorno em que uma operação de restauração pontual pode ser executada, pois é baseada em backups disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspectiva de restauração. Todos os backups necessários para executar uma restauração pontual dentro do período de retenção de backup são mantidos no armazenamento de backup. Por exemplo – se o período de retenção de backup for definido como sete dias, a janela de recuperação será considerada nos últimos sete dias. Nesse cenário, todos os backups necessários para restaurar o servidor nos últimos sete dias são mantidos. Com uma janela de retenção de backup de sete dias, os instantâneos de banco de dados e os backups de log de transações são armazenados nos últimos oito dias (1 dia antes da janela).

## <a name="backup-storage-cost"></a>Custo do armazenamento de backup

O servidor flexível fornece até 100% de seu armazenamento de servidor provisionado como armazenamento de backup sem custo adicional. Qualquer armazenamento de backup adicional usado é cobrado em GB por mês. Por exemplo, se você tiver provisionado um servidor com 250 GB de armazenamento, terá 250 GB de armazenamento disponível para backups de servidor sem custo adicional. Se o uso diário do backup for 25 GB, você poderá ter até 10 dias de armazenamento de backup gratuito. O armazenamento consumido para backups com mais de 250 GB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/mysql/).

Você pode usar a métrica [armazenamento de backup usado](../concepts-monitoring.md) no Azure monitor disponível no portal do Azure para monitorar o armazenamento de backup consumido por um servidor. A métrica **armazenamento de backup** usado representa a soma do armazenamento consumido por todos os backups de banco de dados e backups de log retidos com base no período de retenção de backup definido para o servidor. Uma atividade transacional intensa no servidor pode fazer com que o uso do armazenamento de backup aumente, independentemente do tamanho total do banco de dados.

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup apropriado. Você pode selecionar um período de retenção entre 1 e 35 dias.

> [!IMPORTANT]
> Os backups de um servidor de banco de dados configurado em uma configuração de alta disponibilidade com redundância de zona acontecem do servidor de banco de dados primário, pois a sobrecarga é mínima com backups de instantâneo.

> [!IMPORTANT]
> Atualmente, não há suporte para backups com redundância geográfica no servidor flexível.

## <a name="point-in-time-restore"></a>Restauração em um momento determinado

No banco de dados do Azure para MySQL servidor flexível, a execução de uma restauração pontual cria um novo servidor a partir dos backups do servidor flexível na mesma região que o servidor de origem. Ele é criado com a configuração do servidor original para a camada de computação, o número de vCores, o tamanho do armazenamento, o período de retenção de backup e a opção de redundância de backup. Além disso, marcas e configurações como rede virtual e firewall são herdadas do servidor de origem. A computação e a camada de armazenamento do servidor restaurados podem ser alteradas após a conclusão da restauração.

> [!NOTE]
> Há dois parâmetros de servidor que são redefinidos para valores padrão (e não são copiados do servidor primário) após a operação de restauração
> *   time_zone-esse valor a ser definido como sistema de valor padrão
> *   event_scheduler-a event_scheduler está definida como OFF no servidor restaurado

A Restauração pontual é útil em vários cenários. Alguns dos casos de uso comuns incluem-
-   Quando um usuário exclui acidentalmente dados no banco de dado
-   O usuário remove uma tabela ou um banco de dados importante
-   O aplicativo do usuário substitui acidentalmente os dados bons por dados incorretos devido a um defeito do aplicativo.

Você pode escolher entre um ponto de restauração mais recente e um ponto de restauração personalizado por meio de [portal do Azure](how-to-restore-server-portal.md).

-   **Ponto de restauração mais recente**: o ponto de restauração mais recente ajuda a restaurar o servidor para o último backup realizado no servidor de origem. O carimbo de data/hora para restauração também será exibido no Portal. Essa opção é útil para restaurar rapidamente o servidor para o estado mais atualizado.
-   **Ponto de restauração personalizado**: isso permitirá que você escolha um ponto no tempo dentro do período de retenção definido para esse servidor flexível. Essa opção é útil para restaurar o servidor no momento exato para recuperar-se de um erro do usuário.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos do banco de dados, o tamanho do backup do log de transações, o tamanho da computação da SKU e a hora da restauração também. A recuperação do log de transações é a parte que consome mais tempo do processo de restauração. Se o tempo de restauração for escolhido mais próximo do agendamento de backup de instantâneo completo ou diferencial, as restaurações serão mais rápidas, pois o aplicativo de log de transações é mínimo. Para estimar o tempo de recuperação preciso para seu servidor, é altamente recomendável testá-lo em seu ambiente, pois ele tem muitas variáveis específicas de ambiente.

> [!IMPORTANT]
> Se você estiver restaurando um servidor flexível configurado com alta disponibilidade com redundância de zona, o servidor restaurado será configurado na mesma região e zona que o servidor primário e implantado como um único servidor flexível em um modo não HA. Consulte [alta disponibilidade com redundância de zona](concepts-high-availability.md) para servidor flexível.

> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Para proteger recursos do servidor, após a implantação, da exclusão acidental ou de alterações inesperadas, os administradores podem usar [bloqueios de gerenciamento](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Executar tarefas de pós-restauração

Após uma restauração do **ponto de restauração mais recente** ou do mecanismo de recuperação do **ponto de restauração personalizado** , você deve executar as seguintes tarefas para fazer com que os usuários e aplicativos voltem a funcionar:

-   Se o novo servidor for destinado a substituir o servidor original, redirecione clientes e aplicativos cliente para o novo servidor.
-   Verifique se as regras apropriadas de rede virtual e firewall no nível de servidor estão em vigor para que os usuários se conectem.
-   Verifique se os logons apropriados e as permissões no nível do banco de dados estão em vigor.
-   Configure alertas, conforme apropriado.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
-   Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)
