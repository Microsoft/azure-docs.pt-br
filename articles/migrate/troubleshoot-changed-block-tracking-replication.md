---
title: Solucionar problemas de replicação na migração de VM do VMware sem agente
description: Obter ajuda com falhas do ciclo de replicação
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 5487e51ad73ab903e7b61de266e2c28d282a56c1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568651"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Solucionando problemas de replicação na migração de VM do VMware sem agente

Este artigo descreve alguns problemas comuns e erros específicos que você pode encontrar ao replicar VMs VMware locais usando o método migrar do Azure: servidor sem agente de migração de servidores.

Quando você replica uma máquina virtual VMware usando o método de replicação sem agente, os dados da máquina virtual&#39;s discos (VMDKs) são replicados para discos gerenciados de réplica em sua assinatura do Azure. Quando a replicação é iniciada para uma VM, ocorre um ciclo de replicação inicial no qual as cópias completas dos discos são replicadas. Após a conclusão da replicação inicial, os ciclos de replicação incremental são agendados periodicamente para transferir as alterações ocorridas desde o ciclo de replicação anterior.

Ocasionalmente, você pode ver os ciclos de replicação falharem para uma VM. Essas falhas podem ocorrer devido a motivos variados de problemas na configuração de rede local para problemas no back-end do serviço de nuvem de migrações para Azure. Neste artigo, iremos:

 - Mostre como você pode monitorar o status de replicação e resolver erros.
 - Listar alguns dos erros de replicação que ocorrem com frequência e sugerir etapas adicionais para corrigi-los.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Monitorar o status de replicação usando o portal do Azure

Use as etapas a seguir para monitorar o status de replicação para suas máquinas virtuais:

  1. Vá para a página servidores em migrações para Azure no portal do Azure.
  ![Imagem 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. Navegue até a página "replicando máquinas" clicando em "replicando servidores" no bloco de migração do servidor.
  ![Imagem 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. Você verá uma lista de servidores de replicação juntamente com informações adicionais, como status, integridade, hora da última sincronização, etc. A coluna de integridade indica a integridade da replicação atual da VM. Um valor ' crítico ' ou ' aviso ' na coluna de integridade normalmente indica que o ciclo de replicação anterior da VM falhou. Para obter mais detalhes, clique com o botão direito do mouse na VM e selecione "detalhes do erro". A página detalhes do erro contém informações sobre o erro e detalhes adicionais sobre como solucionar problemas. Você também verá um link "eventos recentes" que pode ser usado para navegar até a página de eventos da VM.
  ![Imagem 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. Clique em "eventos recentes" para ver as falhas do ciclo de replicação anterior da VM. Na página eventos, procure o evento mais recente do tipo "falha no ciclo de replicação" ou "falha no ciclo de replicação para o disco" para a VM.
  ![Imagem 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. Clique no evento para entender as possíveis causas do erro e as etapas de correção recomendadas. Use as informações fornecidas para solucionar problemas e corrigir o erro.
 ![Imagem 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>Erros comuns de replicação

Esta seção descreve alguns dos erros comuns e como você pode solucioná-los.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Erro de Key Vault operação com falha ao tentar replicar VMs

**Erro:** "Falha na operação de Key Vault. Operação: configurar a conta de armazenamento gerenciada, Key Vault: Key-Vault-Name, conta de armazenamento: o nome da conta de armazenamento falhou com o erro: "

**Erro:** "Falha na operação de Key Vault. Operação: gerar definição de assinatura de acesso compartilhado, Key Vault: Key-Vault-Name, conta de armazenamento: o nome da conta de armazenamento falhou com o erro: "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Esse erro normalmente ocorre porque a política de acesso do usuário para o Key Vault não dá ao usuário conectado no momento as permissões necessárias para configurar as contas de armazenamento a serem Key Vault gerenciadas. Para verificar a política de acesso do usuário no cofre de chaves, acesse a página do cofre de chaves no portal do cofre de chaves e selecione políticas de acesso 

Quando o portal cria o cofre de chaves, ele também adiciona uma política de acesso de usuário que concede as permissões de usuário atualmente conectadas para configurar contas de armazenamento a serem Key Vault gerenciadas. Isso pode falhar por dois motivos

- O usuário conectado é uma entidade remota no locatário do Azure de clientes (assinatura do CSP – e o usuário conectado é o administrador do parceiro). A solução alternativa neste caso é excluir o cofre de chaves, fazer logoff do portal e, em seguida, fazer logon com uma conta de usuário do locatário Customers (não uma entidade de segurança remota) e repetir a operação. O parceiro CSP normalmente terá uma conta de usuário no locatário Azure Active Directory do cliente que pode usar. Se não for possível criar uma nova conta de usuário para si própria no locatário clientes Azure Active Directory, faça logon no portal como o novo usuário e, em seguida, repita a operação de replicação. A conta usada deve ter as permissões proprietário ou colaborador + acesso de usuário concedidas à conta no grupo de recursos (migrar grupo de recursos de projeto)

- O outro caso em que isso pode ocorrer é quando um usuário (Usuário1) tentou configurar a replicação inicialmente e encontrou uma falha, mas o cofre de chaves já foi criado (e a política de acesso do usuário foi atribuída apropriadamente a esse usuário). Agora, posteriormente, um usuário diferente (Usuário2) tentará configurar a replicação, mas a operação configurar conta de armazenamento gerenciado ou gerar definição de SAS falhará, pois não há nenhuma política de acesso de usuário correspondente ao Usuário2 no cofre de chaves.

**Resolução**: para resolver esse problema, crie uma política de acesso de usuário para Usuário2 no keyvault concedendo permissão de Usuário2 para configurar a conta de armazenamento gerenciado e gerar definições de SAS. O User2 pode fazer isso a partir de Azure PowerShell usando os cmdlets abaixo:

$userPrincipalId = $ (Get-AzureRmADUser-UserPrincipalName "user2_email_address"). Sessão

Set-AzureRmKeyVaultAccessPolicy-Vaultname "keyvaultname"-ObjectId $userPrincipalId-PermissionsToStorage obter, listar, excluir, definir, atualizar, regeneratekey, getss, listas, exclusões, conjuntos de atualizações, recuperar, backup, restaurar, limpar


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**ID do erro:** 181008

**Mensagem de erro:** VM: VMName. Erro: o evento de tempo limite ' DisposeArtefactsTimeout ' foi encontrado no estado & ' [' Gateway. Service. StateMachine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle ' (' WaitingForArtefactsDisposalPreCycle ')] '.

**Possíveis causas:**

O componente que está tentando replicar os dados para o Azure está inoperante ou não respondendo. As possíveis causas incluem:

- O serviço de gateway em execução no dispositivo migrações para Azure está inoperante.
- O serviço de gateway está enfrentando problemas de conectividade com a conta de armazenamento do barramento de serviço/Hub de eventos/dispositivo.

**Identificar a causa exata de DisposeArtefactsTimedOut e a resolução correspondente:**

1. Verifique se o dispositivo de Migrações para Azure está funcionando.
2. Verifique se o serviço de gateway está em execução no dispositivo:
   1.  Faça logon no dispositivo migrações para Azure usando a área de trabalho remota e faça o seguinte.

   2.  Abra o snap-in MMC de serviços da Microsoft (execute > Services. msc) e verifique se o "serviço de gateway Microsoft Azure" está em execução. Se o serviço for interrompido ou não estiver em execução, inicie o serviço. Como alternativa, você pode abrir o prompt de comando ou o PowerShell e fazer o: "net start asrgwy"

3. Verifique se há problemas de conectividade entre o dispositivo de migração do Azure e a conta de armazenamento do dispositivo: 

    Execute o seguinte comando após baixar o azcopy no dispositivo de migrações para Azure:
    
    _azcopy banca https://[conta]. blob. Core. Windows. net/[contêiner]? RÍGIDO_
    
    **Etapas para executar o teste de benchmark de desempenho:**
    
      1. [Baixar](../storage/common/storage-use-azcopy-v10.md) o azcopy
        
      2. Procure a conta de armazenamento do dispositivo no grupo de recursos. A conta de armazenamento tem um nome que se assemelha a migrategwsa \* \* \* \* \* \* \* \* \* \* . Esse é o valor do parâmetro [Account] no comando acima.
        
      3. Pesquise sua conta de armazenamento no portal do Azure. Verifique se a assinatura que você usa para Pesquisar é a mesma assinatura (assinatura de destino) na qual a conta de armazenamento é criada. Vá para contêineres na seção serviço BLOB. Clique em + contêiner e crie um contêiner. Deixe nível de acesso público para o valor selecionado padrão.
        
      4. Vá para assinatura de acesso compartilhado em configurações. Selecione contêiner em "tipo de recurso permitido". Clique em gerar SAS e cadeia de conexão. Copie o valor SAS.
        
      5. Execute o comando acima no prompt de comando, substituindo a conta, o contêiner, a SAS pelos valores obtidos nas etapas 2, 3 e 4, respectivamente.
        
      Como alternativa, [Baixe](https://go.microsoft.com/fwlink/?linkid=2138967) o armazenamento do Azure Explore no dispositivo e tente carregar 10 blobs de ~ 64 MB nas contas de armazenamento. Se não houver nenhum problema, o carregamento deverá ser bem-sucedido.
        
    **Resolução:** Se esse teste falhar,&#39;s um problema de rede. Envolva sua equipe de rede local para verificar problemas de conectividade. Normalmente, pode haver algumas configurações de firewall que estão causando as falhas.
    
4.  Verifique se há problemas de conectividade entre o dispositivo de migração do Azure e o barramento de serviço:

    Esse teste verifica se o dispositivo de migração do Azure pode se comunicar com o back-end do serviço de nuvem de migrações para Azure. O dispositivo se comunica com o back-end do serviço por meio de filas de mensagens do barramento de serviço e do hub de eventos. Para validar a conectividade do dispositivo para o barramento de serviço, [Baixe](https://go.microsoft.com/fwlink/?linkid=2139104) o Gerenciador do barramento de serviço, tente conectar-se ao barramento de serviço do dispositivo e execute enviar mensagem/receber mensagem. Se não houver nenhum problema, isso deverá ser bem-sucedido.

    **Etapas para executar o teste:**

    1. Copie a cadeia de conexão do barramento de serviço que foi criado no projeto de migração
    2. Abrir o Gerenciador do barramento de serviço
    3. Ir para o arquivo e conectar
    4. Cole a cadeia de conexão e clique em conectar
    5. Isso abrirá o espaço de nome do barramento de serviço
    6. Selecione Snapshot Manager no tópico. Clique com o botão direito do mouse em Snapshot Manager, selecione "receber mensagens" > selecione "inspecionar" e clique em OK
    7. Se a conexão for bem-sucedida, você verá "[x] mensagens recebidas" na saída do console. Se a conexão não for bem-sucedida, você verá uma mensagem informando que a conexão falhou
    
    **Resolução:** Se esse teste falhar, haverá um problema de rede. Envolva sua equipe de rede local para verificar problemas de conectividade. Normalmente, pode haver algumas configurações de firewall que estão causando as falhas.

5. Problemas de conectividade entre o dispositivo de migração do Azure e o Azure Key Vault:

    Esse teste verifica os problemas de conectividade entre o dispositivo migrações para Azure e o Azure Key Vault. O Key Vault é usado para gerenciar o acesso à conta de armazenamento usado para replicação.
    
    **Etapas para verificar a conectividade:**
    
    1. Busque o URI do Key Vault na lista de recursos no grupo de recursos correspondente ao projeto de migrações para Azure.
    
    1. Abra o PowerShell no dispositivo migrações para Azure e execute o seguinte comando:
    
    _Test-NetConnection Key Vault URI-P 443_
    
    Esse comando tentará uma conexão TCP e retornará uma saída.
    
     - Na saída, verifique o campo "_TcpTestSucceeded_". Se o valor for "_true_", não haverá nenhum problema de conectividade entre o dispositivo de migrações para Azure e o Azure Key Vault. Se o valor for "false", haverá um problema de conectividade.
    
    **Resolução:** Se esse teste falhar, haverá um problema de conectividade entre o dispositivo migrações para Azure e o Azure Key Vault. Envolva sua equipe de rede local para verificar problemas de conectividade. Normalmente, pode haver algumas configurações de firewall que estão causando as falhas.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**ID do erro:** 1011

**Mensagem de erro:** O upload de dados para o disco DiskPath, DiskId da máquina virtual VMName; O VMId não foi concluído dentro do tempo esperado.

Esse erro normalmente indica que o dispositivo de migrações para Azure que executa a replicação não consegue se conectar aos serviços de nuvem do Azure, ou que a replicação está progredindo lentamente, fazendo com que o ciclo de replicação expire o tempo limite.

As possíveis causas incluem:

- O dispositivo migrações para Azure está inoperante.
- O serviço de gateway de replicação no dispositivo não está em execução.
- O serviço do gateway de replicação está enfrentando problemas de conectividade com um dos seguintes componentes de serviço do Azure que são usados para replicação: barramento de serviço/Hub de eventos/conta de armazenamento de cache do Azure/Azure Key Vault.
- O serviço de gateway está sendo limitado no nível do vCenter ao tentar ler o disco.

**Identificar a causa raiz e resolver o problema:**

1. Verifique se o dispositivo de Migrações para Azure está funcionando.
2. Verifique se o serviço de gateway está em execução no dispositivo:
   1.  Faça logon no dispositivo migrações para Azure usando a área de trabalho remota e faça o seguinte.

   2.  Abra o snap-in MMC de serviços da Microsoft (execute > Services. msc) e verifique se o "serviço de gateway Microsoft Azure" está em execução. Se o serviço for interrompido ou não estiver em execução, inicie o serviço. Como alternativa, você pode abrir o prompt de comando ou o PowerShell e fazer o: "net start asrgwy".


3. **Verifique se há problemas de conectividade entre o dispositivo de migração do Azure e a conta de armazenamento em cache:** 

    Execute o seguinte comando após baixar o azcopy no dispositivo de migrações para Azure:
    
    _azcopy banca https://[conta]. blob. Core. Windows. net/[contêiner]? RÍGIDO_
    
    **Etapas para executar o teste de benchmark de desempenho:**
    
      1. [Baixar](../storage/common/storage-use-azcopy-v10.md) o azcopy
        
      2. Procure a conta de armazenamento do dispositivo no grupo de recursos. A conta de armazenamento tem um nome que se assemelha a migratelsa \* \* \* \* \* \* \* \* \* \* . Esse é o valor do parâmetro [Account] no comando acima.
        
      3. Pesquise sua conta de armazenamento no portal do Azure. Verifique se a assinatura que você usa para Pesquisar é a mesma assinatura (assinatura de destino) na qual a conta de armazenamento é criada. Vá para contêineres na seção serviço BLOB. Clique em + contêiner e crie um contêiner. Deixe nível de acesso público para o valor selecionado padrão.
        
      4. Vá para assinatura de acesso compartilhado em configurações. Selecione contêiner em "tipo de recurso permitido". Clique em gerar SAS e cadeia de conexão. Copie o valor SAS.
        
      5. Execute o comando acima no prompt de comando, substituindo a conta, o contêiner, a SAS pelos valores obtidos nas etapas 2, 3 e 4, respectivamente.
        
      Como alternativa, [Baixe](https://go.microsoft.com/fwlink/?linkid=2138967) o armazenamento do Azure Explore no dispositivo e tente carregar 10 blobs de ~ 64 MB nas contas de armazenamento. Se não houver nenhum problema, o carregamento deverá ser bem-sucedido.
        
    **Resolução:** Se esse teste falhar,&#39;s um problema de rede. Envolva sua equipe de rede local para verificar problemas de conectividade. Normalmente, pode haver algumas configurações de firewall que estão causando as falhas.
                
4.  **Problemas de conectividade entre o dispositivo de migrações para Azure e o barramento de serviço do Azure:**

    Esse teste verificará se o dispositivo de migrações para Azure pode se comunicar com o back-end do serviço de nuvem de migrações para Azure. O dispositivo se comunica com o back-end do serviço por meio de filas de mensagens do barramento de serviço e do hub de eventos. Para validar a conectividade do dispositivo para o barramento de serviço, [Baixe](https://go.microsoft.com/fwlink/?linkid=2139104) o Gerenciador do barramento de serviço, tente conectar-se ao barramento de serviço do dispositivo e execute enviar mensagem/receber mensagem. Se não houver nenhum problema, isso deverá ser bem-sucedido.

    **Etapas para executar o teste:**
    
    1. Copie a cadeia de conexão do barramento de serviço que foi criado no grupo de recursos correspondente ao projeto de migrações para Azure
    
    1. Abrir o Gerenciador do barramento de serviço
    
    1. Vá para o arquivo > conectar
    
    1. Cole a cadeia de conexão que você copiou na etapa 1 e clique em conectar
    
    1. Isso abrirá o namespace do barramento de serviço.
    
    1. Selecione Snapshot Manager no tópico em namespace. Clique com o botão direito do mouse em Snapshot Manager, selecione "receber mensagens" > selecione "inspecionar" e clique em OK.
    
    Se a conexão for bem-sucedida, você verá "[x] mensagens recebidas" na saída do console. Se a conexão não for bem-sucedida, você verá uma mensagem informando que a conexão falhou.
    
    **Resolução:** Se esse teste falhar, haverá um problema de conectividade entre o dispositivo de migração do Azure e o barramento de serviço. Envolva sua equipe de rede local para verificar esses problemas de conectividade. Normalmente, pode haver algumas configurações de firewall que estão causando as falhas.
    
 5. **Problemas de conectividade entre o dispositivo de migração do Azure e o Azure Key Vault:**

    Esse teste verifica os problemas de conectividade entre o dispositivo migrações para Azure e o Azure Key Vault. O Key Vault é usado para gerenciar o acesso à conta de armazenamento usado para replicação.
    
    **Etapas para verificar a conectividade:**
    
    1. Busque o URI do Key Vault na lista de recursos no grupo de recursos correspondente ao projeto de migrações para Azure.
    
    1. Abra o PowerShell no dispositivo migrações para Azure e execute o seguinte comando:
    
    _Test-NetConnection Key Vault URI-P 443_
    
    Esse comando tentará uma conexão TCP e retornará uma saída.
    
    1. Na saída, verifique o campo "_TcpTestSucceeded_". Se o valor for "_true_", não haverá nenhum problema de conectividade entre o dispositivo de migrações para Azure e o Azure Key Vault. Se o valor for "false", haverá um problema de conectividade.
    
    **Resolução:** Se esse teste falhar, haverá um problema de conectividade entre o dispositivo migrações para Azure e o Azure Key Vault. Envolva sua equipe de rede local para verificar problemas de conectividade. Normalmente, pode haver algumas configurações de firewall que estão causando as falhas.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Erro encontrado ao tentar buscar blocos alterados

Mensagem de erro: ' encontrou um erro ao tentar buscar os blocos de alteração '

O método de replicação sem agente usa a CBT (tecnologia de controle de bloqueio) alterada pela VMware para replicar dados no Azure. O CBT permite que a ferramenta de migração de servidor acompanhe e replique somente os blocos que foram alterados desde o último ciclo de replicação. Esse erro ocorre se o controle de bloco alterado para uma máquina virtual em replicação for redefinido ou se o arquivo de controle de bloco estiver corrompido.

Esse erro pode ser resolvido das duas maneiras a seguir:

- Se você tiver optado por "reparar automaticamente a replicação" selecionando "Sim" quando você disparou a replicação da VM, a ferramenta tentará repará-la para você. Clique com o botão direito do mouse na VM e selecione "reparar replicação".
- Se você não optar por "reparar automaticamente a replicação" ou a etapa acima não funcionou para você, pare a replicação da máquina virtual, [redefina o controle de bloco alterado](https://go.microsoft.com/fwlink/?linkid=2139203) na máquina virtual e reconfigure a replicação.

Um desses problemas conhecido que pode causar uma reinicialização CBT da máquina virtual no VMware vSphere 5,5 é descrito em [VMware KB 2048201: o controle de bloco alterado](https://go.microsoft.com/fwlink/?linkid=2138888) é redefinido após uma operação de armazenamento VMotion no vSphere 5. x. Se estiver usando o VMware vSphere 5.5, certifique-se de aplicar as atualizações descritas nesta KB.

Como alternativa, você pode redefinir o rastreamento de bloco alterado pelo VMware em uma máquina virtual usando o VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Ocorreu um erro interno

Às vezes, você pode encontrar um erro que ocorre devido a problemas no ambiente/API do VMware. Identificamos o seguinte conjunto de erros como erros relacionados ao ambiente VMware. Esses erros têm um formato fixo.

_Mensagem de erro: ocorreu um erro interno. [Mensagem de erro]_

Por exemplo: mensagem de erro: ocorreu um erro interno. [Foi detectada uma configuração de instantâneo inválida].

A seção a seguir lista alguns dos erros do VMware comumente vistos e como você pode atenuá-los.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Mensagem de erro: ocorreu um erro interno. [Conexão do servidor recusada]

O problema é um problema conhecido do VMware e ocorre no VDDK 6,7. Você precisa interromper o serviço de gateway em execução no dispositivo de migrações para Azure, [baixar uma atualização do VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)e reiniciar o serviço de gateway.

Etapas para parar o serviço de gateway:

1. Pressione Windows + R, abra Services. msc. Clique em "Microsoft Azure serviço de gateway" e interrompa-o.
2. Como alternativa, você pode abrir o prompt de comando ou o PowerShell e: net stop asrgwy. Certifique-se de aguardar até obter a mensagem informando que o serviço não está mais em execução.

Etapas para iniciar o serviço de gateway:

1. Pressione Windows + R, abra Services. msc. Clique com o botão direito do mouse em "serviço de gateway Microsoft Azure" e inicie-o.
2. Como alternativa, você pode abrir o prompt de comando ou o PowerShell e: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Mensagem de erro: ocorreu um erro interno. [' Uma configuração de instantâneo inválida foi detectada. ']

Se você tiver uma máquina virtual com vários discos, poderá encontrar esse erro se remover um disco da máquina virtual. Para corrigir esse problema, consulte as etapas neste [artigo do VMware](https://go.microsoft.com/fwlink/?linkid=2138890).

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Mensagem de erro: ocorreu um erro interno. [Gerar instantâneo suspenso]

Esse problema ocorre quando a geração de instantâneos para de responder. Quando esse problema ocorrer, você poderá ver a tarefa criar instantâneo parar às 95% ou 99%. Consulte este [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) para superar esse problema.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Mensagem de erro: ocorreu um erro interno. [Falha ao consolidar os discos na VM _[motivos]_]

Quando consolidamos discos no final do ciclo de replicação, a operação falha. Siga as instruções em [KB do VMware](https://go.microsoft.com/fwlink/?linkid=2138970) selecionando o _motivo_ apropriado para resolver o problema.

Os seguintes erros ocorrem quando as operações relacionadas ao instantâneo do VMware – criar, excluir ou consolidar discos falham. Siga as orientações na próxima seção para corrigir os erros:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Mensagem de erro: ocorreu um erro interno. [Outra tarefa já está em andamento]

Esse problema ocorre quando há tarefas de máquina virtual em conflito em execução em segundo plano ou quando uma tarefa dentro do vCenter Server atinge o tempo limite. Siga a resolução fornecida nos seguintes [KB do VMware](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Mensagem de erro: ocorreu um erro interno. [Operação não permitida no estado atual]

Esse problema ocorre quando vCenter Server agentes de gerenciamento param de funcionar. Para resolver esse problema, consulte a resolução no seguinte [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971).

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Mensagem de erro: ocorreu um erro interno. [Tamanho do disco de instantâneo inválido]

Esse é um problema conhecido do VMware no qual o tamanho do disco indicado pelo instantâneo se torna zero. Siga a resolução fornecida no [VMware KB](https://kb.vmware.com/s/).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Mensagem de erro: ocorreu um erro interno. [Falha na alocação de memória. Memória insuficiente.]

Isso acontece quando o buffer de host NFC está sem memória. Para resolver esse problema, você precisa mover a VM (Compute vMotion) para um host diferente, que tem recursos gratuitos.

## <a name="replication-cycle-failed"></a>Falha no ciclo de replicação

**ID do erro:** 181008

**Mensagem de erro:** VM: ' VMName '. Erro: nenhum disksnapshots foi encontrado para a replicação de instantâneo com a ID de instantâneo: ' Snapshotid '.

**Possíveis causas:**

As possíveis razões são:
1. O caminho de um ou mais discos incluídos foi alterado devido ao Storage VMotion.
2. Um ou mais discos incluídos não estão mais anexados à VM.
      
**Recomendação:**

As recomendações a seguir são fornecidas
1. Restaure os discos incluídos no caminho original usando o Storage vMotion e, em seguida, desabilite o Storage VMotion.
2. Desabilite o Storage VMotion, se habilitado, pare a replicação na máquina virtual e replique a máquina virtual novamente. Se o problema persistir, contate o suporte.

## <a name="next-steps"></a>Próximas etapas

Continue a replicação de VM e execute a [migração de teste](./tutorial-migrate-vmware.md#run-a-test-migration).
