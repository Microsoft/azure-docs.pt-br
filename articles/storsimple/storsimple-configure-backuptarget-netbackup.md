---
title: StorSimple série 8000 como um destino de backup com o NetBackup | Microsoft Docs
description: Descreve a configuração de destino de Backup do StorSimple com o Veritas NetBackup.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: hkanna
ms.openlocfilehash: 17428405a0be45854a2eaaef831864f529ed145a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724907"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple como um destino de backup com o NetBackup

## <a name="overview"></a>Visão geral

O Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. O StorSimple atende às complexidades do crescimento exponencial de dados, usando uma conta de armazenamento do Azure como uma extensão da solução local e dos dados postos em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Neste artigo, abordamos a integração do StorSimple com o Veritas NetBackup e as práticas recomendadas para a integração de ambas as soluções. Também apresentamos recomendações sobre como configurar o Veritas NetBackup para se integrar melhor ao StorSimple. Seguimos as orientações da Veritas quanto às práticas recomendadas, arquitetos e administradores de backup para obter a melhor maneira de configurar o Veritas NetBackup para atender aos requisitos de backup individual e os SLAs (contratos de nível de serviço).

Embora ilustre os principais conceitos e as etapas de configuração, este artigo não é um guia passo a passo de configuração ou instalação. Supomos que a infraestrutura e os componentes básicos estão funcionando e prontos para dar suporte aos conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isso?

As informações nesse artigo são mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que conheçam os conceitos de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Veritas NetBackup.

### <a name="supported-versions"></a>Versões com suporte

-   NetBackup 7.7.x e versões posteriores
-   [StorSimple Atualização 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que o StorSimple como um destino de backup?

O StorSimple é uma boa escolha para um destino de backup porque:

-   Ele oferece armazenamento local padrão para ser usado por aplicativos de backup como um destino de backup rápido sem alterações. Você também pode usar o StorSimple para uma restauração rápida de backups recentes.
-   Sua disposição em camadas na nuvem está totalmente integrada a uma conta de armazenamento em nuvem do Azure para usar o econômico Armazenamento do Azure.
-   Ele oferece armazenamento externo para recuperação de desastre automaticamente.

## <a name="key-concepts"></a>Principais conceitos

Assim como acontece com qualquer solução de armazenamento, o sucesso depende de uma avaliação cuidadosa do desempenho do armazenamento da solução, dos SLAs, da taxa de alteração e das necessidades de aumento da capacidade. A ideia principal é que, com a introdução de uma camada de nuvem, seus horários de acesso e produtividade na nuvem terão um papel fundamental na capacidade do StorSimple de fazer seu trabalho.

O StorSimple foi projetado para oferecer armazenamento a aplicativos que operam em um conjunto de dados de trabalho bem definido (dados mais acessados). Nesse modelo, o conjunto de dados de trabalho é armazenado nas camadas locais e o conjunto de dados que não é de trabalho/menos acessado/arquivado restante é colocado em camadas na nuvem. Esse modelo é representado na figura a seguir. A linha verde quase reta representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa o volume total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha verde reta e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

**Disposição em camadas do StorSimple**
![Diagrama da disposição em camadas do StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Com essa arquitetura em mente, você descobrirá que o StorSimple é ideal para operar como um destino de backup. Você pode usar o StorSimple para:
-   Realize suas restaurações mais frequentes do conjunto de dados de trabalho local.
-   Utilize a nuvem para recuperação de desastre externa e para dados mais antigos, nos quais as restaurações são menos frequentes.

## <a name="storsimple-benefits"></a>Benefícios do StorSimple

O StorSimple oferece uma solução local que está totalmente integrada ao Microsoft Azure, aproveitando as vantagens do acesso contínuo ao armazenamento local e em nuvem.

O StorSimple usa uma disposição em camadas automática entre o dispositivo local, que contém armazenamentos SSD (dispositivos de estado sólido), SAS (SCSI) anexado em série e Armazenamento do Azure. Camadas automática mantém os dados acessados com frequência no local, em camadas SSD e SAS. Ele move dados raramente acessados para o Armazenamento do Azure.

O StorSimple oferece os seguintes benefícios:

-   Algoritmos de eliminação de duplicação e compactação exclusivos que usam a nuvem para alcançar níveis de eliminação de duplicação sem precedentes
-   Alta disponibilidade
-   Replicação geográfica usando a replicação geográfica do Azure
-   Integração do Azure
-   Criptografia de dados na nuvem
-   Aprimoramento da recuperação de desastres e conformidade

Embora o StorSimple apresente dois cenários de implantação principais (destinos de backup primário e secundário), basicamente, ele é um dispositivo de armazenamento em bloco simples. O StorSimple realiza toda a compactação e eliminação de duplicação. Ele envia e recupera perfeitamente os dados entre a nuvem e o sistema de aplicativos e arquivos.

Para saber mais sobre o StorSimple, confira [StorSimple série 8000: solução de armazenamento de nuvem híbrida](storsimple-overview.md). Além disso, você pode examinar as [especificações técnicas do StorSimple série 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Há suporte para o uso do dispositivo StorSimple como destino de backup apenas para StorSimple 8000 Atualização 3 e versões posteriores.

## <a name="architecture-overview"></a>Visão geral da arquitetura

As tabelas a seguir demonstram as diretrizes iniciais do modelo à arquitetura do dispositivo.

**Capacidades do StorSimple de armazenamento local e em nuvem**

| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidade de armazenamento em nuvem | &gt;200 TiB\* | &gt;500 TiB\* |

\*O tamanho de armazenamento não assume nenhuma eliminação de duplicação ou compactação.

**Capacidade do StorSimple para backups primários e secundários**

| Cenário de backup  | Capacidade de armazenamento local  | Capacidade de armazenamento em nuvem  |
|---|---|---|
| Backup principal  | Backups recentes armazenados no armazenamento local para recuperação rápida para atender o RPO (objetivo de ponto de recuperação) | O histórico de backup (RPO) cabe na capacidade da nuvem |
| Backup secundário | Uma cópia secundária dos dados de backup pode ser armazenada na capacidade da nuvem  | N/D  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de backup principal

Nesse cenário, os volumes do StorSimple são apresentados para o aplicativo de backup como o único repositório para backups. A figura a seguir mostra uma arquitetura da solução na qual todos os backups usam volumes em camadas do StorSimple para backups e restaurações.

![StorSimple como um diagrama lógico de destino de backup principal](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.
2.  O servidor de backup grava os dados nos volumes em camadas do StorSimple.
3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.
4.  Um script de instantâneo dispara o Snapshot Manager do StorSimple (iniciar ou excluir).
5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino principal

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção de longo prazo ou arquivamento.

A figura a seguir mostra uma arquitetura em que os backups e restaurações iniciais destinam-se a um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple conforme uma determinada agenda.

É importante dimensionar o volume de alto desempenho para lidar com os requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.
2.  O servidor de backup grava dados no armazenamento de alto desempenho.
3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.
4.  O servidor de backup copia os backups para o StorSimple com base em uma política de retenção.
5.  Um script de instantâneo dispara o Snapshot Manager do StorSimple (iniciar ou excluir).
6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implantar a solução

Implantar essa solução exige três etapas:
1. Prepare a infraestrutura de rede.
2. Implante o dispositivo StorSimple como um destino de backup.
3. Implante o Veritas NetBackup.

Cada etapa é discutida em detalhes nas seções a seguir.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem do Azure, ele requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada em operações como instantâneos de nuvem, gerenciamento de dados, transferência de metadados e disposição de dados menos acessados e mais antigos em camadas no armazenamento em nuvem do Azure.

Para que a solução tenha o desempenho ideal, sugerimos seguir as práticas recomendadas de rede abaixo:

-   O link que conecta a disposição em camadas do StorSimple ao Azure deve atender aos requisitos de largura de banda. Para fazer isso, aplique o nível de QoS (Qualidade de Serviço) adequado para sua infraestrutura, mudando para corresponder ao RTO (Objetivo de Tempo de Recuperação) e SLAs de RTO.

-   As latências máximas de acesso ao Armazenamento de Blobs do Azure devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para ver diretrizes passo a passo de implantação do StorSimple, acesse [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Implantar o NetBackup

Para ver um guia de implantação passo a passo do NetBackup 7.7.x, confira a [Documentação do NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta seção, demonstraremos alguns exemplos de configuração. Os exemplos e recomendações a seguir ilustram a implementação mais básica e fundamental. Essa implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="set-up-storsimple"></a>Configurar o StorSimple

| Tarefas de implantação do StorSimple  | Comentários adicionais |
|---|---|
| Implantação do seu dispositivo StorSimple local. | Versões com suporte: atualização 3 e versões posteriores. |
| Ative o destino de backup. | Use estes comandos para ativar ou desligar o modo de destino de backup e obter o status. Para obter mais informações, veja [Conectar remotamente a um dispositivo StorSimple](storsimple-remote-connect.md).</br> Para ativar o modo de backup: `Set-HCSBackupApplianceMode -enable`. </br> Para desligar o modo de backup: `Set-HCSBackupApplianceMode -disable`. </br> Para obter o estado atual das configurações de modo de backup: `Get-HCSBackupApplianceMode`. |
| Crie um contêiner de volume comum para o seu volume que armazena os dados de backup. Todos os dados de um contêiner de volume passam por eliminação de duplicação. | Contêineres de volume do StorSimple definem domínios de eliminação de duplicação.  |
| Crie os volumes do StorSimple. | Crie volumes com tamanhos o mais próximo possível do uso antecipado, já que o tamanho do volume afeta a duração do instantâneo na nuvem. Para obter informações sobre como dimensionar um volume, leia sobre as [políticas de retenção](#retention-policies).</br> </br> Use os volumes em camadas do StorSimple e marque a caixa de seleção **Usar este volume para dados de arquivamento acessados com menos frequência**. </br> Não há suporte para usar volumes afixados localmente. |
| Crie uma política de backup do StorSimple exclusiva para todos os volumes de destino de backup. | Uma política de backup do StorSimple define o grupo de consistência do volume. |
| Desabilite a agenda à medida que os instantâneos expiram. | Os instantâneos são disparados como uma operação de pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do host

Configure o armazenamento do servidor de backup do host de acordo com estas orientações:  

- Não use volumes estendidos (criados pelo Gerenciador de Disco do Windows), pois não há suporte para eles.
- Formate os volumes usando o NTFS com tamanho de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente ao servidor do NetBackup.
    - Use o iSCSI para servidores físicos.
    - Use discos de passagem para servidores virtuais.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Práticas recomendadas para StorSimple e NetBackup

Configure sua solução de acordo com as diretrizes indicadas nas próximas seções.

### <a name="operating-system-best-practices"></a>Práticas recomendadas do sistema operacional

- Desabilite a criptografia do Windows Server e a eliminação de duplicação do sistema de arquivos NTFS.
- Desabilite a desfragmentação do Windows Server em volumes do StorSimple.
- Desabilite a indexação do Windows Server em volumes do StorSimple.
- Execute uma verificação antivírus no host de origem (não nos volumes do StorSimple).
- Desligue a [Manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) padrão no Gerenciador de Tarefas. Faça isso de uma das seguintes maneiras:
  - Desligue o Configurador de Manutenção no Agendador de Tarefas do Windows.
  - Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de baixar o PsExec, execute o Windows PowerShell como administrador e digite:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Práticas recomendadas do StorSimple

-   Verifique se o dispositivo do StorSimple está atualizado para a [Atualização 3 ou posterior](storsimple-install-update-3.md).
-   Isolar tráfego de iSCSI e de nuvem. Use conexões iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.
-   Verifique se o dispositivo do StorSimple é um destino de backup dedicado. Não há suporte para cargas de trabalho mistas porque elas afetam o RTO e RPO.

### <a name="netbackup-best-practices"></a>Práticas recomendadas do NetBackup

-   O banco de dados do NetBackup deve ser local para o servidor e não residir em um volume do StorSimple.
-   Para recuperação de desastre, faça backup do banco de dados NetBackup em um volume StorSimple.
-   Damos suporte a backups completos e incrementais do NetBackup (também chamados de backups incrementais diferenciais no NetBackup) nesta solução. Recomendamos que você não use backups incrementais sintéticos e cumulativos.
-   Arquivos de dados de backup devem conter apenas os dados de um trabalho específico. Por exemplo, não são permitidos acréscimos de mídia em vários trabalhos diferentes.

Para obter as definições mais recentes do NetBackup e as práticas recomendadas sobre como implementar esses requisitos, consulte a documentação do NetBackup em [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de backup mais comuns é a GFS (Grandfather, Father, and Son). Em uma política GFS, um backup incremental é executado diariamente e backups completos são realizados semanalmente e mensalmente. Essa política resulta em seis volumes em camada StorSimple: um volume contém os backups semanais, mensais e anuais completos; os outros cinco volumes armazenam backups incrementais diários.

No exemplo a seguir, usamos uma rotação de GFS. O exemplo pressupõe o seguinte:

-   Serão usados dados compactados ou com eliminação de duplicação.
-   Os backups completos têm 1 TiB cada.
-   Backups incrementais diários têm 500 GiB cada.
-   Quatro backups semanais são mantidos por um mês.
-   Doze backups mensais mantidos por um ano.
-   Um backup anual é mantido por 10 anos.

Com base nas premissas anteriores, crie um volume em camadas StorSimple de 26 TiB para os backups completos mensais e anuais. Crie um volume em camadas StorSimple de 5 TiB para cada backup diário incremental.

| Retenção de tipo de backup | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Completo semanal | 1 | 4  | 4 |
| Incremental diário | 0,5 | 20 (ciclos, igual ao número de semanas por mês) | 12 (2 para a cota adicional) |
| Mensal completo | 1 | 12 | 12 |
| Anual completo | 1  | 10 | 10 |
| Requisito de GFS |   | 38 |   |
| Cota adicional  | 4  |   | Requisito total de GFS 42  |

\* O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender aos requisitos da política de backup.

## <a name="set-up-netbackup-storage"></a>Configurar o armazenamento do NetBackup

### <a name="to-set-up-netbackup-storage"></a>Configurar o armazenamento do NetBackup

1.  No Console de Administração do NetBackup, selecione **Gerenciamento de Dispositivos e Mídia** > **Dispositivos** > **Pools de Discos**. No Assistente de Configuração do Pool de Disco, selecione o tipo de servidor de armazenamento **AdvancedDisk**e, em seguida, selecione **Avançar**.

    ![Console de Administração do NetBackup, Assistente de Configuração do Pool de Disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selecione seu servidor e clique em **Avançar**.

    ![Console de Administração do NetBackup, selecionar o servidor](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selecione seu volume StorSimple.

    ![Console de Administração do NetBackup, selecionar o disco de volume do StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Insira um nome para o destino de backup e selecione **Avançar** > **Avançar** para concluir o assistente.

5.  Examine as configurações e selecione **Concluir**.

6.  No final de cada atribuição de volume, altere as configurações do dispositivo de armazenamento para corresponder àquelas recomendadas nas [Práticas recomendadas para o StorSimple e o NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Repita as etapas 1 a 6 até concluir a atribuição de volumes do StorSimple.

    ![Console de Administração do NetBackup, configuração do disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como destino de backup principal

> [!NOTE]
> As restaurações de dados de backups que foram dispostos em camadas na nuvem ocorrem com velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco completo no sábado e os backups incrementais são mapeados para discos incrementais de segunda a sexta-feira. Todos os backups e as restaurações são realizados de um volume em camadas do StorSimple.

![Diagrama lógico de configuração de destino de backup principal](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Exemplo de agendamento do StorSimple destino de backup principal GFS

Veja esta exemplo de uma agenda de rotação GFS de quatro semanas, mensal e anual:

| Frequência/tipo de backup | Completo | Incremental (1 a 5 dias)  |   
|---|---|---|
| Semanal (1 a 4 semanas) | Sábado | Segunda a sexta-feira |
| Mensal  | Sábado  |   |
| Anual | Sábado  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do NetBackup

A sequência a seguir pressupõe que NetBackup e o host de destino estão configurados de acordo com as diretrizes de agente do NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Para atribuir volumes do StorSimple a um trabalho de backup do NetBackup

1. No Console de Administração do NetBackup, selecione **Gerenciamento do NetBackup**, clique com botão direito do mouse em **Políticas** e selecione **Nova Política**.

   ![Console de Administração do NetBackup, criar uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Na caixa de diálogo **Adicionar uma Nova Política**, digite um nome para a política e marque a caixa de seleção **Usar Assistente de Configuração de Política**. Selecione **OK**.

   ![Console de Administração do NetBackup, caixa de diálogo Adicionar uma Nova Política](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. No Assistente de Configuração de Política de Backup, escolha o tipo de backup desejado e selecione **Avançar**.

   ![Console de Administração do NetBackup, selecionar tipo de backup](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Para definir o tipo de política, selecione **Standard** e clique em **Avançar**.

   ![Console de Administração do NetBackup, selecionar tipo de política](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Selecione o host, marque a caixa de seleção **Detectar o sistema operacional cliente** e selecione **Adicionar**. Selecione **Avançar**.

   ![Console de Administração do NetBackup, listar clientes em uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Selecione as unidades que você deseja incluir no backup.

   ![Console de Administração do NetBackup, seleções de backup para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Selecione os valores de retenção e frequência que atendem aos seus requisitos de rotação de backup.

   ![Console de Administração do NetBackup, frequência e rotação de backup para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Selecione **Avançar** > **Avançar** > **Concluir**.  É possível modificar o agendamento depois que a política for criada.

9. Expanda a política que você criou e selecione **Agendamentos**.

   ![Console de Administração do NetBackup, agendamentos para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Clique com botão direito do mouse em **Differential-Inc**, selecione **Copiar para a cova** e clique em **OK**.

    ![Console de Administração do NetBackup, copiar agendamento para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Clique com o botão direito do mouse no agendamento recém-criado e selecione **Alterar**.

12. Na guia **Atributos**, marque a caixa de seleção **Substituir seleção de armazenamento de política** e selecione o volume para onde vão os backups incrementais de segunda-feira.

    ![Console de Administração do NetBackup, alterar o agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Na guia **Início da Janela**, selecione a janela de tempo para seus backups.

    ![Console de Administração do NetBackup, alterar o início da janela](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Selecione **OK**.

15. Repita as etapas 10 a 14 para cada backup incremental. Selecione o volume e agendamento apropriado para cada backup que você criar.

16. Clique com o botão direito do mouse no agendamento **Diferencial-Inc** e exclua-o.

17. Modifique o Agendamento completo para atender às suas necessidades de backup.

    ![Console de Administração do NetBackup, alterar o agendamento completo](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Altere a janela de início.

    ![Console de Administração do NetBackup, alterar o início da janela](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. A agenda final tem esta aparência:

    ![Console de Administração do NetBackup, agendamento final](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como destino de backup secundário

> [!NOTE]
>As restaurações de dados de backups que foram dispostos em camadas na nuvem ocorrem com velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (que não seja StorSimple) para servir como um cache temporário. Por exemplo, você pode usar um volume com matriz redundante de volume de discos independentes (RAID) para acomodar o espaço, entrada/saída (E/S) e a largura de banda. Recomendamos o uso de RAID 5, 50 e 10.

A figura a seguir mostra os volumes locais típicos para retenção de curto prazo (para o servidor) e de arquivamento para retenção de longo prazo. Nesse caso, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar seu volume RAID local (para o servidor) para que ele possa lidar com os requisitos de capacidade e desempenho da retenção de curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS de destino de backup secundário

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

A tabela a seguir mostra como configurar backups para execução no local e discos do StorSimple. Ele inclui os requisitos de capacidade total e individuais.

### <a name="backup-configuration-and-capacity-requirements"></a>Configuração de backup e requisitos de capacidade

| Tipo de backup e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2 a 4 |Disco StorSimple (longo prazo) | 1 | 4 | 4 |
| Mensal completo |Disco StorSimple (longo prazo) | 1 | 12 | 12 |
| Anual completo |Disco StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes do GFS |  |  |  | 18\*|

A capacidade total do \* inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Exemplo de agenda do GFS: Agenda semanal, mensal e anual da rotação do GFS

| Semana | Completo | Incremental dia 1 | Incremental dia 2 | Incremental dia 3 | Incremental dia 4 | Incremental dia 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Mensal | StorSimple mensal |   |   |   |   |   |
| Anual | StorSimple anual  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Atribua volumes do StorSimple a um trabalho de arquivamento e duplicação do NetBackup

Como o NetBackup oferece uma ampla gama de opções de armazenamento e gerenciamento de mídia, é recomendável consultar a Veritas ou o arquiteto do NetBackup para avaliar corretamente os requisitos de SLP (política do ciclo de vida de armazenamento).

Depois de definir os pools de disco iniciais, você precisa definir três políticas de ciclo de vida de armazenamento adicionais, compondo um total de quatro política:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Para atribuir os volumes do StorSimple a um trabalho de arquivamento e duplicação do NetBackup

1. No Console de Administração do NetBackup, selecione **Armazenamento** > **Políticas de Ciclo de Vida de Armazenamento** > **Nova Política de Ciclo de Vida de Armazenamento**.

   ![Console de Administração do NetBackup, nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Insira um nome para o instantâneo e selecione **Adicionar**.

3. Na caixa de diálogo **Nova Operação**, na guia **Propriedades**, para **Operação**, selecione **Backup**. Selecione os valores desejados para o **Armazenamento de destino**, o **Tipo de retenção** e o **Período de retenção**. Selecione **OK**.

   ![Console de Administração do NetBackup, caixa de diálogo Nova Operação](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Isso define a primeira operação e repositório de backup.

4. Selecione para realçar a operação anterior e escolha **Adicionar**. Na caixa de diálogo **Alterar a Operação de Armazenamento**, selecione os valores desejados para **Armazenamento de destino**, **Tipo de retenção** e **Período de retenção**.

   ![Console de Administração do NetBackup, caixa de diálogo Alterar Operação de Armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Selecione para realçar a operação anterior e escolha **Adicionar**. Na caixa de diálogo **Nova Política de Ciclo de Vida de Armazenamento**, adicione backups mensais por um ano.

   ![Console de Administração do NetBackup, caixa de diálogo Nova Política do Ciclo de Vida de Armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Repita as etapas 4 e 5 até criar a política de retenção de SLP abrangente o suficiente para atender às suas necessidades.

   ![Console de Administração do NetBackup, Adicionar políticas na caixa de diálogo Nova Política do Ciclo de Vida de Armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Quando terminar de definir sua política de retenção de SLP, em **Política**, defina uma política de backup seguindo as etapas detalhadas em [Atribuir volumes StorSimple a um trabalho de backup do NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. Em **Agendamentos**, na caixa de diálogo **Alterar Agendamento**, clique com o botão direito do mouse em **Completo** e selecione **Alterar**.

   ![Console de Administração do NetBackup, caixa de diálogo Alterar Agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Marque a caixa de seleção **Substituir a seleção de armazenamento da política** e selecione a política de retenção SLP que você criou nas etapas 1 a 6.

   ![Console de Administração do NetBackup, substituir a seleção de armazenamento da política](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Selecione **OK** e repita o processo para o agendamento de backup incremental.

    ![Console de Administração do NetBackup, caixa de diálogo Alterar Agendamento para backups incrementais](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Retenção de tipo de backup | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Completo semanal |  1  |  4 | 4  |
| Incremental diário  | 0,5  | 20 (os ciclos são iguais ao número de semanas por mês) | 12 (2 para a cota adicional) |
| Mensal completo  | 1 | 12 | 12 |
| Anual completo | 1  | 10 | 10 |
| Requisito de GFS  |     |     | 38 |
| Cota adicional  | 4  |    | Requisito total de GFS 42 |

\* O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender aos requisitos da política de backup.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem do StorSimple protegem os dados que residem no dispositivo StorSimple. Criar um instantâneo de nuvem é equivalente a enviar de fitas de backup local para instalações externas. Se você usar o armazenamento com redundância geográfica do Azure, criar um instantâneo de nuvem será equivalente a enviar fitas de backup para vários sites. Se você precisar restaurar um dispositivo depois de um desastre, poderá deixar outro dispositivo StorSimple online e executar um failover. Após o failover, você poderia acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir descreve como criar um script curto para iniciar e excluir instantâneos de nuvem do StorSimple durante o pós-processamento de backup.

> [!NOTE]
> Os instantâneos criados manualmente ou por meio de programação não seguem a política de expiração de instantâneos do StorSimple. Esses instantâneos devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e excluir instantâneos de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e de retenção de dados antes de excluir um instantâneo do StorSimple. Para saber mais sobre como executar um script pós-backup, consulte a [documentação do NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Ciclo de vida de backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script deve ter acesso aos recursos de nuvem do Azure.
-   A conta de usuário deve ter as permissões necessárias.
-   Uma política de backup do StorSimple com os volumes StorSimple associados deve ser configurada, mas não ativada.
-   Você precisará do nome do recurso, chave de registro, nome do dispositivo e ID da política de backup do StorSimple.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou excluir um instantâneo de nuvem

1. [Instale o Azure PowerShell](/powershell/azure/overview).
2. Baixe e instale o script [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) do PowerShell.
3. No servidor que executa o script, execute o PowerShell como administrador. Execute o script com `-WhatIf $true` para ver as alterações que serão feitas pelo script. Depois que a validação for concluída, passe `-WhatIf $false`. Execute o comando abaixo:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adicione o script ao trabalho de backup no NetBackup. Para fazer isso, edite suas os comandos de pré e pós-processamento das opções de trabalho do NetBackup.

> [!NOTE]
> Recomendamos que você execute a política de backup de instantâneo de nuvem do StorSimple como um script pós-processamento no fim do seu trabalho de backup diário. Para saber mais sobre como fazer backup e restaurar seu ambiente de aplicativo de backup para ajudar a atender ao RPO e RTO, consulte seu arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origem de restauração

As restaurações de um dispositivo StorSimple funcionam como qualquer dispositivo de armazenamento em bloco. Restaurações de dados que estão em camadas na nuvem ocorrem em velocidades de nuvem. Para dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo. Para obter informações sobre como executar uma restauração, consulte a [documentação do NetBackup](http://www.veritas.com/docs/000094423). É recomendável estar em conformidade com as práticas recomendadas para restauração do NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o StorSimple Cloud Appliance não tem suporte como um destino de restauração.

Um desastre pode ser causado por uma variedade de fatores. A tabela a seguir lista cenários comuns de recuperação de desastre.

| Cenário | Impacto | Como recuperar | Observações |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauração foram interrompidas. | Substitua o dispositivo com falha e execute [Failover e recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md). | Se você precisar realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho totais serão recuperados da nuvem para o novo dispositivo. Todas as operações ocorrem em velocidades de nuvem. O processo de nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor NetBackup | As operações de backup e restauração foram interrompidas. | Recompile o servidor de backup e execute a restauração do banco de dados. | Você deve recompile ou restaurar o servidor NetBackup no local de recuperação de desastre. Restaure o banco de dados para o ponto mais recente. Se o banco de dados restaurado do NetBackup não estiver sincronizado com os trabalhos de backup mais recentes, será necessário fazer a indexação e a catalogação. Esse processo de nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local. Isso torna tudo ainda mais demorado. |
| Falha do site que resulta na perda do servidor de Backup e do StorSimple | As operações de backup e restauração foram interrompidas. | Restaure o StorSimple primeiro e depois restaure o NetBackup. | Restaure o StorSimple primeiro e depois restaure o NetBackup. Se você precisar realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho completos serão recuperados da nuvem para o novo dispositivo. Todas as operações ocorrem em velocidades de nuvem. |

## <a name="references"></a>Referências

Os documentos a seguir foram mencionados neste artigo:

- [Configuração de Multipath I/O de StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: provisionamento fino](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas compartilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [restaurar de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Sobre mais sobre como executar [failover e recuperação de desastre no dispositivo](storsimple-device-failover-disaster-recovery.md).
