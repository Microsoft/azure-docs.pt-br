---
title: Princípios de recuperação de desastre e preparação no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Princípios de recuperação de desastre e preparação no SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73643cd6954932f933e200baad09e4301300aac2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794684"
---
# <a name="disaster-recovery-principles"></a>Princípios da recuperação de desastres

O SAP HANA nas Instâncias Grandes oferece uma funcionalidade de recuperação de desastre entre carimbos do SAP HANA nas Instâncias Grandes em diferentes regiões do Azure. Por exemplo, se você implantar unidades do SAP HANA nas Instâncias Grandes na região Oeste dos EUA do Azure, será possível usar as unidades do SAP HANA nas Instâncias Grandes na região Leste dos EUA como unidades de recuperação de desastre. Como mencionado anteriormente, a recuperação de desastre não está configurada automaticamente, porque requer que você pague por outra unidade de Instância Grande do HANA na região de recuperação de desastre. A configuração de recuperação de desastres funciona para configurações de expansão, bem como escaláveis. 

Nos cenários implantados até agora, os clientes usam a unidade na região de recuperação de desastre para executar os sistemas de não produção que usam uma instância instalada do HANA. A unidade de Instância Grande HANA deve ser da mesma SKU que a SKU usada para fins de produção. A imagem a seguir mostra como é a configuração do disco entre a unidade do servidor na região de produção do Azure e a região de recuperação de desastre:

![Configuração de recuperação de desastre do ponto de vista do disco](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Conforme mostrado neste gráfico de visão geral, é necessário solicitar um segundo conjunto de volumes de disco. Os volumes de disco de destino são do mesmo tamanho que os volumes de produção para a instância de produção em unidades de recuperação de desastre. Esses volumes de disco estão associados com a unidade de servidor da Instância Grande do HANA no local de recuperação de desastre. Os volumes a seguir são replicados da região de produção para o local da recuperação de desastre:

- /hana/data
- /hana/logbackups 
- /hana/shared (inclui /usr/sap)

O volume /hana/log não é replicado, porque o log de transações do SAP HANA não é necessários da maneira como a restauração desses volumes é feita. 

A base da funcionalidade de recuperação de desastre oferecida é a funcionalidade de replicação de armazenamento oferecida pela infraestrutura do SAP HANA nas Instâncias Grandes. A funcionalidade usada no lado do armazenamento não é um fluxo constante de alterações replicadas de maneira assíncrona conforme as alterações ocorrem no volume de armazenamento. Em vez disso, ela é um mecanismo que depende do fato de que os instantâneos desses volumes são criados com regularidade. O delta entre um instantâneo já replicado e um novo que ainda não foi replicado é, então, transferido para o site de recuperação de desastre em volumes de disco de destino.  Esses instantâneos são armazenados nos volumes e, no caso de um failover de recuperação de desastre, precisam ser restaurados nesses volumes.  

A primeira transferência dos dados completos do volume deve ser feita antes que a quantidade de dados se torne menor do que os deltas entre instantâneos. Como resultado, os volumes no local de recuperação de desastre contêm todos os instantâneos de volume executados no local de produção. Eventualmente, é possível usar esse sistema de DR para obter um status anterior para recuperar dados perdidos, sem reverter o sistema de produção.

Se há uma implantação de MCOD com várias instâncias do SAP HANA independentes em uma unidade do HANA em Instância Grande, espera-se que todas as instâncias do SAP HANA tenham o armazenamento replicado para o lado da recuperação de desastre.

Nos casos em que você usa a Replicação de sistema do HANA como funcionalidade de alta disponibilidade no seu site de produção e usa a replicação baseada em armazenamento para o site de DR, os volumes de ambos os nós do site primário para a instância de DR são replicados. Você deve adquirir armazenamento adicional (mesmo tamanho, a partir do nó primário) no site de DR para acomodar a replicação do primário e secundário para DR. 



>[!NOTE]
>A funcionalidade de replicação de armazenamento do SAP HANA nas Instâncias Grandes está espelhando e replicando os instantâneos de armazenamento. Se você não executar instantâneos de armazenamento como introduzidos na seção Backup e restauração deste artigo, não poderá haver nenhuma replicação para o site de recuperação de desastre. A execução do instantâneo de armazenamento é um pré-requisito para a replicação de armazenamento para o local recuperação de desastres.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparação do cenário de recuperação de desastre
Nesse cenário, você tem um sistema de produção sendo executado do SAP HANA nas Instâncias Grandes na região do Azure de produção. Para as etapas a seguir, suponhamos que o SID desse sistema HANA seja "PRD" e que você tenha um sistema de não produção em execução no SAP HANA nas Instâncias Grandes na região de DR do Azure. Finalmente, vamos supor que o SID seja "TST." A imagem a seguir mostra esta configuração:

![Início da configuração da recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se a instância do servidor ainda não tiver sido solicitada com o conjunto de volumes de armazenamento adicional, o Gerenciamento de Serviços do SAP HANA do Azure anexa o conjunto adicional de volumes como um destino na réplica de produção para a unidade do SAP HANA nas Instâncias Grandes na qual você está executando a instância do TST HANA. Para essa finalidade, é necessário fornecer o SID da sua instância do HANA de produção. Depois que o Gerenciamento de Serviços do SAP HANA no Azure confirmar a anexação desses volumes, será necessário montar esses volumes na unidade da Instância Grande do HANA.

![Próxima etapa de configuração da recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A próxima etapa é instalar a segunda instância do SAP HANA na unidade do SAP HANA nas Instâncias Grandes na região do Azure de recuperação de desastre, em que você executa a instância do TST HANA. A instância do SAP HANA recém-instalada precisa ter o mesmo SID. Os usuários criados precisam ter os mesmos UID e ID de grupo que a instância de produção. Leia [Backup e restauração](hana-backup-restore.md) para obter detalhes. Se a instalação for bem-sucedida, será necessário:

- Execute a etapa 2 da preparação de instantâneo do armazenamento descrita em [Backup e restauração](hana-backup-restore.md).
- Crie uma chave pública para a unidade de DR da unidade do SAP HANA nas Instâncias Grandes, caso ainda não tenha feito isso. Veja a etapa 3 da preparação de instantâneo do armazenamento descrita em [Backup e restauração](hana-backup-restore.md).
- Mantenha *HANABackupCustomerDetails.txt* com a nova instância do HANA e teste se a conectividade com o armazenamento funciona corretamente.  
- Pare a instância do SAP HANA instalada recentemente na unidade do SAP HANA nas Instâncias Grandes na região do Azure de DR.
- Desmonte esses volumes PRD e entre em contato com o Gerenciamento de Serviços do SAP HANA no Azure. Os volumes não podem permanecer montados na unidade, pois eles não podem ser acessados enquanto estão em funcionamento como o destino de replicação de armazenamento.  

![Etapa de configuração da recuperação de desastre antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A equipe de operações estabelece o relacionamento de replicação entre os volumes PRD na região do Azure de produção e os volumes PRD na região do Azure de recuperação de desastre.

>[!IMPORTANT]
>O volume /hana/log não é replicado porque não é necessário restaurar o banco de dados do SAP HANA replicado para um estado consistente no local de recuperação de desastre.

Em seguida, configure ou ajuste a agenda de backup do instantâneo de armazenamento para chegar ao RTO e RPO no caso de desastre. Para minimizar o objetivo de ponto de recuperação, defina os intervalos de replicação a seguir no serviço de Instância Grande do HANA:
- Para os volumes cobertos pelo instantâneo combinado (tipo de instantâneo **hana**), defina para replicar a cada 15 minutos para os destinos de volume de armazenamento equivalentes no site de recuperação de desastre.
- Para o volume de backup de log de transações (tipo de instantâneo **logs**), defina para replicar a cada 3 minutos para os destinos de volume de armazenamento equivalentes no site de recuperação de desastre.

Para minimizar o objetivo de ponto de recuperação, configure o seguinte:
- Execute um instantâneo de armazenamento do tipo **hana** (consulte "Etapa 7: Executar instantâneos") a cada 30 minutos a 1 hora.
- Execute backups de log de transações do SAP HANA a cada 5 minutos.
- Execute um instantâneo de armazenamento do tipo **logs** a cada 5-15 minutos. Com esse período de intervalo, você obtém um RPO de cerca de 15 a 25 minutos.

Com essa configuração, a sequência de backups de log de transações, os instantâneos de armazenamento e a replicação do volume de backup de log de transações do HANA e /hana/data e /hana/shared (inclui /usr/sap) podem ter a aparência dos dados mostrados neste gráfico:

 ![Relação entre um instantâneo de backup de log de transações e um espelho de ajuste em um eixo de tempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para alcançar um RPO ainda melhor, no caso de recuperação de desastre, você pode copiar os backups de log de transações do HANA do SAP HANA (Instâncias Grandes) do Azure e para outra região do Azure. Para obter essa redução adicional de RPO, execute as etapas a seguir:

1. Faça backup do log de transações do HANA com a maior frequência possível para /hana/logbackups.
1. Use rsync para copiar os backups de log de transações para as máquinas virtuais do Azure hospedadas pelo compartilhamento NFS. As VMs estão nas redes virtuais do Azure na região de produção do Azure e nas regiões da recuperação de desastre. É necessário conectar as redes virtuais do Azure ao circuito que se conecta as Instâncias Grandes HANA de produção ao Azure. Consulte o gráfico na seção [Considerações de rede para recuperação de desastre com Instâncias Grandes HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances). 
1. Mantenha os backups de log de transações na região da VM anexada ao armazenamento exportado de NFS.
1. No caso de um failover de desastre, complemente os backups de log de transações que encontrar no volume /hana/logbackups com backups de log de transações mais recentes no compartilhamento de NFS no site de recuperação de desastre. 
1. Inicie um backup de log de transações para restaurar para o backup mais recente que foi salvo na região de recuperação de desastre.

Quando as operações do SAP HANA nas Instâncias Grandes confirmam a configuração do relacionamento de replicação e você inicia os backups de instantâneos de armazenamento de execução, a replicação de dados é iniciada.

![Etapa de configuração da recuperação de desastre antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Conforme a replicação avança, os instantâneos nos volumes PRD nas regiões do Azure de recuperação de desastre não são restaurados. Eles só são armazenados. Se os volumes forem montados nesse estado, eles representarão o estado em que você desmontou esses volumes após a instância do PRD SAP HANA ter sido instalada na unidade de servidor na região do Azure de recuperação de desastre. Eles também representam backups de armazenamento que ainda não foram restaurados.

No caso de um failover, também é possível optar por restaurar para um instantâneo de armazenamento mais antigo em vez do instantâneo de armazenamento mais recente.

## <a name="next-steps"></a>Próximas etapas

- Veja [Procedimento de failover de recuperação de desastre](hana-failover-procedure.md).
