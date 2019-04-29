---
title: O que é o StorSimple Snapshot Manager | Microsoft Docs
description: Descreve o StorSimple Snapshot Manager, sua arquitetura e seus recursos.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789581"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Uma introdução ao StorSimple Snapshot Manager

## <a name="overview"></a>Visão geral
O StorSimple Snapshot Manager é um snap-in do Microsoft Management Console (MMC) que simplifica a proteção de dados e o gerenciamento de backup em um ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, você pode gerenciar dados do Microsoft Azure StorSimple no data center e na nuvem como uma solução única de armazenamento integrado, simplificar assim os processos de backup e reduzindo os custos.

Essa visão geral apresenta o StorSimple Snapshot Manager, descreve seus recursos e explica sua função no Microsoft Azure StorSimple. 

Para obter uma visão geral do sistema inteiro do Microsoft Azure StorSimple, incluindo o dispositivo StorSimple, o serviço do StorSimple Manager, o StorSimple Snapshot Manager e o Adaptador do StorSimple para o SharePoint, veja [StorSimple série 8000: uma solução de armazenamento de nuvem híbrida](storsimple-overview.md). 

> [!NOTE]
> * Você não pode usar o StorSimple Snapshot Manager para gerenciar as Matrizes Virtuais do Microsoft Azure StorSimple (também conhecido como dispositivos virtuais locais StorSimple).
> * Se você planeja instalar a Atualização 2 do StorSimple em seu dispositivo StorSimple, não se esqueça de baixar a versão mais recente do StorSimple Snapshot Manager e instalá-lo **antes de instalar a Atualização 2 do StorSimple**. A versão mais recente do StorSimple Snapshot Manager é compatível com versões anteriores e funciona com todas as versões do Microsoft Azure StorSimple. Se você está usando a versão anterior do StorSimple Snapshot Manager, você precisa atualizá-lo (não é necessário desinstalar a versão anterior antes de instalar a nova versão).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Arquitetura e finalidade do StorSimple Snapshot Manager
O StorSimple Snapshot Manager fornece um console de gerenciamento central que você pode usar para criar cópias de backup, momentâneas e consistentes, de dados locais e em nuvem. Por exemplo, você pode usar o console para:

* Configurar, fazer backup e excluir volumes.
* Configurar grupos de volumes para garantir que o backup dos dados seja consistente com o aplicativo.
* Gerencie políticas de backup para que os dados sejam copiados em um agendamento predeterminado.
* Criar instantâneos locais e de nuvem, que podem ser armazenados na nuvem e usados para a recuperação de desastre.

O StorSimple Snapshot Manager busca no host a lista de aplicativos registrados no provedor VSS. Em seguida, para criar backups consistentes com aplicativos, ele verifica os volumes usados por um aplicativo e sugere grupos de volume para configurar. O StorSimple Snapshot Manager usa esses grupos de volumes para gerar cópias de backup consistentes com o aplicativo. (A consistência do aplicativo existe quando todos os arquivos relacionados e bancos de dados são sincronizados e representam o verdadeiro estado do aplicativo em um momento específico). 

Os backups do StorSimple Snapshot Manager assumem a forma de instantâneos incrementais, que capturam apenas as alterações desde o último backup. Como resultado, os backups exigem menos armazenamento e podem ser criados e restaurados rapidamente. O StorSimple Snapshot Manager usa o VSS (Serviço de Cópias de Sombra de Volume) do Windows para garantir que os instantâneos capturem dados consistentes com o aplicativo. (Para saber mais, vá para a seção Integração com o Serviço de Cópias de Sombra de Volume). Com o StorSimple Snapshot Manager, você pode criar agendamentos de backup ou fazer backups imediatos, conforme necessário. Se você precisar restaurar dados de um backup, o StorSimple Snapshot Manager permite selecionar a partir de um catálogo de instantâneos locais ou em nuvem. O Azure StorSimple restaura apenas os dados necessários e quando necessário, o que impede atrasos na disponibilidade dos dados durante operações de restauração).

![Arquitetura do StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura do StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume
Você pode usar o StorSimple Snapshot Manager para configurar e fazer backup dos seguintes tipos de volumes: 

* **Volumes básicos** – Um volume básico é uma única partição em um disco básico. 
* **Volumes simples** – Um volume simples é um volume dinâmico que contém espaço em disco de um único disco dinâmico. Um volume simples consiste em uma única região em um disco ou várias regiões vinculadas no mesmo disco. (Você pode criar volumes simples somente em discos dinâmicos). Volumes simples não são tolerantes a falhas.
* **Volumes dinâmicos** – Um volume dinâmico é um volume criado em um disco dinâmico. Discos dinâmicos usam um banco de dados para rastrear informações sobre volumes contidos em discos dinâmicos de um computador. 
* **Volumes dinâmicos com espelhamento** – Volumes dinâmicos com espelhamento são construídos com a arquitetura RAID 1. Com o RAID 1, dados idênticos são gravados em dois ou mais discos, produzindo um conjunto espelhado. Uma solicitação de leitura pode ser processada por qualquer disco que contenha os dados solicitados.
* **Volumes compartilhados clusterizados** – Com CSVs (volumes compartilhados clusterizados), vários nós em um cluster de failover podem ler ou gravar simultaneamente no mesmo disco. O failover de um nó para outro nó pode ocorrer rapidamente, sem exigir uma alteração na propriedade, montagem ou desmontagem da unidade e remoção de um volume. 

> [!IMPORTANT]
> Não misture CSVs e não CSVs no mesmo instantâneo. Não há suporte para a combinação de CSVs e não CSVs em um instantâneo. 
> 
> 

Você pode usar o StorSimple Snapshot Manager para restaurar grupos de volume inteiros ou clonar volumes individuais e recuperar arquivos individuais.

* [Grupos de volumes e volumes](#volumes-and-volume-groups) 
* [Tipos de backup e políticas de backup](#backup-types-and-backup-policies) 

Para obter mais informações sobre os recursos do StorSimple Snapshot Manager e como usá-los, veja [Interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Grupos de volumes e volumes
Com o StorSimple Snapshot Manager, você cria volumes e em seguida os configura em grupos de volumes. 

O StorSimple Snapshot Manager usa grupos de volumes para criar cópias de backup consistentes com o aplicativo. A consistência com aplicativos existe quando todos os arquivos e bancos de dados relacionados são sincronizados e representam o estado real de um aplicativo em um momento específico. Os grupos de volumes (que também são conhecidos como *grupos de consistência*) formam a base de um trabalho de backup ou de restauração.

Grupos de volumes e contêineres de volume são coisas distintas. Um contêiner de volume contém um ou mais volumes que compartilham uma conta de armazenamento em nuvem e outros atributos, como o consumo de largura de banda e criptografia. Um contêiner de volume único pode conter até 256 volumes do StorSimple escassamente provisionados. Para obter mais informações sobre contêineres de volume, vá para [Gerenciar contêineres de volume](storsimple-manage-volume-containers.md). Grupos de volumes são conjuntos de volumes que você configura para facilitar as operações de backup. Se você selecionar dois volumes que pertencem a diferentes contêineres de volumes, coloque-os em um único grupo de volumes e, em seguida, crie uma política de backup para ele; será feito backup de cada volume no contêiner de volume apropriado, usando a conta de armazenamento apropriada.

> [!NOTE]
> Todos os volumes em um grupo de volumes devem vir de um único provedor de serviço de nuvem.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o Serviço de Cópias de Sombra de Volume do Windows
O StorSimple Snapshot Manager usa o Serviço de Cópias de Sombra de Volume do Windows para capturar dados consistentes com o aplicativo. O Serviço de Cópias de Sombra de Volume do Windows facilita a consistência com o aplicativo, comunicando-se com aplicativos com reconhecimento de VSS para coordenar a criação de instantâneos incrementais. O VSS garante que os aplicativos estejam temporariamente inativos ou hibernados quando os instantâneos forem realizados. 

A implementação do StorSimple Snapshot Manager do VSS funciona com o SQL Server e volumes NTFS genéricos. O processo é o seguinte: 

1. Um solicitante, que normalmente é um gerenciamento de dados e a solução de proteção (como o StorSimple Snapshot Manager) ou um aplicativo de backup, invoca o VSS e pede para coletar informações do software gravador no aplicativo de destino.
2. O VSS entra em contato com o componente gravador para recuperar uma descrição dos dados. O gravador retorna a descrição dos dados dos quais será feito o backup. 
3. O VSS dá um sinal ao gravador para preparar o aplicativo para backup. O gravador prepara os dados para backup concluindo transações abertas, atualizando logs de transações, e assim por diante, e notifica o VSS.
4. O VSS instrui o gravador para parar temporariamente os armazenamentos de dados do aplicativo e certificar-se de que nenhum dado seja gravado no volume enquanto a cópia de sombra é criada. Essa etapa garante a consistência de dados e não passa de 60 segundos.
5. O VSS instrui o provedor para criar a cópia de sombra. Provedores, que podem ter como base software ou hardware, gerenciam os volumes que estão em execução no momento e criam cópias de sombra neles sob demanda. O provedor cria a cópia de sombra e notifica o VSS quando ela for concluída.
6. O VSS entra em contato com o gravador para notificar o aplicativo de que a E/S pode retomar e também para confirmar se a E/S foi pausada com êxito durante a criação de cópias de sombra. 
7. Se a cópia foi bem-sucedida, o VSS retorna o local da cópia ao solicitante. 
8. Se os dados foram gravados enquanto a cópia de sombra foi criada, o backup será inconsistente. O VSS exclui a cópia de sombra e notifica o solicitante. O solicitante pode repetir o processo de backup automaticamente ou notificar o administrador para tentar novamente mais tarde.

Consulte a ilustração a seguir.

![Processo de VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo do Serviço de Cópias de Sombra de Volume do Windows** 

## <a name="backup-types-and-backup-policies"></a>Tipos de backup e políticas de backup
Com o StorSimple Snapshot Manager, você pode fazer backup de dados e armazená-los localmente e na nuvem. Você pode usar o StorSimple Snapshot Manager para fazer backup de dados imediatamente ou usar uma política de backup para criar um agendamento para fazer backups automaticamente. Políticas de backup também permitem que você especifique quantos instantâneos serão retidos. 

### <a name="backup-types"></a>Tipos de backup
Você pode usar o StorSimple Snapshot Manager para criar os seguintes tipos de backups:

* **Instantâneos locais** – Instantâneos locais são cópias pontuais de dados de volume armazenados no dispositivo StorSimple. Normalmente, esse tipo de backup pode ser criado e restaurado rapidamente. Você pode usar um instantâneo local como faria com uma cópia de backup local.
* **Instantâneos de nuvem** – Instantâneos de nuvem são cópias pontuais de dados de volume armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo duplicado em um sistema de armazenamento externo diferente. Instantâneos de nuvem são particularmente úteis em cenários de recuperação de desastres.

### <a name="on-demand-and-scheduled-backups"></a>Backups agendados e sob demanda
Com o StorSimple Snapshot Manager, você pode iniciar um backup único a ser criado imediatamente ou usar uma política de backup para agendar operações de backup recorrentes.

Uma política de backup é um conjunto de regras automatizadas que você pode usar para agendar backups regulares. Uma política de backup permite que você defina a frequência e os parâmetros de criação de instantâneos de um grupo de volumes específico. Você pode usar diretivas para especificar datas de início e vencimento, horários, frequências e requisitos de retenção, instantâneos locais e de nuvem. Uma política é aplicada imediatamente após você defini-la. 

Você pode usar o StorSimple Snapshot Manager para configurar ou reconfigurar políticas de backup sempre que necessário. 

Configure as seguintes informações para cada política de backup que você criar:

* **Nome** – O nome exclusivo da política de backup selecionada.
* **Tipo** – O tipo de política de backup; instantâneo local ou instantâneo de nuvem.
* **Grupo de volumes** – O grupo de volumes ao qual a política de backup selecionada está atribuída.
* **Retenção** – O número de cópias de backup a serem retidas. Se você marcar a caixa **Todas** , todas as cópias de backup serão mantidas até que seja atingido o número máximo de cópias de backup por volume; nesse momento, a política falhará e gerará uma mensagem de erro. Como alternativa, você pode especificar vários backups a serem retidos (entre 1 e 64).
* **Data** – A data em que a política de backup foi criada.

Para obter mais informações sobre como configurar políticas de backup, vá para [Usar o StorSimple Snapshot Manager para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Gerenciamento e monitoramento do trabalho de backup
Você pode usar o StorSimple Snapshot Manager para monitorar e gerenciar trabalhos de backup futuros, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 backups concluídos. Você pode usar o catálogo para localizar e restaurar volumes ou arquivos individuais. 

Para obter informações sobre como monitorar trabalhos de backup, vá para [Usar o StorSimple Snapshot Manager para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [como usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* Baixe o [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

