---
title: Procedimento de failover do HANA para um local de desastre para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como executar o failover para um site de recuperação de desastre para SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad7cfbac1dffdab4af7afc26c98c0582bc376c99
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494334"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de failover de recuperação de desastre


>[!IMPORTANT]
>Este artigo não é uma substituição para a documentação de administração do SAP HANA ou para as notas SAP. Esperamos que você tenha uma compreensão sólida e experiência em SAP HANA administração e operações, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres (DR). Neste artigo, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

Há dois casos a serem considerados ao fazer failover para um local de recuperação de desastre:

- É necessário que o banco de dados do SAP HANA retorne para o status mais recente dos dados. Nesse caso, há um script de autoatendimento com o qual você pode executar o failover sem a necessidade de entrar em contato com a Microsoft. Para o failback, você precisa trabalhar com a Microsoft.
- Você deseja restaurar para um instantâneo de armazenamento que não seja o instantâneo replicado mais recente. Nesse caso, você precisa trabalhar com a Microsoft. 

>[!NOTE]
>As etapas a seguir devem ser feitas na unidade de instância grande do HANA, que representa a unidade de recuperação de desastre. 
 
Para restaurar os instantâneos de armazenamento replicados mais recentemente, siga as etapas em "executar failover de recuperação de desastre completo-azure_hana_dr_failover" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 

Se você quiser ter várias instâncias de SAP HANA com failover, execute o comando azure_hana_dr_failover várias vezes. Quando solicitado, insira o SAP HANA SID que você deseja que o failover e a restauração. 


Você pode testar o failover de DR também sem afetar a relação de replicação real. Para executar um failover de teste, siga as etapas em "executar um teste de failover de recuperação de desastre-azure_hana_test_dr_failover" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 

>[!IMPORTANT]
>*Não* execute nenhuma transação de produção na instância que você criou no site de recuperação de desastres por meio do processo de **teste de um failover**. O comando azure_hana_test_dr_failover cria um conjunto de volumes que não têm nenhuma relação com o site primário. Como resultado, a sincronização de volta para o site primário *não* é possível. 

Se você quiser ter várias instâncias de SAP HANA para testar, execute o script várias vezes. Quando solicitado, insira o SAP HANA SID da instância que você deseja testar para failover. 

>[!NOTE]
>Se você precisar fazer failover para o site de recuperação de desastre para recuperar alguns dados que foram excluídos horas atrás e precisar que os volumes de recuperação de desastre sejam definidos para um instantâneo anterior, esse procedimento se aplicará. 

1. Desligue a instância de não produção do HANA na unidade de recuperação de desastres de instâncias grandes do HANA que você está executando. Uma instância de produção do HANA inativa é pré-instalado.
1. Certifique-se de que não há nenhum processo do SAP HANA em execução. Use o seguinte comando para esta verificação:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A saída agora deve mostrar o processo **hdbdaemon** em um estado interrompido e nenhum outro processo do HANA em estado de execução ou iniciado.
1. Determine para qual nome de instantâneo ou ID de backup do SAP HANA você deseja ter a recuperação de desastre restaurada. Em casos de recuperação de desastres reais, esse instantâneo é geralmente o instantâneo mais recente. Se você precisar recuperar dados perdidos, escolha um instantâneo mais antigo.
1. Contate o Suporte do Azure por meio de uma solicitação de suporte de alta prioridade. Solicite a restauração desse instantâneo com o nome e a data do instantâneo ou a ID de backup do HANA no site de recuperação de desastre. O padrão é que o lado das operações restaura apenas o volume /hana/data. Se você quiser ter os volumes/Hana/logbackups também, será necessário declarar isso especificamente. *Não restaure o volume/hana/shared.* Em vez disso, escolha arquivos específicos como global. ini fora do diretório **. snapshot** e seus subdiretórios depois de remontar o volume/Hana/Shared para PRD. 

   No lado das operações, ocorrem as seguintes etapas:

   a. A replicação de instantâneos do volume de produção para volumes de recuperação de desastre é interrompida. Isso pode já ter acontecido se uma interrupção no local de produção for o motivo de você precisar realizar o procedimento de recuperação de desastre.
   
   b. O nome do instantâneo de armazenamento ou o instantâneo com a ID de backup escolhido é restaurado nos volumes de recuperação de desastre.
   
   c. Após a restauração, os volumes de recuperação de desastre estão disponíveis para serem montados para as unidades do SAP HANA em Instâncias Grandes na região de recuperação de desastre.
      
1. Monte os volumes de recuperação de desastre para a unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre. 
1. Inicie a instância de produção do SAP HANA inativa.
1. Se você optar por copiar os logs de backup de log de transações para reduzir o tempo de RPO, mescle os backups de log de transações no diretório/Hana/logbackups de recuperação de desastres montado recentemente. Não substitua backups existentes. Copie os backups mais recentes que não foram replicados com a replicação mais recente de um instantâneo de armazenamento.
1. Você também pode restaurar arquivos únicos dos instantâneos que não foram replicados para o volume/hana/shared/PRD na região do Azure de recuperação de desastre.

As etapas a seguir mostram como recuperar a instância de produção SAP HANA com base no instantâneo de armazenamento restaurado e nos backups de log de transações que estão disponíveis.

1. Altere o local de backup para **hana/logbackups** usando o SAP HANA Studio.

   ![Alterar o local de backup para recuperação de DESASTREs](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. O SAP HANA verifica os locais do arquivo de backup e sugere o backup de log de transações mais recente para o qual ser restaurado. A verificação pode levar alguns minutos até que uma tela semelhante à seguinte seja exibida:

   ![Lista de backups de log de transações para recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas destas configurações padrão:

      - Limpe **Usar backups delta**.
      - Selecione **Inicializar área de log**.

   ![Defina o Inicializar a área de log](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Concluir a restauração da recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em mente que o exemplo é de uma restauração de recuperação de desastre de uma configuração de expansão do SAP HANA de três nós.

![Progresso da restauração](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se a restauração parar de responder na tela **concluir** e não mostrar a tela de progresso, confirme se todas as instâncias de SAP Hana nos nós de trabalho estão em execução. Se necessário, inicie manualmente as instâncias SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback de recuperação de desastre para um site de produção
É possível executar failback de uma recuperação de desastre para um local de produção. Vejamos um cenário em que o failover no site de recuperação de desastre foi causado por problemas na região de produção do Azure e não pela necessidade de recuperar dados perdidos. 

Você está executando sua carga de trabalho de produção SAP por um tempo no site de recuperação de desastre. Conforme são resolvidos os problemas no local de produção, convém executar failback no local de produção. Como não é possível perder dados, a etapa no local de produção envolve várias etapas e cooperação com a equipe de operações do SAP HANA no Azure. Cabe a você acionar a equipe de operações para iniciar a sincronização de volta ao site de produção, depois que os problemas forem resolvidos.

Siga estas etapas:

1. A equipe de operações do SAP HANA do Azure obtém o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastre, que agora representam o estado de produção. Nesse estado, a unidade de Instância Grande do HANA no local de produção é desligada.
1. O SAP HANA na equipe de operações do Azure monitora a replicação e garante que ela seja detectada antes de informá-lo.
1. Desligue os aplicativos que usam a instância do HANA de produção no site de recuperação de desastre. Em seguida, faça um backup de log de transações do HANA. Em seguida, você interrompe a instância do HANA em execução nas unidades do SAP HANA em instâncias grandes no site de recuperação de desastre.
1. Depois que a instância do HANA em execução na unidade de instância grande do HANA no site de recuperação de desastre for desligada, a equipe de operações sincronizará manualmente os volumes de disco novamente.
1. O SAP HANA na equipe de operações do Azure inicia a unidade de instância grande do HANA no site de produção novamente. Eles entregam para você. Certifique-se de que a instância de SAP HANA está em um estado de desligamento no tempo de inicialização da unidade de instância grande do HANA.
1. Você executa as mesmas etapas de restauração de banco de dados que fez quando realizou o failover anteriormente no site de recuperação de desastre.

## <a name="monitor-disaster-recovery-replication"></a>Monitorar a replicação de recuperação de desastre

Para monitorar o status de seu progresso de replicação de armazenamento, execute `azure_hana_replication_status`o script. Esse comando deve ser executado de uma unidade que é executada no local de recuperação de desastre para funcionar conforme o esperado. O comando funciona não importa se a replicação está ativa. O comando pode ser executado para cada unidade de instância grande do HANA do seu locatário no local de recuperação de desastre. Ele não pode ser usado para obter detalhes sobre o volume de inicialização. 

Para obter mais informações sobre o comando e sua saída, consulte "Get DR Replication status-azure_hana_replication_status" em [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Próximas etapas
- Consulte [monitorar e solucionar problemas do lado do Hana](hana-monitor-troubleshoot.md).
