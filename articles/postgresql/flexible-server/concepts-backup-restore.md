---
title: Backup e restauração no banco de dados do Azure para PostgreSQL – servidor flexível
description: Saiba mais sobre os conceitos de backup e restauração com o banco de dados do Azure para PostgreSQL-servidor flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92532638"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Backup e restauração no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Os backups formam uma parte essencial de qualquer estratégia de continuidade de negócios. Eles ajudam a proteger os dados contra a corrupção ou exclusão acidental. Banco de dados do Azure para PostgreSQL-o servidor flexível faz backup automaticamente de seu servidor e retém os backups durante até 35 dias. Durante a restauração, você pode especificar a data e a hora em que deseja restaurar dentro do período de retenção. O tempo geral para restaurar e recuperar depende do tamanho dos arquivos de banco de dados e da quantidade de recuperação a ser executada. 

### <a name="backup-process-in-flexible-server"></a>Processo de backup em servidor flexível
O primeiro backup de instantâneo é agendado imediatamente após a criação do servidor flexível. Subsequentemente, um backup de instantâneo diário dos arquivos de dados é executado. Os backups são armazenados em um armazenamento com redundância de zona dentro de uma região. Os logs de transações (logs de gravação antecipada-WAL) também são arquivados continuamente, de modo que você poderá restaurar para a última transação confirmada. Esses backups de dados e logs permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Se o banco de dados estiver configurado com alta disponibilidade, os instantâneos diários serão executados a partir do primário e os backups de log contínuos serão executados a partir do modo de espera.

> [!IMPORTANT]
>Os backups não são executados em servidores interrompidos. No entanto, os backups são retomados quando o banco de dados é iniciado automaticamente após 7 dias ou iniciado pelo usuário.

Os backups só podem ser usados para operações de restauração no servidor flexível. Se você quiser exportar ou importar dados para o servidor flexível, use a metodologia [de despejo e restauração](../howto-migrate-using-dump-and-restore.md) .


### <a name="backup-retention"></a>Retenção de backup

Os backups são mantidos com base na configuração do período de retenção de backup para o servidor. Você pode selecionar um período de retenção entre 7 e 35 dias. O período de retenção padrão é de sete dias. Você pode definir o período de retenção durante a criação do servidor ou pode atualizá-lo posteriormente. Os backups são mantidos mesmo para servidores interrompidos.

O período de retenção de backup controla o quanto o tempo uma restauração pontual pode ser recuperada, pois ela se \' baseia em backups disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspectiva de restauração. Todos os backups necessários para executar uma restauração pontual dentro do período de retenção de backup são mantidos no armazenamento de backup. Por exemplo – se o período de retenção de backup for definido como sete dias, a janela de recuperação será considerada como últimos sete dias. Nesse cenário, todos os dados e logs necessários para restaurar e recuperar o servidor nos últimos sete dias são mantidos. 


### <a name="backup-storage-cost"></a>Custo do armazenamento de backup

O servidor flexível fornece até 100% de seu armazenamento de servidor provisionado como armazenamento de backup sem custo adicional. Qualquer armazenamento de backup adicional usado é cobrado em GB por mês. Por exemplo, se você tiver provisionado um servidor com 250 GiB de armazenamento, terá 250 GiB de capacidade de armazenamento de backup sem custo adicional. Se o uso diário do backup for 25 GiB, você poderá ter até 10 dias de armazenamento de backup gratuito. O consumo de armazenamento de backup excedendo 250 GiB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/postgresql/).

Você pode usar a métrica [Armazenamento de backup usado](../concepts-monitoring.md) no portal do Azure para monitorar o armazenamento de backup consumido por um servidor. A métrica Armazenamento de backup usada representa a soma do armazenamento consumido por todos os backups de banco de dados e backups de log retidos com base no período de retenção de backup definido para o servidor.  Uma atividade transacional intensa no servidor pode fazer com que o uso do armazenamento de backup aumente, independentemente do tamanho total do banco de dados.

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup apropriado e escolhendo as opções de redundância de backup corretas para atender aos objetivos de recuperação desejados.

> [!IMPORTANT]
> Atualmente, não há suporte para backups com redundância geográfica no servidor flexível.

## <a name="point-in-time-restore-overview"></a>Visão geral da restauração pontual

No servidor flexível, a execução de uma restauração pontual cria um novo servidor a partir dos backups de s do servidor flexível \' na mesma região que o servidor de origem. Ele é criado com a configuração do servidor de origem para o tipo de preço, geração de computação, número de vCores, tamanho do armazenamento, período de retenção de backup e opção de redundância de backup. Além disso, as marcas e configurações, como as configurações de VNET e firewall, são herdadas do servidor de origem. 

 > [!IMPORTANT]
> Se você estiver restaurando um servidor flexível configurado com alta disponibilidade com redundância de zona, o servidor restaurado será configurado sem alta disponibilidade e na mesma região que o servidor primário. 

 ### <a name="restore-process"></a>Processo de restauração

Os arquivos de banco de dados físicos são restaurados primeiro dos backups de instantâneo para o local de dados do servidor. O backup apropriado que foi feito antes do ponto no tempo desejado é automaticamente escolhido e restaurado. Um processo de recuperação é então iniciado usando arquivos WAL para colocar o banco de dados em um estado consistente. 

 Por exemplo, vamos supor que os backups são executados às 23h todas as noites. Se o ponto de restauração for de 15 de agosto de 2020 às 10:00, o backup diário de 14 de agosto de 2020 será restaurado. O banco de dados será recuperado até 10h de 25 de agosto de 2020 usando o backup de logs de transações entre 24 de agosto de 23h até 25 de agosto de 10h. 

 Consulte [estas etapas](./how-to-restore-server-portal.md) para restaurar o servidor de banco de dados.

> [!IMPORTANT]
> As operações de restauração no servidor flexível criam um novo servidor de banco de dados e não substituem o servidor de banco de dados existente.

A Restauração pontual é útil em vários cenários. Por exemplo, quando um usuário exclui dados acidentalmente, descarta uma tabela ou um banco de dados importante, ou se um aplicativo acidentalmente substitui dados bons por dados inválidos devido a um defeito no aplicativo. Você poderá restaurar para a última transação devido ao backup contínuo de logs de transações.

Você pode escolher entre um ponto de restauração mais antigo e um ponto de restauração personalizado.

-   **Ponto de restauração mais antigo**: dependendo do período de retenção, ele será o primeiro horário que você pode restaurar. O horário de backup mais antigo será selecionado automaticamente e exibido no Portal. Isso será útil se você quiser investigar ou fazer alguns testes iniciando esse ponto no tempo.

-   **Ponto de restauração personalizado**: essa opção permite que você escolha um ponto no tempo dentro do período de retenção definido para esse servidor flexível. Por padrão, o horário mais recente em UTC é selecionado automaticamente e útil se você deseja restaurar para a última transação confirmada para fins de teste. Opcionalmente, você pode escolher outros dias e hora. 

O tempo estimado para recuperação depende de vários fatores, incluindo o tamanho do banco de dados, o volume de logs de transações a serem processados, a largura de banda da rede e o número total de dados recuperados na mesma região ao mesmo tempo. Normalmente, o tempo de recuperação geral demora de alguns minutos até algumas horas.


> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Para proteger recursos do servidor, após a implantação, da exclusão acidental ou de alterações inesperadas, os administradores podem usar [bloqueios de gerenciamento](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Executar tarefas de pós-restauração

Depois de restaurar o banco de dados, você pode executar as seguintes tarefas para fazer com que seus usuários e aplicativos voltem a funcionar:

-   Se o novo servidor for destinado a substituir o servidor original, redirecione clientes e aplicativos cliente para o novo servidor.

-   Garanta que as regras de rede virtual e de firewall no nível de servidor apropriadas estejam em vigor para que os usuários se conectem. Essas regras não são copiadas do servidor original.
  
-   A computação do servidor restaurado pode ser ampliada/reduzida conforme necessário.

-   Verifique se os logons apropriados e as permissões no nível do banco de dados estão em vigor.

-   Configure alertas, conforme apropriado.
  
-  Se você tiver restaurado o banco de dados configurado com alta disponibilidade e quiser configurar o servidor restaurado com alta disponibilidade, poderá seguir [as etapas](./how-to-manage-high-availability-portal.md).


## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
-   Saiba [como restaurar](./how-to-restore-server-portal.md)