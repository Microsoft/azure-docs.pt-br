---
title: "Registrar/cancelar o registro de um servidor com a Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Saiba como registrar e cancelar o registro de um Windows Server com um Serviço de Sincronização de Armazenamento de Sincronização de Arquivo do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 831623b0fa0d8c03713f608116709e6a590d93c6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Registrar/cancelar o registro de um servidor com a Sincronização de Arquivo do Azure (versão prévia)
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

O artigo a seguir ilustra como registrar e cancelar o registro de um servidor com um Serviço de Sincronização de Armazenamento. Isso pode ser desejável se um servidor estiver sendo desativado ou se um novo ponto de extremidade de serviço for desejado em um Grupo de Sincronização. Consulte [Como implantar a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar a Sincronização de Arquivo do Azure de ponta a ponta.

## <a name="prerequisites"></a>Pré-requisitos
Para registrar um Windows Server com um Serviço de Sincronização de Armazenamento, primeiro você deve preparar um servidor Windows com os pré-requisitos necessários:

* Certifique-se de que um Serviço de Sincronização de Armazenamento foi implantado. Para obter mais informações sobre como implantar um Serviço de Sincronização de Armazenamento, consulte [Como implantar a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-deployment-guide.md).
* Certifique-se de que o servidor está conectado à Internet e que o Azure está acessível.
* Desabilite a Configuração de Segurança Reforçada do IE para administradores com a interface do usuário do Gerenciador do Servidor.
    
    ![Interface do usuário do Gerenciador do Servidor com a Configuração de Segurança Reforçada do IE realçada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Certifique-se de que o módulo AzureRM PowerShell está instalado no servidor. Se o servidor for um membro de um Cluster de Failover, todos os nós no cluster precisarão do módulo AzureRM. É possível encontrar mais detalhes sobre como instalar o módulo do AzureRM em [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Instalar e configurar o Azure PowerShell).

    > [!Note]  
    > É recomendável usar a versão mais recente do módulo AzureRM PowerShell para registrar/cancelar o registro de um servidor. Se o pacote do AzureRM tiver sido instalado anteriormente nesse servidor (e a versão do PowerShell nesse servidor for 5.* ou maior), você poderá usar o cmdlet `Update-Module` para atualizar esse pacote. 

## <a name="register-a-server-with-storage-sync-service"></a>Registrar um servidor com o Serviço de Sincronização de Armazenamento
Antes de um Windows Server poder ser usado como um *ponto de extremidade do servidor* em um *Grupo de Sincronização* da Sincronização de Arquivo do Azure, ele deve ser registrado com um *Serviço de Sincronização de Armazenamento*. Um servidor pode ser registrado apenas com um *Serviço de Sincronização de Armazenamento* de cada vez.

### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
1. [Baixe o agente de Sincronização de Arquivo do Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador do agente de Sincronização de Arquivo do Azure.
    
    ![O primeiro painel do instalador do agente de Sincronização de Arquivo do Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de habilitar as atualizações para o agente de Sincronização de Arquivo do Azure usando o Microsoft Update. É importante porque correções de segurança críticas e aprimoramentos de recursos para o pacote do servidor são enviadas por meio do Microsoft Update.

    ![Certifique-se de que o Microsoft Update está habilitado no painel do Microsoft Update do instalador do agente de Sincronização de Arquivo do Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não tiver sido registrado anteriormente, a interface do usuário de registro do servidor será aberta imediatamente após a conclusão da instalação.

> [!Important]  
> Se o servidor for um membro de um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisará ser instalado em cada nó no cluster.

### <a name="register-the-server-using-the-server-registration-ui"></a>Registrar o servidor usando a interface do usuário de Registro do Servidor
1. Se a interface do usuário de Registro do Servidor não for iniciada imediatamente após a conclusão da instalação do agente de Sincronização de Arquivo do Azure, ela poderá ser iniciada manualmente executando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Clique em *Entrar* para acessar sua assinatura do Azure. 

    ![Caixa de diálogo de abertura da interface do usuário de Registro do Servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Selecione a assinatura, o grupo de recursos e o Serviço de Sincronização de Armazenamento na caixa de diálogo.

    ![Informações do Serviço de Sincronização de Armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Na versão prévia, é necessário entrar mais uma vez para concluir o processo. 

    ![Caixa de diálogo Entrar](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for um membro de um Cluster de Failover, cada servidor precisará executar o Registro do Servidor. Quando você exibe os servidores registrados no Portal do Azure, a Sincronização de Arquivo do Azure reconhece automaticamente cada nó como um membro do mesmo Cluster de Failover e os agrupa corretamente.

## <a name="unregister-the-server-with-storage-sync-service"></a>Cancelar o registro de um servidor com o Serviço de Sincronização de Armazenamento
Há várias etapas que são necessárias para cancelar o registro de um servidor com um Serviço de Sincronização de Armazenamento. Vamos analisar como cancelar o registro de um servidor corretamente.

### <a name="optional-recall-all-tiered-data"></a>(Opcional) Realizar o recall de todos os dados em camadas
Quando habilitadas para um ponto de extremidade de servidor, as camadas de nuvem colocarão os arquivos em *camadas* para seus compartilhamentos de Arquivo do Azure. Isso permite que os compartilhamentos de arquivos locais atuem como um cache, em vez de uma cópia completa do conjunto de dados, para utilizar o espaço no servidor de arquivos de forma eficiente. No entanto, se um ponto de extremidade do servidor for removido com arquivos em camadas ainda localmente no servidor, esses arquivos se tornarão inacessíveis. Portanto, se o acesso contínuo ao arquivo for desejado, será necessário realizar o recall de todos os arquivos em camadas dos Arquivos do Azure antes de continuar com o cancelamento de registro. 

Isso pode ser feito com o cmdlet do PowerShell como mostrado abaixo:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Se o volume local que hospeda o servidor não tiver espaço livre suficiente para realizar o recall de todos os dados em camadas, o cmdlet `Invoke-StorageSyncFileRecall` falhará.  

### <a name="remove-the-server-from-all-sync-groups"></a>Remover o servidor de todos os Grupos de Sincronização
Antes de cancelar o servidor no Serviço de Sincronização de Armazenamento, todos os pontos de extremidade do servidor para esse servidor devem ser removidos. Isso pode ser feito por meio do Portal:

1. Navegue até o Serviço de Sincronização do Armazenamento no qual o servidor está registrado.
2. Remova todos os pontos de extremidade do servidor para este servidor em cada Grupo de Sincronização no Serviço de Sincronização de Armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel Grupo de Sincronização.

    ![Removendo um ponto de extremidade de servidor de um Grupo de Sincronização](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Isso também pode ser feito com um script simples do PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Cancelar o registro do servidor
Agora que foi feito o recall de todos os dados e o servidor foi removido de todos os Grupos de Sincronização, é possível cancelar o registro do servidor. 

1. No portal do Azure, navegue até a seção "Servidores Registrados" do Serviço de Sincronização de Armazenamento.
2. Clique com botão direito do mouse no servidor do qual deseja cancelar o registro e clique em “Cancelar Registro do Servidor”.

    ![Cancelar registro do servidor](media/storage-sync-files-server-registration/unregister-server-1.png)