---
title: Backup e restauração-banco de dados do Azure para MariaDB
description: Saiba mais sobre backups automáticos e restauração do servidor do Banco de Dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: 1d5fc158d2e37223e048c4106206ddba4602fabb
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659930"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Backup e restauração no Banco de Dados do Azure para MariaDB

O Banco de Dados do Azure para MariaDB cria backups de servidor automaticamente e os armazena no armazenamento com redundância local ou geográfica configurado pelo usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

## <a name="backups"></a>Backups

O Banco de Dados do Azure para MariaDB usa backups completos, diferenciais e de log de transações. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Esses arquivos de backup não são expostos pelo usuário e não podem ser exportados. Esses backups só podem ser usados para operações de restauração no banco de dados do Azure para MariaDB. Você pode usar [mysqldump](howto-migrate-dump-restore.md) para copiar um banco de dados.

### <a name="backup-frequency"></a>Frequência de backup

#### <a name="servers-with-up-to-4-tb-storage"></a>Servidores com armazenamento de até 4 TB

Para servidores que dão suporte a até 4 TB de armazenamento máximo, os backups completos ocorrem uma vez a cada semana. Os backups diferenciais ocorrem duas vezes por dia. Os backups de log de transações ocorrem a cada cinco minutos.

#### <a name="servers-with-up-to-16-tb-storage"></a>Servidores com armazenamento de até 16 TB
Em um subconjunto de [regiões do Azure](concepts-pricing-tiers.md#storage), todos os servidores recentemente provisionados podem dar suporte a armazenamento de até 16 TB. Os backups nesses grandes servidores de armazenamento são baseados em instantâneo. O primeiro backup de instantâneo completo é agendado imediatamente após a criação de um servidor. O primeiro backup de instantâneo completo é mantido como o backup base do servidor. Os backups de instantâneo subsequentes são apenas backups diferenciais. 

Os backups de instantâneo diferenciais ocorrem pelo menos uma vez por dia. Os backups de instantâneo diferenciais não ocorrem em um agendamento fixo. Os backups de instantâneo diferenciais ocorrem a cada 24 horas, a menos que o log de transações (binlog em MariaDB) exceda 50 GB desde o último backup diferencial. Em um dia, são permitidos no máximo seis instantâneos diferenciais. 

Os backups de log de transações ocorrem a cada cinco minutos. 

### <a name="backup-retention"></a>Retenção de backup

Os backups são mantidos com base na configuração do período de retenção de backup no servidor. Você pode selecionar um período de retenção de 7 a 35 dias. O período de retenção padrão é de 7 dias. Você pode definir o período de retenção durante a criação do servidor ou posterior atualizando a configuração de backup usando [portal do Azure](howto-restore-server-portal.md#set-backup-configuration) ou [CLI do Azure](howto-restore-server-cli.md#set-backup-configuration). 

O período de retenção de backup determina até quando a restauração de pontos anteriores pode ser feita, já que ele se baseia em backups disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspectiva de restauração. Todos os backups necessários para executar uma restauração pontual dentro do período de retenção de backup são mantidos no armazenamento de backup. Por exemplo, se o período de retenção de backup for definido como 7 dias, a janela de recuperação será considerada nos últimos 7 dias. Nesse cenário, todos os backups necessários para restaurar o servidor nos últimos sete dias são mantidos. Com uma janela de retenção de backup de sete dias:
- Os servidores com armazenamento de até 4 TB manterão até 2 backups de banco de dados completos, todos os backups diferenciais e backups de log de transações executados desde o backup de banco de dados completo mais antigo.
-   Os servidores com armazenamento de até 16 TB manterão o instantâneo completo do banco de dados, todos os instantâneos diferenciais e backups de log de transações nos últimos 8 dias.

#### <a name="long-term-retention-of-backups"></a>Retenção de longo prazo de backups
A retenção de longo prazo de backups além de 35 dias ainda não tem suporte nativo do serviço no momento. Você tem a opção de usar o mysqldump para fazer backups e armazená-los para retenção de longo prazo. Nossa equipe de suporte tem publicado um [artigo passo a passo](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) para compartilhar como você pode obtê-lo. 

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

O Banco de Dados do Azure para MariaDB fornece a flexibilidade de escolher entre o armazenamento de backup com redundância local ou geográfica nas camadas de Uso Geral e Otimizado para Memória. Quando os backups são armazenados no armazenamento de backup com redundância geográfica, eles não são somente armazenados dentro da região em que o servidor está hospedado, mas também replicados em um [datacenter emparelhado](../best-practices-availability-paired-regions.md). Isso fornece maior proteção e capacidade de restaurar o servidor em uma região diferente em caso de desastre. A camada Básica oferece apenas o armazenamento de backup de redundância local.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Mudando de localmente redundante para armazenamento de backup com redundância geográfica
A configuração do armazenamento com redundância local ou geográfica para backup só é permitida durante a criação do servidor. Quando o servidor é provisionado, você não pode alterar a opção de redundância do armazenamento de backup. Para mover o armazenamento de backup do armazenamento com redundância local para o armazenamento com redundância geográfica, criar um novo servidor e migrar os dados usando [dump e Restore](howto-migrate-dump-restore.md) é a única opção com suporte.

### <a name="backup-storage-cost"></a>Custo do armazenamento de backup

O Banco de Dados do Azure para MariaDB fornece até 100% de seu armazenamento de servidor configurado como armazenamento de backup, sem custo adicional. Qualquer armazenamento de backup adicional usado é cobrado em GB por mês. Por exemplo, se você tiver provisionado um servidor com 250 GB de armazenamento, terá 250 GB de armazenamento adicional disponível para backups de servidor sem custo adicional. O armazenamento consumido para backups com mais de 250 GB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/mariadb/). 

Você pode usar a métrica [armazenamento de backup usado](concepts-monitoring.md) no Azure monitor disponível por meio do portal do Azure para monitorar o armazenamento de backup consumido por um servidor. A métrica armazenamento de backup usado representa a soma do armazenamento consumido por todos os backups de banco de dados completos, backups diferenciais e backups de log retidos com base no período de retenção de backup definido para o servidor. A frequência dos backups é gerenciada pelo serviço e explicada anteriormente. Uma atividade transacional intensa no servidor pode fazer com que o uso do armazenamento de backup aumente, independentemente do tamanho total do banco de dados. Para o armazenamento com redundância geográfica, o uso de armazenamento de backup é o dobro do armazenamento com redundância local. 

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup apropriado e escolhendo as opções de redundância de backup corretas para atender aos objetivos de recuperação desejados. Você pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Uso Geral e servidores com otimização de memória podem optar por ter armazenamento com redundância geográfica para backups.

## <a name="restore"></a>Restaurar

No banco de dados do Azure para MariaDB, a execução de uma restauração cria um novo servidor a partir dos backups do servidor original e restaura todos os bancos de dados contidos no servidor.

Há dois tipos de restauração disponíveis:

- A **restauração pontual** está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o servidor original utilizando a combinação de backups completos e de log de transações.
- A **restauração geográfica** só estará disponível se você tiver configurado o servidor para armazenamento com redundância geográfica e ele permitir que você restaure o servidor para uma região diferente utilizando o backup mais recente feito.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem a esse servidor também serão excluídos e não poderão ser recuperados. Para proteger os recursos do servidor, após a implantação, contra exclusão acidental ou alterações inesperadas, os administradores podem usar [bloqueios de gerenciamento](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Restauração em um momento determinado

Independentemente de sua opção de redundância de backup, você pode executar uma restauração para qualquer ponto anterior dentro de seu período de retenção de backup. Um novo servidor é criado na mesma região do Azure do servidor original. Ele é criado com a configuração do servidor original para o tipo de preço, a geração de computação, o número de núcleos virtuais, o tamanho do armazenamento, o período de retenção de backup e a opção de redundância de backup.

A Restauração pontual é útil em vários cenários. Por exemplo, quando um usuário exclui dados acidentalmente, descarta uma tabela ou um banco de dados importante, ou se um aplicativo acidentalmente substitui dados bons por dados inválidos devido a um defeito no aplicativo.

Talvez seja necessário aguardar a execução do próximo backup de log de transações antes de poder restaurar para um ponto anterior nos últimos cinco minutos.

### <a name="geo-restore"></a>Restauração geográfica

É possível restaurar um servidor para outra região do Azure onde o serviço está disponível caso você tenha configurado o servidor para backups com redundância geográfica. Os servidores que dão suporte a até 4 TB de armazenamento podem ser restaurados para a região emparelhada geograficamente ou para qualquer região que ofereça suporte a até 16 TB de armazenamento. Para servidores que dão suporte a até 16 TB de armazenamento, os backups geográficos podem ser restaurados em qualquer região que dê suporte a servidores de 16 TB também. Examine os [tipos de preço do banco de dados do Azure para MariaDB](concepts-pricing-tiers.md) para a lista de regiões com suporte.

A restauração geográfica é a opção de recuperação padrão quando o servidor não está disponível devido a um incidente na região em que ele está hospedado. Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá restaurar um servidor do backup com redundância geográfica para um servidor em qualquer outra região. A restauração geográfica utiliza o backup mais recente do servidor. Há um atraso entre quando um backup é feito e quando ele é replicado em uma região diferente. Esse atraso pode ser de até uma hora, então, em caso de desastre pode haver perda de dados de até uma hora.

> [!IMPORTANT]
>Se uma restauração geográfica for executada para um servidor recém-criado, a sincronização de backup inicial poderá levar mais de 24 horas, dependendo do tamanho dos dados, pois o tempo de cópia inicial do backup do instantâneo completo é muito maior. Os backups de instantâneo subsequentes são cópias incrementais e, portanto, as restaurações são mais rápidas após 24 horas de criação do servidor. Se você estiver avaliando restaurações geográficas para definir o RTO, recomendamos que você aguarde e avalie a restauração geográfica **somente após 24 horas** de criação do servidor para obter estimativas melhores.

Durante a restauração geográfica, as configurações de servidor que podem ser alteradas incluem as opções de geração de computação, vCore, período de retenção de backup e redundância de backup. Não há suporte para a alteração do tipo de preço (Básico, Uso Geral ou Otimizado para Memória) ou do tamanho de armazenamento durante a restauração geográfica.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

### <a name="perform-post-restore-tasks"></a>Executar tarefas de pós-restauração

Após uma restauração de um dos mecanismos de recuperação, você deve executar as seguintes tarefas para colocar os usuários e os aplicativos novamente em execução:

- Se o novo servidor é usado para substituir o servidor original, redirecione clientes e aplicativos de cliente para o novo servidor
- Verifique se as regras de VNet apropriadas estão em vigor para os usuários se conectarem. Essas regras não são copiadas do servidor original.
- Verifique se as permissões e os logons adequados no nível do banco de dados estão em vigor
- Configurar os alertas, conforme apropriado

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre continuidade dos negócios, confira a  [visão geral de continuidade dos negócios](concepts-business-continuity.md).
- Para restaurar para um ponto no tempo usando o portal do Azure, consulte [restaurar servidor para um ponto no tempo usando o portal do Azure](howto-restore-server-portal.md).
- Para restaurar para um ponto no tempo usando CLI do Azure, consulte [restaurar servidor para um ponto no tempo usando a CLI](howto-restore-server-cli.md).
