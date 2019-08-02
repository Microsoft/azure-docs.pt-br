---
title: Servidor de Backup do Azure protege o estado do sistema e restaura para o bare-metal
description: Use o Servidor de Backup do Azure para fazer backup do estado do sistema e fornecer proteção de recuperação bare-metal (BMR).
author: rayne-wiselman
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: raynew
ms.openlocfilehash: a21169a5d9da7c9f1baf8a7d1e7365348860fca1
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465042"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Fazer backup de estado do sistema e restaurar bare-metal com o servidor de Backup do Azure

Use o Servidor de Backup do Azure para fazer backup do estado do sistema e fornecer proteção de recuperação bare-metal (BMR).

*   **Backup de estado do sistema**: Faz backup dos arquivos do sistema operacional, para que você possa recuperar quando um computador é iniciado, mas os arquivos do sistema e o registro são perdidos. Um backup de estado do sistema inclui:
    * Membro do domínio: Arquivos de inicialização, banco de dados de registro de classe COM+, registro
    * Controlador de domínio: Windows Server Active Directory (NTDS), arquivos de inicialização, banco de dados de registro de classe COM+, registro, volume do sistema (SYSVOL)
    * Computador que executa serviços de cluster: Metadados do servidor de cluster
    * Computador que executa serviços de certificado: Dados do certificado
* **Backup bare-metal**: Faz backup de arquivos do sistema operacional e de todos os dados em volumes críticos (exceto dados do usuário). Por definição, um backup de BMR inclui um backup de estado do sistema. Ele oferece proteção quando um computador não será iniciado e você precisa recuperar tudo.

A tabela a seguir resume o que você pode fazer backup e recuperar. Para obter informações detalhadas sobre as versões de aplicativo que podem ser protegidos com o estado do sistema e a BMR, consulte [O que faz o servidor de Backup do Azure?](backup-mabs-protection-matrix.md).

|Backup|Problema|Recuperar de backup do Servidor de Backup do Azure|Recuperar de backup de estado do sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de arquivo**<br /><br />Backup de dados regular<br /><br />Backup de estado do sistema/BMR|Dados de arquivos perdidos|S|N|N|
|**Dados de arquivo**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|**Dados de arquivo**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de arquivo**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|S|Não|Sim (BMR, seguida de recuperação regular de dados do arquivo de backup)|
|**Dados do SharePoint**:<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**:<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|**Dados do SharePoint**:<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Recuperação de desastres|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|VM perdida|S|N|N|
|Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|Sistema operacional perdido ou danificado|N|S|S|
|Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|Host do Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />Backup de estado do sistema de host/BMR|Host do Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguida de recuperação regular do Servidor de Backup do Azure|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Dados do aplicativo perdidos|S|N|N|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|y|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (logs de transação/banco de dados intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (logs de transação/banco de dados perdidos)|N|N|S<br /><br />Recuperação de BMR, seguida de recuperação regular do Servidor de Backup do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona o backup de estado do sistema

Quando um backup de estado do sistema é executado, o Servidor de Backup se comunica com o Backup do Windows Server para solicitar um backup de estado do sistema do servidor. Por padrão, o Servidor de Backup e o Backup do Windows Server usam a unidade que tenha mais espaço livre. Informações sobre a unidade são salvas no arquivo PSDataSourceConfig.xml. Esta é a unidade em que o Backup do Windows Server usa para backups.

Você pode personalizar a unidade usada pelo Servidor de Backup para o backup de estado do sistema. No servidor protegido, vá para C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Abra o arquivo PSDataSourceConfig.xml para edição. Altere o valor \<FilesToProtect\> para a letra da unidade. Salve e feche o arquivo. Se houver um conjunto de grupos de proteção para proteger o estado do sistema do computador, execute uma verificação de consistência. Se um alerta for gerado, selecione **Modificar grupo de proteção** no alerta e, em seguida, conclua o assistente. Em seguida, execute outra verificação de consistência.

Observe que, se o servidor de proteção estiver em um cluster, é possível que uma unidade de cluster seja selecionada como a unidade com mais espaço livre. Se essa propriedade da unidade foi alternada para outro nó e um backup de estado do sistema for executado, a unidade não estará disponível e o backup falhará. Nesse cenário, modificar PSDataSourceConfig.xml para apontar para uma unidade local.

Em seguida, o Backup do Windows Server cria uma pasta chamada WindowsImageBackup na raiz da pasta de restauração. À medida que o Backup do Windows Server cria o backup, todos os dados são colocados nessa pasta. Quando o backup for concluído, o arquivo é transferido para o computador do Servidor de Backup. Observe as seguintes informações:

* Esta pasta e seu conteúdo não são limpos quando o backup ou a transferência for concluída. A melhor maneira de pensar nisso é que o espaço está sendo reservado para a próxima vez que um backup for concluído.
* A pasta é criada toda vez que um backup é feito. O carimbo de data/hora e reflete a hora de seu último backup de estado do sistema.

## <a name="bmr-backup"></a>Backup de BMR

Para a BMR (incluindo um backup de estado do sistema), o trabalho de backup é salvo diretamente para um compartilhamento no computador do Servidor de Backup. Ele não será salvo em uma pasta no servidor protegido.

Servidor de Backup chama o Backup do Windows Server e compartilha o volume de réplica para esse backup BMR. Nesse caso, ele não informa o Backup do Windows Server para usar a unidade com mais espaço livre. Em vez disso, ele usa o compartilhamento que foi criado para o trabalho.

Quando o backup for concluído, o arquivo é transferido para o computador do Servidor de Backup. Logs são armazenados em C:\windows\logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

-   BMR não tem suporte para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operacional cliente.

-   Você não pode proteger o estado do sistema e BMR para o mesmo computador em grupos de proteção diferentes.

-   Um computador do Servidor de Backup não pode se proteger de BMR.

-   Proteção de curto prazo para fita (disco para fita ou D2T) não tem suporte para a BMR. Há suporte para armazenamento de longo prazo para fita (disco para disco para fita ou D2D2T).

-   Para proteção de BMR, o Backup do Windows Server deve ser instalado no computador protegido.

-   Para proteção de BMR, ao contrário da proteção de estado do sistema, o servidor de Backup não tem requisitos de espaço no computador protegido. Backup do Windows Server transfere diretamente os backups para o computador do Servidor de Backup. O trabalho de transferência de backup não aparece na exibição de **trabalhos** do Servidor de Backup.

-   O Servidor de Backup reserva 30 GB de espaço no volume de réplica para a BMR. Você pode alterar isso na página de **Alocação de disco** no assistente Modificar Grupo de Proteção ou usando os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell. No volume de ponto de recuperação, a proteção BMR exige cerca de 6 GB para uma retenção de cinco dias.
    * Observe que você não pode reduzir o tamanho do volume de réplica para menos de 15 GB.
    * Servidor de Backup não calcula o tamanho da fonte de dados BMR. Ele presume 30 GB para todos os servidores. Altere o valor com base no tamanho dos backups da BMR esperados no seu ambiente. O tamanho de um backup de BMR pode ser calculado aproximadamente como a soma de espaço usado em todos os volumes críticos. Volumes críticos = volume de inicialização + volume do sistema + volume que hospeda os dados de estado do sistema, como o Active Directory.

-   Se você alterar da proteção de estado do sistema para a proteção BMR, a proteção BMR exige menos espaço no *volume de ponto de recuperação*. No entanto, o espaço extra no volume não é recuperado. Você pode diminuir manualmente o tamanho do volume na página **Modificar Alocação de Disco** do assistente Modificar Grupo de Proteção ou usando os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell.

    Se você alterar a proteção de estado do sistema para a proteção BMR, a proteção BMR exige mais espaço no *volume de réplica*. O volume é estendido automaticamente. Se você quiser alterar as alocações de espaço padrão, use o cmdlet do PowerShell Modify-DiskAllocation.

-   Se você alterar da proteção BMR para a proteção de estado do sistema, é necessário mais espaço no volume de ponto de recuperação. O Servidor de Backup pode tentar aumentar automaticamente o volume. Se não houver espaço suficiente no pool de armazenamento, ocorrerá um erro.

    Se você alterar da proteção BMR para a proteção de estado do sistema, é necessário mais espaço no computador protegido. Isso ocorre porque a proteção de estado do sistema primeiro grava a réplica para o computador local e, em seguida, transfere-o para o computador do Servidor de Backup.

## <a name="before-you-begin"></a>Antes de começar

1.  **Implantar o Servidor de Backup do Azure**. Verifique se o Servidor de Backup é implantado corretamente. Para obter mais informações, consulte:
    * [Requisitos de sistema para o Servidor de Backup do Azure](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do Servidor de Backup](backup-mabs-protection-matrix.md)

2.  **Configurar o armazenamento**. Você pode armazenar dados de backup em disco, fita e na nuvem com o Azure. Para obter mais informações, consulte [Preparar dados de armazenamento](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Configurar o agente de proteção**. Instale o agente de proteção no computador que você deseja fazer backup. Para obter mais informações, consulte [Implantar o agente de proteção do DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Fazer backup do estado do sistema e bare-metal
Configurar um grupo de proteção, conforme descrito em [Implantar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Observe que você não pode proteger o estado do sistema e BMR para o mesmo computador em grupos diferentes. Além disso, quando você seleciona a BMR, o estado do sistema é habilitado automaticamente.


1.  Para abrir o assistente Criar Novo Grupo de Proteção no Console do administrador do Servidor de Backup, selecione **Proteção** > **Ações** > **Criar grupo de proteção**.

2.  Na página **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores** e, depois, selecione **Avançar**.

3.  Na página **Selecionar membros do grupo**, expanda o computador e, em seguida, selecione **BMR** ou **o estado do sistema**.

    Observe que você não pode proteger o estado do sistema e BMR para o mesmo computador em grupos diferentes. Além disso, quando você seleciona a BMR, o estado do sistema é habilitado automaticamente. Para obter mais informações, consulte [Implantar Grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Na página **Selecionar método de proteção de dados**, selecione como deseja lidar com o backup de curto e longo prazo. Backup de curto prazo é sempre primeiro para o disco e, em seguida, com a opção de fazer backup do disco para o Azure cloud usando o Backup do Azure (curto ou longo prazo). Uma alternativa para backup de longo prazo para a nuvem é configurar o backup de longo prazo para um dispositivo de fita autônoma ou biblioteca de fitas que está conectada ao Servidor de Backup.

5.  Na página **Selecionar objetivos de curto prazo**, selecione como você deseja fazer backup no armazenamento de curto prazo em disco:
    1. Para **Período de retenção**, selecione quanto tempo deseja manter os dados no disco. 
    2. Para **Frequência de sincronização**, selecione a frequência na qual você deseja executar um backup incremental em disco. Se você não quiser definir um intervalo de backup, você pode verificar a opção **Pouco antes de um ponto de recuperação**. Servidor de Backup será executado um backup completo expresso antes de cada ponto de recuperação for agendado.

6.  Se você deseja armazenar dados em fita para armazenamento de longo prazo, na página **Especificar objetivos de longo prazo**, selecione quanto tempo deseja manter os dados da fita (1-99 anos). 
    1. Para **Frequência de backup**, selecione a frequência que o backup em fita deve ser executado. A frequência é baseada no período de retenção que você selecionou:
        * Quando o período de retenção é de 1 a 99 anos, você pode selecionar backups com frequência diária, semanal, mensal, trimestral de quinzenal, semestral ou anual.
        * Quando o período de retenção é de 1 a 11 meses, você pode selecionar backups com frequência diária, semanal, quinzenal ou mensal.
        * Quando o período de retenção é de 1 a 4 semanas, você pode selecionar backups com frequência diária ou semanal.

    2. Na página **Selecionar detalhes de fita e biblioteca**, selecione a fita e a biblioteca a ser usada, e se os dados devem ser compactados e criptografados.

7.  Na página **Examinar alocação do disco**, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

    1. **Tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup.
    2. **Espaço em disco para ser provisionado no Servidor de Backup do Azure** é o espaço que o Servidor de Backup recomenda para o grupo de proteção. Servidor de Backup escolhe o volume de backup ideal com base nas configurações. No entanto, você pode editar as opções de volume de backup em **Detalhes de alocação de disco**. 
    3. Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. Suas edições alteram os valores de **Armazenamento Total** e **Armazenamento Gratuito** no painel de **Armazenamento em Disco Disponível**. Espaço pela falta de provisionamento é a quantidade de armazenamento que o Servidor de Backup recomenda que você adicione ao volume, para garantir backups estáveis.

8.  Na página **Escolher método de criação de réplica**, selecione como você deseja controlar a replicação inicial de dados completo. Se você optar por replicar pela rede, recomendamos que você escolha um horário de menos movimento. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

9. Na página **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Você pode escolher executar uma verificação somente quando dados de réplica se tornam inconsistentes ou em um agendamento. Se você não desejar configurar a verificação de consistência automática, você pode executar uma verificação manual a qualquer momento. Para executar uma verificação manual, na área de **Proteção** do Console do Administrador do Servidor de Backup do Azure, clique com o botão direito no grupo de proteção e, depois, selecione **Executar Verificação de Consistência**.

10. Se você tiver selecionado para backup na nuvem usando o Backup do Azure, na página **Especificar Dados de Proteção Online**, certifique-se de selecionar as cargas de trabalho que você deseja fazer backup para o Azure.

11. Na página **Especificar Agendamento de Backup Online**, selecione a frequência com que ocorrem os backups incrementais para o Azure. Você pode agendar backups para executar a cada dia, semana, mês e ano e selecionar a data e hora em que ele deve ser executado. Backups podem ocorrer até duas vezes por dia. Cada vez que um backup é executado, um ponto de recuperação de dados é criado no Azure da cópia dos dados de backup armazenados no disco do Servidor de Backup.

12. Na página **Especificar Política de Retenção Online**, selecione como os pontos de recuperação são criados a partir de backups diários, semanais, mensais e anuais são mantidos no Azure.

13. Na página **Escolher Replicação Online**, selecione como ocorre a replicação inicial completa de dados. Você pode replicar pela rede ou fazer um backup offline (propagação offline). O backup offline usa a funcionalidade de importação do Azure. Para obter mais informações, consulte [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md).

14. Na página **Resumo**, examine as configurações. Depois de selecionar **Criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação de dados termina, na página **Status**, o status do grupo de proteção é **OK**. Em seguida, ocorre o backup pelas configurações do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou BMR
Você pode recuperar BMR ou estado do sistema para um local de rede. Se você tiver feito backup da BMR, use o Ambiente de Recuperação do Windows (WinRE) para iniciar seu sistema e conectá-lo à rede. Em seguida, use o Backup do Windows Server para recuperar o local de rede. Se tiver feito backup do estado do sistema, basta usar o Backup do Windows Server para recuperar o local de rede.

### <a name="restore-bmr"></a>Restaurar BMR
Execute a recuperação no computador do Servidor de Backup:

1.  No painel **Recuperação**, localize o computador que deseja recuperar e selecione **Recuperação Bare-Metal**.

2.  Pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e hora para o ponto de recuperação que você deseja usar.

3.  Na página **Selecionar Tipo de Recuperação**, selecione **Copiar para uma pasta de rede.**

4.  Na página **Especificar destino**, selecione onde você deseja copiar os dados. Lembre-se de que o destino selecionado deve ter espaço suficiente. É recomendável que você crie uma nova pasta.

5.  Na página **Especificar opções de recuperação**, selecione as configurações de segurança que serão aplicadas. Em seguida, selecione se deseja usar a rede de área de armazenamento (SAN)-com base em instantâneos de hardware, para uma recuperação mais rápida. (Essa é uma opção somente se você tiver uma rede SAN com essa funcionalidade disponível e a capacidade de criar e dividir para torná-la gravável. Além disso, o computador protegido e o computador do Servidor de Backup devem estar conectados à mesma rede.)

6.  Configure opções de notificação. Na página **Confirmação**, selecione **Recuperar**.

Configure o local de compartilhamento:

1.  No local de recuperação, vá para a pasta que contém o backup.

2.  Compartilhe a pasta que é um nível acima de WindowsImageBackup, para que a raiz da pasta compartilhada seja a pasta WindowsImageBackup. Se você não fizer isso, a restauração não localizará o backup. Para se conectar usando o Ambiente de Recuperação do Windows (WinRE), você precisa de um compartilhamento que você pode acessar no WinRE com o endereço IP correto e as credenciais.

Restaure o sistema:

1.  Inicie o computador no qual você deseja restaurar a imagem usando o DVD do Windows para o sistema que você está restaurando.

2.  Na primeira página, verifique as configurações de idioma e local. Na página **Instalar**, selecione **Reparar seu computador**.

3.  Na página **Opções de Recuperação do Sistema**, selecione **Restaurar seu computador usando uma imagem do sistema que você criou anteriormente**.

4.  Na página **Selecionar um backup de imagem do sistema**, selecione **Selecionar uma imagem do sistema** > **Avançado** > **Pesquisar por uma imagem de sistema na rede**. Se um aviso for exibido, selecione **Sim**. Vá para o caminho de compartilhamento, insira as credenciais e, em seguida, selecione o ponto de recuperação. Isso verifica os backups específicos disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que você deseja usar.

5.  Na página **Escolher como restaurar o backup**, selecione **Formatar e reparticionar discos**. Na página seguinte, verifique as configurações. 

6.  Para iniciar a restauração, selecione **Concluir**. Uma reinicialização é necessária.

### <a name="restore-system-state"></a>Restaurar o estado do sistema

Execute a recuperação no Servidor de Backup:

1.  No painel de **Recuperação**, localize o computador que deseja recuperar e selecione **Recuperação Bare-Metal**.

2.  Pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e hora para o ponto de recuperação que você deseja usar.

3.  Na página **Selecionar Tipo de Recuperação**, selecione **Copiar para uma pasta de rede**.

4.  Na página **Especificar destino**, selecione onde você deseja copiar os dados. Lembre-se de que o destino selecionado deve ter espaço suficiente. É recomendável que você crie uma nova pasta.

5.  Na página **Especificar opções de recuperação**, selecione as configurações de segurança que serão aplicadas. Em seguida, selecione se deseja usar a rede SAN com base em instantâneos de hardware, para uma recuperação mais rápida. (Essa é uma opção somente se você tiver uma rede SAN com essa funcionalidade disponível e a capacidade de criar e dividir para torná-la gravável. Além disso, o computador protegido e o servidor do Servidor de Backup devem estar conectados à mesma rede.)

6.  Configure opções de notificação. Na página **Confirmação**, selecione **Recuperar**.

Fazer backup do Windows Server:

1.  Selecione **Ações** > **Recuperação** > **Este Servidor** > **Avançar**.

2.  Selecione **Outro Servidor**, selecione a página **Especificar Tipo de Local** e, em seguida, selecione **Pasta compartilhada remota**. Digite o caminho para a pasta que contém o ponto de recuperação.

3.  Na página **Selecionar Tipo de Recuperação**, selecione **Estado do Sistema**. 

4. Na página **Selecionar Local para Recuperação do Estado do Sistema**, selecione **Local Original**.

5.  Na página **Confirmação**, selecione **Recuperar**. Após a restauração, reinicie o servidor.

6.  Você também pode executar a restauração de estado do sistema em um prompt de comando. Para fazer isso, inicie o Backup do Windows Server no computador que você deseja recuperar. Para obter o identificador de versão, no prompt de comando, digite: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Use o identificador de versão para iniciar a restauração de estado do sistema. No prompt de comando, insira: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Confirme que você deseja iniciar a recuperação. Você pode ver o processo na janela do Prompt de Comando. Um log de restauração é criado. Após a restauração, reinicie o servidor.

