---
title: Solucionar problemas de Servidor de Backup do Azure
description: Solucionar problemas de instalação, registro de Servidor de Backup do Azure e backup e restauração de cargas de trabalho do aplicativo.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 09e5fe5da7e316257cbbdcb89074fe8a4bc692c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91403000"
---
# <a name="troubleshoot-azure-backup-server"></a>Solucionar problemas de Servidor de Backup do Azure

Use as informações nas tabelas a seguir para solucionar problemas de erros encontrados durante o uso do Servidor de Backup do Azure.

## <a name="basic-troubleshooting"></a>Solução básica de problemas

Recomendamos que você execute a seguinte validação, antes de iniciar a solução de problemas do servidor de Backup do Microsoft Azure (MABS):

- [Verifique se o Agente de MARS (Serviços de Recuperação do Microsoft Azure) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verifique se há conectividade de rede entre o agente MARS e o Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (no console de Serviço). Se necessário, reinicie e repita a operação
- [Certifique-se de que há de 5 a 10% de espaço de volume livre disponível no local da pasta temporária](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Se o registro estiver falhando, verifique se o servidor no qual você está tentando instalar o Servidor de Backup do Azure já não está registrado com outro cofre
- Se a instalação por push falhar, verifique se o agente de DPM já existe. Se sim, desinstale o agente e tente instalar novamente
- [Verifique se algum outro processo ou software antivírus está interferindo no Backup do Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Verifique se o serviço SQL Agent está em execução e definido como automático no servidor MABS<br>

## <a name="configure-antivirus-for-mabs-server"></a>Configurar o antivírus para o MABS Server

O MABS é compatível com os produtos de software antivírus mais populares. Recomendamos as seguintes etapas para evitar conflitos:

1. **Desabilitar monitoramento em tempo real** – desabilite o monitoramento em tempo real pelo software antivírus para o seguinte:
    - `C:\Program Files<MABS Installation path>\XSD` pasta
    - `C:\Program Files<MABS Installation path>\Temp` pasta
    - Letra da unidade do volume de Armazenamento de Backup Moderno
    - Logs de réplica e transferência: para fazer isso, desabilite o monitoramento em tempo real de **dpmra.exe**, que está localizado na pasta `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . O monitoramento em tempo real degrada o desempenho porque o software antivírus examina as réplicas toda vez que o MABS sincroniza com o servidor protegido e examina todos os arquivos afetados toda vez que o MABS aplica as alterações às réplicas.
    - Console do administrador: para evitar um impacto no desempenho, desabilite o monitoramento em tempo real do processo de **csc.exe** . O processo de **csc.exe** é o \# compilador C, e o monitoramento em tempo real pode prejudicar o desempenho porque o software antivírus examina os arquivos emitidos pelo processo de **csc.exe** ao gerar mensagens XML. **CSC.exe** está localizado nos seguintes caminhos:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - Para o agente MARS instalado no servidor MABS, recomendamos que você exclua os seguintes arquivos e locais:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` como um processo
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Local de rascunho (se você não estiver usando o local padrão)
2. **Desabilitar o monitoramento em tempo real no servidor protegido**: desabilite o monitoramento em tempo real do **dpmra.exe**, localizado na pasta `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , no servidor protegido.
3. **Configure o software antivírus para excluir os arquivos infectados nos servidores protegidos e o servidor mAbs**: para evitar a corrupção de dados de réplicas e pontos de recuperação, configure o software antivírus para excluir arquivos infectados, em vez de limpá-los automaticamente ou colocá-los em quarentena. A limpeza e a quarentena automáticas podem fazer com que o software antivírus modifique arquivos, fazendo alterações que o MABS não consegue detectar.

Execute manualmente um trabalho de sincronização com uma consistência. Verifique o trabalho toda vez que o software antivírus exclui um arquivo da réplica, mesmo que a réplica esteja marcada como inconsistente.

### <a name="mabs-installation-folders"></a>Pastas de instalação do MABS

As pastas de instalação padrão do DPM são as seguintes:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Você também pode executar o seguinte comando para localizar o caminho da pasta de instalação:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Credenciais do cofre fornecidas inválidas

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Registrando em um cofre | Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. | Ação recomendada: <br> <ul><li> Faça o download do arquivo de credenciais mais recentes do cofre e tente novamente. <br>(OU)</li> <li> Se a ação anterior não funcionar, tente fazer o download das credenciais para um diretório local diferente ou criar um novo cofre. <br>(OU)</li> <li> Tente atualizar as configurações de data e hora conforme descrito [neste artigo](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>(OU)</li> <li> Verifique para ver se c:\windows\temp tem mais de 65000 arquivos. Mover arquivos obsoletos para outro local ou exclua os itens na pasta Temp. <br>(OU)</li> <li> Verificar o status dos certificados. <br> a. Abra **Gerenciar Certificados de Computador** (no painel de controle). <br> b. Expandir o nó **Pessoal** e seus **Certificados** de nó filho.<br> c.  Remover o certificado **Ferramentas do Microsoft Azure**. <br> d. Repetir o registro no cliente de Backup do Azure. <br> (OU) </li> <li> Verificar se alguma política de grupo está em vigor. </li></ul> |

## <a name="replica-is-inconsistent"></a>A réplica está inconsistente

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | A réplica está inconsistente | Verifique se a opção de verificação de consistência automática no Assistente de grupo de proteção está ativada. Para obter mais informações sobre opções de replicação e verificações de consistência, confira [este artigo](/system-center/dpm/create-dpm-protection-groups).<br> <ol><li> No caso do backup de estado do sistema/BMR, verifique se o Backup do Windows Server está instalado no servidor protegido.</li><li> Verifique se há problemas relacionados ao espaço no pool de armazenamento do DPM no Servidor de Backup do Azure/DPM, e alocar armazenamento conforme necessário.</li><li> Verificar o estado do Serviço de Cópias de Sombra de Volume no servidor protegido. Se ele estiver em um estado desabilitado, defina-o para iniciar manualmente. Inicie o serviço no servidor. Em seguida, volte para o console do Servidor de Backup do Azure/DPM e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Falha na criação de ponto de recuperação online

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | Falha na criação de ponto de recuperação online | **Mensagem de erro**: O Agente de Backup do Microsoft Azure não conseguiu criar um instantâneo do volume selecionado. <br> **Solução alternativa**: Tente aumentar o espaço no volume de ponto de recuperação e de réplica.<br> <br> **Mensagem de erro**: O Agente de Backup do Microsoft Azure não conseguiu conectar-se ao serviço OBEngine <br> **Solução alternativa**: verifique se o OBEngine consta na lista de serviços em execução no computador. Se o serviço OBEngine não estiver em execução, use o comando "net start OBEngine" para iniciar o serviço OBEngine. <br> <br> **Mensagem de erro**: A frase secreta de criptografia para este servidor não está definida. Configure uma frase secreta de criptografia. <br> **Solução alternativa**: Tente configurar uma frase secreta de criptografia. Se falhar, execute as seguintes etapas: <br> <ol><li>Verifique se o local de rascunho existe. Esse é o local que é mencionado no registro **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, com o nome **ScratchLocation** deve existir.</li><li> Se houver um local temporário, tente registrar novamente usando a frase secreta antiga. *Sempre que você configurar uma frase secreta de criptografia, salve-a em um local seguro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Os servidores DPM originais e externos devem ser registrados no mesmo cofre

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Restaurar | **Erro de código**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Mensagem de erro**: Os servidores DPM originais e externos devem ser registrados no mesmo cofre | **Causa**: esse problema ocorre quando você está tentando restaurar arquivos para o servidor alternativo a partir do servidor original usando a opção de recuperação externa do DPM e se o servidor que está sendo recuperado e o servidor original do qual os dados são copiados não estão associados ao mesmo cofre dos serviços de recuperação.<br/> <br/>**Solução alternativa**: para resolver esse problema, verifique se tanto o servidor original como o alternativo estão registrados no mesmo cofre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Falha em trabalhos de criação de ponto de recuperação online para VM do VMware

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | Falha em trabalhos de criação de ponto de recuperação online para VM do VMware. O DPM encontrou um erro de VMware ao tentar obter informações de ChangeTracking. ErrorCode – FileFaultFault (ID 33621 ) |  <ol><li> Redefinir o CTK no VMware para as VMs afetadas.</li> <li>Verificar se o disco independente não está em vigor no VMware.</li> <li>Interrompa a proteção para as VMs afetadas e proteja novamente com o botão **Atualizar**. </li><li>Executar uma CC para as VMs afetadas.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM no Servidor

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Enviar agentes por push para servidores protegidos | Falha na operação do agente devido a um erro de comunicação com o serviço de Coordenador de Agentes do DPM no \<ServerName> . | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <ul><li> Se você estiver anexando um computador de um domínio não confiável, siga [estas etapas](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains). <br> (OU) </li><li> Se você estiver anexando um computador de um domínio confiável, solucione o problema usando as etapas descritas neste [blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>(OU)</li><li> Tente desabilitar o antivírus como uma etapa de solução de problemas. Se isso resolver o problema, modifique as configurações de antivírus conforme sugerido [neste artigo](/system-center/dpm/run-antivirus-server).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A instalação não pôde atualizar os metadados do Registro

| Operação | Detalhes do erro | Solução alternativa |
|-----------|---------------|------------|
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da Filtragem ReFS. | Ajuste a chave do Registro **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Defina o valor Dword como 1. |
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da SnapOptimization do Volume. | Crie a chave do Registro **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** com um valor de cadeia de caracteres vazia. |

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados ao registro e ao agente

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Enviar agentes por push para servidores protegidos | As credenciais especificadas para o servidor são inválidas. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <br> Tente instalar o agente de proteção manualmente no servidor de produção, como especificado [neste artigo](/system-center/dpm/deploy-dpm-protection-agent).|
| O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure (ID: 100050) | O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <br>1. Execute o comando a seguir de um prompt elevado: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Isso abrirá a janela do Internet Explorer. <br/> 2. Acesse **Ferramentas** > **Opções de Internet** > **Conexões** > **Configurações de LAN**. <br/> 3. Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<br/> 4. Se o seu computador tiver acesso limitado à Internet, verifique se as configurações de firewall no computador ou proxy permitem essas [URLs](install-mars-agent.md#verify-internet-access) e [endereço IP](install-mars-agent.md#verify-internet-access).|
| Falha na instalação do Agente de backup do Azure | Falha na instalação dos Serviços de Recuperação do Microsoft Azure. Todas as alterações feitas ao sistema pela instalação dos Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instalar o Agente do Azure manualmente.

## <a name="configuring-protection-group"></a>Configurar grupo de proteção

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não pôde enumerar o componente do aplicativo no computador protegido (nome do computador protegido). | Selecione **Atualizar** na tela da interface do usuário configurar grupo de proteção no nível de componente/fonte de dados relevante. |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL Server, verifique se as permissões da função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido conforme descrito [neste artigo](/system-center/dpm/back-up-sql-server).
| Configurar grupos de proteção | Não há espaço livre suficiente no pool de armazenamento para esse grupo de proteção. | Os discos que são adicionados ao pool de armazenamento [não devem conter uma partição](/system-center/dpm/create-dpm-protection-groups). Exclua todos os volumes existentes nos discos. Em seguida, adicione-os ao pool de armazenamento.|
| Alteração da política |Não foi possível modificar a política de backup. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo ter passado. Se o problema persistir, contate o Suporte da Microsoft. | **Causa:**<br/>Esse erro ocorre em três condições: quando as configurações de segurança são habilitadas, quando você tenta reduzir o período de retenção abaixo dos valores mínimos especificados anteriormente, e quando você está em uma versão sem suporte. (As versões sem suporte são aquelas menores que Backup do Microsoft Azure Server versão 2.0.9052 e Servidor de Backup do Azure atualização 1.) <br/>**Ação recomendada:**<br/> Para continuar com atualizações relacionadas à política, defina o período de retenção acima do período mínimo de retenção especificado. (O período mínimo de retenção é de sete dias para diariamente, quatro semanas para semanal, três semanas para mensal ou um ano para anual.) <br><br>Opcionalmente, outra abordagem preferencial será atualizar o agente de backup e o Servidor de Backup do Azure para utilizar todas as atualizações de segurança. |

## <a name="backup"></a>Backup

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | Ocorreu um erro inesperado durante a execução do trabalho. O dispositivo não está pronto. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas:** <br> <ul><li>Defina o Espaço de Armazenamento de Cópia de Sombra para ilimitado nos itens no grupo de proteção e execute a verificação de consistência.<br></li> (OU) <li>Tente excluir o grupo de proteção existente e criar vários grupos novos. Cada novo grupo de proteção deve ter um item individual dentro dele.</li></ul> |
| Backup | Se você estiver fazendo backup somente do estado do sistema, verifique se há espaço livre suficiente no computador protegido para armazenar o backup do estado do sistema. | <ol><li>Verifique se o Backup do Windows Server está instalado no computador protegido.</li><li>Verifique se há espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil de verificar isso é acessar o computador protegido, abrir o Backup do Windows Server, clicar nas seleções e, em seguida, selecionar a BMR. A interface do usuário lhe mostrará quanto espaço é necessário. Abra **WSB** > **Backup local** > **Agendamento de backup** > **Selecionar configuração de backup** > **Servidor completo** (o tamanho é exibido). Use esse tamanho para verificação.</li></ol>
| Backup | Falha de backup para BMR | Se o tamanho de BMR for grande, mova alguns arquivos do aplicativo para a unidade do sistema operacional e tente novamente. |
| Backup | A opção de proteger novamente uma VM do VMware em um novo Servidor de Backup do Azure não aparece como disponível para adicionar. | As propriedades do VMware são apontadas para uma instância antiga e obsoleta do Servidor de Backup do Azure. Para resolver o problema:<br><ol><li>No VCenter (equivalente do SC-VMM), vá para a guia **Resumo** e então **Atributos Personalizados**.</li>  <li>Exclua o antigo nome do Servidor de Backup do Azure do valor **DPMServer**.</li>  <li>Volte para o novo Servidor de Backup do Azure e modifique o PG.  Depois de selecionar o botão **Atualizar**, a VM é exibida com uma caixa de seleção conforme disponível para adicionar para proteção.</li></ol> |
| Backup | Erro ao acessar pastas/arquivos compartilhados | Tente modificar as configurações de antivírus conforme sugerido neste artigo [Executar software antivírus no servidor DPM](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Alterar frase secreta

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Alterar frase secreta |O PIN de Segurança que foi inserido está incorreto. Forneça o PIN de Segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de segurança inválido ou expirado enquanto estiver executando uma operação crítica (como alterar uma senha). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de Segurança válido. Para obter o PIN, entre no portal do Azure e acesse o cofre dos Serviços de Recuperação. Em seguida, vá para **Configurações** > **Propriedades** > **Gerar PIN de segurança**. Use esse PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas, ou quando você tenta alterar a frase secreta quando você está usando uma versão sem suporte.<br/>**Ação recomendada:**<br/> Para alterar a senha, você deve primeiro atualizar o agente de backup para a versão mínima, que é 2.0.9052. Você também precisa atualizar o Servidor de Backup do Azure para a versão mínima de atualização 1 e, em seguida, inserir um PIN de segurança válido. Para obter o PIN, entre no portal do Azure e acesse o cofre dos Serviços de Recuperação. Em seguida, vá para **Configurações** > **Propriedades** > **Gerar PIN de segurança**. Use esse PIN para alterar a frase secreta. |

## <a name="configure-email-notifications"></a>Configurar notificações por email

| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurando notificações por email usando uma conta corporativa ou de estudante |ID do erro: 2013| **Causa:**<br> Tentando usar a conta corporativa ou de estudante <br>**Ação recomendada:**<ol><li> A primeira coisa a garantir é que a opção "Permitir Retransmissão Anônima em um Conector de Recebimento" para o servidor DPM esteja configurada no Exchange. Para obter mais informações sobre como configurar isso, confira [Permitir Retransmissão Anônima em um Conector de Recebimento](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Se você não pode usar uma retransmissão SMTP interna e precisa configurar usando o servidor do Office 365, você pode configurar o IIS para ser uma retransmissão. Configure o servidor DPM para [retransmitir o SMTP para o Office 365 usando o IIS](/exchange/mail-flow/test-smtp-with-telnet).<br><br>  Certifique-se de usar o formato usuário\@domínio.com e *não* domínio\usuário.<br><br><li>Aponte o DPM para usar o nome do servidor local como servidor SMTP, porta 587. Em seguida, aponte para o e-mail de usuário de onde os e-mails devem vir.<li> O nome de usuário e a senha na página de instalação de SMTP do DPM devem ser para uma conta de domínio no domínio em que o DPM está. </li><br> Quando você estiver alterando o endereço do servidor SMTP, faça a alteração nas novas configurações, feche a caixa configurações e, em seguida, abra-a novamente para ter certeza de que ela reflete o novo valor.  Simplesmente alterar e testar pode nem sempre fazer com que as novas configurações tenham efeito, então testar assim é a melhor prática.<br><br>A qualquer momento durante esse processo, você pode limpar essas configurações fechando o console do DPM e editando as seguintes chaves do registro: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys**. Você pode adicioná-las novamente na interface do usuário quando você iniciá-lo novamente.

## <a name="common-issues"></a>Problemas comuns

Esta seção aborda os erros comuns que você pode encontrar ao usar o Servidor de Backup do Azure.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Mensagem de erro | Ação recomendada |
-- | --
O backup falhou porque a réplica de backup em disco é inválida ou está ausente. | Para resolver esse problema, verifique as etapas abaixo e repita a operação: <br/> 1. Criar um ponto de recuperação de disco<br/> 2. Executar verificação de consistência na fonte de dados <br/> 3. Interromper a proteção da fonte de dados e reconfigurar a proteção dessa fonte de dados

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou porque os metadados na réplica são inválidos. | Crie um ponto de recuperação de disco dessa fonte de dados e tente fazer o backup online novamente

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou devido à réplica inconsistente da fonte de dados. | Execute uma verificação de consistência nessa fonte de dados e tente novamente

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Mensagem de erro | Ação recomendada |
-- | --
O backup falhou porque a réplica de backup de disco não pôde ser clonada.| Verifique se todos os arquivos de réplica de backup de disco anteriores (.vhdx) estão desmontados e se não há backup de disco para disco em andamento durante os backups online
