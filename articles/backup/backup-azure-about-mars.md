---
title: Sobre o agente MARS
description: Saiba como o agente MARS dá suporte aos cenários de backup
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562592"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Sobre o agente de MARS (Serviços de Recuperação do Microsoft Azure)

Este artigo descreve como o serviço de backup do Azure usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup e restaurar arquivos, pastas e o volume ou o estado do sistema de um computador local para o Azure.

## <a name="backup-scenarios"></a>Cenários de backup

O agente MARS dá suporte aos seguintes cenários de backup:

![Cenários de backup MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Arquivos e pastas**: proteger seletivamente arquivos e pastas do Windows.
- **Nível de volume**: Proteja todo o volume do Windows de seu computador.
- **Nível do sistema**: proteger todo o estado do sistema do Windows.

### <a name="additional-scenarios"></a>Cenários adicionais

- **Fazer backup de arquivos e pastas específicos em máquinas virtuais do Azure**: o método principal para fazer backup de VMs (máquinas virtuais) do Azure é usar uma extensão de backup do Azure na VM. A extensão faz o backup de toda a VM. Se desejar fazer backup de arquivos e pastas específicos em uma VM, você poderá instalar o agente MARS nas VMs do Azure. Para obter mais informações, consulte [arquitetura: backup de VM do Azure interno](./backup-architecture.md#architecture-built-in-azure-vm-backup).

- **Propagação offline**: backups completos iniciais de dados no Azure normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede. Os backups subsequentes transferem apenas o Delta, ou incrementais, a quantidade de dados. O backup do Azure compacta os backups iniciais. Por meio do processo de *propagação offline*, o backup do Azure pode usar discos para carregar os dados de backup inicial compactados offline no Azure. Para obter mais informações, consulte [backup offline do Azure-backup usando Azure data Box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Cenários de restauração

O agente MARS dá suporte aos seguintes cenários de restauração:

![Cenários de recuperação MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Mesmo servidor**: o servidor no qual o backup foi criado originalmente.
  - **Arquivos e pastas**: escolha os arquivos e as pastas individuais que você deseja restaurar.
  - **Nível de volume**: escolha o volume e o ponto de recuperação que você deseja restaurar. Em seguida, restaure-o no mesmo local ou em um local alternativo no mesmo computador.  Crie uma cópia dos arquivos existentes, substitua os arquivos existentes ou ignore a recuperação de arquivos existentes.
  - **Nível do sistema**: escolha o estado do sistema e o ponto de recuperação para restaurar no mesmo computador em um local especificado.

- **Servidor alternativo**: um servidor que não seja o servidor onde o backup foi feito.
  - **Arquivos e pastas**: escolha os arquivos e as pastas individuais cujo ponto de recuperação você deseja restaurar em um computador de destino.
  - **Nível de volume**: escolha o volume e o ponto de recuperação que você deseja restaurar para outro local. Crie uma cópia dos arquivos existentes, substitua os arquivos existentes ou ignore a recuperação de arquivos existentes.
  - **Nível do sistema**: escolha o estado do sistema e o ponto de recuperação para restaurar como um arquivo de estado do sistema para um computador alternativo.

## <a name="backup-process"></a>Processo de backup

1. No portal do Azure, crie um [cofre dos serviços de recuperação](install-mars-agent.md#create-a-recovery-services-vault)e escolha arquivos, pastas e o estado do sistema de **metas de backup**.
2. [Baixe as credenciais do cofre dos serviços de recuperação e o instalador do agente](./install-mars-agent.md#download-the-mars-agent) em um computador local.

3. [Instale o agente](./install-mars-agent.md#install-and-register-the-agent) e use as credenciais do cofre baixado para registrar o computador no cofre dos serviços de recuperação.
4. No console do agente no cliente, [Configure o backup](./backup-windows-with-mars-agent.md#create-a-backup-policy) para especificar o que fazer backup, quando fazer backup (o agendamento), por quanto tempo os backups devem ser mantidos no Azure (a política de retenção) e começar a proteger.

![Diagrama do agente de backup do Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Informações adicionais

- O **backup inicial** (primeiro backup) é executado de acordo com as configurações de backup.  O agente MARS usa o VSS para fazer um instantâneo pontual dos volumes selecionados para backup. O agente usa apenas a operação do gravador de sistema do Windows para capturar o instantâneo. Ele não usa nenhum gravador VSS de aplicativo e não captura instantâneos consistentes com o aplicativo. Depois de tirar o instantâneo com o VSS, o agente MARS cria um VHD (disco rígido virtual) na pasta de cache que você especificou quando configurou o backup. O agente também armazena somas de verificação para cada bloco de dados.

- Os **backups incrementais** (backups subsequentes) são executados de acordo com o agendamento especificado. Durante backups incrementais, os arquivos alterados são identificados e um novo VHD é criado. O VHD é compactado e criptografado e é enviado para o cofre. Após a conclusão do backup incremental, o novo VHD é mesclado com o VHD criado após a replicação inicial. Esse VHD mesclado fornece o estado mais recente a ser usado para comparação para o backup em andamento.

- O agente MARS pode executar o trabalho de backup no **modo otimizado** usando o diário de alterações USN (número de sequência de atualização) ou, no **modo não otimizado** , verificando alterações em diretórios ou arquivos por meio da verificação de todo o volume. O modo não otimizado é mais lento porque o agente precisa verificar cada arquivo no volume e compará-lo com os metadados para determinar os arquivos alterados.  O **backup inicial** sempre será executado no modo não otimizado. Se o backup anterior tiver falhado, o próximo trabalho de backup agendado será executado no modo não otimizado. Para saber mais sobre esses modos e como verificá-los, consulte [Este artigo](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode).

## <a name="next-steps"></a>Próximas etapas

[Matriz de suporte do agente do MARS](./backup-support-matrix-mars-agent.md)

[FAQ do agente MARS](./backup-azure-file-folder-backup-faq.md)
