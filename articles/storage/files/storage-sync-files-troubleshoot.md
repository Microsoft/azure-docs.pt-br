---
title: Solucionar problemas de sincronização de arquivos do Azure | Microsoft Docs
description: Solucionar problemas comuns com a Sincronização de arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 53297a16889190383e0455c484339adc049c1cb1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759422"
---
# <a name="troubleshoot-azure-file-sync"></a>Solucionar problemas da Sincronização de Arquivos do Azure
Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo foi projetado para ajudá-lo a solucionar problemas e resolver problemas encontrados com a implantação da Sincronização de arquivos do Azure. Nós também descrevemos como coletar logs importantes do sistema para ajudar em uma investigação mais profunda dos problemas. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. O [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. O [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. O Suporte da Microsoft. Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Estou tendo um problema com o Azure File Sync no meu servidor (sincronização, nível de nuvem, etc.). Deve remover e recriar o ponto de extremidade do meu servidor?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalação do agente e registro do servidor
<a id="agent-installation-failures"></a>**Como solucionar problemas de falhas de instalação do agente**  
Se a instalação do agente de Sincronização de arquivos do Azure estiver falhando, execute o seguinte comando em um prompt de comandos com privilégios elevados para habilitar o registro em log durante a instalação do agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Examine o installer.log para determinar a causa da falha de instalação.

<a id="agent-installation-on-DC"></a>**A instalação do agente falha no Controlador de Domínio do Active Directory**  
Se você tentar instalar o agente de sincronização em um controlador de domínio do Active Directory no qual o proprietário da função PDC esteja em uma versão do Windows Server 2008 R2 ou inferior, poderá atingir o problema em que o agente de sincronização não será instalado.

Para resolver, transfira a função de PDC para outro controlador de domínio em execução no Windows Server 2012 R2 ou mais recente e, em seguida, instale a sincronização.

<a id="server-registration-missing"></a>**O servidor não está listado em servidores registrados no portal do Azure**  
Se algum servidor não estiver listado em **Servidores registrados** de um Serviço de Sincronização de Armazenamento:
1. Entre no servidor que você deseja registrar.
2. Abra o Explorador de arquivos e, em seguida, vá para o diretório de instalação do agente de sincronização de armazenamento (o local padrão é C:\Program Files\Azure\StorageSyncAgent). 
3. Execute ServerRegistration.exe e siga o assistente para registrar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**O Registro do Servidor exibe a seguinte mensagem depois de instalar o agente de Sincronização de Arquivos do Azure: "Este servidor já está registrado"** 

![Uma captura de tela da caixa de diálogo de Registro do Servidor com a mensagem de erro “o servidor já está registrado”](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem é exibida se o servidor foi registrado anteriormente com um Serviço de Sincronização de Armazenamento. Para cancelar o registro do servidor com o Serviço de Sincronização de Armazenamento atual e registrar com o novo Serviço de Sincronização de Armazenamento, siga as etapas para [Cancelar o registro de um servidor com uma Sincronização de arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado em **Servidores registrados** no Serviço de Sincronização de Armazenamento, no servidor cujo registro você deseja cancelar, execute os seguintes comandos do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor for parte de um cluster, você pode usar opcional *StorageSyncServer Reset - CleanClusterRegistration* parâmetro também para remover o registro de cluster.

<a id="web-site-not-trusted"></a>**Ao registrar um servidor, obtenho várias respostas de "sites não confiáveis". Por quê?**  
Esse erro ocorre porque a política **Segurança reforçada do Internet Explorer** está habilitada durante o registro do servidor. Para obter mais informações sobre como desabilitar corretamente a política **Segurança aprimorada do Internet Explorer**, consulte [Preparar o Windows Server para usar com o Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [Como implantar o Azure File Sync](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gerenciamento de grupo de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "O Compartilhamento de Arquivos do Azure especificado já está sendo usado por um CloudEndpoint diferente"**  
Esse problema ocorre quando o compartilhamento de arquivos do Azure já está sendo usado por outro ponto de extremidade de nuvem. 

Se essa mensagem aparecer e o compartilhamento de arquivos do Azure não estiver sendo usado por um ponto de extremidade de nuvem no momento, conclua as seguintes etapas para limpar os metadados da Sincronização de arquivos do Azure no compartilhamento de arquivos do Azure:

> [!Warning]  
> Excluir os metadados em um compartilhamento de arquivos do Azure que está sendo usado no momento por um ponto de extremidade de nuvem faz com que as operações da Sincronização de arquivos do Azure falhem. 

1. Navegue até o seu compartilhamento de arquivos do Azure no Portal do Azure.  
2. Clique com botão direito no compartilhamento de arquivos do Azure e selecione **Editar metadados**.
3. Clique com botão direito em **SyncService** e selecione **Excluir**.

<a id="cloud-endpoint-authfailed"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "AuthorizationFailed"**  
Esse problema ocorre quando a conta de usuário não tem direitos suficientes para criar um ponto de extremidade de nuvem. 

Para criar um ponto de extremidade de nuvem, sua conta de usuário deve ter as seguintes permissões de Autorização da Microsoft:  
* Ler: Obter a definição da função
* Gravação: Criar ou atualizar definição de função personalizada
* Ler: Obter a atribuição da função
* Gravação: Criar atribuição de função

As seguintes funções internas têm as permissões de Autorização da Microsoft adequadas:  
* Proprietário
* Administrador de Acesso do Usuário

Para determinar se sua função de conta de usuário tem as permissões necessárias:  
1. No portal do Azure, clique em **Grupos de recursos**.
2. Selecione o grupo de recursos em que a conta de armazenamento está localizada e clique em **Controle de acesso (IAM)**.
3. Selecione a guia **Atribuições de função**.
4. Selecione a **Função** (por exemplo, o proprietário ou colaborador) para sua conta de usuário.
5. Na lista **Provedor de Recursos**, selecione **Autorização da Microsoft**. 
    * **Atribuição de função** deve ter **Permissões de Leitura** e de **Gravação**.
    * **Definição de função** deve ter **Permissões de Leitura** e de **Gravação**.

<a id="server-endpoint-createjobfailed"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (código de erro: -2134375898)**  
Esse problema ocorre se o caminho do ponto de extremidade de servidor estiver no volume do sistema e a camada de nuvem estiver habilitada. A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.

<a id="server-endpoint-deletejobexpired"></a>**Falha na exclusão de ponto de extremidade de nuvem, com este erro: "MgmtServerJobExpired"**                
Esse problema ocorre se o servidor estiver offline ou não tiver conectividade de rede. Se o servidor não estiver mais disponível, cancele o registro do servidor no portal que excluirá os pontos de extremidade do servidor. Para excluir os pontos de extremidade do servidor, siga as etapas descritas em [Cancelar o registro de um servidor com a Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Não é possível abrir a página de propriedades do ponto de extremidade do servidor ou atualizar a política de camada de nuvem**  
Esse problema pode ocorrer se uma operação de gerenciamento no ponto de extremidade do servidor falhar. Se a página de propriedades do ponto de extremidade de servidor não abrir no Portal do Azure, atualizar o ponto de extremidade de servidor usando comandos do PowerShell a partir do servidor poderá solucionar esse problema. 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**O ponto de extremidade do servidor tem um status de integridade “Sem Atividade” ou “Pendente” e o estado do servidor na folha de servidores registrados é “Aparece offline”**  

Esse problema poderá ocorrer se o processo do Monitor de Sincronização de Armazenamento não estiver em execução ou o servidor não conseguir se comunicar com o serviço de Sincronização de Arquivos do Azure devido a um proxy ou firewall.

Para resolver esse problema, execute as seguintes etapas:

1. Abra o Gerenciador de Tarefas no servidor e verifique se o processo do Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) está em execução. Se o processo não estiver funcionando, primeiro tente reiniciar o servidor. Se a reinicialização do servidor não resolver o problema, atualize o agente de Sincronização de Arquivos do Azure para a [versão](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes).
2. Verifique se as configurações de Firewall e Proxy estão definidas corretamente:
    - Se o servidor estiver atrás de um firewall, verifique se a porta 443 de saída é permitida. Se o firewall restringe o tráfego a domínios específicos, confirme se os domínios listados na [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) do Firewall estão acessíveis.
    - Se o servidor estiver atrás de um proxy, defina as configurações de proxy específicas do aplicativo ou para todo o computador seguindo as etapas na [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy) do Proxy.

<a id="endpoint-noactivity-sync"></a>**O ponto de extremidade do servidor tem um status de integridade “Sem Atividade” e o estado do servidor na folha de servidores registrados é “Online”**  

Um status de integridade do ponto de extremidade de servidor de “Sem Atividade” significa que o ponto de extremidade do servidor não registrou a atividade de sincronização em log nas últimas duas horas.

Um ponto de extremidade do servidor não pode registrar a atividade de sincronização em log pelos seguintes motivos:

- Um agente da versão 4.3.0.0 ou anterior é instalado e o servidor tem uma sessão de sincronização ativa do VSS (SnapshotSync). Quando uma sessão de sincronização do VSS está ativa para um ponto de extremidade do servidor, outros pontos de extremidade no do mesmo não podem iniciar uma sessão de sincronização inicial até que a sessão de sincronização VSS seja concluída. Para resolver esse problema, instale o agente da versão 5.0.2.0 ou posterior que dá suporte à sincronização de vários pontos de extremidade de servidor em um volume quando a sessão de sincronização de um VSS está ativa.

    Para verificar a atividade de sincronização atual em um servidor, confira [Como fazer para monitorar o andamento de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- O servidor atingiu o número máximo de sessões de sincronização simultâneas. 
    - Agente da versão 4.x e posterior: O limite varia com base nos recursos de sistema disponíveis.
    - Versão do agente 3.x: duas sessões de sincronização ativas por processador ou um máximo de oito sessões de sincronização ativas por servidor.

> [!Note]  
> Se o estado do servidor na folha de servidores registrados for “Aparece Offline”, execute as etapas documentadas na seção [O ponto de extremidade do servidor tem um status de integridade “Sem Atividade” ou “Pendente” e o estado do servidor na folha de servidores registrados é “Aparece offline”](#server-endpoint-noactivity).

## <a name="sync"></a>Sincronizar
<a id="afs-change-detection"></a>**Se eu criar um arquivo diretamente em meu compartilhamento de arquivos do Azure usando SMB ou por meio do portal, quanto tempo levará para que o arquivo seja sincronizado com os servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a> **A integridade do ponto de extremidade do servidor está em um estado pendente por várias horas**  
Esse problema é esperado quando você cria um ponto de extremidade de nuvem e usa um compartilhamento de arquivos do Azure que contém dados. A tarefa de enumeração de alterações que verifica as alterações no compartilhamento de arquivos do Azure deve ser concluída antes que os arquivos possam ser sincronizados entre os nós de extremidade da nuvem e do servidor. O tempo para concluir o trabalho depende do tamanho do espaço para nome no compartilhamento de arquivos do Azure. A integridade do ponto de extremidade do servidor deve ser atualizada quando o trabalho de enumeração de alterações for concluído.

### <a id="broken-sync"></a>Como monitoro a integridade da sincronização?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Em cada grupo de sincronização, você pode detalhar seus pontos de extremidade de servidor individuais para ver o status das últimas sessões de sincronização concluídas. Uma coluna de integridade verde e um valor de não sincronização de arquivos de 0 indicam que a sincronização está funcionando conforme o esperado. Se esse não for o caso, veja abaixo uma lista de erros comuns de sincronização e como manipular arquivos que não estão sendo sincronizados. 

![Uma captura de tela do portal do Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Servidor](#tab/server)
Vá para os logs de telemetria do servidor, que podem ser encontrados no Visualizador de Eventos em`Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. O evento 9102 corresponde a uma sessão de sincronização concluída; Para obter o status mais recente de sincronização, procure o evento mais recente com o ID 9102. SyncDirection informa se esta sessão foi um upload ou download. Se o HResult for 0, a sessão de sincronização foi bem-sucedida. Um HResult diferente de zero significa que houve um erro durante a sincronização; veja abaixo uma lista de erros comuns. Se o PerItemErrorCount for maior que 0, isso significa que alguns arquivos ou pastas não foram sincronizados corretamente. É possível ter um HResult de 0, mas um PerItemErrorCount que seja maior que 0.

Abaixo está um exemplo de um upload bem-sucedido. Por uma questão de brevidade, apenas alguns dos valores contidos em cada evento 9102 estão listados abaixo. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Por outro lado, um upload malsucedido pode ter esta aparência:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Às vezes, as sessões de sincronização falham no geral ou possuem um PerItemErrorCount diferente de zero, mas ainda fazem avançar o progresso, com alguns arquivos sendo sincronizados com êxito. Isso pode ser visto nos campos Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), que informam quanto da sessão está sendo bem-sucedida. Se você vir várias sessões de sincronização em uma fila que estão falhando, mas com uma contagem Applied* crescente, forneça o tempo de sincronização para tentar novamente antes de abrir um ticket de suporte.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Como monitoro o progresso de uma sessão de sincronização atual?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Dentro do seu grupo de sincronização, vá para o ponto de extremidade do servidor em questão e examine a seção Atividade de Sincronização para ver a contagem de arquivos carregados ou baixados na sessão de sincronização atual. Observe que esse status será atrasado em cerca de 5 minutos e, se sua sessão de sincronização for pequena o suficiente para ser concluída dentro desse período, ela pode não ser informada no portal. 

# <a name="servertabserver"></a>[Servidor](#tab/server)
Observe o evento 9302 mais recente no log de telemetria no servidor (no Visualizador de Eventos, vá para Aplicativos e Logs de Serviços\Microsoft\FileSync\Agent\Telemetry). Este evento indica o estado da atual sessão de sincronização. TotalItemCount indica quantos arquivos devem ser sincronizados, AppliedItemCount o número de arquivos que foram sincronizados até o momento e PerItemErrorCount o número de arquivos que não estão sendo sincronizados (veja abaixo como lidar com isso).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Como sei se meus servidores estão em sincronia um com o outro?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Para cada servidor em um determinado grupo de sincronização, verifique se:
- Os timestamps da última tentativa de sincronização para upload e download são recentes.
- O status é verde para carregamento e download.
- O campo Atividade de Sincronização mostra poucos ou nenhum arquivo restante para sincronizar.
- O campo Arquivos que não são sincronizados é 0 para upload e download.

# <a name="servertabserver"></a>[Servidor](#tab/server)
Observe as sessões de sincronização concluídas, marcadas por 9102 eventos no registro de eventos de telemetria de cada servidor (no Visualizador de Eventos, vá para `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Em qualquer servidor, você quer garantir que as últimas sessões de upload e download sejam concluídas com sucesso. Para fazer isso, verifique se HResult e PerItemErrorCount são 0 para upload e download (o campo SyncDirection indica se uma determinada sessão é uma sessão de upload ou download). Observe que, se você não vir uma sessão de sincronização concluída recentemente, é provável que uma sessão de sincronização esteja em andamento, o que é esperado se você acabou de adicionar ou modificar uma grande quantidade de dados.
2. Quando um servidor estiver totalmente atualizado com a nuvem e não tiver alterações para sincronizar nas duas direções, você verá sessões de sincronização vazias. Eles são indicados por eventos de upload e download nos quais todos os campos Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount e SyncSizeBytes) são zero, o que significa que não há nada a ser sincronizado. Observe que essas sessões de sincronização vazias podem não ocorrer em servidores de alta rotatividade, pois há sempre algo novo para sincronizar. Se não houver atividade de sincronização, elas devem ocorrer a cada 30 minutos. 
3. Se todos os servidores estiverem atualizados com a nuvem, o que significa que suas sessões de upload e download recentes são sessões de sincronização vazias, você pode dizer com razoável certeza que o sistema como um todo está em sincronia. 
    
Observe que, se você fez alterações diretamente no compartilhamento de arquivos do Azure, o Azure File Sync não detectará essa alteração até que a enumeração de alterações seja executada, o que acontece uma vez a cada 24 horas. É possível que um servidor diga que está atualizado com a nuvem quando, na verdade, está faltando alterações recentes feitas diretamente no compartilhamento de arquivos do Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Como faço para ver se há arquivos ou pastas específicos que não estão sendo sincronizados?
Se o seu PerItemErrorCount no servidor ou a contagem de Não Sincronização de Arquivos no portal for maior que 0 para qualquer sessão de sincronização, isso significa que alguns itens não estão sendo sincronizados. Arquivos e pastas podem ter características que os impedem de sincronizar. Essas características podem ser persistentes e exigem ação explícita para retomar a sincronização, por exemplo, removendo caracteres não suportados do nome do arquivo ou da pasta. Eles também podem ser temporários, o que significa que o arquivo ou a pasta retornará automaticamente à sincronização. por exemplo, arquivos com identificadores abertos retomarão automaticamente a sincronização quando o arquivo for fechado. Quando o mecanismo do Azure File Sync detecta esse problema, é produzido um log de erros que pode ser analisado para listar os itens que atualmente não estão sendo sincronizados corretamente.

Para ver esses erros, execute o script do PowerShell **FileSyncErrorsReport.ps1** (localizado no diretório de instalação do Agente do Azure File Sync) para identificar os arquivos que falharam na sincronização devido a identificadores abertos, caracteres não suportados ou outros problemas . O campo ItemPath informa a localização do arquivo em relação ao diretório de sincronização raiz. Veja abaixo a lista de erros comuns de sincronização para as etapas de correção.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Solução de problemas por erros de sincronização de arquivos/diretórios
**ItemResulta erros de sincronização de log por item**  

| HRESULT | HRESULT (decimal) | Cadeia de caracteres de erro | Problema | Correção |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Uma alteração de arquivo ou diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não está sincronizada. Este item será sincronizado após o mudanças dependentes serem sincronizadas. | Nenhuma ação é necessária. |
| 0x7b | 123 | ERROR_INVALID_NAME | O nome do arquivo ou diretório é inválido. | Renomeie o arquivo ou diretório em questão. Veja [Tratamento de caracteres sem suporte](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | O nome do arquivo ou diretório é inválido. | Renomeie o arquivo ou diretório em questão. Veja [Tratamento de caracteres sem suporte](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Um arquivo não pode ser sincronizado porque está em uso. O arquivo será sincronizado quando não estiver mais em uso. | Nenhuma ação é necessária. O Azure File Sync cria um instantâneo temporário do VSS uma vez por dia no servidor para sincronizar arquivos que tenham identificadores abertos. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Um arquivo foi alterado, mas a alteração ainda não foi detectada pela sincronização. A sincronização será recuperada depois que essa alteração for detectada. | Nenhuma ação é necessária. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | O arquivo não pode ser sincronizado porque o limite de compartilhamento de arquivos do Azure foi atingido. | Para resolver esse problema, veja a seção [Você atingiu o limite de armazenamento de compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) no guia de solução de problemas. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | Esse erro pode ocorrer pelos seguintes motivos: arquivo é criptografado por uma solução sem suporte (como NTFS EFS), arquivo tem uma exclusão pendente ou o arquivo está localizado em uma pasta de somente leitura de replicação do DFS-R | Se o arquivo é criptografado por uma solução sem suporte, descriptografar o arquivo e usar uma solução de criptografia com suporte. Para obter uma lista de soluções com suporte, veja a seção [Soluções de criptografia](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) no guia de planejamento. Se o arquivo estiver em um estado de exclusão pendente, ele será excluído assim que todos os identificadores de arquivos abertos forem fechados. Se o arquivo está localizado em uma pasta de somente leitura de replicação do DFS-R, sincronização de arquivos do Azure não oferece suporte pontos de extremidade do servidor em pastas de somente leitura de replicação do DFS-R. Ver [guia de planejamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) para obter mais informações.
| 0x20 | 32 | ERROR_SHARING_VIOLATION | Um arquivo não pode ser sincronizado porque está em uso. O arquivo será sincronizado quando não estiver mais em uso. | Nenhuma ação é necessária. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Um arquivo foi alterado durante a sincronização, portanto, ele precisa ser sincronizado novamente. | Nenhuma ação é necessária. |

#### <a name="handling-unsupported-characters"></a>Manipulando Caracteres Não Suportados
Se o script **FileSyncErrorsReport.ps1** do PowerShell mostrar falhas devido a caracteres sem suporte (códigos de erro 0x7b e 0x8007007b), será preciso remover ou renomear os caracteres com conflitantes dos respectivos nomes dos arquivos. PowerShell provavelmente será impresso esses caracteres como pontos de interrogação ou retângulos vazios, pois a maior parte desses caracteres não têm nenhuma codificação de visual padrão. Uma [Ferramenta de Avaliação](storage-sync-files-planning.md#evaluation-tool) pode ser usada para identificar os caracteres sem suporte.

A tabela abaixo contém todos os caracteres unicode que o Azure File Sync ainda não suporta.

| Conjunto de caracteres | Contagem de caracteres |
|---------------|-----------------|
| <ul><li>0x0000009D (osc comando do sistema operacional)</li><li>0x00000090 (cadeia de controle de dispositivo de controladores de domínio)</li><li>0x0000008F (ss3 único shift três)</li><li>0x00000081 (predefinição de octeto alta)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverso alimentação de linha)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (apresentação árabe formulários-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (especiais) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (não caracteres)</li><li>0x0002FFFE - 0x0002FFFF = 2 (não caracteres)</li><li>0x0003FFFE - 0x0003FFFF = 2 (noncharacter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (noncharacter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (noncharacter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (noncharacter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (noncharacter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (noncharacter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (noncharacter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (noncharacter)</li><li>0x000BFFFE - 0x000BFFFF = 2 (noncharacter)</li><li>0x000CFFFE - 0x000CFFFF = 2 (noncharacter)</li><li>0x000DFFFE - 0x000DFFFF = 2 (noncharacter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (undefined)</li><li>0x000FFFFE - 0x000FFFFF = 2 (área de suplementares de uso particular)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Erros comuns de sincronização
<a id="-2147023673"></a>**A sessão de sincronização foi cancelada.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Cadeia de caracteres de erro** | ERROR_CANCELLED |
| **Correção necessária** | Não  |

As sessões de sincronização podem falhar por diversos motivos, incluindo o servidor que está sendo reiniciado ou atualizado, instantâneos do VSS etc. Embora esse erro pareça exigir acompanhamento, é seguro ignorar esse erro, a menos que ele persista por um período de várias horas.

<a id="-2147012889"></a>**Não foi possível estabelecer uma conexão com o serviço.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Cadeia de caracteres de erro** | WININET_E_NAME_NOT_RESOLVED |
| **Correção necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**A solicitação do usuário foi limitada pelo serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Cadeia de caracteres de erro** | ECS_E_USER_REQUEST_THROTTLED |
| **Correção necessária** | Não  |

Nenhuma ação é necessária; o servidor tentará novamente. Se esse erro persistir por mais de algumas horas, crie uma solicitação de suporte.

<a id="-2134364065"></a>**A sincronização não pode acessar o compartilhamento de arquivos do Azure especificado no ponto de extremidade da nuvem.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Cadeia de caracteres de erro** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Correção necessária** | Sim |

Esse erro ocorre porque o agente do Azure File Sync não pode acessar o compartilhamento de arquivos do Azure, o que pode ocorrer porque o compartilhamento de arquivos do Azure ou a conta de armazenamento que o hospeda não existe mais. Você pode solucionar esse erro trabalhando nas seguintes etapas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se a conta de armazenamento não contém regras de rede.](#troubleshoot-network-rules)
3. [Verifique se que existe o compartilhamento de arquivos do Azure.](#troubleshoot-azure-file-share)
4. [Certifique-se de que a sincronização de arquivos do Azure tem acesso à conta de armazenamento.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**O nome da conta de armazenamento usado não pôde ser resolvido.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Correção necessária** | Sim |

1. Verifique se você pode resolver o nome DNS de armazenamento do servidor.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
3. [Verifique se a conta de armazenamento não contém regras de rede.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**Falha na sincronização devido a um problema com o banco de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Cadeia de caracteres de erro** | JET_errWriteConflict |
| **Correção necessária** | Sim |

Esse erro ocorre quando há um problema com o banco de dados interno usado pelo Azure File Sync. Quando esse problema ocorrer, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

<a id="-2134364053"></a>**A versão do agente de Sincronização de Arquivos do Azure instalada no servidor não tem suporte.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Cadeia de caracteres de erro** | ECS_E_AGENT_VERSION_BLOCKED |
| **Correção necessária** | Sim |

Este erro ocorrerá se versão do agente de Sincronização de Arquivos do Azure instalada no servidor não for compatível. Para resolver esse problema [atualize]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) para uma [versão do agente compatível]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Você atingiu o limite de armazenamento de compartilhamento de arquivos do Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Cadeia de caracteres de erro** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Correção necessária** | Sim |

Esse erro ocorre quando o limite de armazenamento de compartilhamento de arquivos do Azure é atingido, o que pode acontecer se uma cota for aplicada a um compartilhamento de arquivos do Azure ou se o uso exceder os limites de um compartilhamento de arquivos do Azure. Para obter mais informações, consulte o [limites atuais para um compartilhamento de arquivos do Azure](storage-files-scale-targets.md).

1. Navegue até o grupo de sincronização no Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Observe o nome do compartilhamento de arquivos do Azure no painel aberto.
4. Selecione a conta de armazenamento vinculada. Se esse link falhar, a conta de armazenamento referenciada foi removida.

    ![Uma captura de tela mostrando o painel de detalhes do ponto de extremidade da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecione **Arquivos** para visualizar a lista de compartilhamentos de arquivos.
6. Clique nos três pontos no final da linha do compartilhamento de arquivos do Azure mencionado pelo ponto de extremidade da nuvem.
7. Verifique se o **Uso** está abaixo da **Cota**. Observe que, a menos que uma cota alternativa tenha sido especificada, a cota corresponderá ao [tamanho máximo do compartilhamento de arquivos do Azure](storage-files-scale-targets.md).

    ![Uma captura de tela das propriedades do compartilhamento de arquivos do Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se o compartilhamento estiver cheio e uma cota não estiver configurada, uma maneira possível de corrigir esse problema é transformar cada subpasta do terminal do servidor atual em seu próprio terminal do servidor em seus próprios grupos de sincronização separados. Dessa forma, cada subpasta será sincronizada com compartilhamentos de arquivos individuais do Azure.

<a id="-2134351824"></a>**O compartilhamento de arquivos do Azure não foi encontrado.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Cadeia de caracteres de erro** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Correção necessária** | Sim |

Este erro ocorre quando o compartilhamento de arquivos do Azure não está acessível. Para solucionar problemas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se que existe o compartilhamento de arquivos do Azure.](#troubleshoot-azure-file-share)

Se o compartilhamento de arquivos do Azure tiver sido excluído, você precisará criar um novo compartilhamento de arquivos e, em seguida, recriar o grupo de sincronização. 

<a id="-2134364042"></a>**A sincronização é pausada enquanto esta assinatura do Azure é suspensa.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Correção necessária** | Sim |

Este erro ocorre quando a assinatura do Azure é suspensa. A sincronização será reativada quando a assinatura do Azure for restaurada. Consulte [Por que minha assinatura do Azure está desativada e como eu a reativo?](../../billing/billing-subscription-become-disable.md) para obter mais informações.

<a id="-2134364052"></a> **A conta de armazenamento tem um firewall ou redes virtuais configuradas.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimal)** | -2134364052 |
| **Cadeia de caracteres de erro** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Correção necessária** | Sim |

Esse erro ocorre quando o compartilhamento de arquivos do Azure está inacessível devido a um firewall de conta de armazenamento ou porque a conta de armazenamento pertence a uma rede virtual. O Azure File Sync ainda não tem suporte para esse recurso. Para solucionar problemas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se a conta de armazenamento não contém regras de rede.](#troubleshoot-network-rules)

Remova essas regras para corrigir esse problema. 

<a id="-2134375911"></a>**Falha na sincronização devido a um problema com o banco de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Correção necessária** | Não  |

Esse erro geralmente se resolve e pode ocorrer se houver:

* Um alto número de alterações de arquivos nos servidores do grupo de sincronização.
* Um grande número de erros em arquivos e diretórios individuais.

Se esse erro persistir por mais de algumas horas, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

<a id="-2146762487"></a>**O servidor não conseguiu estabelecer uma conexão segura. O serviço de nuvem recebeu um certificado inesperado.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Cadeia de caracteres de erro** | CERT_E_UNTRUSTEDROOT |
| **Correção necessária** | Sim |

Esse erro pode ocorrer se sua organização estiver usando um proxy de finalização SSL ou se uma entidade mal-intencionada estiver interceptando o tráfego entre o servidor e o serviço de Sincronização de Arquivos do Azure. Se tiver certeza de que isso é esperado (porque sua organização está usando um proxy de finalização SSL), você pula a verificação de certificado com uma substituição de registro.

1. Crie o valor do Registro SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Reinicie o serviço de sincronização no servidor registrado.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ao definir esse valor do Registro, o agente do Azure File Sync aceitará qualquer certificado SSL confiável localmente ao transferir dados entre o servidor e o serviço de nuvem.

<a id="-2147012894"></a>**Não foi possível estabelecer uma conexão com o serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Cadeia de caracteres de erro** | WININET_E_TIMEOUT |
| **Correção necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a> **A sincronização falhou devido a um problema com a autenticação.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Cadeia de caracteres de erro** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Correção necessária** | Sim |

Esse erro pode ser causado por:

- A hora do servidor está incorreta
- Falha na exclusão do ponto de extremidade do servidor
- O certificado usado para autenticação expirou. 
    Para verificar se o certificado está expirado, execute as seguintes etapas:  
    1. Abra o snap-in MMC de Certificados, selecione Conta de Computador e navegue até Certificados (Computador Local)\Pessoal\Certificados.
    2. Verifique se o certificado de autenticação do cliente expirou.

Se o horário do servidor estiver correto, execute as seguintes etapas para resolver o problema:

1. Verifique se a versão do agente de Sincronização de Arquivos do Azure 4.0.1.0 ou posterior está instalada.
2. Execute os seguintes comandos do PowerShell no servidor:

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
    Login-AzStorageSync -SubscriptionID <guid> -TenantID <guid>
    Reset-AzStorageSyncServerCertificate -SubscriptionId <guid> -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**O volume onde se encontra o ponto de extremidade do servidor é baixo espaço em disco.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Cadeia de caracteres de erro** | JET_errLogDiskFull |
| **Correção necessária** | Sim |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Cadeia de caracteres de erro** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Correção necessária** | Sim |

Este erro ocorre porque o volume foi preenchido. Esse erro geralmente ocorre porque os arquivos fora do terminal do servidor estão usando espaço no volume. Libere espaço no volume adicionando terminais adicionais do servidor, movendo arquivos para um volume diferente ou aumentando o tamanho do volume em que o terminal do servidor está.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**O serviço ainda não está pronto para sincronizar com esse ponto de extremidade do servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Cadeia de caracteres de erro** | ECS_E_REPLICA_NOT_READY |
| **Correção necessária** | Não  |

Esse erro ocorre porque há alterações no compartilhamento de arquivos do Azure diretamente e a detecção de alterações está em andamento. A sincronização começará quando a detecção de alterações for concluída.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Falha na sincronização devido a problemas com muitos arquivos individuais.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | -2134364145 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Correção necessária** | Sim |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Correção necessária** | Sim |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Cadeia de caracteres de erro** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Correção necessária** | Sim |

Nos casos em que há muitos erros de sincronização por arquivo, as sessões de sincronização podem começar a falhar. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> O Azure File Sync cria um instantâneo temporário do VSS uma vez por dia no servidor para sincronizar arquivos que tenham identificadores abertos.

<a id="-2134376423"></a>**Falha na sincronização devido a um problema com o caminho do ponto de extremidade de servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_INVALID_PATH |
| **Correção necessária** | Sim |

Assegure-se de que o caminho exista, esteja em um volume NTFS local e não seja um ponto de nova análise ou um terminal do servidor existente.

<a id="-2134375817"></a>**Falha na sincronização porque a versão do driver de filtro não é compatível com a versão do agente**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Cadeia de caracteres de erro** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Correção necessária** | Sim |

Esse erro ocorre porque a versão do driver do filtro de Camada de Nuvem (StorageSync.sys) carregada não é compatível com o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc). Se o agente de Sincronização de Arquivos do Azure tiver sido atualizado, reinicie o servidor para concluir a instalação. Se o erro persistir, desinstale o agente, reinicie o servidor e reinstale o agente de Sincronização de Arquivos do Azure.

<a id="-2134376373"></a>**O serviço está indisponível no momento.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Cadeia de caracteres de erro** | ECS_E_SERVICE_UNAVAILABLE |
| **Correção necessária** | Não  |

Este erro ocorre porque o serviço de Sincronização de Arquivos do Azure está indisponível. Esse erro será resolvido automaticamente quando o serviço de Sincronização de Arquivos do Azure estiver disponível novamente.

<a id="-2134375922"></a>**Falha na sincronização devido a um problema temporário com o banco de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Correção necessária** | Não  |

Este erro ocorre devido a um problema interno com o banco de dados de sincronização. Esse erro será resolvido automaticamente quando o Azure File Sync for sincronizado novamente. Se esse erro persistir por um período prolongado, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

### <a name="common-troubleshooting-steps"></a>Etapas de solução de problemas comuns
<a id="troubleshoot-storage-account"></a>**Verifique se que a conta de armazenamento existe.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Navegue até o grupo de sincronização no Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Observe o nome do compartilhamento de arquivos do Azure no painel aberto.
4. Selecione a conta de armazenamento vinculada. Se esse link falhar, a conta de armazenamento referenciada foi removida.
    ![Uma captura de tela mostrando o painel de detalhes do ponto de extremidade da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**Verifique se que a conta de armazenamento não contém quaisquer regras de rede.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Uma vez na conta de armazenamento, selecione **Firewalls e redes virtuais** no lado esquerdo da conta de armazenamento.
2. Dentro da conta de armazenamento, o botão de opção **Permitir acesso de todas as redes** deve ser selecionado.
    ![Uma captura de tela mostrando o firewall da conta de armazenamento e as regras de rede desativadas.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Verifique se que existe o compartilhamento de arquivos do Azure.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Clique em **visão geral** sobre o sumário à esquerda para retornar à página de conta de armazenamento principal.
2. Selecione **Arquivos** para visualizar a lista de compartilhamentos de arquivos.
3. Verifique se o compartilhamento de arquivos referenciado pelo ponto de extremidade da nuvem aparece na lista de compartilhamentos de arquivos (você deve ter notado isso na etapa 1 acima).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Certifique-se de que a sincronização de arquivos do Azure tem acesso à conta de armazenamento.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Clique em **Controle de acesso (IAM)** no sumário à esquerda.
1. Clique na guia **Atribuições de função** para a lista de usuários e aplicativos (*entidades de serviço*) que têm acesso à sua conta de armazenamento.
1. Verifique se **Serviço de Sincronização de Arquivos Híbridos** aparece na lista com a função **Leitor e o Acesso de Dados**. 

    ![Uma captura de tela da entidade de serviço do serviço de sincronização de arquivo híbrida na guia de controle de acesso da conta de armazenamento](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se o **Serviço de Sincronização de Arquivos do Azure híbrido** não aparecer na lista, execute as seguintes etapas:

    - Clique em **Adicionar**.
    - No campo **Função**, selecione **Leitor e Acesso a Dados**.
    - No campo **Selecionar**, digite **Sincronização de Arquivos do Azure híbrido**, selecione a função e clique em **Salvar**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$foundSyncPrincipal = $false
Get-AzRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Como evito que os usuários criem arquivos contendo caracteres não suportados no servidor?
Você pode usar [Telas de Arquivos do Gerenciador de Recursos de Servidor de Arquivos (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) para impedir que arquivos com caracteres não suportados em seus nomes sejam criados no servidor. Pode ser necessário fazer isso usando o PowerShell, pois a maioria dos caracteres não suportados não pode ser impressa e, portanto, é necessário converter suas representações hexadecimais como caracteres primeiro.

Primeiro, crie um grupo de arquivos FSRM usando o [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Este exemplo define o grupo para conter apenas dois dos caracteres não suportados, mas você pode incluir quantos caracteres forem necessários no seu grupo de arquivos.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Depois de definir um grupo de arquivos do FSRM, você poderá criar uma tela de arquivo do FSRM usando o cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Observe que as telas de arquivos só devem ser usadas para bloquear a criação de caracteres não suportados pelo Azure File Sync. Se as triagens de arquivo forem usadas em outros cenários, a sincronização tentará continuamente fazer o download dos arquivos do compartilhamento de arquivos do Azure para o servidor e será bloqueada devido à triagem de arquivos, resultando em alta saída de dados. 

## <a name="cloud-tiering"></a>Disposição em camadas de nuvem 
Há dois caminhos para falhas na definição de camadas de nuvem:

- A definição de camadas de arquivos pode falhar, o que significa que a Sincronização de arquivos do Azure tenta definir camadas de arquivos nos Arquivos do Azure sem sucesso.
- Os arquivos podem não ser recuperados, o que significa que o filtro do sistema de arquivos do Azure File Sync (StorageSync.sys) falha ao baixar dados quando um usuário tenta acessar um arquivo que foi colocado em camadas.

Há duas classes principais de falhas que podem ocorrer por meio de um desses caminhos de falha:

- Falhas no armazenamento em nuvem
    - *Problemas transitórios de disponibilidade do serviço de armazenamento*. Para obter mais informações, consulte o [Contrato de Nível de Serviço (SLA) para o Armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Compartilhamento de arquivos do Azure inacessível*. Essa falha geralmente ocorre quando você exclui o compartilhamento de arquivos do Azure quando ainda é um ponto de extremidade de nuvem em um grupo de sincronização.
    - *Conta de armazenamento inacessível*. Essa falha normalmente acontece quando você exclui a conta de armazenamento enquanto ela ainda tem um compartilhamento de arquivos do Azure que é um ponto de extremidade de nuvem em um grupo de sincronização. 
- Falhas de servidor 
  - *O filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) não está carregado*. Para responder às solicitações de definição de camadas/recuperação, o filtro do sistema de arquivos da Sincronização de arquivos do Azure deve ser carregado. O filtro pode não ser carregado por diversos motivos, mas o motivo mais comum é quando um administrador o descarrega manualmente. O filtro do sistema de arquivos da Sincronização de arquivos do Azure deve ser carregado sempre para que a Sincronização de arquivos do Azure funcione corretamente.
  - *Ponto de nova análise ausente, corrompido ou desfeito por algum outro motivo*. Um ponto de nova análise é uma estrutura de dados especial em um arquivo que consiste em duas partes:
    1. Uma marca de nova análise, que indica para o sistema operacional que o filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) pode precisar executar alguma ação na E/S do arquivo. 
    2. Dados de nova análise, que indica para o filtro do sistema de arquivos qual é o URI do arquivo no ponto de extremidade de nuvem associado (o compartilhamento de arquivos do Azure). 
        
       A maneira mais comum de corromper um ponto de nova análise é quando um administrador tenta modificar a marca ou seus dados. 
  - *Problemas de conectividade de rede*. Para a definir camadas ou recuperar um arquivo, o servidor deve ter conectividade com a Internet.

As seções a seguir indicam como solucionar problemas de definição de camadas de nuvem e determinar se o problema é do armazenamento em nuvem ou do servidor.

<a id="monitor-tiering-activity"></a>**Como monitorar a atividade em camadas em um servidor**  
Para monitorar a atividade em camadas em um servidor, use as identificações de evento 9003, 9016 e 9029 no log de eventos da Telemetria (localizado no Visualizador de Eventos, em Aplicativos e Serviços\Microsoft\FileSync\Agent).

- A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo, Contagem total de erros, ErrorCode, etc. Observe que um evento é registrado por código de erro.
- A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo, o percentual de espaço livre é, Número de arquivos fantasmados na sessão, Número de arquivos que falharam no fantasma etc.
- A ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo, Número de arquivos tentados na sessão, Número de arquivos em camadas na sessão, Número de arquivos já em camadas, etc.

<a id="monitor-recall-activity"></a>**Como monitorar a atividade de recuperação em um servidor**  
Para monitorar a atividade de recall em um servidor, use as IDs do evento 9005, 9006, 9009 e 9059 no log de eventos da Telemetria (localizado no Visualizador de Eventos, em Aplicativos e Serviços\Microsoft\FileSync\Agent).

- A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo, Total de arquivos exclusivos acessados, Total de arquivos exclusivos com acesso com falha, etc.
- ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo, Total de solicitações com falha, ErrorCode etc. Observe que um evento é registrado por código de erro.
- A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo, ShareId, Nome do Aplicativo e TotalEgressNetworkBytes.

<a id="files-fail-tiering"></a>**Como solucionar problemas de arquivos que falham ao serem dispostos em camadas**  
Se os arquivos não camada para arquivos do Azure:

1. No Visualizador de Eventos, revise os logs de eventos de telemetria, operacionais e de diagnóstico, localizados em Aplicativos e Serviços\Microsoft\FileSync\Agent. 
   1. Verifique se que os arquivos existem no compartilhamento de arquivos do Azure.

      > [!NOTE]
      > Um arquivo deve ser sincronizado com um Compartilhamento de Arquivo do Azure antes de poder ser disposto em camadas.

   2. Verifique se o servidor tem conectividade com a Internet. 
   3. Verifique se que os drivers de filtro de sincronização de arquivos do Azure (storagesync. sys e storagesyncguard. sys) estão em execução:
       - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um ID de Evento 9003 é registrada uma vez por hora no log de eventos da Telemetria se um arquivo falhar na camada (um evento é registrado por código de erro). Os logs de eventos operacionais e de diagnóstico devem ser usados se informações adicionais forem necessárias para diagnosticar um problema.

<a id="files-fail-recall"></a>**Como solucionar problemas de arquivos que falham quando seu recall é realizado**  
Se os arquivos não ser recuperados:
1. No Visualizador de Eventos, revise os logs de eventos de telemetria, operacionais e de diagnóstico, localizados em Aplicativos e Serviços\Microsoft\FileSync\Agent.
    1. Verifique se que os arquivos existem no compartilhamento de arquivos do Azure.
    2. Verifique se o servidor tem conectividade com a Internet. 
    3. Abra o snap-in MMC de serviços e verifique se que o serviço de agente de sincronização de armazenamento (FileSyncSvc) está em execução.
    4. Verifique se que os drivers de filtro de sincronização de arquivos do Azure (storagesync. sys e storagesyncguard. sys) estão em execução:
        - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Uma ID de Evento 9006 é registrada uma vez por hora no log de eventos de Telemetria se um arquivo não for recuperado (um evento é registrado por código de erro). Os logs de eventos operacionais e de diagnóstico devem ser usados se informações adicionais forem necessárias para diagnosticar um problema.

<a id="files-unexpectedly-recalled"></a>**Como solucionar problemas da realização de recall inesperada de arquivos em um servidor**  
Antivírus, backup e outros aplicativos que leem grandes quantidades de arquivos causarão recalls indesejados a menos que eles respeitem o atributo offline e ignorem a leitura do conteúdo desses arquivos. Ignorar arquivos off-line de produtos que dão suporte a essa opção ajuda a evitar recuperações não intencionais durante operações, como verificações antivírus ou trabalhos de backup.

Consulte seu fornecedor de software sobre como configurar sua solução para ignorar a leitura de arquivos offline.

Recuperações não intencionais também podem ocorrer em outros cenários, como quando você estiver pesquisando arquivos no Explorador de arquivos. Abrir uma pasta com arquivos de nuvem em camadas no Explorador de arquivos no servidor pode resultar em recuperações não intencionais. Isso é ainda mais provável se uma solução antivírus estiver habilitada no servidor.

> [!NOTE]
>Use a ID de evento 9059 no log de eventos de telemetria para determinar quais aplicativos estão causando os recalls. Este evento oferece distribuição de recall de aplicativo para um ponto de extremidade do servidor e é registrado uma vez por hora.

## <a name="general-troubleshooting"></a>Solução de problemas gerais
Se você encontrar problemas com a Sincronização de arquivos do Azure em um servidor, comece executando o seguinte:
1. No Visualizador de Eventos, revise os logs de eventos de telemetria, operacionais e de diagnóstico.
    - Os problemas de sincronização, classificação em camadas e recuperação são registrados nos logs de eventos de telemetria, diagnóstico e operacional em Aplicativos e Serviços\Microsoft\FileSync\Agent.
    - Problemas relacionados ao gerenciamento de um servidor (por exemplo, definições de configuração) são registrados nos logs de eventos operacionais e de diagnóstico em aplicativos e Services\Microsoft\FileSync\Management.
2. Verifique se o serviço de Sincronização de Arquivo do Azure está em execução no servidor:
    - Abra o snap-in do MMC de Serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
3. Verifique se que os drivers de filtro de sincronização de arquivos do Azure (storagesync. sys e storagesyncguard. sys) estão em execução:
    - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

Se o problema não for resolvido, execute a ferramenta de AFSDiag:
1. Crie um diretório que será usado para salvar a saída da AFSDiag (por exemplo, C:\Output).
2. Abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos (pressione Enter depois de cada comando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o nível de rastreamento do modo de kernel da Sincronização de arquivos do Azure, insira **1** (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
4. Para o nível de rastreamento do modo de usuário da Sincronização de arquivos do Azure, insira **1** (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
5. Reproduzir o problema. Quando tiver terminado, clique em **D**.
6. Um arquivo. zip que contém logs e arquivos de rastreamento é salvo no diretório de saída que você especificou.

## <a name="see-also"></a>Consulte também
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
- [Solucionar problemas de Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar problemas de Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
