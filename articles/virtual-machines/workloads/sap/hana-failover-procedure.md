---
title: Procedimento de failover do HANA para um site de desastres para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como executar failover para um site de recuperação de desastres para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a33340524556f5da1703cae3532f053fbe8ba13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670983"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de failover de recuperação de desastre


>[!IMPORTANT]
>Este artigo não substitui a documentação de administração do SAP HANA ou as Notas do SAP. Esperamos que você tenha uma compreensão sólida e experiência com administração e operações em SAP HANA, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres (DR). Neste artigo, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

Há dois casos a serem considerados ao fazer failover para um site de recuperação de desastre:

- É necessário que o banco de dados do SAP HANA retorne para o status mais recente dos dados. Nesse caso, há um script de autoatendimento com o qual você pode fazer failover sem precisar entrar em contato com a Microsoft. Para o failback, você precisa trabalhar com a Microsoft.
- Sua intenção é restaurar para um instantâneo de armazenamento que não seja o último instantâneo replicado. Nesse caso, você precisa trabalhar com a Microsoft. 

>[!NOTE]
>As etapas a seguir devem ser executadas na unidade do SAP HANA nas Instâncias Grandes, que representa a unidade de DR. 
 
Para restaurar os instantâneos de armazenamento replicados mais recentemente, siga as etapas em “Executar failover de recuperação de desastre completo - azure_hana_dr_failover” em [Ferramentas de instantâneos da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Se você quiser que várias instâncias do SAP HANA façam failover, execute o comando azure_hana_dr_failover várias vezes. Quando solicitado, insira o SID do SAP HANA que você deseja fazer failover e restaurar. 


Você também pode testar o failover de recuperação de desastres sem afetar a relação de replicação real. Para executar um failover de teste, siga as etapas em “Executar um teste de failover de recuperação de desastre - azure_hana_test_dr_failover” em [Ferramentas de instantâneo da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>*Não* execute nenhuma transação na instância que você criou no site de recuperação de desastre por meio do processo de **teste de um failover**. O comando azure_hana_test_dr_failover cria um conjunto de volumes que não têm nenhuma relação com o site primário. Como resultado, a sincronização de volta para o site primário *não* é possível. 

Se você quiser ter várias instâncias do SAP HANA para testar, execute o script várias vezes. Quando solicitado, insira o SID do SAP HANA da instância que você deseja testar para fazer failover. 

>[!NOTE]
>Se você precisar fazer failover no site de recuperação de desastre para resgatar alguns dados que foram excluídos há algumas horas e precisar que os volumes de DR sejam definidos para um instantâneo anterior, este procedimento se aplicará. 

1. Desligue a instância de não produção do HANA na unidade de recuperação de desastre do SAP HANA nas Instâncias Grandes que você está executando. Uma instância de produção inativa do HANA é pré-instalada.
1. Certifique-se de que não há nenhum processo do SAP HANA em execução. Use o comando a seguir para essa verificação:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A saída agora deve mostrar o processo **hdbdaemon** em um estado interrompido e nenhum outro processo do HANA em estado de execução ou iniciado.
1. Determine para qual nome de instantâneo ou ID de backup do SAP HANA você deseja ter a recuperação de desastre restaurada. Em casos de recuperação de desastres reais, esse instantâneo é geralmente o instantâneo mais recente. Se você precisar recuperar dados perdidos, escolha um instantâneo mais antigo.
1. Contate o Suporte do Azure por meio de uma solicitação de suporte de alta prioridade. Solicite a restauração desse instantâneo, com o nome e a data do instantâneo, ou a ID de backup do HANA no site de DR. O padrão é que o lado das operações restaura apenas o volume /hana/data. Se você também quiser ter os volumes /hana/logbackups, será necessário declarar isso especificamente. *Não restaure o volume/hana/shared.* Em vez disso, escolha arquivos específicos, como global.ini do diretório **.snapshot** e de seus subdiretórios após montar novamente o volume /hana/shared para PRD. 

   No lado das operações, ocorrem as seguintes etapas:

   a. A replicação de instantâneos do volume de produção para volumes de recuperação de desastre é interrompida. Isso pode já ter acontecido se uma interrupção no local de produção for o motivo de você precisar realizar o procedimento de recuperação de desastre.
   
   b. O nome do instantâneo de armazenamento ou o instantâneo com a ID de backup escolhido é restaurado nos volumes de recuperação de desastre.
   
   c. Após a restauração, os volumes de recuperação de desastre estão disponíveis para serem montados para as unidades do SAP HANA em Instâncias Grandes na região de recuperação de desastre.
      
1. Monte os volumes de recuperação de desastre para a unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre. 
1. Inicie a instância de produção do SAP HANA inativa.
1. Se optar por copiar logs de backup de log de transações para reduzir o tempo de RPO, mescle esses backups de log de transações no diretório /hana/logbackups da recuperação de desastre montado recentemente. Não substitua backups existentes. Copie os backups mais recentes que não foram replicados com a replicação mais recente de um instantâneo de armazenamento.
1. Também é possível restaurar arquivos únicos dos instantâneos que não foram replicados para o volume /hana/shared/PRD na região do Azure de recuperação de desastre.

As próximas etapas mostram como recuperar a instância de produção SAP HANA com base no instantâneo de armazenamento restaurado e nos backups de log de transações disponíveis.

1. Altere o local de backup para **hana/logbackups** usando o SAP HANA Studio.

   ![Alterar o local de backup para recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. O SAP HANA verifica os locais do arquivo de backup e sugere o backup de log de transações mais recente para o qual ser restaurado. A verificação pode levar alguns minutos até que uma tela como a seguinte seja exibida:

   ![Lista de backups de log de transações para recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas destas configurações padrão:

      - Limpe **Usar backups delta**.
      - Selecione **Inicializar área de log**.

   ![Defina o Inicializar a área de log](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Concluir a restauração da recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em mente que o exemplo é de uma restauração de recuperação de desastre de uma configuração de expansão do SAP HANA de três nós.

![Progresso da restauração](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se a restauração parar de responder na tela **Concluir** e não for mostrada na tela de andamento, confirme se todas as instâncias do SAP HANA nos nós de trabalho estão em execução. Se necessário, inicie manualmente as instâncias SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback de recuperação de desastre para um site de produção
É possível executar failback de uma recuperação de desastre para um local de produção. Vejamos um cenário em que o failover no site de recuperação de desastre foi causado por problemas na região de produção do Azure e não pela necessidade de recuperar dados perdidos. 

Você vem executando sua carga de trabalho de produção SAP por um tempo no site de recuperação de desastre. Conforme são resolvidos os problemas no local de produção, convém executar failback no local de produção. Como não é possível perder dados, a etapa no local de produção envolve várias etapas e cooperação com a equipe de operações do SAP HANA no Azure. Cabe a você acionar a equipe de operações para iniciar a sincronização de volta ao site de produção, depois que os problemas forem resolvidos.

Siga estas etapas:

1. A equipe de operações do SAP HANA do Azure obtém o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastre, que agora representam o estado de produção. Nesse estado, a unidade de Instância Grande do HANA no local de produção é desligada.
1. A equipe de operações do SAP HANA do Azure monitora a replicação e garante que ela seja obtida antes de informar a você.
1. Desligue os aplicativos que usam a instância do HANA de produção no site de recuperação de desastre. Em seguida, faça um backup de log de transações do HANA. Depois, você interrompe a instância do HANA que está em execução nas unidades do SAP HANA em Instâncias Grandes no site de recuperação de desastre.
1. Após a instância do HANA em execução na unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre ser desligada, a equipe de operações sincronizará manualmente os volumes de disco novamente.
1. A equipe de operações do SAP HANA no Azure inicia a unidade da Instância Grande do HANA no local de produção novamente. Em seguida, a equipe a entrega para você. Verifique se a instância do SAP HANA está em um estado de desligamento no tempo de inicialização da unidade de Instância Grande do HANA.
1. Execute as mesmas etapas de restauração de banco de dados como você fez anteriormente no failover para o site de recuperação de desastre.

## <a name="monitor-disaster-recovery-replication"></a>Monitorar a replicação de recuperação de desastre

Para monitorar o status do andamento da sua replicação de armazenamento, execute o script `azure_hana_replication_status`. Esse comando deve ser executado a partir de uma unidade em execução no site de recuperação de desastre para funcionar conforme o esperado. O comando funciona independentemente se a replicação está ativa. O comando pode ser executado para cada unidade de Instância Grande do HANA do seu locatário no local de recuperação de desastre. Ele não pode ser usado para obter detalhes sobre o volume de inicialização. 

Para obter mais informações sobre o comando e sua saída, consulte “Get DR Replication status - azure_hana_replication_status” em [Ferramentas de instantâneo da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf).


## <a name="next-steps"></a>Próximas etapas
- Consulte [Monitoramento e solução de problemas por parte do HANA](hana-monitor-troubleshoot.md)
