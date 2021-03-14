---
title: Visão geral do backup operacional para BLOBs do Azure
description: Saiba mais sobre o backup operacional para BLOBs do Azure (em versão prévia).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b10191c8a01d3cc7a92dee8ca9bf59a506497a60
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744069"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Visão geral do backup operacional para BLOBs do Azure (em versão prévia)

O backup operacional para BLOBs é uma solução de proteção de dados local gerenciada que permite proteger seus blobs de blocos de vários cenários de perda de dados, como corrupções, exclusões de BLOB e exclusão acidental da conta de armazenamento. Os dados são armazenados localmente na própria conta de armazenamento de origem e podem ser recuperados para um momento determinado, sempre que necessário. Portanto, ele fornece um meio simples, seguro e econômico para proteger seus BLOBs.

O backup operacional para BLOBs integra-se ao [centro de backup](backup-center-overview.md), entre outros recursos de gerenciamento de backup, para fornecer um único painel de vidro que pode ajudá-lo a controlar, monitorar, operar e analisar backups em escala.

## <a name="how-operational-backup-works"></a>Como funciona o backup operacional

O backup operacional de BLOBs é uma solução de **backup local** . Portanto, os dados de backup não são transferidos para o cofre de backup, mas são armazenados na própria conta de armazenamento de origem. No entanto, o cofre de backup ainda serve como a unidade de gerenciamento de backups. Além disso, essa é uma solução de **backup contínua** , o que significa que você não precisa agendar nenhum backup e todas as alterações serão retidas e restauráveis a partir do estado em um momento determinado.

O backup operacional usa recursos da plataforma de BLOB para proteger seus dados e permitir a recuperação quando necessário:

- **Restauração pontual**: a [restauração pontual de blob](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview) permite restaurar dados de BLOB para um estado anterior. Isso, por sua vez, usa a **exclusão reversível**, o **feed de alterações** e o **controle de versão de blob** para reter dados durante a duração especificada. O backup operacional cuida da habilitação da restauração pontual, bem como dos recursos subjacentes para garantir que os dados sejam retidos durante o período especificado.

- **Excluir bloqueio**: o bloqueio de exclusão impede que a conta de armazenamento seja excluída acidentalmente ou por usuários não autorizados. O backup operacional quando configurado também aplica automaticamente um bloqueio de exclusão para reduzir as possibilidades de perda de dados devido à exclusão da conta de armazenamento.

Consulte a [matriz de suporte](blob-backup-support-matrix.md) para saber mais sobre as limitações da solução atual.

### <a name="protection"></a>Proteção

O backup operacional é configurado e gerenciado no nível da **conta de armazenamento** e se aplica a todos os blobs de blocos na conta de armazenamento. O backup operacional usa uma **política de backup** para gerenciar a duração para a qual os dados de backup (incluindo versões mais antigas e blobs excluídos) serão mantidos, dessa forma, definindo o período até o qual você pode restaurar seus dados. A política de backup pode ter uma retenção máxima de 360 dias ou um número equivalente de semanas completas (51) ou meses (11).

Quando você configura o backup para uma conta de armazenamento e atribui uma política de backup com uma retenção de ' n' dias, as propriedades subjacentes são definidas conforme descrito abaixo. Você pode exibir essas propriedades na guia **proteção de dados** do serviço blob em sua conta de armazenamento.

- **Restauração pontual**: Defina como ' n' dias, conforme definido na política de backup. Se a conta de armazenamento já tiver um ponto no tempo habilitado com uma retenção de, por exemplo, ' x ' dias, antes de configurar o backup, a duração da restauração pontual será definida para o maior dos dois valores, que é Max (n, x). Se você já tiver habilitado a restauração pontual e especificado que a retenção deve ser maior do que na política de backup, ela permanecerá inalterada.

- **Exclusão reversível**: Defina como ' n + 5 ' dias, ou seja, cinco dias além da duração especificada na política de backup. Se a conta de armazenamento que está sendo configurada para o backup operacional já tiver a exclusão reversível habilitada com uma retenção de, digamos, ' y ' dias, a retenção de exclusão reversível será definida como o máximo dos dois valores, ou seja, Max (n + 5, y). Se você já tiver habilitado a exclusão reversível e especificou que a retenção deve ser maior que a de acordo com a política de backup, ela permanecerá inalterada.

- **Controle de versão para BLOBs e feed de alterações de blob**: o controle de versão e o feed de alterações estão habilitados para contas de armazenamento que foram configuradas para backup operacional.

- **Excluir bloqueio**: a configuração do backup operacional em uma conta de armazenamento também aplica um bloqueio de exclusão na conta de armazenamento. O bloqueio de exclusão aplicado pelo backup pode ser exibido na guia **bloqueios** da conta de armazenamento.

Para permitir que o backup habilite essas propriedades nas contas de armazenamento a serem protegidas, o cofre de backup deve receber a função de **colaborador de backup da conta de armazenamento** nas respectivas contas de armazenamento.

>[!NOTE]
>O backup operacional dá suporte a operações somente em blobs de blocos e as operações em contêineres não podem ser restauradas. Se você excluir um contêiner da conta de armazenamento chamando a operação **excluir contêiner** , esse contêiner não poderá ser restaurado com uma operação de restauração. Sugerimos que você habilite a exclusão reversível para aprimorar a proteção e a recuperação dos dados.

### <a name="management"></a>Gerenciamento

Depois que você tiver habilitado o backup em uma conta de armazenamento, uma instância de backup será criada correspondente à conta de armazenamento no cofre de backup. Você pode executar qualquer operação relacionada ao backup para uma conta de armazenamento, como iniciar restaurações, monitorar, interromper a proteção e assim por diante, por meio de sua instância de backup correspondente.

O backup operacional também se integra diretamente com o centro de backup para ajudá-lo a gerenciar a proteção de todas as suas contas de armazenamento de forma centralizada, juntamente com todas as outras cargas de trabalho com suporte de backup. O centro de backup é seu único painel de vidro para todos os seus requisitos de backup, como monitoramento de trabalhos e estado de backups e restaurações, garantia de conformidade e governança, análise de uso de backup e execução de operações relacionadas ao backup e à restauração de dados.

### <a name="restore"></a>Restaurar

Você pode restaurar dados de qualquer ponto no tempo para o qual um ponto de recuperação existe. Um ponto de recuperação é criado quando uma conta de armazenamento está em estado protegido e pode ser usado para restaurar dados desde que ele esteja no período de retenção definido pela política de backup (e, portanto, o recurso de restauração pontual do serviço blob na conta de armazenamento). O backup operacional usa a restauração pontual do blob para restaurar dados de um ponto de recuperação.

O backup operacional oferece a opção de restaurar todos os blobs de blocos na conta de armazenamento, procurar e restaurar contêineres específicos ou usar correspondências de prefixo para restaurar um subconjunto de BLOBs. Todas as restaurações podem ser executadas somente na conta de armazenamento de origem.

## <a name="pricing"></a>Preços

Você não incorrerá em nenhum encargo de gerenciamento ou taxa de instância ao usar o backup operacional para BLOBs. No entanto, você incorrerá nos seguintes encargos:

- As restaurações são feitas usando a restauração de ponto no tempo BLOB e atraim encargos com base na quantidade de dados processados. Para obter mais informações, consulte [preços de restauração pontual](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview#pricing-and-billing).

- Retenção de dados devido à [exclusão reversível para BLOBs](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview), [suporte ao feed de alterações no armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-change-feed)e [controle de versão de blob](https://docs.microsoft.com/azure/storage/blobs/versioning-overview).

## <a name="next-steps"></a>Próximas etapas

- [Configurar e gerenciar o backup de BLOBs do Azure](blob-backup-configure-manage.md)
