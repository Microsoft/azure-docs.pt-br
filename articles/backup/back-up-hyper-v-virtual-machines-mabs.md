---
title: Fazer backup de máquinas virtuais do Hyper-V com MABS
description: Este artigo contém os procedimentos para fazer backup e recuperação de máquinas virtuais usando o Backup do Microsoft Azure Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: a020559229771fff1ecc8fb512a5b2af70240cdd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509499"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Fazer backup de máquinas virtuais do Hyper-V com Servidor de Backup do Azure

Este artigo explica como fazer backup de máquinas virtuais do Hyper-V usando o MABS (servidor de Backup do Microsoft Azure).

## <a name="supported-scenarios"></a>Cenários com suporte

O MABS pode fazer backup de máquinas virtuais em execução em servidores de host Hyper-V nos seguintes cenários:

- **Máquinas virtuais com armazenamento local ou direto** – Faça backup das máquinas virtuais hospedadas em servidores autônomos de host Hyper-V que tenham armazenamento local ou diretamente anexado. Por exemplo: um disco rígido, um dispositivo SAN (rede de área de armazenamento) ou um dispositivo NAS (armazenamento anexado à rede). O agente de proteção do MABS deve ser instalado em todos os hosts.

- **Máquinas virtuais em um cluster com armazenamento CSV** – Faça backup das máquinas virtuais hospedadas em um cluster do Hyper-V com o armazenamento CSV (Volume Compartilhado Clusterizado). O agente de proteção do MABS é instalado em cada nó de cluster.

## <a name="host-versus-guest-backup"></a>Host versus backup de convidado

O MABS pode fazer um backup em nível de host ou convidado de VMs do Hyper-V. No nível do host, o agente de proteção do MABS é instalado no servidor ou cluster do host Hyper-V e protege todas as VMs e os arquivos de dados em execução nesse host.   No nível de convidado, o agente é instalado em cada máquina virtual e protege a carga de trabalho presente nessa máquina.

Os dois métodos têm vantagens e desvantagens:

- Os backups em nível de host são flexíveis porque funcionam independentemente do tipo de sistema operacional em execução nas máquinas convidadas e não exigem a instalação do agente de proteção MABS em cada VM. Se você implantar o backup em nível de host, poderá recuperar uma máquina virtual inteira, ou arquivos e pastas (recuperação em nível de item).

- O backup em nível de convidado será útil se você quiser proteger cargas de trabalho específicas em execução em uma máquina virtual. No nível do host, você pode recuperar uma VM inteira ou arquivos específicos, mas ele não fornecerá recuperação no contexto de um aplicativo específico. Por exemplo, para recuperar itens específicos do SharePoint de uma VM de backup, você deve fazer backup em nível de convidado dessa VM. Use o backup em nível de convidado se desejar proteger os dados armazenados em discos de passagem. A passagem permite que a máquina virtual acesse diretamente o dispositivo de armazenamento e não armazena dados de volume virtual em um arquivo VHD.

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

O MABS executa o backup com VSS da seguinte maneira. As etapas nessa descrição são numeradas para proporcionar maior clareza.

1. O mecanismo de sincronização baseado em bloco do MABS faz uma cópia inicial da máquina virtual protegida e garante que a cópia da máquina virtual esteja completa e consistente.

2. Depois que a cópia inicial é feita e verificada, o MABS usa o gravador VSS do Hyper-V para capturar backups. O gravador VSS fornece um conjunto consistente de dados de blocos de disco que são sincronizados com o servidor MABS. Essa abordagem fornece o benefício de um "backup completo" com o servidor MABS, minimizando os dados de backup que devem ser transferidos pela rede.

3. O agente de proteção do MABS em um servidor que executa o Hyper-V usa as APIs do Hyper-V existentes para determinar se uma máquina virtual protegida também oferece suporte ao VSS.

   - Se uma máquina virtual atender aos requisitos para o backup online e possuir o componente dos serviços de integração do Hyper-V instalado, o gravador VSS do Hyper-V recursivamente encaminha a solicitação VSS para todos os processos de reconhecimento de VSS na máquina virtual. Essa operação ocorre sem que o agente de proteção do MABS esteja instalado na máquina virtual. Essa solicitação VSS recursiva permite que o gravador VSS do Hyper-V verifique se as operações de gravação no disco estão sincronizadas de modo que um instantâneo de VSS seja capturado sem a perda de dados.

     O componente dos serviços de integração do Hyper-V invoca o gravador VSS do Hyper-V nos VSS (Serviços de Cópias de Sombra de Volume) em máquinas virtuais para verificar se os dados do aplicativo estão em um estado consistente.

   - Se a máquina virtual não estiver em conformidade com os requisitos de backup online, o MABS usará automaticamente as APIs do Hyper-V para pausar a máquina virtual antes de capturar os arquivos de dados.

4. Depois que a cópia de linha de base inicial da máquina virtual é sincronizada com o servidor MABS, todas as alterações feitas nos recursos da máquina virtual são capturadas em um novo ponto de recuperação. O ponto de recuperação representa o estado consistente da máquina virtual em um horário específico. As capturas dele podem ocorrer pelo menos uma vez por dia. Quando um novo ponto de recuperação é criado, o MABS usa a replicação em nível de bloco em conjunto com o gravador VSS do Hyper-V para determinar quais blocos foram alterados no servidor que está executando o Hyper-V após a criação do último ponto de recuperação. Esses blocos de dados são então transferidos para o servidor MABS e aplicados à réplica dos dados protegidos.

5. O servidor MABS usa o VSS nos volumes que hospedam dados de recuperação para que várias cópias de sombra estejam disponíveis. Cada uma dessas cópias de sombra fornece uma recuperação separada. Os pontos de recuperação do VSS são armazenados no servidor MABS. A cópia temporária feita no servidor que executa o Hyper-V só é armazenada durante a sincronização MABS.

>[!NOTE]
>
>A partir do Windows Server 2016, os discos rígidos virtuais do Hyper-V têm controle de alterações interno conhecido como RCT (controle de alterações resiliente). O MABS usa RCT (o controle de alterações nativo no Hyper-V), que diminui a necessidade de verificações de consistência demoradas em cenários como falhas de VM. O RCT fornece melhor resiliência que o controle de alterações fornecido pelos backups baseados em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante as verificações de consistência.

## <a name="backup-prerequisites"></a>Pré-requisitos de backup

Estes são os pré-requisitos para fazer backup de máquinas virtuais do Hyper-V com MABS:

|Pré-requisito|Detalhes|
|------------|-------|
|Pré-requisitos do MABS|-Se você quiser executar a recuperação em nível de item para máquinas virtuais (recuperar arquivos, pastas, volumes), será necessário instalar a função Hyper-V no servidor MABS.  Se você quiser apenas recuperar a máquina virtual e não o nível de item, a função não será necessária.<br />-Você pode proteger até 800 máquinas virtuais de 100 GB cada em um servidor MABS e permitir vários servidores MABS que dão suporte a clusters maiores.<br />-MABS exclui o arquivo de paginação de backups incrementais para melhorar o desempenho de backup da máquina virtual.<br />-MABS pode fazer backup de um servidor ou cluster do Hyper-V no mesmo domínio que o servidor MABS ou em um domínio filho ou confiável. Se desejar fazer backup do Hyper-V em um grupo de trabalho ou em um domínio não confiável, você precisará configurar a autenticação. Para um único servidor Hyper-V, você pode usar autenticação NTLM ou de certificado. Para um cluster, você pode usar somente a autenticação de certificado.<br />-   Não há suporte para o uso do backup em nível de host para fazer backup de dados da máquina virtual em discos de passagem. Nesse cenário, recomendamos que você use o backup em nível de host para fazer backup de arquivos VHD e backup em nível de convidado para fazer backup dos outros dados que não estão visíveis no host.<br />   -Você pode fazer backup de VMs armazenadas em volumes com eliminação de duplicação.|
|Pré-requisitos da VM do Hyper-V|-A versão dos componentes de integração em execução na máquina virtual deve ser a mesma que a versão do host do Hyper-V. <br />-   Para cada backup de máquinas virtuais, você deverá liberar espaço no volume que hospeda os arquivos do disco rígido virtual para permitir espaço suficiente ao Hyper-V para discos diferenciais (AVHDs) durante o backup. O espaço deve ser, pelo menos, igual ao tempo da janela de cálculo do **Tamanho inicial do disco\*Taxa de rotatividade\*Backup**. Se estiver executando vários backups em um cluster, você precisará ter capacidade de armazenamento suficiente para acomodar os AVHDs para cada uma das máquinas virtuais usando esse cálculo.<br />-Para fazer backup de máquinas virtuais localizadas em servidores host Hyper-V que executam o Windows Server 2012 R2, a máquina virtual deve ter um controlador SCSI especificado, mesmo que ele não esteja conectado a nada. (No Windows Server 2012 R2 online backup, o host Hyper-V monta um novo VHD na VM e depois o desmonta. Somente o controlador SCSI pode dar suporte a isso e, portanto, é necessário para o backup online da máquina virtual.  Sem essa configuração, a ID de evento 10103 será emitida quando você tentar fazer backup da máquina virtual.)|
|Pré-requisitos do Linux|-Você pode fazer backup de máquinas virtuais do Linux usando o MABS. Somente os instantâneos consistentes de arquivos têm suporte.|
|Fazer backup de VMs com armazenamento CSV|-   Para o armazenamento CSV, instale o provedor de hardware dos VSS (Serviços de Cópias de Sombra de Volume) no servidor Hyper-V. Entre em contato com seu fornecedor de SAN (rede de área de armazenamento) para o provedor de hardware dos VSS.<br />-Se um único nó for desligado inesperadamente em um cluster CSV, o MABS executará uma verificação de consistência nas máquinas virtuais que estavam em execução nesse nó.<br />-   Se precisar reiniciar um servidor Hyper-V que tenha a Criptografia de Unidade de Disco BitLocker habilitada no cluster CSV, você deverá executar uma verificação de consistência nas máquinas virtuais do Hyper-V.|
|Fazer backup de VMs com armazenamento SMB|-Ative a montagem automática no servidor que está executando o Hyper-V para habilitar a proteção da máquina virtual.<br />   -   Desabilite o Descarregamento de Chimney TCP.<br />-   Verifique se todas as contas do computador$ do Hyper-V têm permissões completas sobre os compartilhamentos específicos de arquivos SMB remotos.<br />-Verifique se o caminho do arquivo para todos os componentes da máquina virtual durante a recuperação para o local alternativo tem menos de 260 caracteres. Caso contrário, a recuperação pode ter sucesso, mas o Hyper-V não conseguirá montar a máquina virtual.<br />-Não há suporte para os seguintes cenários:<br />     Implantações em que alguns componentes da máquina virtual estão em volumes locais e alguns componentes estão em volumes remotos; um endereço IPv4 ou IPv6 para o servidor de arquivos do local de armazenamento e a recuperação de uma máquina virtual em um computador que usa compartilhamentos SMB remotos.<br />-   Você precisará habilitar o serviço do Agente VSS do Servidor de Arquivos em cada servidor SMB – Adicione-o em **Adicionar funções e recursos** > **Selecionar funções de servidor** > **Serviços de Arquivo e Armazenamento** > **Serviços de Arquivo** > **Serviço de Arquivos** > **Serviço de Agente VSS de Servidor de Arquivos**.|

## <a name="back-up-virtual-machines"></a>Fazer backup de máquinas virtuais

1. Configure seu [servidor mAbs](backup-azure-microsoft-azure-backup.md) e [seu armazenamento](backup-mabs-add-storage.md). Ao configurar seu armazenamento, use estas diretrizes de capacidade de armazenamento.
   - Tamanho médio da máquina virtual – 100 GB
   - Número de máquinas virtuais por servidor MABS-800
   - Tamanho total de 800 VMs – 80 TB
   - Espaço necessário para o armazenamento de backup – 80 TB

2. Configure o agente de proteção do MABS no servidor Hyper-V ou nos nós do cluster Hyper-V. Se estiver fazendo um backup em nível de convidado, você instalará o agente nas VMs que deseja fazer backup no nível de convidado.

3. No console do administrador do mAbs, selecione **proteção**  >  **Criar grupo de proteção** para abrir o assistente para **criar novo grupo de proteção** .

4. Na página **Selecionar Membros do Grupo**, selecione as VMs que você deseja proteger dos servidores host Hyper-V em que estão localizadas. É recomendável que você coloque todas as VMs que terão a mesma política de proteção no mesmo grupo de proteção. Habilite a colocação para proporcionar um uso eficiente do espaço. A colocação permite que você localize dados de grupos de proteção diferentes no mesmo armazenamento em disco ou fita para que várias fontes de dados tenham uma única réplica e volume de pontos de recuperação.

5. Na página **Selecionar Método de Proteção de Dados**, especifique um nome do grupo de proteção. Selecione **Desejo proteção de curto prazo usando disco** e selecione **Desejo proteção online** se você deseja fazer backup de dados no Azure usando o serviço de Backup do Azure.

6. Em **Especificar objetivos de curto prazo** > **Intervalo de retenção**, especifique quanto tempo deseja manter os dados do disco. Em **frequência de sincronização**, especifique com que frequência os backups incrementais dos dados devem ser executados. Como alternativa, em vez de selecionar um intervalo para backups incrementais, você pode habilitar **Logo antes de um ponto de recuperação**. Com essa configuração habilitada, o MABS executará um backup completo expresso antes de cada ponto de recuperação agendado.

    > [!NOTE]
    >
    >Se você estiver protegendo cargas de trabalho do aplicativo, os pontos de recuperação serão criados de acordo com a frequência de sincronização, desde que o aplicativo dê suporte a backups incrementais. Se não estiver, o MABS executará um backup completo expresso, em vez de um backup incremental, e criará pontos de recuperação de acordo com o agendamento de backup expresso.

7. Na página **Examinar alocação de disco**, examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

   O **Tamanho total dos dados** é o tamanho dos dados de que você deseja fazer backup e **Espaço em disco a ser provisionado no MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal com base nas configurações. No entanto, você pode editar as opções de volume de backup nos **Detalhes de alocação do disco**. Para as cargas de trabalho, selecione o armazenamento preferido no menu suspenso. Suas edições alteram os valores de **Armazenamento Total** e **Armazenamento Gratuito** no painel de **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de MABS de armazenamento que sugere que você adicione ao volume para continuar com os backups sem problemas no futuro.

8. Na página **Escolher método de criação de réplica**, especifique como a replicação inicial de dados no grupo de proteção será executada. Se você optar por **replicar automaticamente pela rede**, recomendamos que você escolha um horário de pico. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere selecionar **manualmente**, o que requer a replicação de dados offline usando mídia removível.

9. Na página **Opções de verificação de consistência**, selecione como deseja automatizar as verificações de consistência. Você pode habilitar uma verificação para ser executada somente quando os dados de réplica se tornarem inconsistentes, ou de acordo com uma agenda. Se não quiser configurar a verificação de consistência automática, você poderá executar uma verificação manual a qualquer momento clicando com o botão direito do mouse no grupo de proteção e selecionando **Executar Verificação de Consistência**.

    Depois de criar o grupo de proteção, a replicação inicial dos dados ocorrerá de acordo com o método selecionado. Após a replicação inicial, cada backup ocorrerá em conformidade com as configurações do grupo de proteção. Se você precisar recuperar os dados de backup, observe o seguinte:

## <a name="back-up-replica-virtual-machines"></a>Fazer backup de máquinas virtuais de réplica

Se o MABS estiver em execução no Windows Server 2012 R2 ou superior, você poderá fazer backup de máquinas virtuais de réplica. Isso é útil por vários motivos:

**Reduz o impacto dos backups sobre a carga de trabalho em execução** – Fazer backup em uma máquina virtual gera uma sobrecarga à medida que um instantâneo é criado. Descarregando o processo de backup em um site remoto secundário, a carga de trabalho em execução não é mais afetada pela operação de backup. Isso aplica-se somente a implantações em que a cópia de backup está armazenada em um site remoto. Por exemplo, você pode fazer backups diários e armazenar dados localmente para garantir tempos de restauração rápidos, mas faça backups mensais ou trimestrais de máquinas virtuais de réplica armazenados remotamente para retenção de longo prazo.

**Economiza largura de banda** – Em uma implantação remota típica de escritório/filial, você precisará que uma quantidade adequada de largura de banda seja provisionada pelos administradores para transferir dados de backup entre os locais. Se você criar uma estratégia de failover e replicação além de sua estratégia de backup de dados, poderá reduzir a quantidade de dados redundantes enviados pela rede. Ao fazer backup dos dados da máquina virtual de réplica em vez do primário, você economiza a sobrecarga de enviar os dados de backup pela rede.

**Habilitar o backup de hoster** – Você pode usar um datacenter hospedado como um site de réplica, sem a necessidade de um datacenter secundário. Nesse caso, o SLA do hoster requer backup consistente de máquinas virtuais de réplica.

Uma máquina virtual de réplica é desativada até que um failover seja iniciado e o VSS não possa assegurar um backup consistente com o aplicativo para uma máquina virtual de réplica. Portanto, o backup de uma máquina virtual de réplica será consistente somente com falhas. Se não for possível garantir a consistência de falha, o backup falhará e isso poderá ocorrer em algumas condições:

- A máquina virtual de réplica não está íntegra e se apresenta em um estado crítico.

- A máquina virtual de réplica está sendo sincronizada novamente (no estado de Ressincronização em andamento ou de Ressincronização Necessária).

- A replicação inicial entre os locais primário e secundário está em andamento ou pendente para a máquina virtual.

- Logs .hrl estão sendo aplicados à máquina virtual de réplica, ou uma ação anterior para aplicar os logs .hrl ao disco virtual falhou ou foi cancelada ou interrompida.

- A migração ou failover da máquina virtual de réplica está em andamento

## <a name="recover-backed-up-virtual-machines"></a>Recuperar as máquinas virtuais com backup

Quando for possível recuperar uma máquina virtual do backup, use o Assistente de Recuperação para selecionar a máquina virtual e o ponto de recuperação específico. Use o Assistente de Recuperação e recupere a máquina virtual:

1. No console do administrador do MABS, digite o nome da VM ou expanda a lista de itens protegidos e selecione a VM que você deseja recuperar.

2. No painel **pontos de recuperação para** , no calendário, selecione qualquer data para ver os pontos de recuperação disponíveis. No painel **Caminho**, selecione o ponto de recuperação que você deseja usar no Assistente de Recuperação.

3. No menu **ações** , selecione **recuperar** para abrir o assistente de recuperação.

    A VM e o ponto de recuperação selecionados aparecem na tela **Examinar Seleção de Recuperação**. Selecione **Avançar**.

4. Na tela **Selecionar tipo de recuperação** , selecione onde você deseja restaurar os dados e, em seguida, selecione **Avançar**.

    - **Recuperar na instância original**: quando você recupera a instância original, o VHD original e todos os pontos de verificação associados são excluídos. MABS recupera o VHD e outros arquivos de configuração para o local original usando o gravador VSS do Hyper-V. Ao término do processo de recuperação, as máquinas virtuais ainda estarão altamente disponíveis.
        O grupo de recursos deve estar presente para recuperação. Se não estiver disponível, recupere em um local alternativo e crie a máquina virtual altamente disponível.

    - **Recuperar como máquina virtual em qualquer host**: o mAbs dá suporte à ALR (recuperação de local alternativo), que fornece uma recuperação direta de uma máquina virtual protegida do Hyper-v para um host Hyper-v diferente, independentemente da arquitetura do processador. As máquinas virtuais do Hyper-V que são recuperadas para um nó de cluster não estarão altamente disponíveis. Se você escolher essa opção, o Assistente de Recuperação apresentará uma tela adicional para identificar o destino e o caminho de destino.
    
        >[!NOTE]
        >Se você selecionar o host original, o comportamento será o mesmo que **recuperar na instância original**. O VHD original e todos os pontos de verificação associados serão excluídos.

    - **Copiar para uma pasta de rede**: o mAbs dá suporte à ILR (recuperação em nível de item), que permite fazer a recuperação em nível de item de arquivos, pastas, volumes e VHDs (discos rígidos virtuais) de um backup em nível de host de máquinas virtuais do Hyper-V em um volume ou compartilhamento de rede em um servidor mAbs protegido. O agente de proteção do MABS não precisa ser instalado dentro do convidado para executar a recuperação em nível de item. Se você escolher essa opção, o Assistente de Recuperação apresentará uma tela adicional para identificar o destino e o caminho de destino.

5. Em **especificar opções de recuperação** , configure as opções de recuperação e selecione **Avançar**:

    - Se você estiver recuperando uma VM com pouca largura de banda, selecione **Modificar** para habilitar a **limitação do uso de largura de banda**. Depois de ativar a opção de limitação, você pode especificar a quantidade de largura de banda que você deseja disponibilizar e a hora em que ela estará disponível.
    - Selecione **habilitar a recuperação baseada em San usando instantâneos de hardware** se você tiver configurado sua rede.
    - Selecione **Enviar um email ao concluir a recuperação** e forneça os endereços de email se desejar que notificações de email sejam enviadas quando o processo de recuperação for concluído.

6. Na tela de Resumo, verifique se todos os detalhes estão corretos. Se os detalhes não estiverem corretos ou se você quiser fazer uma alteração, selecione **voltar**. Se estiver satisfeito com as configurações, selecione **recuperar** para iniciar o processo de recuperação.

7. A tela **Status de Recuperação** fornece informações sobre o trabalho de recuperação.

## <a name="next-steps"></a>Próximas etapas

[Recuperar dados do Servidor de Backup do Azure](./backup-azure-alternate-dpm-server.md)
