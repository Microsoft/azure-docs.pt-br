---
title: Gerenciar servidores registrados com a Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como registrar e cancelar o registro de um Windows Server com um Serviço de Sincronização de Armazenamento de Sincronização de Arquivo do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013914"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Gerenciar servidores registrados com a Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

O artigo a seguir ilustra como registrar e gerenciar um servidor com um Serviço de Sincronização de Armazenamento. Consulte [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar a Sincronização de Arquivos do Azure de ponta a ponta.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrar/cancelar o registro de um servidor no Serviço de Sincronização de Armazenamento
Registrar um servidor na Sincronização de arquivos do Azure estabelece uma relação de confiança entre o Windows Server e o Azure. Essa relação pode ser usada para criar *pontos de extremidade do servidor* no servidor, que representam as pastas específicas que devem ser sincronizadas com um compartilhamento de arquivos do Azure (também conhecidas como *pontos de extremidade de nuvem*). 

### <a name="prerequisites"></a>Pré-requisitos
Para registrar um servidor em um Serviço de Sincronização de Armazenamento, primeiro você deve preparar o servidor com os pré-requisitos necessários:

* O servidor deve estar executando uma versão do Windows Server compatível. Para obter mais informações, veja [Requisitos de sistema da Sincronização de Arquivos do Azure e interoperabilidade](storage-sync-files-planning.md#windows-file-server-considerations).
* Certifique-se de que um Serviço de Sincronização de Armazenamento foi implantado. Para obter mais informações sobre como implantar um Serviço de Sincronização de Armazenamento, consulte [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).
* Certifique-se de que o servidor está conectado à Internet e que o Azure está acessível.
* Desabilite a Configuração de Segurança Reforçada do IE para administradores com a interface do usuário do Gerenciador do Servidor.
    
    ![Interface do usuário do Gerenciador do Servidor com a Configuração de Segurança Reforçada do IE realçada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Certifique-se de que o módulo Azure PowerShell está instalado no servidor. Se o servidor for um membro de um Cluster de Failover, todos os nós no cluster precisarão do módulo Az. É possível encontrar mais detalhes sobre como instalar o módulo Az em [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > É recomendável usar a versão mais recente do módulo Az PowerShell para registrar/cancelar o registro de um servidor. Se o pacote do Az tiver sido instalado anteriormente nesse servidor (e a versão do PowerShell nesse servidor for 5.* ou maior), você poderá usar o cmdlet `Update-Module` para atualizar esse pacote. 
* Se você utilizar um servidor de proxy de rede em seu ambiente, configure as definições de proxy no seu servidor para que o agente de sincronização use.
    1. Determine o número da porta e o endereço IP do proxy
    2. Edite esses dois arquivos:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adicione as linhas na Figura 1 (abaixo desta seção) em /System.ServiceModel nos dois arquivos acima alterando 127.0.0.1:8888 para o endereço IP correto (substituir 127.0.0.1) e o número de porta correto (substituir 8888):
    4. Defina as configurações de proxy do WinHTTP por meio da linha de comando:
        * Mostre o proxy: netsh winhttp mostra o proxy
        * Defina o proxy:    netsh winhttp define o proxy 127.0.0.1:8888
        * Reinicie o proxy: netsh winhttp reiniciar o proxy
        * se isso for definido após o agente ser instalado, reinicie o nosso agente de sincronização: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrar um servidor com o Serviço de Sincronização de Armazenamento
Para que um servidor possa ser usado como um *ponto de extremidade do servidor* em um *grupo de sincronização* da Sincronização de arquivos do Azure, ele deve ser registrado em um *Serviço de Sincronização de Armazenamento*. Um servidor apenas pode ser registrado em um único Serviço de Sincronização de Armazenamento de cada vez.

#### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
1. [Baixe o agente de Sincronização de Arquivo do Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador do agente de Sincronização de Arquivo do Azure.
    
    ![O primeiro painel do instalador do agente de Sincronização de Arquivo do Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de habilitar as atualizações para o agente de Sincronização de Arquivo do Azure usando o Microsoft Update. É importante porque correções de segurança críticas e aprimoramentos de recursos para o pacote do servidor são enviadas por meio do Microsoft Update.

    ![Certifique-se de que o Microsoft Update está habilitado no painel do Microsoft Update do instalador do agente de Sincronização de Arquivo do Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não tiver sido registrado anteriormente, a interface do usuário de registro do servidor será aberta imediatamente após a conclusão da instalação.

> [!Important]  
> Se o servidor for um membro de um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisará ser instalado em cada nó no cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrar o servidor usando a interface do usuário de registro do servidor
1. Se a interface do usuário de registro do servidor não for iniciada imediatamente após a conclusão da instalação do agente de Sincronização de Arquivo do Azure, ela poderá ser iniciada manualmente executando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Clique em *Entrar* para acessar sua assinatura do Azure. 

    ![Abrindo a caixa de diálogo da interface do usuário de registro de servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Selecione a assinatura, o grupo de recursos e o Serviço de Sincronização de Armazenamento na caixa de diálogo.

    ![Informações do Serviço de Sincronização de Armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Na versão prévia, é necessário entrar mais uma vez para concluir o processo. 

    ![Caixa de diálogo Entrar](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for um membro de um Cluster de Failover, cada servidor precisará executar o Registro do Servidor. Quando você exibe os servidores registrados no Portal do Azure, a Sincronização de arquivos do Azure reconhece automaticamente cada nó como um membro do mesmo Cluster de Failover e agrupa-os corretamente.

#### <a name="register-the-server-with-powershell"></a>Registrar o servidor com o PowerShell
Você também pode executar o registro do servidor por meio do PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Cancelar o registro de um servidor com o Serviço de Sincronização de Armazenamento
Há várias etapas que são necessárias para cancelar o registro de um servidor com um Serviço de Sincronização de Armazenamento. Vamos analisar como cancelar o registro de um servidor corretamente.

> [!Warning]  
> Não tente solucionar problemas com sincronização, hierarquização em nuvem ou qualquer outro aspecto do Azure File Sync - Sincronização de Arquivos do Azure cancelando o registro e o registro de um servidor ou removendo e recriando os pontos de extremidade do servidor, a menos que instruído explicitamente por um engenheiro da Microsoft. Cancelar o registro de um servidor e remover os terminais do servidor é uma operação destrutiva e os arquivos em camadas nos volumes com terminais do servidor não serão "reconectados" a seus locais no compartilhamento de arquivos do Azure depois que os pontos de extremidade do servidor e do servidor registrados forem recriados, o que resultará em sincronização erros. Observe também que os arquivos em camadas que existem fora de um namespace do terminal do servidor podem ser permanentemente perdidos. Os arquivos em camadas podem existir nos terminais do servidor, mesmo que a camada em nuvem nunca tenha sido ativada.

#### <a name="optional-recall-all-tiered-data"></a>(Opcional) Realizar o recall de todos os dados em camadas
Se você gostaria que os arquivos que estão atualmente em camadas para estar disponível após a remoção do Azure File Sync - Sincronização de Arquivos do Azure (ou seja, esse é um de produção, não um teste, o ambiente), lembre-se todos os arquivos em cada volume que contém os pontos de extremidade do servidor. Desabilitar nuvem camadas para todos os pontos de extremidade do servidor e, em seguida, execute o seguinte cmdlet do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Se o volume local que hospeda o ponto de extremidade do servidor não tiver espaço livre suficiente para recuperar todos os dados em camadas, o cmdlet `Invoke-StorageSyncFileRecall` falhará.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Remover o servidor de todos os grupos de sincronização
Antes de cancelar o registro do servidor no Serviço de Sincronização de Armazenamento, todos os pontos de extremidade do servidor nesse servidor devem ser removidos. Isso pode ser feito no portal do Azure:

1. Navegue até o Serviço de Sincronização do Armazenamento no qual o servidor está registrado.
2. Remova todos os pontos de extremidade do servidor deste servidor em cada grupo de sincronização no Serviço de Sincronização de Armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel Grupo de sincronização.

    ![Removendo um ponto de extremidade do servidor de um grupo de sincronização](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Isso também pode ser feito com um script simples do PowerShell:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Cancelar o registro do servidor
Agora que todos os dados foram recuperados e o servidor foi removido de todos os grupos de sincronização, é possível cancelar o registro do servidor. 

1. Na portal do Azure, navegue até a seção *servidores registrados* do serviço de sincronização de armazenamento.
2. Clique com botão direito do mouse no servidor do qual deseja cancelar o registro e clique em “Cancelar Registro do Servidor”.

    ![Cancelar o registro do servidor](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Garantindo que a Sincronização de arquivos do Azure seja uma boa vizinha no seu data center 
Como a Sincronização de arquivos do Azure raramente será o único serviço em execução em seu data center, é interessante limitar o uso de rede e de armazenamento da Sincronização de arquivos do Azure.

> [!Important]  
> Definir limites muito baixos afetará o desempenho de sincronização e de recuperação da Sincronização de arquivos do Azure.

### <a name="set-azure-file-sync-network-limits"></a>Definir limites de rede da Sincronização de arquivos do Azure
Você pode limitar a utilização de rede da Sincronização de arquivos do Azure usando os cmdlets `StorageSyncNetworkLimit`.

> [!Note]  
> Os limites de rede não se aplicam quando um arquivo em camadas é acessado.

Por exemplo, você pode criar uma nova limitação para garantir que a Sincronização de arquivos do Azure não use mais de 10 Mbps entre 9h e 17h de segunda a sexta-feira: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Você pode ver o seu limite usando o seguinte cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para remover os limites de rede, use `Remove-StorageSyncNetworkLimit`. Por exemplo, o comando a seguir remove todos os limites de rede:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Usar a QoS de armazenamento do Windows Server 
Quando a Sincronização de arquivos do Azure estiver hospedada em uma máquina virtual em execução em um host de virtualização do Windows Server, você poderá usar a QoS de armazenamento (qualidade de serviço de armazenamento) para regular o consumo de E/S do armazenamento. A política de QoS de armazenamento pode ser definida como um nível máximo (ou um limite, da mesma forma em que o limite de StorageSyncNetwork é imposto acima) ou como um nível mínimo (ou uma reserva). Definir um nível mínimo em vez de um máximo permite que a Sincronização de arquivos do Azure seja disparada para usar a largura de banda de armazenamento disponível se outras cargas de trabalho não a estiverem usando. Para obter mais informações, consulte [Qualidade de serviço do armazenamento](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Confira também
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)