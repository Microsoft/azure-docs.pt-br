---
title: StorSimple série 8000 como um destino de backup com o Backup Exec | Microsoft Docs
description: Descreve a configuração de destino de backup do StorSimple com o Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: e11d541f0450c0de4ba6d60f889fc7471b1fa1aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724122"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>O StorSimple como destino de backup com o Backup Exec

## <a name="overview"></a>Visão geral

O Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. O StorSimple atende às complexidades do crescimento exponencial de dados, usando uma conta de armazenamento do Azure como uma extensão da solução local e dos dados postos em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Neste artigo, abordamos a integração do StorSimple com o Veritas Backup Exec e as práticas recomendadas para a integração de ambas as soluções. Também apresentamos recomendações sobre como configurar o Backup Exec para se integrar melhor ao StorSimple. Seguimos as orientações da Veritas quanto às práticas recomendadas, arquitetos e administradores de backup para obter a melhor maneira de configurar o Backup Exec para atender aos requisitos de backup individual e os SLAs (contratos de nível de serviço).

Embora ilustre os principais conceitos e as etapas de configuração, este artigo não é um guia passo a passo de configuração ou instalação. Supomos que a infraestrutura e os componentes básicos estão funcionando e prontos para dar suporte aos conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isso?

As informações nesse artigo são mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que conheçam os conceitos de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Backup Exec.

## <a name="supported-versions"></a>Versões com suporte

-   [Backup Exec 16 e versões posteriores](http://backupexec.com/compatibility)
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
![Diagrama da disposição em camadas do StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

![StorSimple como um diagrama lógico de destino de backup principal](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.
2.  O servidor de backup grava os dados nos volumes em camadas do StorSimple.
3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.
4.  Um script de instantâneo dispara o Snapshot Manager na nuvem do StorSimple (iniciar ou excluir).
5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.


### <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino principal

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção de longo prazo ou arquivamento.

A figura a seguir mostra uma arquitetura em que os backups e restaurações iniciais destinam-se a um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple conforme uma determinada agenda.

É importante dimensionar o volume de alto desempenho para lidar com os requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.
2.  O servidor de backup grava dados no armazenamento de alto desempenho.
3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.
4.  O servidor de backup copia os backups para o StorSimple com base em uma política de retenção.
5.  Um script de instantâneo dispara o Snapshot Manager na nuvem do StorSimple (iniciar ou excluir).
6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implantar a solução

Implantar a solução exige três etapas:
1. Prepare a infraestrutura de rede.
2. Implante o dispositivo StorSimple como um destino de backup.
3. Implantar Backup Exec.

Cada etapa é discutida em detalhes nas seções a seguir.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem do Azure, ele requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada em operações como instantâneos de nuvem, gerenciamento, transferência de metadados e disposição de dados menos acessados e mais antigos em camadas no armazenamento em nuvem do Azure.

Para que a solução tenha o desempenho ideal, sugerimos seguir as práticas recomendadas de rede abaixo:

-   O link que conecta a disposição em camadas do seu StorSimple ao Azure deve atender aos requisitos de largura de banda. Faça isso aplicando o nível de QoS (Qualidade de Serviço) necessário para sua infraestrutura, mudando para corresponder ao RPO e SLAs de RTO (Objetivo de Tempo de Recuperação).
-   As latências máximas de acesso ao Armazenamento de Blobs do Azure devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para ver diretrizes passo a passo de implantação do StorSimple, acesse [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Implantar Backup Exec

Para ver as práticas recomendadas de instalação do Backup Exec, veja [Práticas recomendadas para a instalação do Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

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

- Não use volumes estendidos (criados pelo Gerenciamento de Disco do Windows). Não há suporte para discos estendidos.
- Formate os volumes usando o NTFS com tamanho de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente ao servidor do Backup Exec.
    - Use o iSCSI para servidores físicos.
    - Use discos de passagem para servidores virtuais.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Práticas recomendadas para StorSimple e Backup Exec

Configure sua solução de acordo com as diretrizes indicadas nas seções a seguir.

### <a name="operating-system-best-practices"></a>Práticas recomendadas do sistema operacional

- Desabilite a criptografia do Windows Server e a eliminação de duplicação do sistema de arquivos NTFS.
- Desabilite a desfragmentação do Windows Server em volumes do StorSimple.
- Desabilite a indexação do Windows Server em volumes do StorSimple.
- Execute uma verificação antivírus no host de origem (não nos volumes do StorSimple).
- Desligue a [Manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) padrão no Gerenciador de Tarefas. Faça isso de uma das seguintes maneiras:
  - Desligue o Configurador de Manutenção no Agendador de Tarefas do Windows.
  - Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de baixar o PsExec, execute o Azure PowerShell como administrador e digite:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Práticas recomendadas do StorSimple

  -   Verifique se o dispositivo do StorSimple está atualizado para a [Atualização 3 ou posterior](storsimple-install-update-3.md).
  -   Isolar tráfego de iSCSI e de nuvem. Use conexões iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.
  -   Verifique se o dispositivo do StorSimple é um destino de backup dedicado. Não há suporte para cargas de trabalho mistas porque elas afetam o RTO e RPO.

### <a name="backup-exec-best-practices"></a>Práticas recomendadas de Backup Exec

-   O Backup Exec deve ser instalado em uma unidade local do servidor e não em um volume StorSimple.
-   Defina as **operações de gravação simultâneas** do armazenamento do Backup Exec para o máximo permitido.
    -   Defina **tamanho do buffer e de bloco** do armazenamento do Backup Exec como 512 KB.
    -   Ative a **leitura e gravação em buffer** do armazenamento do Backup Exec.
-   O StorSimple dá suporte a backups completos e incrementais do Backup Exec. Não é recomendável usar backups diferenciais e sintéticos.
-   Os arquivos de dados de backup devem conter apenas dados de um trabalho específico. Por exemplo, não são permitidos acréscimos de mídia em vários trabalhos diferentes.
-   Desabilite a verificação do trabalho. Se necessário, a verificação deve ser agendada após o trabalho de backup mais recente. É importante compreender que esse trabalho afeta sua janela de backup.
-   Selecione **Armazenamento** > **Seu disco** > **Detalhes** > **Propriedades**. Desligue **Pré-alocar espaço em disco**.

Para as definições mais recentes do Backup Exec e as práticas recomendadas sobre como implementar esses requisitos, consulte [o site da Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de backup mais comuns é a GFS (Grandfather, Father, and Son). Em uma política GFS, um backup incremental é executado diariamente e backups completos são realizados semanalmente e mensalmente. Essa política resulta em seis volumes do StorSimple em camadas. Um volume contém os backups completos semanais, mensais e anuais. Os outros cinco volumes armazenam backups incrementais diários.

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

## <a name="set-up-backup-exec-storage"></a>Configurar o armazenamento de Backup Exec

### <a name="to-set-up-backup-exec-storage"></a>Para configurar o armazenamento de Backup Exec

1.  No console de gerenciamento do Backup Exec, selecione **Armazenamento** > **Configurar Armazenamento** > **Armazenamento Baseado em Disco** > **Avançar**.

    ![Console de gerenciamento do Backup Exec, página de configuração de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selecione **Armazenamento em Disco** e selecione **Avançar**.

    ![Console de gerenciamento do Backup Exec, página selecionar armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Insira um nome representativo, por exemplo, **Sábado Completo** e uma descrição. Selecione **Avançar**.

    ![Console de gerenciamento do Backup Exec, página de nome e descrição](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selecione o disco no qual você deseja criar o dispositivo de armazenamento em disco e selecione **Avançar**.

    ![Console de gerenciamento do Backup Exec, tela de seleção do disco de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Aumente o número de operações de gravação para **16** e selecione **Avançar**.

    ![Console de gerenciamento do Backup Exec, página de configurações de operações de gravação simultâneas](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Examine as configurações e selecione **Concluir**.

    ![Console de gerenciamento do Backup Exec, página de resumo de configuração de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  No final de cada atribuição de volume, altere as configurações do dispositivo de armazenamento para corresponder àquelas recomendadas nas [Práticas recomendadas para o StorSimple e o Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Console de gerenciamento do Backup Exec, página de configurações do dispositivo de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Repita as etapas 1 a 7 até concluir a atribuição de volumes do StorSimple para o Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como destino de backup principal

> [!NOTE]
> As restaurações de dados de um backup que foram dispostas em camadas na nuvem ocorrem em velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco completo no sábado e os backups incrementais são mapeados para discos incrementais de segunda a sexta-feira. Todos os backups e as restaurações são realizados de um volume em camadas do StorSimple.

![Diagrama lógico de configuração de destino de backup principal](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Exemplo de agendamento do StorSimple destino de backup principal GFS

Veja esta exemplo de uma agenda de rotação GFS de quatro semanas, mensal e anual:

| Frequência/tipo de backup | Completo | Incremental (1 a 5 dias)  |   
|---|---|---|
| Semanal (1 a 4 semanas) | Sábado | Segunda a sexta-feira |
| Mensal  | Sábado  |   |
| Anual | Sábado  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do Backup Exec

A sequência a seguir pressupõe que o Backup Exec e o host de destino estão configurados de acordo com as diretrizes do agente do Backup Exec.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Para atribuir volumes do StorSimple a um trabalho de backup do Backup Exec

1.  No console de gerenciamento do Backup Exec, selecione **Host** > **Backup** > **Backup em Disco**.

    ![No console de gerenciamento do Backup Exec, selecione o host, backup e backup em disco](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Na caixa de diálogo **Propriedades de Definição de Backup**, em **Backup**, selecione **Editar**.

    ![Console de gerenciamento do Backup Exec, caixa de diálogo Propriedades de Definição de Backup](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Configure os backups completos e incrementais para que eles atendam aos requisitos de RPO e RTO e estejam em conformidade com as práticas recomendadas da Veritas.

4.  Na caixa de diálogo **Opções de Backup**, selecione **Armazenamento**.

    ![Console de gerenciamento do Backup Exec, caixa de diálogo Armazenamento das Opções de Backup](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Atribua volumes do StorSimple correspondentes ao o agendamento de backup.

    > [!NOTE]
    > **Compactação** e **Tipo de criptografia** são definidos como **Nenhum**.

6.  Em **Verificar**, marque a caixa de seleção **Não verificar os dados para este trabalho**. Usar esta opção pode afetar a disposição em camadas do StorSimple.

    > [!NOTE]
    > A desfragmentação, a indexação e a verificação em segundo plano afetam negativamente a disposição em camadas do StorSimple.

    ![Console de gerenciamento do Backup Exec, configurações de verificação das Opções de Backup](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Depois de configurar o restante das suas opções de backup para atender aos seus requisitos, selecione **OK** para concluir.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como destino de backup secundário

> [!NOTE]
>As restaurações de dados de backups que foram dispostos em camadas na nuvem ocorrem com velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (que não seja StorSimple) para servir como um cache temporário. Por exemplo, você pode usar um volume com matriz redundante de volume de discos independentes (RAID) para acomodar o espaço, entrada/saída (E/S) e a largura de banda. Recomendamos o uso de RAID 5, 50 e 10.

A figura a seguir mostra os volumes locais típicos para retenção de curto prazo (para o servidor) e de arquivamento para retenção de longo prazo. Nesse caso, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar seu volume RAID local (para o servidor) para que ele possa lidar com os requisitos de capacidade e desempenho da retenção de curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS de destino de backup secundário

![StorSimple como diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

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


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Atribuir volumes StorSimple a um trabalho de arquivamento e eliminação de duplicação do Backup Exec

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Para atribuir volumes StorSimple a um trabalho de arquivamento e eliminação de duplicação do Backup Exec

1.  No console de gerenciamento do Backup Exec, clique com o botão direito do mouse no trabalho que você deseja que realize arquivamento em um volume do StorSimple e selecione **Propriedades de Definição de Backup** > **Editar**.

    ![Console de gerenciamento do Backup Exec, guia Propriedades de Definição de Backup](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selecione **Adicionar Estágio** > **Duplicado em Disco** > **Editar**.

    ![Console de gerenciamento de Backup Exec, adicionar estágio](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Na caixa de diálogo **Opções de Duplicação**, selecione os valores que você deseja usar para **Fonte** e **Agendamento**.

    ![Console de gerenciamento, propriedades de definição de backup e opções de duplicação do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Na lista suspensa de **Armazenamento**, selecione o volume StorSimple no qual você deseja que o trabalho de arquivamento armazene os dados.

    ![Console de gerenciamento, propriedades de definição de backup e opções de duplicação do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Selecione **Verificar** e marque a caixa de seleção **Não verificar os dados para este trabalho**.

    ![Console de gerenciamento, propriedades de definição de backup e opções de duplicação do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selecione **OK**.

    ![Console de gerenciamento, propriedades de definição de backup e opções de duplicação do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Na coluna **Backup**, adicione um novo estágio. Para a fonte, use **incremental**. Para o destino, escolha o volume do StorSimple no qual o trabalho de backup está arquivado. Repita as etapas 1 a 6.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem do StorSimple protegem os dados que residem no dispositivo StorSimple. Criar um instantâneo de nuvem é equivalente a enviar de fitas de backup local para instalações externas. Se você usar o armazenamento com redundância geográfica do Azure, criar um instantâneo de nuvem será equivalente a enviar fitas de backup para vários sites. Se você precisar restaurar um dispositivo depois de um desastre, poderá deixar outro dispositivo StorSimple online e executar um failover. Após o failover, você poderia acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir descreve como criar um script curto para iniciar e excluir instantâneos de nuvem do StorSimple durante o pós-processamento de backup.

> [!NOTE]
> Os instantâneos criados manualmente ou por meio de programação não seguem a política de expiração de instantâneos do StorSimple. Esses instantâneos devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e excluir instantâneos de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e de retenção de dados antes de excluir um instantâneo do StorSimple. Para saber mais sobre como executar um script pós-backup, consulte a [documentação do Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Ciclo de vida de backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Adicione o script ao seu trabalho de backup no Backup Exec editando seus comandos de pré e pós-processamento das as opções do trabalho do Backup Exec.

   ![Console do Backup Exec, opções de backup, guia de comandos pré e pós-processamento](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Recomendamos que você execute a política de backup de instantâneo de nuvem do StorSimple como um script pós-processamento no fim do seu trabalho de backup diário. Para saber mais sobre como fazer backup e restaurar seu ambiente de aplicativo de backup para ajudar a atender ao RPO e RTO, consulte seu arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origem de restauração

As restaurações de um dispositivo StorSimple funcionam como qualquer dispositivo de armazenamento em bloco. Restaurações de dados que estão em camadas na nuvem ocorrem em velocidades de nuvem. Para dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo. Para obter informações sobre como executar uma restauração, consulte a documentação do Backup Exec. Sugerimos estar em conformidade com as práticas recomendadas de restauração do Backup Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o StorSimple Cloud Appliance não tem suporte como um destino de restauração.

Um desastre pode ser causado por uma variedade de fatores. A tabela a seguir lista cenários comuns de recuperação de desastre.

| Cenário | Impacto | Como recuperar | Observações |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauração foram interrompidas. | Substitua o dispositivo com falha e execute [Failover e recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md). | Se você precisar realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho totais serão recuperados da nuvem para o novo dispositivo. Todas as operações ocorrem em velocidades de nuvem. O processo de nova verificação da indexação e catalogação pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor Backup Exec | As operações de backup e restauração foram interrompidas. | Recrie o servidor de backup e execute a restauração do banco de dados conforme detalhado em [Como fazer backup e restauração manual do BEDB (banco de dados do Backup Exec)](http://www.veritas.com/docs/000041083). | Você deve recompilar ou restaurar o servidor Backup Exec no local de recuperação de desastre. Restaure o banco de dados para o ponto mais recente. Se o banco de dados restaurado do Backup Exec não está sincronizado com os trabalhos de backup mais recentes, é necessário fazer a indexação e a catalogação. Esse processo de nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local. Isso torna tudo ainda mais demorado. |
| Falha do site que resulta na perda do servidor de Backup e do StorSimple | As operações de backup e restauração foram interrompidas. | Restaure o StorSimple primeiro e depois o Backup Exec. | Restaure o StorSimple primeiro e depois o Backup Exec. Se você precisar realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho completos serão recuperados da nuvem para o novo dispositivo. Todas as operações ocorrem em velocidades de nuvem. |

## <a name="references"></a>Referências

Os documentos a seguir foram mencionados neste artigo:

- [Configuração de Multipath I/O de StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: provisionamento fino](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas compartilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [restaurar de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Sobre mais sobre como executar [failover e recuperação de desastre no dispositivo](storsimple-device-failover-disaster-recovery.md).
