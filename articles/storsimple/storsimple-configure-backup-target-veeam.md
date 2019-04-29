---
title: StorSimple série 8000 como um destino de backup com o Veeam | Microsoft Docs
description: Descreve a configuração de destino de backup do StorSimple com o Veeam.
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
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: e7659cca9081834d41f64ef0fbd8ea3686044bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633634"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple como um destino de backup com o Veeam

## <a name="overview"></a>Visão geral

O Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. O StorSimple atende às complexidades do crescimento exponencial de dados, usando uma conta de armazenamento do Azure como uma extensão da solução local e dos dados postos em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Neste artigo, abordamos a integração do StorSimple com o Veeam e as práticas recomendadas para a integração de ambas as soluções. Também apresentamos recomendações sobre como configurar o Veeam para se integrar melhor ao StorSimple. Seguimos as orientações da Veem quanto às práticas recomendadas, arquitetos e administradores de backup para obter a melhor maneira de configurar o Veeam para atender aos requisitos de backup individual e os SLAs (contratos de nível de serviço).

Embora ilustre os principais conceitos e as etapas de configuração, este artigo não é um guia passo a passo de configuração ou instalação. Supomos que a infraestrutura e os componentes básicos estão funcionando e prontos para dar suporte aos conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isso?

As informações nesse artigo são mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que conheçam os conceitos de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Veeam.

### <a name="supported-versions"></a>Versões com suporte

-   Veeam 9 e versões posteriores
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
![Diagrama da disposição em camadas do StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Capacidade de armazenamento | 8100 | 8600 |
|---|---|---|
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

![StorSimple como um diagrama lógico de destino de backup principal](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

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

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

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
3. Implantar Veeam.

Cada etapa é discutida em detalhes nas seções a seguir.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem do Azure, ele requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada em operações como instantâneos de nuvem, gerenciamento de dados, transferência de metadados e disposição de dados menos acessados e mais antigos em camadas no armazenamento em nuvem do Azure.

Para que a solução tenha o desempenho ideal, sugerimos seguir as práticas recomendadas de rede abaixo:

-   O link que conecta a disposição em camadas do seu StorSimple ao Azure deve atender aos requisitos de largura de banda. Faça isso aplicando o nível de QoS (Qualidade de Serviço) necessário para sua infraestrutura, mudando para corresponder ao RPO e SLAs de RTO (Objetivo de Tempo de Recuperação).
-   As latências máximas de acesso ao Armazenamento de Blobs do Azure devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para ver diretrizes passo a passo de implantação do StorSimple, acesse [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Implantar Veeam

Para consultar as práticas recomendadas de instalação do Veeam, veja [Práticas recomendadas de backup e replicação do Veeam](https://bp.veeam.expert/) e leia o guia do usuário na [Central de Ajuda da Veeam (Documentação Técnica)](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Não use volumes estendidos (criados pelo Gerenciamento de Disco do Windows). Não há suporte para volumes estendidos.
- Formate os volumes usando o NTFS com tamanho da unidade de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente ao servidor Veeam.
    - Use o iSCSI para servidores físicos.


## <a name="best-practices-for-storsimple-and-veeam"></a>Práticas recomendadas para StorSimple e Veeam

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

### <a name="veeam-best-practices"></a>Práticas recomendadas de Veeam

-   O banco de dados do Veeam deve ser local para o servidor e não residir em um volume do StorSimple.
-   Para recuperação de desastre, faça backup do banco de dados Veeam em um volume StorSimple.
-   Oferecemos suporte a backups completos e incrementais do Veeam para esta solução. É recomendável que você não use backups diferenciais e sintéticos.
-   Arquivos de dados de backup devem conter apenas os dados de um trabalho específico. Por exemplo, não são permitidos acréscimos de mídia em vários trabalhos diferentes.
-   Desligue a verificação do trabalho. Se necessário, a verificação deve ser agendada após o trabalho de backup mais recente. É importante compreender que esse trabalho afeta sua janela de backup.
-   Desative a pré-alocação de mídia.
-   Verifique se processamento em paralelo está habilitado.
-   Desligue a compactação.
-   Desligue a eliminação de duplicação no trabalho de backup.
-   Configure a otimização para **Destino LAN**.
-   Ative **Criar backup ativo completo** (a cada 2 semanas).
-   No repositório de backup, configure **Usar arquivos de backup por VM**.
-   Defina **Usar vários fluxos de upload por trabalho** para **8** (o máximo permitido é 16). Ajuste esse número para mais ou menos conforme a utilização da CPU no dispositivo StorSimple.

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

## <a name="set-up-veeam-storage"></a>Configurar o armazenamento de Veeam

### <a name="to-set-up-veeam-storage"></a>Para configurar o armazenamento de Veeam

1.  No console de Backup e Replicação do Veeam, em **Ferramentas de Repositório**, acesse **Infraestrutura de Backup**. Clique com o botão direito do mouse em **Repositórios de Backup** e selecione **Adicionar Repositório de Backup**.

    ![Console de gerenciamento do Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Na caixa de diálogo **Novo Repositório de Backup**, digite um nome e uma descrição para o repositório. Selecione **Avançar**.

    ![Console de gerenciamento do Veeam, página de nome e descrição](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Para o tipo, selecione **Servidor Microsoft Windows**. Selecione o servidor Veeam. Selecione **Avançar**.

    ![Console de gerenciamento Veeam, selecione o tipo de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Para especificar o **Local**, navegue e selecione o volume. Escolha a caixa de seleção **Limite máximo de tarefas simultâneas para:** e defina o valor para **4**. Isso garante que somente quatro discos virtuais serão processados simultaneamente enquanto cada VM (máquina virtual) é processada. Selecione o botão **Avançado**.

    ![Console de gerenciamento Veeam, selecione o volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Na caixa de diálogo **Configurações de Compatibilidade do Armazenamento**, marque a caixa de seleção **Usar arquivos de backup por VM**.

    ![Console de gerenciamento Veeam, configurações de compatibilidade do armazenamento](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Na caixa de diálogo **Novo Repositório de Backup**, marque a caixa de seleção **Habilitar o serviço vPower NFS no servidor de montagem (recomendado)**. Selecione **Avançar**.

    ![Console de gerenciamento do Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Examine as configurações e selecione **Avançar**.

    ![Console de gerenciamento do Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Um repositório é adicionado ao servidor Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como destino de backup principal

> [!IMPORTANT]
> As restaurações de dados de um backup que foram dispostas em camadas na nuvem ocorrem em velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco completo no sábado e os backups incrementais são mapeados para discos incrementais de segunda a sexta-feira. Todos os backups e as restaurações são realizados de um volume em camadas do StorSimple.

![Diagrama lógico de configuração de destino de backup principal](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Exemplo de agendamento do StorSimple destino de backup principal GFS

Veja esta exemplo de uma agenda de rotação GFS de quatro semanas, mensal e anual:

| Frequência/tipo de backup | Completo | Incremental (1 a 5 dias)  |   
|---|---|---|
| Semanal (1 a 4 semanas) | Sábado | Segunda a sexta-feira |
| Mensal  | Sábado  |   |
| Anual | Sábado  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do Veeam

Para o cenário de destino principal de backup, crie um trabalho diário com seu volume StorSimple do Veeam primário. Para um cenário de destino de backup secundário, crie um trabalho diário usando o DAS (Armazenamento Anexado Direto), NAS (Armazenamento Anexado à Rede) ou JBOD (Apenas um Monte de Discos).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Para atribuir volumes do StorSimple a um trabalho de backup do Veeam

1.  No console de Backup e Replicação do Veeam, selecione **Backup e Replicação**. Clique com botão direito do mouse em **Backup** e selecione **VMware** ou **Hyper-V** de acordo com seu ambiente.

    ![Console de gerenciamento do Veeam, trabalho de novo backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Na caixa de diálogo **Novo Trabalho de Backup**, digite um nome e uma descrição para o trabalho de backup diário.

    ![Console de gerenciamento do Veeam, página novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selecione uma máquina virtual para o backup.

    ![Console de gerenciamento do Veeam, página novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selecione os valores desejados para **Proxy de backup** e **Repositório de backup**. Selecione um valor para **Pontos de restauração para manter no disco** de acordo com as definições de RPO e RTO para seu ambiente no armazenamento anexado localmente. Selecione **Avançado**.

    ![Console de gerenciamento do Veeam, página novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Na caixa de diálogo **Configurações Avançadas**, na guia **Backup**, selecione **Incremental**. Verifique se a caixa de seleção **Criar backups totais sintéticos periodicamente** está desmarcada. Marque a caixa de seleção **Criar backups ativos completos periodicamente**. Em **Backup ativo completo**, marque a caixa de seleção **Semanalmente nos dias selecionados** no sábado.

    ![Console de gerenciamento do Veeam, página de configurações avançadas de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na guia **Armazenamento**, verifique se a caixa de seleção **Habilitar a eliminação de duplicação de dados embutidos** está desmarcada. Marque as caixas de seleção **Excluir os blocos do arquivo de permuta** e **Excluir os blocos de arquivos eliminados**. Defina **Nível de compactação** para **Nenhum**. Para um desempenho e eliminação de duplicação equilibrados, defina **Otimização de armazenamento** como **Destino de LAN**. Selecione **OK**.

    ![Console de gerenciamento do Veeam, página de configurações avançadas de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Para obter detalhes sobre as configurações de eliminação de duplicação e compactação do Veeam, consulte [Compactação de Dados e Eliminação de Duplicação](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Na caixa de diálogo **Editar Trabalho de Backup**, você pode selecionar a caixa de seleção **Habilitar processamento com reconhecimento de aplicativos** (opcional).

    ![Console de gerenciamento do Veeam, página de processamento convidado de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Defina o agendamento para execução uma vez ao dia em uma hora especificada por você.

    ![Console de gerenciamento do Veeam, página agendar novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como destino de backup secundário

> [!NOTE]
> As restaurações de dados de backups que foram dispostos em camadas na nuvem ocorrem com velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (que não seja StorSimple) para servir como um cache temporário. Por exemplo, você pode usar um volume com matriz redundante de volume de discos independentes (RAID) para acomodar o espaço, entrada/saída (E/S) e a largura de banda. Recomendamos o uso de RAID 5, 50 e 10.

A figura a seguir mostra os volumes local para retenção típica de curto prazo (para o servidor) e de arquivamento para retenção de longo prazo. Nesse caso, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar seu volume RAID local (para o servidor) para que ele possa lidar com os requisitos de capacidade e desempenho da retenção de curto prazo.

![StorSimple como diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS de destino de backup secundário

A tabela a seguir mostra como configurar backups para execução no local e discos do StorSimple. Ele inclui os requisitos de capacidade total e individuais.

| Tipo de backup e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2 a 4 |Disco StorSimple (longo prazo) | 1 | 4 | 4 |
| Mensal completo |Disco StorSimple (longo prazo) | 1 | 12 | 12 |
| Anual completo |Disco StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes do GFS |  |  |  | 18\*|

A capacidade total do \* inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local.


### <a name="gfs-example-schedule"></a>Exemplo de agenda do GFS

Agenda semanal, mensal e anual da rotação do GFS

| Semana | Completo | Incremental dia 1 | Incremental dia 2 | Incremental dia 3 | Incremental dia 4 | Incremental dia 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2 a 4 |   |   |   |   |   |
| Mensal | StorSimple mensal |   |   |   |   |   |
| Anual | StorSimple anual  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Atribuir volumes do StorSimple a um trabalho de cópia do Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Para atribuir volumes do StorSimple a um trabalho de cópia do Veeam

1.  No console de Backup e Replicação do Veeam, selecione **Backup e Replicação**. Clique com botão direito do mouse em **Backup** e selecione **VMware** ou **Hyper-V** de acordo com seu ambiente.

    ![Console de gerenciamento Veeam, página de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Na caixa de diálogo **Novo Trabalho de Cópia de Backup**, digite um nome e uma descrição para o trabalho.

    ![Console de gerenciamento Veeam, página de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selecione as VMs que você deseja processar. Selecione nos backups e, em seguida, selecione o backup diário que você criou anteriormente.

    ![Console de gerenciamento Veeam, página de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Exclua objetos do trabalho de cópia de backup, se necessário.

5.  Selecione o repositório de backup e defina um valor para **Restaurar pontos a serem mantidos**. Verifique se a caixa de seleção **Manter os seguintes pontos de restauração para fins de arquivamento** está marcada. Defina a frequência de backup e selecione **Avançado**.

    ![Console de gerenciamento Veeam, página de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Especifique as configurações avançadas a seguir:

    * Na guia **Manutenção**, desligue a proteção contra corrupção no nível do armazenamento.

    ![Console de gerenciamento do Veeam, página de configurações avançadas de novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na guia **Armazenamento**, verifique se a compressão e a eliminação de duplicação estão desativadas.

    ![Console de gerenciamento do Veeam, página de configurações avançadas de novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Especifique se a transferência de dados é direta.

8.  Defina o agendamento da janela de cópia de backup de acordo com suas necessidades e conclua o assistente.

Para obter mais informações, consulte [Criar trabalhos de cópia de backup](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem do StorSimple protegem os dados que residem no dispositivo StorSimple. Criar um instantâneo de nuvem é equivalente a enviar de fitas de backup local para instalações externas. Se você usar o armazenamento com redundância geográfica do Azure, criar um instantâneo de nuvem será equivalente a enviar fitas de backup para vários sites. Se você precisar restaurar um dispositivo depois de um desastre, poderá deixar outro dispositivo StorSimple online e executar um failover. Após o failover, você poderia acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir descreve como criar um script curto para iniciar e excluir instantâneos de nuvem do StorSimple durante o pós-processamento de backup.

> [!NOTE]
> Os instantâneos criados manualmente ou por meio de programação não seguem a política de expiração de instantâneos do StorSimple. Esses instantâneos devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e excluir instantâneos de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e de retenção de dados antes de excluir um instantâneo do StorSimple. Para saber mais sobre como executar um script pós-backup, consulte a documentação do Veeam.


### <a name="backup-lifecycle"></a>Ciclo de vida de backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Para adicionar o script ao seu trabalho de backup, edite as opções avançadas do seu trabalho Veeam.

    ![Guia de scripts de configurações avançadas de backup Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Recomendamos que você execute a política de backup de instantâneo de nuvem do StorSimple como um script pós-processamento no fim do seu trabalho de backup diário. Para saber mais sobre como fazer backup e restaurar seu ambiente de aplicativo de backup para ajudar a atender ao RPO e RTO, consulte seu arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origem de restauração

As restaurações de um dispositivo StorSimple funcionam como qualquer dispositivo de armazenamento em bloco. Restaurações de dados que estão em camadas na nuvem ocorrem em velocidades de nuvem. Para dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo.

Com o Veeam você obtém recuperação rápida e granular no nível do arquivo por meio do StorSimple usando as exibições de explorer no console do Veeam. Use os Veeam Explorers para recuperar itens individuais, como mensagens de e-mail, objetos do directory Active e itens do SharePoint a partir de backups. A recuperação pode ser feita sem interrupções de VMs locais. Você também pode realizar uma recuperação pontual para bancos de dados do Banco de Dados SQL do Azure e Oracle. O Veeam e o StorSimple tornam o processo de recuperação em nível de item do Azure rápido e fácil. Para obter informações sobre como executar uma restauração, consulte a documentação do Veeam:

- Para [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Para [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Para [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Para [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Para [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o StorSimple Cloud Appliance não tem suporte como um destino de restauração.

Um desastre pode ser causado por uma variedade de fatores. A tabela a seguir lista cenários comuns de recuperação de desastre.

| Cenário | Impacto | Como recuperar | Observações |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauração foram interrompidas. | Substitua o dispositivo com falha e execute [Failover e recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md). | Se você precisar realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho totais serão recuperados da nuvem para o novo dispositivo. Todas as operações ocorrem em velocidades de nuvem. O processo de nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor Veeam | As operações de backup e restauração foram interrompidas. | Recompile o servidor de backup e execute a restauração do banco de dados conforme detalhado na [Central de Ajuda da Veeam (Documentação Técnica)](https://www.veeam.com/documentation-guides-datasheets.html).  | Você deve recompilar ou restaurar o servidor Veeam no local de recuperação de desastre. Restaure o banco de dados para o ponto mais recente. Se o banco de dados restaurado do Veeam não estiver sincronizado com os trabalhos de backup mais recentes, será necessário fazer a indexação e a catalogação. Esse processo de nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local. Isso torna tudo ainda mais demorado. |
| Falha do site que resulta na perda do servidor de Backup e do StorSimple | As operações de backup e restauração foram interrompidas. | Restaure o StorSimple primeiro e depois o Veeam. | Restaure o StorSimple primeiro e depois o Veeam. Se você precisar realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho completos serão recuperados da nuvem para o novo dispositivo. Todas as operações ocorrem em velocidades de nuvem. |


## <a name="references"></a>Referências

Os documentos a seguir foram mencionados neste artigo:

- [Configuração de Multipath I/O de StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: provisionamento fino](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas compartilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [restaurar de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Sobre mais sobre como executar [failover e recuperação de desastre no dispositivo](storsimple-device-failover-disaster-recovery.md).
