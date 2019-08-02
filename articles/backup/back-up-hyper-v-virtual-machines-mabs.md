---
title: Fazer backup de máquinas virtuais do Hyper-V com Servidor de Backup do Azure
description: Este artigo contém os procedimentos para fazer backup e recuperação de máquinas virtuais usando o Backup do Microsoft Azure Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: dacurwin
ms.openlocfilehash: 46d9f33dedff5a5682385b9cb22cf310581eefde
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466854"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Fazer backup de máquinas virtuais do Hyper-V com Servidor de Backup do Azure

Este artigo explica como fazer backup de máquinas virtuais do Hyper-V usando o MABS (servidor de Backup do Microsoft Azure).

## <a name="supported-scenarios"></a>Cenários com suporte
O MABS pode fazer backup de máquinas virtuais em execução em servidores de host Hyper-V nos seguintes cenários:

-   **Máquinas virtuais com armazenamento local ou direto** – backup de máquinas virtuais hospedadas em servidores autônomos de host Hyper-V que têm armazenamento local ou diretamente anexado. Por exemplo: um disco rígido, um dispositivo SAN (rede de área de armazenamento) ou um dispositivo NAS (armazenamento anexado à rede). O agente de proteção do MABS deve ser instalado em todos os hosts.

-   **Máquinas virtuais em um cluster com armazenamento CSV** -backup de máquinas virtuais hospedadas em um cluster Hyper-V com armazenamento volume compartilhado clusterizado (CSV). O agente de proteção do MABS é instalado em cada nó de cluster.



## <a name="host-versus-guest-backup"></a>Host versus backup de convidado
O MABS pode fazer um backup em nível de host ou convidado de VMs do Hyper-V. No nível do host, o agente de proteção do MABS é instalado no servidor ou cluster do host Hyper-V e protege todas as VMs e os arquivos de dados em execução nesse host.   No nível de convidado, o agente é instalado em cada máquina virtual e protege a carga de trabalho presente nessa máquina.

Ambos os métodos têm prós e contras:

-   Os backups em nível de host são flexíveis porque funcionam independentemente do tipo de sistema operacional em execução nas máquinas convidadas e não exigem a instalação do agente de proteção MABS em cada VM. Se você implantar o backup em nível de host, poderá recuperar uma máquina virtual inteira, ou arquivos e pastas (recuperação em nível de item).

-   O backup em nível de convidado será útil se você quiser proteger cargas de trabalho específicas em execução em uma máquina virtual. No nível do host, você pode recuperar uma VM inteira ou arquivos específicos, mas ele não fornecerá recuperação no contexto de um aplicativo específico. Por exemplo, para recuperar itens específicos do SharePoint de uma VM de backup, você deve fazer backup em nível de convidado dessa VM. Use o backup em nível de convidado se desejar proteger os dados armazenados em discos de passagem. A passagem permite que a máquina virtual acesse diretamente o dispositivo de armazenamento e não armazena dados de volume virtual em um arquivo VHD.



## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup
O MABS executa o backup com VSS da seguinte maneira. As etapas nesta descrição são numeradas para ajudar com a clareza.

1. O mecanismo de sincronização baseado em bloco do MABS faz uma cópia inicial da máquina virtual protegida e garante que a cópia da máquina virtual esteja completa e consistente.

2. Depois que a cópia inicial é feita e verificada, o MABS usa o gravador VSS do Hyper-V para capturar backups. O gravador VSS fornece um conjunto consistente de dados de blocos de disco que são sincronizados com o servidor MABS. Essa abordagem fornece o benefício de um "backup completo" com o servidor MABS, minimizando os dados de backup que devem ser transferidos pela rede.

3. O agente de proteção do MABS em um servidor que executa o Hyper-V usa as APIs do Hyper-V existentes para determinar se uma máquina virtual protegida também oferece suporte ao VSS.

   - Se uma máquina virtual estiver em conformidade com os requisitos de backup online e tiver o componente de serviços de integração do Hyper-V instalado, o gravador VSS do Hyper-V recursivamente encaminha a solicitação VSS para todos os processos de reconhecimento de VSS na máquina virtual. Essa operação ocorre sem que o agente de proteção do MABS esteja instalado na máquina virtual. A solicitação VSS recursiva permite que o gravador VSS do Hyper-V garanta que as operações de gravação em disco sejam sincronizadas para que um instantâneo do VSS seja capturado sem a perda de dados.

     O componente de serviços de integração do Hyper-V invoca o gravador VSS do Hyper-V no VSS (serviços de cópias de sombra de volume) em máquinas virtuais para garantir que seus dados de aplicativo estejam em um estado consistente.

   - Se a máquina virtual não estiver em conformidade com os requisitos de backup online, o MABS usará automaticamente as APIs do Hyper-V para pausar a máquina virtual antes de capturar os arquivos de dados.

4. Depois que a cópia de linha de base inicial da máquina virtual é sincronizada com o servidor MABS, todas as alterações feitas nos recursos da máquina virtual são capturadas em um novo ponto de recuperação. O ponto de recuperação representa o estado consistente da máquina virtual em um momento específico. As capturas de ponto de recuperação podem ocorrer pelo menos uma vez por dia. Quando um novo ponto de recuperação é criado, o MABS usa a replicação em nível de bloco em conjunto com o gravador VSS do Hyper-V para determinar quais blocos foram alterados no servidor que está executando o Hyper-V após a criação do último ponto de recuperação. Esses blocos de dados são então transferidos para o servidor MABS e aplicados à réplica dos dados protegidos.

5. O servidor MABS usa o VSS nos volumes que hospedam dados de recuperação para que várias cópias de sombra estejam disponíveis. Cada uma dessas cópias de sombra fornece uma recuperação separada. Os pontos de recuperação do VSS são armazenados no servidor MABS. A cópia temporária feita no servidor que executa o Hyper-V é armazenada somente durante a sincronização de MABS.

>[!NOTE]
>
>A partir do Windows Server 2016, os discos rígidos virtuais do Hyper-V têm controle de alterações interno conhecido como RCT (controle de alterações resiliente). O MABS usa RCT (o controle de alterações nativo no Hyper-V), que diminui a necessidade de verificações de consistência demoradas em cenários como falhas de VM. O RCT fornece melhor resiliência do que o rastreamento de alterações fornecido pelos backups baseados em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante as verificações de consistência.


## <a name="backup-prerequisites"></a>Pré-requisitos de backup
Estes são os pré-requisitos para fazer backup de máquinas virtuais do Hyper-V com MABS:

|Pré-requisito|Detalhes|
|------------|-------|
|Pré-requisitos do MABS|-Se você quiser executar a recuperação em nível de item para máquinas virtuais (recuperar arquivos, pastas, volumes), será necessário instalar a função Hyper-V no servidor MABS.  Se você quiser apenas recuperar a máquina virtual e não o nível de item, a função não será necessária.<br />-Você pode proteger até 800 máquinas virtuais de 100 GB cada em um servidor MABS e permitir vários servidores MABS que dão suporte a clusters maiores.<br />-MABS exclui o arquivo de paginação de backups incrementais para melhorar o desempenho de backup da máquina virtual.<br />-MABS pode fazer backup de um servidor ou cluster do Hyper-V no mesmo domínio que o servidor MABS ou em um domínio filho ou confiável. Se desejar fazer backup do Hyper-V em um grupo de trabalho ou em um domínio não confiável, você precisará configurar a autenticação. Para um único servidor Hyper-V, você pode usar autenticação NTLM ou de certificado. Para um cluster, você pode usar somente a autenticação de certificado.<br />-Não há suporte para o uso do backup em nível de host para fazer backup de dados de máquina virtual em discos de passagem. Nesse cenário, recomendamos que você use o backup em nível de host para fazer backup de arquivos VHD e backup em nível de convidado para fazer backup dos outros dados que não estão visíveis no host.<br />   -Você pode fazer backup de VMs armazenadas em volumes com eliminação de duplicação.|
|Pré-requisitos de VM do Hyper-V|-A versão dos componentes de integração em execução na máquina virtual deve ser a mesma que a versão do host do Hyper-V. <br />-Para cada backup de máquina virtual, você precisará de espaço livre no volume que hospeda os arquivos de disco rígido virtual para permitir espaço suficiente no Hyper-V para discos diferenciais (AVHD) durante o backup. O espaço deve ser pelo menos igual ao tempo da janela de **backup\*da taxa\*de variação do tamanho inicial do disco** de cálculo. Se você estiver executando vários backups em um cluster, precisará de capacidade de armazenamento suficiente para acomodar o AVHDs para cada uma das máquinas virtuais usando esse cálculo.<br />-Para fazer backup de máquinas virtuais localizadas em servidores host Hyper-V que executam o Windows Server 2012 R2, a máquina virtual deve ter um controlador SCSI especificado, mesmo que ele não esteja conectado a nada. (No Windows Server 2012 R2 online backup, o host Hyper-V monta um novo VHD na VM e depois o desmonta. Somente o controlador SCSI pode dar suporte a isso e, portanto, é necessário para o backup online da máquina virtual.  Sem essa configuração, a ID de evento 10103 será emitida quando você tentar fazer backup da máquina virtual.)|
|Pré-requisitos do Linux|-Você pode fazer backup de máquinas virtuais do Linux usando o MABS 2012 R2. Somente instantâneos consistentes com arquivos têm suporte.|
|Fazer backup de VMs com armazenamento CSV|-Para armazenamento CSV, instale o provedor de hardware do VSS (serviços de cópias de sombra de volume) no servidor Hyper-V. Contate seu fornecedor de SAN (rede de área de armazenamento) para o provedor de hardware VSS.<br />-Se um único nó for desligado inesperadamente em um cluster CSV, o MABS executará uma verificação de consistência nas máquinas virtuais que estavam em execução nesse nó.<br />-Se precisar reiniciar um servidor Hyper-V que tenha Criptografia de Unidade de Disco BitLocker habilitado no cluster CSV, você deverá executar uma verificação de consistência para máquinas virtuais do Hyper-V.|
|Fazer backup de VMs com armazenamento SMB|-Ative a montagem automática no servidor que está executando o Hyper-V para habilitar a proteção da máquina virtual.<br />   -Desabilitar descarregamento de Chimney TCP.<br />-Verifique se todas as contas do computador $ do Hyper-V têm permissões completas sobre os compartilhamentos de arquivos SMB remotos específicos.<br />-Verifique se o caminho do arquivo para todos os componentes da máquina virtual durante a recuperação para o local alternativo tem menos de 260 caracteres. Caso contrário, a recuperação pode ter sucesso, mas o Hyper-V não conseguirá montar a máquina virtual.<br />-Não há suporte para os seguintes cenários:<br />     Implantações em que alguns componentes da máquina virtual estão em volumes locais e alguns componentes estão em volumes remotos; um endereço IPv4 ou IPv6 para o servidor de arquivos do local de armazenamento e a recuperação de uma máquina virtual em um computador que usa compartilhamentos SMB remotos.<br />-Você precisará habilitar o serviço de agente VSS do servidor de arquivos em cada servidor SMB – adicione-o em **adicionar funções e recursos** >  > **selecionar funções** > de servidor Serviços**de arquivo e armazenamento**serviços de**arquivos**  >  **Serviço de arquivo** **Serviço de agente VSS do servidor de arquivos.**  > |

## <a name="back-up-virtual-machines"></a>Fazer backup de máquinas virtuais

1. Configure seu [servidor mAbs](backup-azure-microsoft-azure-backup.md) e [seu armazenamento](backup-mabs-add-storage.md). Ao configurar seu armazenamento, use essas diretrizes de capacidade de armazenamento.
   - Tamanho médio da máquina virtual-100 GB
   - Número de máquinas virtuais por servidor MABS-800
   - Tamanho total de 800 VMs-80 TB
   - Espaço necessário para armazenamento de backup-80 TB

2. Configure o agente de proteção do MABS no servidor Hyper-V ou nos nós do cluster Hyper-V. Se estiver fazendo um backup em nível de convidado, você instalará o agente nas VMs que deseja fazer backup no nível de convidado.

3. No console do administrador do mAbs, clique em **proteção** > **Criar grupo de proteção** para abrir o assistente para **criar novo grupo de proteção** .

4. Na página **selecionar membros do grupo** , selecione as VMs que você deseja proteger dos servidores host do Hyper-V nos quais elas estão localizadas. Recomendamos que você Coloque todas as VMs que terão a mesma política de proteção em um grupo de proteção. Para fazer uso eficiente do espaço, habilite a colocação. A colocação permite que você localize dados de grupos de proteção diferentes no mesmo armazenamento de disco ou fita, para que várias fontes de dados tenham uma única réplica e volume de ponto de recuperação.

5. Na página **selecionar método de proteção de dados** , especifique um nome de grupo de proteção. Selecione **desejo proteção de curto prazo usando disco** e selecione **desejo proteção online** se você quiser fazer backup de dados no Azure usando o serviço de backup do Azure. 


6. Em **especificar objetivos** > de curto prazo**período de retenção**, especifique quanto tempo deseja manter os dados do disco. Em **frequência de sincronização**, especifique com que frequência os backups incrementais dos dados devem ser executados. Como alternativa, em vez de selecionar um intervalo para backups incrementais, você pode habilitar **logo antes de um ponto de recuperação**. Com essa configuração habilitada, o MABS executará um backup completo expresso antes de cada ponto de recuperação agendado.

    > [!NOTE]
    >
    >Se você estiver protegendo cargas de trabalho de aplicativo, os pontos de recuperação serão criados de acordo com a frequência de sincronização, desde que o aplicativo dê suporte a backups incrementais. Se não estiver, o MABS executará um backup completo expresso, em vez de um backup incremental, e criará pontos de recuperação de acordo com o agendamento de backup expresso.

    

7. Na página **rever alocação do disco** , examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

   O **Tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup e o **espaço em disco a ser provisionado no mAbs** é o espaço que o mAbs recomenda para o grupo de proteção. MABS escolhe o volume de backup ideal, com base nas configurações. No entanto, você pode editar as opções de volume de backup nos **detalhes de alocação de disco**. Para as cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. Suas edições alteram os valores de **Armazenamento Total** e **Armazenamento Gratuito** no painel de **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de MABS de armazenamento que sugere que você adicione ao volume para continuar com os backups sem problemas no futuro.

8. Na página **escolher método de criação de réplica** , especifique como a replicação inicial de dados no grupo de proteção será executada. Se você optar por **replicar automaticamente pela rede**, recomendamos que você escolha um horário de pico. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere selecionar **manualmente**, o que requer a replicação de dados offline usando mídia removível.

9. Na página **Opções de verificação de consistência** , selecione como você deseja automatizar as verificações de consistência. Você pode habilitar uma verificação para ser executada somente quando os dados da réplica se tornarem inconsistentes ou de acordo com uma agenda. Se você não quiser configurar a verificação de consistência automática, poderá executar uma verificação manual a qualquer momento clicando com o botão direito do mouse no grupo de proteção e selecionando **executar verificação de consistência**.

    Depois de criar o grupo de proteção, a replicação inicial dos dados ocorre de acordo com o método selecionado. Após a replicação inicial, cada backup ocorrerá em linha com as configurações do grupo de proteção. Se você precisar recuperar os dados de backup, observe o seguinte:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Fazer backup de máquinas virtuais configuradas para migração dinâmica
Quando as máquinas virtuais estiverem envolvidas na migração ao vivo, o MABS continuará a proteger as máquinas virtuais, desde que o agente de proteção do MABS esteja instalado no host do Hyper-V. A maneira como o MABS protege as máquinas virtuais depende do tipo de migração ao vivo envolvido.

**Migração ao vivo em um cluster** – quando uma máquina virtual é migrada em um cluster, o mAbs detecta a migração e faz o backup da máquina virtual a partir do novo nó de cluster, sem nenhum requisito de intervenção do usuário. Como o local de armazenamento não foi alterado, o MABS continua com backups completos expressos. 

**Migração ao vivo fora do cluster** – quando uma máquina virtual é migrada entre servidores autônomos, diferentes clusters ou entre um servidor autônomo e um cluster, o mAbs detecta a migração e pode fazer backup da máquina virtual sem usuário intervenção.

### <a name="requirements-for-maintaining-protection"></a>Requisitos para manter a proteção

Veja a seguir os requisitos para manter a proteção durante a migração ao vivo:

- Os hosts do Hyper-V para as máquinas virtuais devem estar localizados em uma nuvem do System Center VMM, em um servidor do VMM, executando pelo menos o System Center 2012 com SP1.

- O agente de proteção do MABS deve ser instalado em todos os hosts do Hyper-V.

- Os servidores MABS devem estar conectados ao servidor do VMM. Todos os servidores de host Hyper-V na nuvem do VMM também devem estar conectados aos servidores MABS. Isso permite que o MABS se comunique com o servidor do VMM para que o MABS possa descobrir em qual servidor host Hyper-V a máquina virtual está sendo executada, e criar um novo backup desse servidor Hyper-V. Se não for possível estabelecer uma conexão com o servidor Hyper-V, o backup falhará com uma mensagem informando que o agente de proteção do MABS está inacessível.

- Todos os servidores MABS, servidores VMM e servidores host Hyper-V devem estar no mesmo domínio.

### <a name="details-about-live-migration"></a>Detalhes sobre a migração dinâmica

Observe o seguinte para backup durante a migração ao vivo:


- Se uma migração ao vivo transferir armazenamento, o MABS executará uma verificação de consistência completa da máquina virtual e continuará com os backups completos expressos. Quando ocorre a migração ao vivo do armazenamento, o Hyper-V reorganiza o disco rígido virtual (VHD) ou o VHDX, o que causa um pico único no tamanho dos dados de backup do MABS.

- No host da máquina virtual, ative a montagem automática para habilitar a proteção virtual e desabilite o descarregamento de Chimney TCP.

- O MABS usa a porta 6070 como a porta padrão para hospedar o serviço auxiliar do DPM-VMM. Para alterar o registro:

    1. Navegue até **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Crie um valor DWORD de 32 bits: DpmVmmHelperServicePort e escreva o número da porta atualizada como parte da chave do registro.
    3.  Abra ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```o e altere o número da porta de 6070 para a nova porta. Por exemplo: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Reinicie o serviço auxiliar do DPM-VMM e reinicie o serviço DPM.

### <a name="set-up-protection-for-live-migration"></a>Configurar a proteção para migração ao vivo

Para configurar a proteção para migração dinâmica:

1. Configure o servidor MABS e seu armazenamento e instale o agente de proteção do MABS em cada servidor host Hyper-V ou nó de cluster na nuvem do VMM. Se você estiver usando o armazenamento SMB em um cluster, instale o agente de proteção do MABS em todos os nós de cluster.

2. Instale o console do VMM como um componente de cliente no servidor MABS para que o MABS possa se comunicar com o servidor do VMM. O console deve ter a mesma versão que a que está sendo executada no servidor do VMM.

3. Atribua a conta MABSMachineName $ como uma conta de administrador somente leitura no servidor de gerenciamento do VMM.

4. Conecte todos os servidores de host Hyper-V a todos os servidores `Set-DPMGlobalProperty` mAbs com o cmdlet do PowerShell. O cmdlet aceita vários nomes de servidor MABS. Use o formato: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Para obter mais informações, consulte [set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx).

5. Depois que todas as máquinas virtuais em execução nos hosts do Hyper-V nas nuvens do VMM forem descobertas no VMM, configure um grupo de proteção e adicione as máquinas virtuais que deseja proteger. As verificações de consistência automáticas devem ser habilitadas no nível do grupo de proteção para proteção em cenários de mobilidade de máquina virtual.

6. Depois que as configurações são definidas, quando uma máquina virtual migra de um cluster para outro, todos os backups continuam conforme o esperado. Você pode verificar se a migração dinâmica está habilitada conforme o esperado da seguinte maneira:

   1. Verifique se o serviço auxiliar do DPM-VMM está em execução. Se não estiver, inicie-o.

   2. Abra Microsoft SQL Server Management Studio e conecte-se à instância que hospeda o banco de dados MABS (DPMDB). No DPMDB, execute a seguinte consulta `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`:.

      Esta consulta contém uma propriedade chamada `KnownVMMServer`. Esse valor deve ser o mesmo valor fornecido com o `Set-DPMGlobalProperty` cmdlet.

   3. Execute a consulta a seguir para validar o parâmetro *VMMIdentifier* no `PhysicalPathXML` para uma máquina virtual específica. Substitua `VMName` pelo nome da máquina virtual.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Abra o arquivo. XML que essa consulta retorna e valide se o campo *VMMIdentifier* tem um valor.


### <a name="run-manual-migration"></a>Executar migração manual

Depois de concluir as etapas nas seções anteriores, e o trabalho do Gerenciador de resumo do MABS for concluído, a migração será habilitada. Por padrão, esse trabalho começa à meia-noite e é executado a cada manhã. Se você quiser executar uma migração manual, para verificar se tudo está funcionando conforme o esperado, faça o seguinte:

1. Abra SQL Server Management Studio e conecte-se à instância que hospeda o banco de dados MABS.

2. Execute a seguinte consulta: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Essa consulta retorna a **ScheduleID**. Anote essa ID, pois você a usará na próxima etapa.

3. Na SQL Server Management Studio, expanda **SQL Server Agent**e, em seguida, expanda **trabalhos**. Clique com o  botão direito do mouse em ScheduleID que você anotou e selecione **Iniciar trabalho na etapa**.

O desempenho do backup é afetado quando o trabalho é executado. O tamanho e a escala da sua implantação determinam quanto tempo o trabalho leva para ser concluído.


## <a name="back-up-replica-virtual-machines"></a>Fazer backup de máquinas virtuais de réplica

Se o MABS estiver em execução no Windows Server 2012 R2 ou superior, você poderá fazer backup de máquinas virtuais de réplica. Isso é útil por vários motivos:

**Reduz o impacto dos backups na carga de trabalho em execução** – fazer um backup de uma máquina virtual incorre em sobrecarga conforme um instantâneo é criado. Descarregando o processo de backup em um site remoto secundário, a carga de trabalho em execução não é mais afetada pela operação de backup. Isso é aplicável somente a implantações em que a cópia de backup está armazenada em um site remoto. Por exemplo, você pode fazer backups diários e armazenar dados localmente para garantir tempos de restauração rápidos, mas fazer backups mensais ou trimestrais de máquinas virtuais de réplica armazenadas remotamente para retenção de longo prazo.

**Economiza largura de banda** – em uma implantação típica de escritório/filial remota, você precisa de uma quantidade apropriada de largura de banda provisionada para transferir dados de backup entre sites. Se você criar uma estratégia de replicação e failover, além de sua estratégia de backup de dados, poderá reduzir a quantidade de dados redundantes enviados pela rede. Ao fazer backup dos dados da máquina virtual de réplica em vez do primário, você economiza a sobrecarga de enviar os dados de backup pela rede.

**Habilita o backup** do hoster – você pode usar um datacenter hospedado como um site de réplica, sem a necessidade de um datacenter secundário. Nesse caso, o SLA do hoster requer backup consistente de máquinas virtuais de réplica.

Uma máquina virtual de réplica é desativada até que um failover seja iniciado, e o VSS não pode garantir um backup consistente com o aplicativo para uma máquina virtual de réplica. Assim, o backup de uma máquina virtual de réplica será consistente somente com falhas. Se não for possível garantir a consistência de falhas, o backup falhará e isso poderá ocorrer em várias condições:

- A máquina virtual de réplica não está íntegra e está em um estado crítico.

- A máquina virtual de réplica está sendo ressincronizada (no estado ressincronização em andamento ou ressincronização necessária).

- A replicação inicial entre o site primário e o secundário está em andamento ou pendente para a máquina virtual.

- os logs. HRL estão sendo aplicados à máquina virtual de réplica ou uma ação anterior para aplicar os logs. HRL no disco virtual falhou ou foi cancelada ou interrompida.

- A migração ou o failover da máquina virtual de réplica está em andamento

## <a name="recover-backed-up-virtual-machines"></a>Recuperar as máquinas virtuais de backup

Quando você puder recuperar uma máquina virtual de backup, use o assistente de recuperação para selecionar a máquina virtual e o ponto de recuperação específico. Para abrir o assistente de recuperação e recuperar uma máquina virtual:

1. No console do administrador do MABS, digite o nome da VM ou expanda a lista de itens protegidos e selecione a VM que você deseja recuperar.

2. No painel **pontos de recuperação para** , no calendário, clique em qualquer data para ver os pontos de recuperação disponíveis. Em seguida, no painel **caminho** , selecione o ponto de recuperação que deseja usar no assistente de recuperação.

3. No menu **ações** , clique em **recuperar** para abrir o assistente de recuperação.

    A VM e o ponto de recuperação selecionados aparecem na tela **examinar seleção de recuperação** . Clique em **Avançar**.

4. Na tela **Selecionar tipo de recuperação** , selecione o local em que deseja restaurar os dados e clique em **Avançar**.

    -   **Recuperar na instância original**: Quando você recupera a instância original, o VHD original é excluído. MABS recupera o VHD e outros arquivos de configuração para o local original usando o gravador VSS do Hyper-V. No final do processo de recuperação, as máquinas virtuais ainda estão altamente disponíveis.
        O grupo de recursos deve estar presente para recuperação. Se não estiver disponível, recupere para um local alternativo e torne a máquina virtual altamente disponível.

    -   **Recuperar como máquina virtual em qualquer host**: O MABS dá suporte à ALR (recuperação de local alternativo), que fornece uma recuperação direta de uma máquina virtual protegida do Hyper-V para um host do Hyper-V diferente, independentemente da arquitetura do processador. As máquinas virtuais do Hyper-V que são recuperadas para um nó de cluster não estarão altamente disponíveis. Se você escolher essa opção, o assistente de recuperação apresentará uma tela adicional para identificar o destino e o caminho de destino.

    -   **Copiar para uma pasta de rede**: O MABS dá suporte à ILR (recuperação em nível de item), que permite fazer a recuperação em nível de item de arquivos, pastas, volumes e VHDs (discos rígidos virtuais) de um backup em nível de host de máquinas virtuais do Hyper-V em um volume ou compartilhamento de rede em um servidor MABS protegido. O agente de proteção do MABS não precisa ser instalado dentro do convidado para executar a recuperação em nível de item. Se você escolher essa opção, o assistente de recuperação apresentará uma tela adicional para identificar o destino e o caminho de destino.

5. Em **especificar opções de recuperação** , configure as opções de recuperação e clique em **Avançar**:

    - Se você estiver recuperando uma VM com pouca largura de banda, clique em **Modificar** para habilitar a **limitação do uso de largura de banda**. Depois de ativar a opção de limitação, você pode especificar a quantidade de largura de banda que deseja disponibilizar e a hora em que a largura de banda está disponível.
    - Selecione **habilitar a recuperação baseada em San usando instantâneos de hardware** se você tiver configurado sua rede.
    - Selecione **enviar um email quando a recuperação for concluída** e forneça os endereços de email, se desejar que as notificações por email sejam enviadas quando o processo de recuperação for concluído.

6. Na tela Resumo, verifique se todos os detalhes estão corretos. Se os detalhes não estiverem corretos ou se você quiser fazer uma alteração, clique em **voltar**. Se você estiver satisfeito com as configurações, clique em **recuperar** para iniciar o processo de recuperação.

7. A tela **status da recuperação** fornece informações sobre o trabalho de recuperação.


## <a name="next-steps"></a>Próximas etapas
[Recuperar dados de outro Servidor de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
