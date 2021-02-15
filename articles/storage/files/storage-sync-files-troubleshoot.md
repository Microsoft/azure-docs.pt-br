---
title: Solucionar problemas de sincronização de arquivos do Azure | Microsoft Docs
description: Solucione problemas comuns em uma implantação no Sincronização de Arquivos do Azure, que você pode usar para transformar o Windows Server em um cache rápido do seu compartilhamento de arquivos do Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 2/1/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 534f9e76cfca4037634cfca089f1131984456636
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820697"
---
# <a name="troubleshoot-azure-file-sync"></a>Solucionar problemas da Sincronização de Arquivos do Azure
Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo foi projetado para ajudá-lo a solucionar problemas e resolver problemas encontrados com a implantação da Sincronização de arquivos do Azure. Nós também descrevemos como coletar logs importantes do sistema para ajudar em uma investigação mais profunda dos problemas. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. [Página de P e R da Microsoft para Armazenamento do Azure](/answers/products/azure?product=storage).
2. O [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. O Suporte da Microsoft. Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

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

<a id="parameter-is-incorrect"></a>**O acesso a um volume no Windows Server 2012 R2 falha com o erro: O parâmetro está incorreto**  
Depois de criar um ponto de extremidade de servidor no Windows Server 2012 R2, o seguinte erro ocorrerá ao acessar o volume:

letradaunidade:\ não está acessível.  
O parâmetro está incorreto.

Para resolver esse problema, instale o [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) e reinicie o servidor. Se essa atualização não for instalada porque uma atualização posterior já está instalada, vá para Windows Update, instale as atualizações mais recentes do Windows Server 2012 R2 e reinicie o servidor.

<a id="server-registration-missing-subscriptions"></a>**O Registro de Servidor não lista todas as Assinaturas do Azure**  
Ao registrar um servidor usando o ServerRegistration.exe, as assinaturas estão ausentes quando você clica na lista suspensa da Assinatura do Azure.

Esse problema ocorre porque ServerRegistration.exe recuperará somente as assinaturas dos primeiros 5 locatários do Azure AD. 

Para aumentar o limite do locatário de registro do servidor no servidor, crie um valor DWORD chamado ServerRegistrationTenantLimit em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync com um valor maior que 5.

Você também pode solucionar esse problema usando os seguintes comandos do PowerShell para registrar o servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**O registro do servidor exibe a seguinte mensagem: "Os pré-requisitos estão ausentes"**  
Essa mensagem será exibida se o módulo Az ou AzureRM do PowerShell não estiver instalado no PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe não é compatível com o PowerShell 6.x. Você pode usar o cmdlet Register-AzStorageSyncServer no PowerShell 6.x para registrar o servidor.

Para instalar o módulo Az ou AzureRM no PowerShell 5.1, execute as seguintes etapas:

1. Digite **PowerShell** de um prompt de comandos com privilégios elevados e pressione Enter.
2. Instale o módulo Az ou AzureRM mais recente seguindo a documentação:
    - [Módulo Az (requer o .NET 4.7.2)](/powershell/azure/install-az-ps?viewFallbackFrom=azps-1.1.0)
    - [Módulo AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Execute ServerRegistration.exe e siga o assistente para registrar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**O registro do servidor exibe a seguinte mensagem: "Este servidor já está registrado"** 

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

<a id="server-registration-missing"></a>**O servidor não está listado em servidores registrados no portal do Azure**  
Se algum servidor não estiver listado em **Servidores registrados** de um Serviço de Sincronização de Armazenamento:
1. Entre no servidor que você deseja registrar.
2. Abra o Explorador de arquivos e, em seguida, vá para o diretório de instalação do agente de sincronização de armazenamento (o local padrão é C:\Program Files\Azure\StorageSyncAgent). 
3. Execute ServerRegistration.exe e siga o assistente para registrar o servidor com um Serviço de Sincronização de Armazenamento.

## <a name="sync-group-management"></a>Gerenciamento de grupo de sincronização

### <a name="cloud-endpoint-creation-errors"></a>Erros de criação de ponto de extremidade de nuvem

<a id="cloud-endpoint-using-share"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "O Compartilhamento de Arquivos do Azure especificado já está sendo usado por um CloudEndpoint diferente"**  
Esse erro ocorre se o compartilhamento de arquivo do Azure já está em uso por outro ponto de extremidade da nuvem. 

Se essa mensagem aparecer e o compartilhamento de arquivos do Azure não estiver sendo usado por um ponto de extremidade de nuvem no momento, conclua as seguintes etapas para limpar os metadados da Sincronização de arquivos do Azure no compartilhamento de arquivos do Azure:

> [!Warning]  
> Excluir os metadados em um compartilhamento de arquivos do Azure que está sendo usado no momento por um ponto de extremidade de nuvem faz com que as operações da Sincronização de arquivos do Azure falhem. 

1. Navegue até o seu compartilhamento de arquivos do Azure no Portal do Azure.  
2. Clique com botão direito no compartilhamento de arquivos do Azure e selecione **Editar metadados**.
3. Clique com botão direito em **SyncService** e selecione **Excluir**.

<a id="cloud-endpoint-authfailed"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "AuthorizationFailed"**  
Esse erro ocorre quando a conta de usuário não tem direitos suficientes para criar um ponto de extremidade na nuvem. 

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
2. Selecione o grupo de recursos em que a conta de armazenamento está localizada e clique em **Controle de acesso (IAM)** .
3. Selecione a guia **Atribuições de função**.
4. Selecione a **Função** (por exemplo, o proprietário ou colaborador) para sua conta de usuário.
5. Na lista **Provedor de Recursos**, selecione **Autorização da Microsoft**. 
    * **Atribuição de função** deve ter **Permissões de Leitura** e de **Gravação**.
    * **Definição de função** deve ter **Permissões de Leitura** e de **Gravação**.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Erros de criação e exclusão do ponto de extremidade do servidor

<a id="-2134375898"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2134375898 ou 0x80c80226)**  
Esse erro ocorrerá se o caminho do ponto de extremidade de servidor estiver no volume do sistema e a camada de nuvem estiver habilitada. A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.

<a id="-2147024894"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2147024894 ou 0x80070002)**  
Esse erro ocorrerá se o caminho do ponto de extremidade do servidor especificado não for válido. Verifique se o caminho do ponto de extremidade do servidor especificado é um volume NTFS anexado localmente. Observação: a Sincronização de Arquivos do Azure não é compatível com as unidades mapeadas como um caminho de ponto de extremidade do servidor.

<a id="-2134375640"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2134375640 ou 0x80c80328)**  
Esse erro ocorrerá se o caminho do ponto de extremidade do servidor especificado não for um volume NTFS. Verifique se o caminho do ponto de extremidade do servidor especificado é um volume NTFS anexado localmente. Observação: a Sincronização de Arquivos do Azure não é compatível com as unidades mapeadas como um caminho de ponto de extremidade do servidor.

<a id="-2134347507"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2134347507 ou 0x80c8710d)**  
Esse erro ocorre porque a Sincronização de Arquivos do Azure não é compatível com pontos de extremidade de servidor em volumes que têm uma pasta de informações de volume do sistema compactada. Para resolver esse problema, descompacte a pasta Informações de Volume do Sistema. Se a pasta Informações de Volume do Sistema for a única pasta compactada no volume, execute as seguintes etapas:

1. Baixe a ferramenta [PsExec](/sysinternals/downloads/psexec).
2. Execute o comando a seguir em um prompt de comando elevado para iniciar um prompt de comando em execução na conta do sistema: **PsExec.exe -i -s -d cmd**
3. No prompt de comando em execução na conta do sistema, digite os seguintes comandos e pressione Enter:   
    **cd /d "letra da unidade:\System Volume Information"**  
    **compact /u /s**

<a id="-2134376345"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2134376345 ou 0x80C80067)**  
Esse erro ocorrerá se o limite de pontos de extremidade do servidor por servidor for atingido. A Sincronização de Arquivos do Azure atualmente permite até 30 pontos de extremidade de servidor por servidor. Para obter mais informações, confira [Destinos de escala da Sincronização de Arquivos do Azure](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2134376427 ou 0x80c80015)**  
Esse erro ocorrerá se outro ponto de extremidade do servidor já estiver sincronizando o caminho do ponto de extremidade do servidor especificado. A Sincronização de Arquivos do Azure não é compatível com vários pontos de extremidade de servidor sincronizando o mesmo diretório ou volume.

<a id="-2160590967"></a>**Falha na criação de ponto de extremidade de nuvem, com este erro: "MgmtServerJobFailed" (Código de erro: -2160590967 ou 0x80c80077)**  
Esse erro ocorrerá se o caminho do ponto de extremidade do servidor contém arquivos em camadas órfãos. Se um ponto de extremidade do servidor foi removido recentemente, aguarde a conclusão da limpeza de arquivos em camadas órfãos. Uma ID do Evento 6662 é registrada no log de eventos de telemetria após o início da limpeza de arquivos em camadas órfãs. Uma ID do Evento 6661 será registrada depois que a limpeza de arquivos em camadas órfãos for concluída e um ponto de extremidade do servidor puder ser recriado usando o caminho. Se a criação do ponto de extremidade do servidor falhar depois que a limpeza de arquivos em camadas for concluída ou se a ID do Evento 6661 não puder ser encontrada no log de eventos de telemetria devido à substituição do log de eventos, remova os arquivos em camadas órfãs executando as etapas documentadas na seção [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).

<a id="-2134347757"></a>**Falha na exclusão de ponto de extremidade de nuvem, com este erro: "MgmtServerJobExpired" (Código de erro: -2134347757 ou 0x80c87013)**  
Esse erro ocorrerá se o servidor estiver offline ou não tiver conectividade de rede. Se o servidor não estiver mais disponível, cancele o registro do servidor no portal que excluirá os pontos de extremidade do servidor. Para excluir os pontos de extremidade do servidor, siga as etapas descritas em [Cancelar o registro de um servidor com a Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Integridade do ponto de extremidade do servidor

<a id="server-endpoint-provisioningfailed"></a>**Não é possível abrir a página de propriedades do ponto de extremidade do servidor ou atualizar a política de camada de nuvem**  
Esse problema pode ocorrer se uma operação de gerenciamento no ponto de extremidade do servidor falhar. Se a página de propriedades do ponto de extremidade de servidor não abrir no Portal do Azure, atualizar o ponto de extremidade de servidor usando comandos do PowerShell a partir do servidor poderá solucionar esse problema. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**O ponto de extremidade do servidor tem um status de integridade "Sem Atividade" ou "Pendente" e o estado do servidor na folha de servidores registrados é "Aparece offline"**  

Esse problema poderá ocorrer se o processo do Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) não estiver em execução ou o servidor não conseguir acessar o serviço de Sincronização de Arquivos do Azure.

No servidor que está sendo exibido como "Aparece offline" no portal, examine a ID do Evento 9301 no log de eventos de telemetria (localizado em Applications and Services\Microsoft\FileSync\Agent no Visualizador de Eventos) para determinar por que o servidor não consegue acessar o serviço de Sincronização de Arquivos do Azure. 

- Se **GetNextJob foi concluído com o status: 0** for registrado, o servidor pode se comunicar com o serviço de Sincronização de Arquivos do Azure. 
    - Abra o Gerenciador de Tarefas no servidor e verifique se o processo do Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) está em execução. Se o processo não estiver funcionando, primeiro tente reiniciar o servidor. Se a reinicialização do servidor não resolver o problema, atualize o agente de Sincronização de Arquivos do Azure para a [versão](./storage-files-release-notes.md). 

- Se **GetNextJob concluído com status:-2134347756** for registrado, o servidor não poderá se comunicar com o serviço de sincronização de arquivos do Azure devido a uma configuração de pedido de firewall, proxy ou TLS Cipher Suite. 
    - Se o servidor estiver atrás de um firewall, verifique se a porta 443 de saída é permitida. Se o firewall restringe o tráfego a domínios específicos, confirme se os domínios listados na [documentação](./storage-sync-files-firewall-and-proxy.md#firewall) do Firewall estão acessíveis.
    - Se o servidor estiver atrás de um proxy, defina as configurações de proxy específicas do aplicativo ou para todo o computador seguindo as etapas na [documentação](./storage-sync-files-firewall-and-proxy.md#proxy) do Proxy.
    - Use o cmdlet Test-StorageSyncNetworkConnectivity para verificar a conectividade de rede para os pontos de extremidade de serviço. Para saber mais, confira [Testar conectividade de rede para pontos de extremidade de serviço](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).
    - Se a ordem do conjunto de criptografia TLS estiver configurada no servidor, você poderá usar a política de grupo ou os cmdlets TLS para adicionar conjuntos de codificação:
        - Para usar a política de grupo, consulte [Configurando a ordem do conjunto de codificação TLS usando política de grupo](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Para usar os cmdlets TLS, consulte [Configurando o TLS Cipher Suite Order usando cmdlets do PowerShell do TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets).
    
        O Sincronização de Arquivos do Azure atualmente dá suporte aos seguintes conjuntos de codificação para o protocolo TLS 1,2:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Se **GetNextJob foi concluído com o status: -2134347764** for registrado, o servidor não poderá se comunicar com o serviço de Sincronização de Arquivos do Azure devido a um certificado expirado ou excluído.  
    - Execute o seguinte comando do PowerShell no servidor para redefinir o certificado usado para autenticação:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**O ponto de extremidade do servidor tem um status de integridade "Sem Atividade" e o estado do servidor na folha de servidores registrados é "Online"**  

Um status de integridade do ponto de extremidade de servidor de “Sem Atividade” significa que o ponto de extremidade do servidor não registrou a atividade de sincronização em log nas últimas duas horas.

Para verificar a atividade de sincronização atual em um servidor, confira [Como fazer para monitorar o andamento de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Um ponto de extremidade do servidor pode não registrar a atividade de sincronização por várias horas devido a um bug ou a insuficiência de recursos do sistema. Verifique se a [versão mais recente do agente](./storage-files-release-notes.md) da Sincronização de Arquivos do Azure está instalada. Se o problema persistir, abra uma solicitação de suporte.

> [!Note]  
> Se o estado do servidor na folha de servidores registrados for "Aparece Offline", execute as etapas documentadas na seção [O ponto de extremidade do servidor tem um status de integridade "Sem Atividade" ou "Pendente" e o estado do servidor na folha de servidores registrados é "Aparece offline"](#server-endpoint-noactivity).

## <a name="sync"></a>Sincronizar
<a id="afs-change-detection"></a>**Se eu criar um arquivo diretamente em meu compartilhamento de arquivos do Azure usando SMB ou por meio do portal, quanto tempo levará para que o arquivo seja sincronizado com os servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a> **A integridade do ponto de extremidade do servidor está em um estado pendente por várias horas**  
Esse problema é esperado quando você cria um ponto de extremidade de nuvem e usa um compartilhamento de arquivos do Azure que contém dados. A tarefa de enumeração de alterações que verifica as alterações no compartilhamento de arquivos do Azure deve ser concluída antes que os arquivos possam ser sincronizados entre os nós de extremidade da nuvem e do servidor. O tempo para concluir o trabalho depende do tamanho do espaço para nome no compartilhamento de arquivos do Azure. A integridade do ponto de extremidade do servidor deve ser atualizada quando o trabalho de enumeração de alterações for concluído.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Como monitoro a integridade da sincronização?
# <a name="portal"></a>[Portal](#tab/portal1)
Em cada grupo de sincronização, você pode detalhar seus pontos de extremidade de servidor individuais para ver o status das últimas sessões de sincronização concluídas. Uma coluna de integridade verde e um valor de não sincronização de arquivos de 0 indicam que a sincronização está funcionando conforme o esperado. Se esse não for o caso, veja abaixo uma lista de erros comuns de sincronização e como manipular arquivos que não estão sendo sincronizados. 

![Uma captura de tela do portal do Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Servidor](#tab/server)
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
# <a name="portal"></a>[Portal](#tab/portal1)
Dentro do seu grupo de sincronização, vá para o ponto de extremidade do servidor em questão e examine a seção Atividade de Sincronização para ver a contagem de arquivos carregados ou baixados na sessão de sincronização atual. Observe que esse status será atrasado em cerca de 5 minutos e, se sua sessão de sincronização for pequena o suficiente para ser concluída dentro desse período, ela pode não ser informada no portal. 

# <a name="server"></a>[Servidor](#tab/server)
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
# <a name="portal"></a>[Portal](#tab/portal1)
Para cada servidor em um determinado grupo de sincronização, verifique se:
- Os timestamps da última tentativa de sincronização para upload e download são recentes.
- O status é verde para carregamento e download.
- O campo Atividade de Sincronização mostra poucos ou nenhum arquivo restante para sincronizar.
- O campo Arquivos que não são sincronizados é 0 para upload e download.

# <a name="server"></a>[Servidor](#tab/server)
Observe as sessões de sincronização concluídas, marcadas por 9102 eventos no registro de eventos de telemetria de cada servidor (no Visualizador de Eventos, vá para `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Em qualquer servidor, você quer garantir que as últimas sessões de upload e download sejam concluídas com sucesso. Para fazer isso, verifique se HResult e PerItemErrorCount são 0 para upload e download (o campo SyncDirection indica se uma determinada sessão é uma sessão de upload ou download). Observe que, se você não vir uma sessão de sincronização concluída recentemente, é provável que uma sessão de sincronização esteja em andamento, o que é esperado se você acabou de adicionar ou modificar uma grande quantidade de dados.
2. Quando um servidor estiver totalmente atualizado com a nuvem e não tiver alterações para sincronizar nas duas direções, você verá sessões de sincronização vazias. Eles são indicados por eventos de upload e download nos quais todos os campos Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount e SyncSizeBytes) são zero, o que significa que não há nada a ser sincronizado. Observe que essas sessões de sincronização vazias podem não ocorrer em servidores de alta rotatividade, pois há sempre algo novo para sincronizar. Se não houver atividade de sincronização, elas devem ocorrer a cada 30 minutos. 
3. Se todos os servidores estiverem atualizados com a nuvem, o que significa que suas sessões de upload e download recentes são sessões de sincronização vazias, você pode dizer com razoável certeza que o sistema como um todo está em sincronia. 
    
Observe que, se você fez alterações diretamente no compartilhamento de arquivos do Azure, o Azure File Sync não detectará essa alteração até que a enumeração de alterações seja executada, o que acontece uma vez a cada 24 horas. É possível que um servidor diga que está atualizado com a nuvem quando, na verdade, está faltando alterações recentes feitas diretamente no compartilhamento de arquivos do Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Como faço para ver se há arquivos ou pastas específicos que não estão sendo sincronizados?
Se o seu PerItemErrorCount no servidor ou a contagem de Não Sincronização de Arquivos no portal for maior que 0 para qualquer sessão de sincronização, isso significa que alguns itens não estão sendo sincronizados. Arquivos e pastas podem ter características que os impedem de sincronizar. Essas características podem ser persistentes e exigem ação explícita para retomar a sincronização, por exemplo, removendo caracteres não suportados do nome do arquivo ou da pasta. Eles também podem ser temporários, o que significa que o arquivo ou a pasta retornará automaticamente à sincronização. por exemplo, arquivos com identificadores abertos retomarão automaticamente a sincronização quando o arquivo for fechado. Quando o mecanismo do Azure File Sync detecta esse problema, é produzido um log de erros que pode ser analisado para listar os itens que atualmente não estão sendo sincronizados corretamente.

Para ver esses erros, execute o script do PowerShell **FileSyncErrorsReport.ps1** (localizado no diretório de instalação do Agente do Azure File Sync) para identificar os arquivos que falharam na sincronização devido a identificadores abertos, caracteres não suportados ou outros problemas . O campo ItemPath informa a localização do arquivo em relação ao diretório de sincronização raiz. Veja abaixo a lista de erros comuns de sincronização para as etapas de correção.

> [!Note]  
> Se o script FileSyncErrorsReport.ps1 retornar "Nenhum erro de arquivo encontrado" ou não listar erros por item para o grupo de sincronização, a causa será:
>
>- Causa 1: a última sessão de sincronização concluída não tinha erros por item. O portal deve ser atualizado em breve para mostrar 0 Arquivos Não Sincronizando. 
>    - Verifique a [ID do Evento 9102](?tabs=server%252cazure-portal#broken-sync) no log de eventos de telemetria para confirmar se o PerItemErrorCount é 0. 
>
>- Causa 2: o log de eventos de ItemResults no servidor foi encapsulado devido a muitos erros por item e o log de eventos não contém mais erros para esse grupo de sincronização.
>    - Para evitar esse problema, aumente o tamanho do log de eventos de ItemResults. O log de eventos ItemResults está localizado em "Applications and Services Logs\Microsoft\FileSync\Agent" no Visualizador de Eventos. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Solução de problemas por erros de sincronização de arquivos/diretórios
**ItemResulta erros de sincronização de log por item**  

| HRESULT | HRESULT (decimal) | Cadeia de caracteres de erro | Problema | Correção |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | O arquivo em camadas no servidor não está acessível. Esse problema ocorrerá se o arquivo em camadas não tiver sido recuperado antes da exclusão de um ponto de extremidade do servidor. | Para resolver esse problema, confira [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | A alteração de arquivo ou diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não está sincronizada. Este item será sincronizado após o mudanças dependentes serem sincronizadas. | Nenhuma ação é necessária. Se o erro persistir por vários dias, use o script do PowerShell FileSyncErrorsReport.ps1 para determinar por que a pasta dependente ainda não está sincronizada. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | A alteração de arquivo ou diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não está sincronizada. Este item será sincronizado após o mudanças dependentes serem sincronizadas. | Nenhuma ação é necessária. Se o erro persistir por vários dias, use o script do PowerShell FileSyncErrorsReport.ps1 para determinar por que a pasta dependente ainda não está sincronizada. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | A alteração de arquivo ou diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não está sincronizada e a sessão de sincronização falhou. Este item será sincronizado após o mudanças dependentes serem sincronizadas. | Nenhuma ação é necessária. Se o erro persistir, investigue a falha da sessão de sincronização. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | O nome do arquivo ou diretório é inválido. | Renomeie o arquivo ou diretório em questão. Veja [Tratamento de caracteres sem suporte](?tabs=portal1%252cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | O nome do arquivo ou diretório é inválido. | Renomeie o arquivo ou diretório em questão. Veja [Tratamento de caracteres sem suporte](?tabs=portal1%252cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | O arquivo não pode ser sincronizado porque está em uso. O arquivo será sincronizado quando não estiver mais em uso. | Nenhuma ação é necessária. O Azure File Sync cria um instantâneo temporário do VSS uma vez por dia no servidor para sincronizar arquivos que tenham identificadores abertos. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | O arquivo foi alterado, mas a alteração ainda não foi detectada pela sincronização. A sincronização será recuperada depois que essa alteração for detectada. | Nenhuma ação é necessária. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | O arquivo foi excluído e a sincronização não está ciente da alteração. | Nenhuma ação é necessária. A sincronização não registrará mais esse erro quando a detecção de alterações detectar que o arquivo foi excluído. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | A exclusão de um arquivo ou diretório não pode ser sincronizada porque o item já foi excluído no destino e a sincronização não está ciente da alteração. | Nenhuma ação é necessária. A sincronização interromperá o registro desse erro quando a detecção de alteração for executada no destino e a sincronização detectar que o item foi excluído. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | O arquivo ou diretório foi ignorado, mas será sincronizado durante a próxima sessão de sincronização. Se esse erro for relatado durante o download do item, o nome do arquivo ou diretório quase certamente será inválido. | Nenhuma ação será necessária se esse erro for relatado ao carregar o arquivo. Se o erro for relatado ao baixar o arquivo, renomeie o arquivo ou o diretório em questão. Veja [Tratamento de caracteres sem suporte](?tabs=portal1%252cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | A criação de um arquivo ou diretório não pode ser sincronizada porque o item já existe no destino e a sincronização não está ciente da alteração. | Nenhuma ação é necessária. A sincronização interromperá o registro desse erro quando a detecção de alteração for executada no destino e a sincronização estiver ciente desse novo item. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | O arquivo não pode ser sincronizado porque o limite de compartilhamento de arquivos do Azure foi atingido. | Para resolver esse problema, veja a seção [Você atingiu o limite de armazenamento de compartilhamento de arquivos do Azure](?tabs=portal1%252cazure-portal#-2134351810) no guia de solução de problemas. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | O arquivo é criptografado por uma solução não compatível (como EFS NTFS). | Descriptografe o arquivo e use uma solução de criptografia compatível. Para obter uma lista de soluções com suporte, veja a seção [Soluções de criptografia](./storage-sync-files-planning.md#encryption) no guia de planejamento. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | O arquivo está localizado em uma pasta de replicação somente leitura do DFS-R. | O arquivo está localizado em uma pasta de replicação somente leitura do DFS-R. A Sincronização de Arquivos do Azure não oferece suporte a pontos de extremidade de servidor em pastas de replicação somente leitura do DFS-R. Consulte o [guia de planejamento](./storage-sync-files-planning.md#distributed-file-system-dfs) para obter mais informações. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O arquivo tem um estado de exclusão pendente. | Nenhuma ação é necessária. O arquivo será excluído assim que todos os identificadores de arquivo abertos forem fechados. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | O arquivo não pode ser sincronizado, já que as configurações do firewall e da rede virtual na conta de armazenamento estão habilitadas e o servidor não tem acesso à conta de armazenamento. | Adicione a rede virtual ou o endereço IP do servidor seguindo as etapas documentadas na seção [Definir configurações de rede virtual e de firewall](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implantação. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | O arquivo não pode ser sincronizado porque o tamanho do descritor de segurança excede o limite de 64 KiB. | Para resolver esse problema, remova as entradas de controle de acesso no arquivo para reduzir o tamanho do descritor de segurança. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | O arquivo não pode ser sincronizado devido a um erro inesperado. | Se o erro persistir por vários dias, abra um caso de suporte. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | O arquivo não pode ser sincronizado porque está em uso. O arquivo será sincronizado quando não estiver mais em uso. | Nenhuma ação é necessária. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | O arquivo foi alterado durante a sincronização, portanto, ele precisa ser sincronizado novamente. | Nenhuma ação é necessária. |
| 0x80070017 | -2147024873 | ERROR_CRC | O arquivo não pode ser sincronizado devido a um erro de CRC. Esse erro pode ocorrer se um arquivo em camadas não tiver sido recuperado antes da exclusão de um ponto de extremidade do servidor ou se o arquivo estiver corrompido. | Para resolver esse problema, confira [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) para remover arquivos em camadas que são órfãos. Se o erro continuar após a remoção dos arquivos em camada órfãos, execute [chkdsk](/windows-server/administration/windows-commands/chkdsk) no volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | O arquivo não pode ser sincronizado porque o número máximo de arquivos de conflito foi atingido. A Sincronização de Arquivos do Azure dá suporte a 100 arquivos de conflito por arquivo. Para saber mais sobre conflitos de arquivo, confira as [perguntas frequentes](./storage-files-faq.md#afs-conflict-resolution) da Sincronização de Arquivos do Azure. | Para resolver esse problema, reduza o número de arquivos com conflito. O arquivo será sincronizado assim que o número de arquivos com conflito for menor que 100. |

#### <a name="handling-unsupported-characters"></a>Manipulando Caracteres Não Suportados
Se o script do **FileSyncErrorsReport.ps1** PowerShell mostrar erros de sincronização por item devido a caracteres sem suporte (código de erro 0x8007007b ou 0x80c80255), você deverá remover ou renomear os caracteres com falha dos respectivos nomes de arquivo. PowerShell provavelmente será impresso esses caracteres como pontos de interrogação ou retângulos vazios, pois a maior parte desses caracteres não têm nenhuma codificação de visual padrão. 
> [!Note]  
> Uma [Ferramenta de Avaliação](storage-sync-files-planning.md#evaluation-cmdlet) pode ser usada para identificar os caracteres sem suporte. Se o conjunto de seus conjuntos de um tiver vários arquivos com caracteres inválidos, use o script [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) para renomear os arquivos que contêm caracteres sem suporte.

A tabela abaixo contém todos os caracteres unicode que o Azure File Sync ainda não suporta.

| Conjunto de caracteres | Contagem de caracteres |
|---------------|-----------------|
| <ul><li>0x0000009D (osc comando do sistema operacional)</li><li>0x00000090 (cadeia de controle de dispositivo de controladores de domínio)</li><li>0x0000008F (ss3 único shift três)</li><li>0x00000081 (predefinição de octeto alta)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverso alimentação de linha)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (apresentação árabe formulários-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (especiais) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (não caracteres)</li><li>0x0002FFFE - 0x0002FFFF = 2 (não caracteres)</li><li>0x0003FFFE - 0x0003FFFF = 2 (não caracteres)</li><li>0x0004FFFE - 0x0004FFFF = 2 (não caracteres)</li><li>0x0005FFFE - 0x0005FFFF = 2 (não caracteres)</li><li>0x0006FFFE - 0x0006FFFF = 2 (não caracteres)</li><li>0x0007FFFE - 0x0007FFFF = 2 (não caracteres)</li><li>0x0008FFFE - 0x0008FFFF = 2 (não caracteres)</li><li>0x0009FFFE - 0x0009FFFF = 2 (não caracteres)</li><li>0x000AFFFE - 0x000AFFFF = 2 (não caracteres)</li><li>0x000BFFFE - 0x000BFFFF = 2 (não caracteres)</li><li>0x000CFFFE - 0x000CFFFF = 2 (não caracteres)</li><li>0x000DFFFE - 0x000DFFFF = 2 (não caracteres)</li><li>0x000EFFFE - 0x000EFFFF = 2 (indefinido)</li><li>0x000FFFFE - 0x000FFFFF = 2 (área de suplementares de uso particular)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Erros comuns de sincronização
<a id="-2147023673"></a>**A sessão de sincronização foi cancelada.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Cadeia de caracteres de erro** | ERROR_CANCELLED |
| **Correção necessária** | Não |

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
| **Correção necessária** | Não |

Nenhuma ação é necessária; o servidor tentará novamente. Se esse erro persistir por várias horas, crie uma solicitação de suporte.

<a id="-2134364043"></a>**A sincronização é bloqueada até que a detecção de alterações seja concluída após a restauração**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimal)** | -2134364043 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Correção necessária** | Não |

Nenhuma ação é necessária. Quando um compartilhamento de arquivo ou arquivo (ponto de extremidade na nuvem) é restaurado usando o Backup do Azure, a sincronização é bloqueada até que a detecção de alteração seja concluída no compartilhamento de arquivo do Azure. A detecção de alteração é executada imediatamente quando a restauração é concluída, e a duração é baseada no número de arquivos no compartilhamento de arquivo.

<a id="-2147216747"></a>**A sincronização falhou porque o banco de dados de sincronização foi descarregado.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimal)** | -2147216747 |
| **Cadeia de caracteres de erro** | SYNC_E_METADATA_INVALID_OPERATION |
| **Correção necessária** | Não |

Esse erro normalmente ocorre quando um aplicativo de backup cria um instantâneo de VSS e o banco de dados de sincronização é descarregado. Se esse erro persistir por várias horas, crie uma solicitação de suporte.

<a id="-2134364065"></a>**A sincronização não pode acessar o compartilhamento de arquivos do Azure especificado no ponto de extremidade da nuvem.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Cadeia de caracteres de erro** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Correção necessária** | Sim |

Esse erro ocorre porque o agente do Azure File Sync não pode acessar o compartilhamento de arquivos do Azure, o que pode ocorrer porque o compartilhamento de arquivos do Azure ou a conta de armazenamento que o hospeda não existe mais. Você pode solucionar esse erro trabalhando nas seguintes etapas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se que existe o compartilhamento de arquivos do Azure.](#troubleshoot-azure-file-share)
3. [Certifique-se de que a sincronização de arquivos do Azure tem acesso à conta de armazenamento.](#troubleshoot-rbac)
4. [Verifique se as configurações de firewall e de rede virtual na conta de armazenamento estão definidas adequadamente (se habilitadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**A sincronização falhou porque a solicitação não está autorizada a executar esta operação.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (decimal)** | -2134351804 |
| **Cadeia de caracteres de erro** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Correção necessária** | Sim |

Esse erro ocorre porque o agente de Sincronização de Arquivos do Azure não está autorizado a acessar o compartilhamento de arquivo do Azure. Você pode solucionar esse erro trabalhando nas seguintes etapas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se que existe o compartilhamento de arquivos do Azure.](#troubleshoot-azure-file-share)
3. [Verifique se as configurações de firewall e de rede virtual na conta de armazenamento estão definidas adequadamente (se habilitadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
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
3. [Verifique se as configurações de firewall e de rede virtual na conta de armazenamento estão definidas adequadamente (se habilitadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ocorreu um erro desconhecido durante o acesso à conta de armazenamento.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (decimal)** | -2134364022 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Correção necessária** | Sim |

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se as configurações de firewall e de rede virtual na conta de armazenamento estão definidas adequadamente (se habilitadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**O ImageLoad falhou devido ao erro de conta de armazenamento.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (decimal)** | -2134364014 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Correção necessária** | Sim |

Esse erro ocorre porque a conta de armazenamento tem um [bloqueio de recurso](../../azure-resource-manager/management/lock-resources.md) somente leitura. Para resolver esse problema, remova o bloqueio de recurso somente leitura na conta de armazenamento. 

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

Este erro ocorre quando a assinatura do Azure é suspensa. A sincronização será reativada quando a assinatura do Azure for restaurada. Consulte [Por que minha assinatura do Azure está desativada e como eu a reativo?](../../cost-management-billing/manage/subscription-disabled.md) para obter mais informações.

<a id="-2134375618"></a> **A conta de armazenamento tem um firewall ou redes virtuais configuradas.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (decimal)** | -2134375618 |
| **Cadeia de caracteres de erro** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Correção necessária** | Sim |

Esse erro ocorre quando o compartilhamento de arquivos do Azure está inacessível devido a um firewall de conta de armazenamento ou porque a conta de armazenamento pertence a uma rede virtual. Verifique se as configurações de firewall e de rede virtual na conta de armazenamento estão definidas adequadamente. Para obter mais informações, confira [Definir configurações de rede virtual e firewall](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Falha na sincronização devido a um problema com o banco de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Correção necessária** | Não |

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

Esse erro pode ocorrer se sua organização estiver usando um proxy de finalização TLS ou se uma entidade mal-intencionada estiver interceptando o tráfego entre o servidor e o serviço de Sincronização de Arquivos do Azure. Se tiver certeza de que isso é esperado (porque sua organização está usando um proxy de finalização TLS), você pula a verificação de certificado com uma substituição de registro.

1. Crie o valor do Registro SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Reinicie o serviço de sincronização no servidor registrado.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ao definir esse valor do Registro, o agente da Sincronização de Arquivos do Azure aceitará qualquer certificado TLS/SSL confiável localmente ao transferir dados entre o servidor e o serviço de nuvem.

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

Esse erro normalmente ocorre porque a hora do servidor está incorreta. Se o servidor estiver em execução em uma máquina virtual, verifique se a hora no host está correta.

<a id="-2134364040"></a>**Falha na sincronização devido à expiração do certificado.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimal)** | -2134364040 |
| **Cadeia de caracteres de erro** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Correção necessária** | Sim |

Esse erro ocorre porque o certificado usado para autenticação expirou.

Para confirmar se o certificado está expirado, faça o seguinte:  
1. Abra o snap-in MMC de Certificados, selecione Conta de Computador e navegue até Certificados (Computador Local)\Pessoal\Certificados.
2. Verifique se o certificado de autenticação do cliente expirou.

Se o certificado de autenticação de cliente tiver expirado, faça o seguinte para resolver o problema:

1. Verifique se a versão do agente de Sincronização de Arquivos do Azure 4.0.1.0 ou posterior está instalada.
2. Execute o seguinte comando do PowerShell no servidor:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Falha na sincronização devido a um certificado de autenticação não encontrado.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimal)** | -2134375896 |
| **Cadeia de caracteres de erro** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Correção necessária** | Sim |

Esse erro ocorre porque o certificado usado para autenticação não foi encontrado.

Para resolver esse problema, execute as seguintes etapas:

1. Verifique se a versão do agente de Sincronização de Arquivos do Azure 4.0.1.0 ou posterior está instalada.
2. Execute o seguinte comando do PowerShell no servidor:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Falha na sincronização devido a uma identidade de autenticação não encontrada.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimal)** | -2134364039 |
| **Cadeia de caracteres de erro** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Correção necessária** | Sim |

Esse erro ocorre porque a exclusão do ponto de extremidade do servidor falhou, e o ponto de extremidade está agora em um estado parcialmente excluído. Para resolver esse problema, repita a exclusão do ponto de extremidade do servidor.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**O volume onde se encontra o ponto de extremidade do servidor é baixo espaço em disco.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Cadeia de caracteres de erro** | JET_errLogDiskFull |
| **Correção necessária** | Sim |

| | |
|-|-|
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
| **Correção necessária** | Não |

Esse erro ocorre porque o ponto de extremidade na nuvem foi criado com conteúdo já existente no compartilhamento de arquivo do Azure. A Sincronização de Arquivos do Azure precisa verificar todo o conteúdo no compartilhamento de arquivo do Azure antes de permitir que o ponto de extremidade do servidor prossiga com a respectiva sincronização inicial.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Falha na sincronização devido a problemas com muitos arquivos individuais.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | -2134375877 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Correção necessária** | Sim |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Correção necessária** | Sim |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Cadeia de caracteres de erro** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Correção necessária** | Sim |

As sessões de sincronização falham com um desses erros quando há muitos arquivos que estão falhando na sincronização com erros por item. Execute as etapas documentadas na [como fazer ver se há arquivos específicos ou pastas que não estão sincronizando?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) seção para resolver os erros por item. Para ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED de erro de sincronização, abra um caso de suporte.

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
| **Correção necessária** | Não |

Este erro ocorre porque o serviço de Sincronização de Arquivos do Azure está indisponível. Esse erro será resolvido automaticamente quando o serviço de Sincronização de Arquivos do Azure estiver disponível novamente.

<a id="-2146233088"></a>**Falha na sincronização devido a uma exceção.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimal)** | -2146233088 |
| **Cadeia de caracteres de erro** | COR_E_EXCEPTION |
| **Correção necessária** | Não |

Esse erro ocorre porque a sincronização falhou devido a uma exceção. Se o erro persistir por várias horas, crie uma solicitação de suporte.

<a id="-2134364045"></a>**A sincronização falhou porque a conta de armazenamento fez failover para outra região.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimal)** | -2134364045 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Correção necessária** | Sim |

Esse erro ocorre porque a conta de armazenamento fez failover para outra região. A Sincronização de Arquivos do Azure não oferece suporte ao recurso de failover da conta de armazenamento. Não deve ser realizado o failover das contas de armazenamento que contêm compartilhamentos de arquivos do Azure que estejam sendo usadas como pontos de extremidade de nuvem na Sincronização de Arquivos do Azure. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas. Para resolver esse problema, mova a conta de armazenamento para a região primária.

<a id="-2134375922"></a>**Falha na sincronização devido a um problema temporário com o banco de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Correção necessária** | Não |

Este erro ocorre devido a um problema interno com o banco de dados de sincronização. Esse erro será resolvido automaticamente quando a sincronização ocorrer novamente. Se esse erro persistir por um período prolongado, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

<a id="-2134364024"></a>**A sincronização falhou devido a uma alteração no locatário do Azure Active Directory**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimal)** | -2134364024 | 
| **Cadeia de caracteres de erro** | ECS_E_INVALID_AAD_TENANT |
| **Correção necessária** | Sim |

Verifique se você tem a versão mais recente do agente da Sincronização de Arquivos do Azure. Com o agente V10 e as versões posteriores, a Sincronização de Arquivos do Azure dá suporte à migração da assinatura para um locatário diferente do Azure Active Directory.
 
Depois de obter a versão mais recente do agente, você precisa conceder acesso à conta de armazenamento ao aplicativo Microsoft.StorageSync (confira [Garantir que Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Falha na sincronização devido a uma exceção de firewall e rede virtual não configurada**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimal)** | -2134364010 | 
| **Cadeia de caracteres de erro** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Correção necessária** | Sim |

Esse erro ocorre se as configurações de firewall e rede virtual estão habilitadas na conta de armazenamento e a exceção "Permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" não está marcada. Para resolver esse problema, siga as etapas documentadas na seção [Definir configurações de rede virtual e de firewall](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implantação.

<a id="-2147024891"></a>**Falha na sincronização porque as permissões na pasta Informações do Volume do Sistema estão incorretas.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimal)** | -2147024891 |
| **Cadeia de caracteres de erro** | ERROR_ACCESS_DENIED |
| **Correção necessária** | Sim |

Esse erro pode ocorrer se a conta NT AUTHORITY\SYSTEM não tiver permissões para a pasta Informações de Volume do Sistema no volume em que o ponto de extremidade do servidor está localizado. Se houver falha ao sincronizar os arquivos individuais com ERROR_ACCESS_DENIED, execute as etapas documentadas na seção [Solução de problemas por erros de sincronização de arquivo/diretório](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors).

Para resolver esse problema, execute as seguintes etapas:

1. Baixe a ferramenta [PsExec](/sysinternals/downloads/psexec).
2. Execute o comando a seguir em um prompt de comando elevado para iniciar um prompt de comando usando a conta do sistema: **PsExec.exe -i -s -d cmd** 
3. No prompt de comando em execução na conta do sistema, execute o seguinte comando para confirmar se a conta NT AUTHORITY\SYSTEM não tem acesso à pasta Informações de Volume do Sistema: **cacls "letra da unidade:\informações de volume do sistema" /T /C**
4. Se a conta NT AUTHORITY\SYSTEM não tiver acesso à pasta Informações de Volume do Sistema, execute o seguinte comando: **cacls "letra da unidade:\informações de volume do sistema" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Se a etapa 4 falhar com o acesso negado, execute o seguinte comando para se tornar proprietário da pasta Informações de Volume do Sistema e repita a etapa 4: **takeown /A /R /F "letra da unidade:\Informações de volume do sistema"**

<a id="-2134375810"></a>**A sincronização falhou porque o compartilhamento de arquivos do Azure foi excluído e recriado.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimal)** | -2134375810 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Correção necessária** | Sim |

Esse erro ocorre porque Sincronização de Arquivos do Azure não dá suporte à exclusão e recriação de um compartilhamento de arquivos do Azure no mesmo grupo de sincronização. 

Para resolver esse problema, exclua e recrie o grupo de sincronização executando as seguintes etapas:

1. Excluir todos os pontos de extremidade do servidor no grupo de sincronização.
2. Excluir ponto de extremidade na nuvem. 
3. Excluir o grupo de sincronização.
4. Caso a camada de nuvem tenha sido habilitada em um ponto de extremidade de servidor, exclua os arquivos em camada órfãos no servidor executando as etapas documentadas na seção [Arquivos em camada não podem ser acessados no servidor depois de excluir um ponto de extremidade de servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
5. Recriar o grupo de sincronização.

<a id="-2145844941"></a>**Falha na sincronização porque a solicitação HTTP foi redirecionada**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (decimal)** | -2145844941 |
| **Cadeia de caracteres de erro** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Correção necessária** | Sim |

Esse erro ocorre porque Sincronização de Arquivos do Azure não dá suporte ao redirecionamento de HTTP (código de status 3xx). Para resolver esse problema, desative o redirecionamento HTTP no servidor proxy ou no dispositivo de rede.

<a id="-2134364027"></a>**Ocorreu um tempo limite durante a transferência de dados offline, mas ela ainda está em andamento.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (decimal)** | -2134364027 |
| **Cadeia de caracteres de erro** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Correção necessária** | Não |

Esse erro ocorre quando uma operação de ingestão de dados excede o tempo limite. Esse erro pode ser ignorado se a sincronização estiver progredindo (AppliedItemCount é maior que 0). Confira [Como fazer para monitorar o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**Falha na sincronização porque o caminho do ponto de extremidade do servidor não foi encontrado no servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (decimal)** | -2134375814 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Correção necessária** | Sim |

Esse erro ocorre se o diretório usado como o caminho do ponto de extremidade do servidor foi renomeado ou excluído. Se o diretório foi renomeado, renomeie o diretório de volta para o nome original e reinicie o serviço do agente de sincronização de armazenamento (FileSyncSvc).

Se o diretório foi excluído, execute as seguintes etapas para remover o ponto de extremidade do servidor existente e criar um novo ponto de extremidade do servidor usando um novo caminho:

1. Remova o ponto de extremidade do servidor no grupo de sincronização seguindo as etapas documentadas em [remover um ponto de extremidade do servidor](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).
2. Crie um novo ponto de extremidade do servidor no grupo de sincronização seguindo as etapas documentadas em [Adicionar um ponto de extremidade do servidor](./storage-sync-files-server-endpoint.md#add-a-server-endpoint).

### <a name="common-troubleshooting-steps"></a>Etapas de solução de problemas comuns
<a id="troubleshoot-storage-account"></a>**Verifique se que a conta de armazenamento existe.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navegue até o grupo de sincronização no Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Observe o nome do compartilhamento de arquivos do Azure no painel aberto.
4. Selecione a conta de armazenamento vinculada. Se esse link falhar, a conta de armazenamento referenciada foi removida.
    ![Uma captura de tela mostrando o painel de detalhes do ponto de extremidade da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

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

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Verifique se que existe o compartilhamento de arquivos do Azure.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Clique em **visão geral** sobre o sumário à esquerda para retornar à página de conta de armazenamento principal.
2. Selecione **Arquivos** para visualizar a lista de compartilhamentos de arquivos.
3. Verifique se o compartilhamento de arquivos referenciado pelo ponto de extremidade da nuvem aparece na lista de compartilhamentos de arquivos (você deve ter notado isso na etapa 1 acima).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Certifique-se de que a sincronização de arquivos do Azure tem acesso à conta de armazenamento.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Clique em **Controle de acesso (IAM)** no sumário à esquerda.
1. Clique na guia **Atribuições de função** para a lista de usuários e aplicativos (*entidades de serviço*) que têm acesso à sua conta de armazenamento.
1. Verifique se **Microsoft.StorageSync** ou **Serviço Híbrido Sincronização de Arquivos** (nome do aplicativo antigo) aparece na lista com a função de **Leitor e Acesso a Dados**. 

    ![Uma captura de tela da entidade de serviço do Serviço Híbrido de Sincronização de Arquivos na guia de controle de acesso da conta de armazenamento](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se o **Microsoft.StorageSync** ou o **Serviço Híbrido de Sincronização de Arquivos do Azure** não aparecer na lista, execute as seguintes etapas:

    - Clique em **Adicionar**.
    - No campo **Função**, selecione **Leitor e Acesso a Dados**.
    - No campo **Selecionar**, digite **Microsoft.StorageSync**, selecione a função e clique em **Salvar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Como evito que os usuários criem arquivos contendo caracteres não suportados no servidor?
Você pode usar [Telas de Arquivos do Gerenciador de Recursos de Servidor de Arquivos (FSRM)](/windows-server/storage/fsrm/file-screening-management) para impedir que arquivos com caracteres não suportados em seus nomes sejam criados no servidor. Pode ser necessário fazer isso usando o PowerShell, pois a maioria dos caracteres não suportados não pode ser impressa e, portanto, é necessário converter suas representações hexadecimais como caracteres primeiro.

Primeiro, crie um grupo de arquivos FSRM usando o [cmdlet New-FsrmFileGroup](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Este exemplo define o grupo para conter apenas dois dos caracteres não suportados, mas você pode incluir quantos caracteres forem necessários no seu grupo de arquivos.

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
- Pode haver falha ao recuperar os arquivos, o que significa que o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) falha ao baixar dados quando um usuário tenta acessar um arquivo que foi colocado em camadas.

Há duas classes principais de falhas que podem ocorrer por meio de um desses caminhos de falha:

- Falhas no armazenamento em nuvem
    - *Problemas transitórios de disponibilidade do serviço de armazenamento*. Para obter mais informações, consulte o [Contrato de Nível de Serviço (SLA) para o Armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Compartilhamento de arquivos do Azure inacessível*. Essa falha geralmente ocorre quando você exclui o compartilhamento de arquivos do Azure quando ainda é um ponto de extremidade de nuvem em um grupo de sincronização.
    - *Conta de armazenamento inacessível*. Essa falha normalmente acontece quando você exclui a conta de armazenamento enquanto ela ainda tem um compartilhamento de arquivo do Azure que é um ponto de extremidade na nuvem em um grupo de sincronização. 
- Falhas de servidor 
  - *O filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) não está carregado*. Para responder às solicitações de definição de camadas/recuperação, o filtro do sistema de arquivos da Sincronização de arquivos do Azure deve ser carregado. O filtro pode não ser carregado por diversos motivos, mas o motivo mais comum é quando um administrador o descarrega manualmente. O filtro do sistema de arquivos da Sincronização de arquivos do Azure deve ser carregado sempre para que a Sincronização de arquivos do Azure funcione corretamente.
  - *Ponto de nova análise ausente, corrompido ou desfeito por algum outro motivo*. Um ponto de nova análise é uma estrutura de dados especial em um arquivo que consiste em duas partes:
    1. Uma marca de nova análise, que indica para o sistema operacional que o filtro do sistema de arquivos da Sincronização de arquivos do Azure (StorageSync.sys) pode precisar executar alguma ação na E/S do arquivo. 
    2. Dados de nova análise, que indica para o filtro do sistema de arquivos qual é o URI do arquivo no ponto de extremidade de nuvem associado (o compartilhamento de arquivos do Azure). 
        
       A maneira mais comum de corromper um ponto de nova análise é quando um administrador tenta modificar a marca ou seus dados. 
  - *Problemas de conectividade de rede*. Para a definir camadas ou recuperar um arquivo, o servidor deve ter conectividade com a Internet.

As seções a seguir indicam como solucionar problemas de definição de camadas de nuvem e determinar se o problema é do armazenamento em nuvem ou do servidor.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Como monitorar a atividade em camadas em um servidor  
Para monitorar a atividade em camadas em um servidor, use as identificações de evento 9003, 9016 e 9029 no log de eventos da Telemetria (localizado no Visualizador de Eventos, em Aplicativos e Serviços\Microsoft\FileSync\Agent).

- A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo, Contagem total de erros, ErrorCode, etc. Observe que um evento é registrado por código de erro.
- A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo, o percentual de espaço livre é, Número de arquivos fantasmados na sessão, Número de arquivos que falharam no fantasma etc.
- A ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo, Número de arquivos tentados na sessão, Número de arquivos em camadas na sessão, Número de arquivos já em camadas, etc.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Como monitorar a atividade de recuperação em um servidor
Para monitorar a atividade de recall em um servidor, use as IDs do evento 9005, 9006, 9009 e 9059 no log de eventos da Telemetria (localizado no Visualizador de Eventos, em Aplicativos e Serviços\Microsoft\FileSync\Agent).

- A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo, Total de arquivos exclusivos acessados, Total de arquivos exclusivos com acesso com falha, etc.
- ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo, Total de solicitações com falha, ErrorCode etc. Observe que um evento é registrado por código de erro.
- A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo, ShareId, Nome do Aplicativo e TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Como solucionar problemas de arquivos que falham ao serem dispostos em camadas
Se os arquivos não camada para arquivos do Azure:

1. No Visualizador de Eventos, revise os logs de eventos de telemetria, operacionais e de diagnóstico, localizados em Aplicativos e Serviços\Microsoft\FileSync\Agent. 
   1. Verifique se que os arquivos existem no compartilhamento de arquivos do Azure.

      > [!NOTE]
      > Um arquivo deve ser sincronizado com um Compartilhamento de Arquivo do Azure antes de poder ser disposto em camadas.

   2. Verifique se o servidor tem conectividade com a Internet. 
   3. Verifique se que os drivers de filtro de sincronização de arquivos do Azure (storagesync. sys e storagesyncguard. sys) estão em execução:
       - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um ID de Evento 9003 é registrada uma vez por hora no log de eventos da Telemetria se um arquivo falhar na camada (um evento é registrado por código de erro). Verifique a seção [Erros e correção de camadas](#tiering-errors-and-remediation) para ver se as etapas de correção estão listadas para o código de erro.

### <a name="tiering-errors-and-remediation"></a>Erros de disposição em camadas e correção

| HRESULT | HRESULT (decimal) | Cadeia de caracteres de erro | Problema | Correção |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Falha na camada do arquivo porque o carregamento inicial está em andamento. | Nenhuma ação é necessária. O arquivo será colocado em camadas depois que o carregamento inicial for concluído. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Falha ao colocar o arquivo em camada porque ele está em uso. | Nenhuma ação é necessária. O arquivo será colocado em camada quando não estiver mais em uso. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Falha ao colocar o arquivo em camada porque ele foi excluído por sincronização. | Nenhuma ação é necessária. Os arquivos na lista de exclusões de sincronização não podem ser colocados em camadas. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Falha ao colocar o arquivo em camada porque ele não foi encontrado no servidor. | Nenhuma ação é necessária. Se o erro persistir, verifique se o arquivo existe no servidor. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Falha ao colocar o arquivo em camada porque ele foi excluído no compartilhamento de arquivo do Azure. | Nenhuma ação é necessária. O arquivo deve ser excluído no servidor quando a próxima sessão de sincronização de download for executada. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Falha ao colocar o arquivo em camada devido a um problema de rede. | Nenhuma ação é necessária. Se o erro persistir, verifique a conectividade de rede do compartilhamento de arquivo do Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Falha ao colocar o arquivo em camada devido a um problema de rede. | Nenhuma ação é necessária. Se o erro persistir, verifique a conectividade de rede do compartilhamento de arquivo do Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Falha ao colocar o arquivo em camada devido ao erro de acesso negado. Esse erro pode ocorrer se o arquivo está localizado em uma pasta de replicação somente leitura do DFS-R. | A Sincronização de Arquivos do Azure não oferece suporte a pontos de extremidade de servidor em pastas de replicação somente leitura do DFS-R. Consulte o [guia de planejamento](./storage-sync-files-planning.md#distributed-file-system-dfs) para obter mais informações. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Falha ao colocar o arquivo em camada devido a um problema de rede. | Nenhuma ação é necessária. Se o erro persistir, verifique a conectividade de rede do compartilhamento de arquivo do Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Falha ao colocar o arquivo em camada porque o tamanho dele é menor do que o tamanho compatível. | Se a versão do agente for menor que 9.0, o tamanho de arquivo mínimo com suporte é de 64 KB. Se a versão do agente for 9.0 e mais recente, o tamanho de arquivo mínimo com suporte é baseado no tamanho do cluster do sistema de arquivos (tamanho de cluster de sistema de arquivos duplo). Por exemplo, se o tamanho do cluster do sistema de arquivos for 4 KB, o tamanho mínimo do arquivo será 8 KB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Falha ao colocar o arquivo em camada devido a um problema de armazenamento do Azure. | Se o erro persistir, abra uma solicitação de suporte. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Falha ao colocar o arquivo em camada porque ele foi recuperado ao mesmo tempo. | Nenhuma ação é necessária. O arquivo será colocado em camadas quando o recall for concluído e o arquivo não estiver mais em uso. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Falha ao colocar o arquivo em camada porque ele não foi sincronizado com o compartilhamento de arquivo do Azure. | Nenhuma ação é necessária. O arquivo terá uma camada depois de sincronizado com o compartilhamento de arquivo do Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Falha ao colocar o arquivo em camada porque o driver do filtro de camada de nuvem (storagesync.sys) não está em execução. | Para solucionar esse problema, abra um prompt de comandos com privilégios elevados e execute o seguinte comando: `fltmc load storagesync`<br>Se o driver de filtro storagesync não for carregado ao executar o comando fltmc, desinstale o agente de Sincronização de Arquivos do Azure, reinicie o servidor e reinstale o agente de Sincronização de Arquivos do Azure. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Falha ao colocar o arquivo em camada devido a espaço em disco insuficiente no volume em que se encontra o ponto de extremidade do servidor. | Para resolver esse problema, libere pelo menos 100 MB de espaço em disco no volume em que se encontra o ponto de extremidade do servidor. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Falha ao colocar o arquivo em camada porque ele não foi sincronizado com o compartilhamento de arquivo do Azure. | Nenhuma ação é necessária. O arquivo terá uma camada depois de sincronizado com o compartilhamento de arquivo do Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Falha ao colocar o arquivo em camada porque ele é um ponto de nova análise não compatível. | Se o arquivo for um ponto de nova análise de eliminação de duplicação de dados, siga as etapas no [guia de planejamento](./storage-sync-files-planning.md#data-deduplication) para habilitar o suporte à eliminação de duplicação de dados. Arquivos com pontos de nova análise que não sejam a eliminação de duplicação de dados não têm suporte e não serão em camadas.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Falha ao colocar o arquivo em camada porque ele foi modificado. | Nenhuma ação é necessária. O arquivo terá uma camada assim que o arquivo modificado tiver sido sincronizado com o compartilhamento de arquivo do Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Falha ao colocar o arquivo em camada porque ele não foi sincronizado com o compartilhamento de arquivo do Azure. | Nenhuma ação é necessária. O arquivo terá uma camada depois de sincronizado com o compartilhamento de arquivo do Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Falha ao colocar o arquivo em camada devido a um problema de rede. | Nenhuma ação é necessária. Se o erro persistir, verifique a conectividade de rede do compartilhamento de arquivo do Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Falha ao colocar o arquivo em camada porque ele foi modificado. | Nenhuma ação é necessária. O arquivo terá uma camada assim que o arquivo modificado tiver sido sincronizado com o compartilhamento de arquivo do Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Falha ao colocar o arquivo em camada devido a recursos insuficientes do sistema. | Se o erro persistir, investigue qual driver de modo kernel ou aplicativo está esgotando os recursos do sistema. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Falha na camada do arquivo devido a um erro de e/s durante a gravação no banco de dados de camadas da nuvem. | Se o erro persistir, execute Chkdsk no volume e verifique o hardware de armazenamento. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Falha na camada do arquivo porque o banco de dados de camadas da nuvem não está em execução. | Para resolver esse problema, reinicie o serviço ou servidor FileSyncSvc. Se o erro persistir, execute Chkdsk no volume e verifique o hardware de armazenamento. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Como solucionar problemas de arquivos que falham quando seu recall é realizado  
Se os arquivos não ser recuperados:
1. No Visualizador de Eventos, revise os logs de eventos de telemetria, operacionais e de diagnóstico, localizados em Aplicativos e Serviços\Microsoft\FileSync\Agent.
    1. Verifique se que os arquivos existem no compartilhamento de arquivos do Azure.
    2. Verifique se o servidor tem conectividade com a Internet. 
    3. Abra o snap-in MMC de serviços e verifique se que o serviço de agente de sincronização de armazenamento (FileSyncSvc) está em execução.
    4. Verifique se que os drivers de filtro de sincronização de arquivos do Azure (storagesync. sys e storagesyncguard. sys) estão em execução:
        - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Uma ID de Evento 9006 é registrada uma vez por hora no log de eventos de Telemetria se um arquivo não for recuperado (um evento é registrado por código de erro). Verifique a seção [Erros e correção de recuperação](#recall-errors-and-remediation) para ver se as etapas de correção estão listadas para o código de erro.

### <a name="recall-errors-and-remediation"></a>Erros e correção de recuperação

| HRESULT | HRESULT (decimal) | Cadeia de caracteres de erro | Problema | Correção |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Falha ao recuperar o arquivo devido a um tempo limite de E/S. Esse problema pode ocorrer por vários motivos: restrições de recursos de servidor, conectividade de rede ruim ou um problema de armazenamento do Azure (por exemplo, limitação). | Nenhuma ação é necessária. Se o erro persistir por várias horas, abra um caso de suporte. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Falha ao recuperar o arquivo devido a um problema de rede.  | Se o erro persistir, verifique a conectividade de rede do compartilhamento de arquivo do Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Falha ao recuperar o arquivo porque o ponto de extremidade do servidor foi excluído. | Para resolver esse problema, confira [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Falha ao recuperar o arquivo devido a um erro de acesso negado. Esse problema pode ocorrer se as configurações de firewall e rede virtual na conta de armazenamento estiverem ativadas e o servidor não tiver acesso à conta de armazenamento. | Para solucionar esse problema, adicione a rede virtual ou o endereço IP do servidor seguindo as etapas documentadas na seção [Definir configurações de rede virtual e de firewall](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implantação. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Falha ao recuperar o arquivo porque ele não está acessível no compartilhamento de arquivo do Azure. | Para resolver esse problema, verifique se o arquivo existe no compartilhamento de arquivo do Azure. Se o arquivo existir no compartilhamento de arquivo do Azure, atualize para a [versão mais recente do agente](./storage-files-release-notes.md#supported-versions) da Sincronização de Arquivos do Azure. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Falha ao recuperar o arquivo devido a uma falha de autorização na conta de armazenamento. | Para solucionar esse problema, verifique se a [Sincronização de Arquivos do Azure tem acesso à conta de armazenamento](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Falha ao recuperar o arquivo porque o compartilhamento de arquivo do Azure não está acessível. | Verifique se o compartilhamento de arquivos existe e está acessível. Se o compartilhamento de arquivos foi excluído e recriado, execute as etapas documentadas na seção [A sincronização falhou porque o compartilhamento de arquivos do Azure foi excluído e recriado](?tabs=portal1%252cazure-portal#-2134375810) para excluir e recriar o grupo de sincronização. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Falha ao recuperar o arquivo devido a recursos insuficientes do sistema. | Se o erro persistir, investigue qual driver de modo kernel ou aplicativo está esgotando os recursos do sistema. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Falha ao recuperar o arquivo devido a memória insuficiente. | Se o erro persistir, investigue qual aplicativo ou driver no modo kernel está causando a condição de pouca memória. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Falha ao recuperar o arquivo devido a espaço em disco insuficiente. | Para resolver esse problema, libere espaço no volume movendo os arquivos para um volume diferente, aumente o tamanho do volume ou force os arquivos para a camada usando o cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor
Os arquivos em camadas em um servidor ficarão inacessíveis se os arquivos não forem recuperados antes da exclusão de um ponto de extremidade do servidor.

Erros registrados se os arquivos em camadas não estão acessíveis
- Ao sincronizar um arquivo, o código de erro -2147942467 (0x80070043 – ERROR_BAD_NET_NAME) é registrado no log de eventos de ItemResults
- Ao recuperar um arquivo, o código de erro -2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) é registrado no log do evento RecallResults

A restauração do acesso aos arquivos em camadas será possível se as seguintes condições forem atendidas:
- O ponto de extremidade do servidor foi excluído nos últimos 30 dias
- O ponto de extremidade de nuvem não foi excluído 
- O compartilhamento de arquivo não foi excluído
- O grupo de sincronização não foi excluído

Se as condições acima forem atendidas, você poderá restaurar o acesso aos arquivos no servidor ao recriar o ponto de extremidade do servidor, no mesmo caminho no servidor dentro do mesmo grupo de sincronização, dentro de 30 dias. 

Se as condições acima não forem atendidas, não será possível restaurar o acesso, pois esses arquivos em camadas no servidor estarão órfãos. Siga as instruções abaixo para remover os arquivos órfãos em camadas.

**Observações**
- Quando arquivos em camadas não estiverem acessíveis no servidor, o arquivo completo ainda deverá estar acessível se você acessar diretamente o compartilhamento de arquivo do Azure.
- Para evitar arquivos órfãos em camadas no futuro, siga as etapas documentadas em [Remover um ponto de extremidade do servidor](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) ao excluir um ponto de extremidade do servidor.

<a id="get-orphaned"></a>**Como obter a lista de arquivos órfãos em camadas** 

1. Verifique se a versão do agente de Sincronização de Arquivos do Azure v5.1 ou posterior está instalada.
2. Execute os seguintes comandos do PowerShell para listar arquivos órfãos em camadas:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Salve o arquivo de saída OrphanTieredFiles.txt em caso de arquivos que precisam ser restaurados do backup após serem excluídos.

<a id="remove-orphaned"></a>**Como remover arquivos órfãos em camadas** 

*Opção 1: excluir os arquivos órfãos em camadas*

Essa opção exclui os arquivos órfãos em camadas no Windows Server, mas se o ponto de extremidade do servidor existir devido à recreação após 30 dias ou se estiver conectado a um grupo de sincronização diferente, ele precisará ser removido. Se os arquivos forem atualizados no Windows Server ou no compartilhamento de arquivo do Azure antes de o ponto de extremidade do servidor ser recriado, conflitos ocorrerão.

1. Verifique se a versão do agente de Sincronização de Arquivos do Azure v5.1 ou posterior está instalada.
2. Faça backup do compartilhamento de arquivo do Azure e do local do ponto de extremidade do servidor.
3. Remova o ponto de extremidade do servidor no grupo de sincronização (se existir) seguindo as etapas documentadas em [Remover um ponto de extremidade do servidor](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Se o ponto de extremidade do servidor não for removido antes de você usar o cmdlet Remove-StorageSyncOrphanedTieredFiles, a exclusão do arquivo órfão em camadas no servidor resultará na exclusão do arquivo completo no compartilhamento de arquivo do Azure. 

4. Execute os seguintes comandos do PowerShell para listar arquivos órfãos em camadas:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Salve o arquivo de saída OrphanTieredFiles.txt em caso de arquivos que precisam ser restaurados do backup após serem excluídos.
6. Execute os seguintes comandos do PowerShell para excluir arquivos órfãos em camadas:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Observações** 
- Os arquivos em camadas modificados no servidor que não estão sincronizados com o compartilhamento de arquivo do Azure serão excluídos.
- Os arquivos em camadas que são acessíveis (não órfãos) não serão excluídos.
- Os arquivos que não estiverem em camadas permanecerão no servidor.

7. Opcional: recrie o ponto de extremidade do servidor se ele tiver sido excluído na etapa 3.

*Opção 2: montar o compartilhamento de arquivo do Azure e copiar localmente os arquivos que estão órfãos no servidor*

Essa opção não requer a remoção do ponto de extremidade do servidor, mas requer espaço em disco suficiente para copiar os arquivos completos localmente.

1. [Monte](./storage-how-to-use-files-windows.md) o compartilhamento de arquivo do Azure no Windows Server que tem arquivos órfãos em camadas.
2. Execute os seguintes comandos do PowerShell para listar arquivos órfãos em camadas:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Use o arquivo de saída OrphanTieredFiles.txt para identificar arquivos órfãos em camadas no servidor.
4. Substitua os arquivos órfãos em camadas copiando o arquivo completo do compartilhamento de arquivo do Azure para o Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Como solucionar problemas em arquivos que foram recuperados inesperadamente em um servidor  
Antivírus, backup e outros aplicativos que leem grandes quantidades de arquivos causarão recalls indesejados a menos que eles respeitem o atributo offline e ignorem a leitura do conteúdo desses arquivos. Ignorar arquivos off-line de produtos que dão suporte a essa opção ajuda a evitar recuperações não intencionais durante operações, como verificações antivírus ou trabalhos de backup.

Consulte seu fornecedor de software sobre como configurar sua solução para ignorar a leitura de arquivos offline.

Recuperações não intencionais também podem ocorrer em outros cenários, como quando você estiver pesquisando arquivos no Explorador de arquivos. Abrir uma pasta com arquivos de nuvem em camadas no Explorador de arquivos no servidor pode resultar em recuperações não intencionais. Isso é ainda mais provável se uma solução antivírus estiver habilitada no servidor.

> [!NOTE]
>Use a ID de evento 9059 no log de eventos de telemetria para determinar quais aplicativos estão causando os recalls. Este evento oferece distribuição de recall de aplicativo para um ponto de extremidade do servidor e é registrado uma vez por hora.

### <a name="tls-12-required-for-azure-file-sync"></a>O TLS 1.2 é necessário para a Sincronização de Arquivos do Azure

Você pode exibir as configurações de TLS no servidor examinando as [configurações de Registro](/windows-server/security/tls/tls-registry-settings). 

Se você estiver usando um proxy, consulte a documentação do proxy e verifique se ele está configurado para usar o TLS 1.2.

## <a name="general-troubleshooting"></a>Solução de problemas gerais
Se você encontrar problemas com a Sincronização de arquivos do Azure em um servidor, comece executando o seguinte:
1. No Visualizador de Eventos, revise os logs de eventos de telemetria, operacionais e de diagnóstico.
    - Os problemas de sincronização, classificação em camadas e recuperação são registrados nos logs de eventos de telemetria, diagnóstico e operacional em Aplicativos e Serviços\Microsoft\FileSync\Agent.
    - Problemas relacionados ao gerenciamento de um servidor (por exemplo, definições de configuração) são registrados nos logs de eventos operacionais e de diagnóstico em aplicativos e Services\Microsoft\FileSync\Management.
2. Verifique se o serviço de Sincronização de Arquivo do Azure está em execução no servidor:
    - Abra o snap-in do MMC de Serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
3. Verifique se que os drivers de filtro de sincronização de arquivos do Azure (storagesync. sys e storagesyncguard. sys) estão em execução:
    - Em um prompt de comandos com privilégios elevados, digite `fltmc`. Verifique se os drivers de filtro de sistema de arquivos StorageSync.sys e StorageSyncGuard.sys estão listados.

Se o problema não for resolvido, execute a ferramenta AFSDiag e envie o arquivo zip de saída gerado por ela para o engenheiro de suporte atribuído ao seu caso para diagnóstico adicional.

Para executar o AFSDiag, execute as etapas abaixo.

Para a versão do agente v11 e posterior:
1. Abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos (pressione Enter depois de cada comando):

    > [!NOTE]
    >O AFSDiag criará o diretório de saída e uma pasta temporária dentro dele antes de coletar logs e excluirá a pasta temporária após a execução. Especifique um local de saída que não contenha dados.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reproduza o problema. Quando tiver terminado, clique em **D**.
3. Um arquivo. zip que contém logs e arquivos de rastreamento é salvo no diretório de saída que você especificou. 

Para a versão do agente V10 e anterior:
1. Crie um diretório que será usado para salvar a saída da AFSDiag (por exemplo, C:\Output).
    > [!NOTE]
    >O AFSDiag excluirá todo o conteúdo do diretório de saída antes de coletar logs. Especifique um local de saída que não contenha dados.
2. Abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos (pressione Enter depois de cada comando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o nível de rastreamento do modo de kernel da Sincronização de arquivos do Azure, insira **1** (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
4. Para o nível de rastreamento do modo de usuário da Sincronização de arquivos do Azure, insira **1** (a menos que especificado para criar rastreamentos mais detalhados) e pressione Enter.
5. Reproduza o problema. Quando tiver terminado, clique em **D**.
6. Um arquivo. zip que contém logs e arquivos de rastreamento é salvo no diretório de saída que você especificou.


## <a name="see-also"></a>Confira também
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
- [Solucionar problemas de Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar problemas de Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
