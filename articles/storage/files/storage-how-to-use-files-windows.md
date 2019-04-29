---
title: Usar um compartilhamento de arquivos do Azure com o Windows | Microsoft Docs
description: Saiba como usar um compartilhamento de arquivos do Azure com Windows e Windows Server.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae097cb1c778c0ad60512753ae6ef28da04e8004
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764384"
---
# <a name="use-an-azure-file-share-with-windows"></a>Usar um compartilhamento de arquivos do Azure com o Windows
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser usados perfeitamente no Windows e no Windows Server. Este artigo aborda as considerações para usar um compartilhamento de arquivos do Azure com Windows e Windows Server.

Para usar um compartilhamento de Arquivos do Azure fora da região na qual o Azure está hospedado, por exemplo, localmente ou em uma região diferente do Azure, o sistema operacional deverá dar suporte a SMB 3.0. 

Você pode usar compartilhamentos de arquivos do Azure em uma instalação do Windows que esteja em execução em uma VM do Azure ou localmente. A tabela abaixo mostra quais versões do sistema operacional dão suporte ao acesso de compartilhamentos de arquivos em quais ambientes:

| Versão do Windows        | Versão do SMB | Montável na VM do Azure | Montável no local |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019    | SMB 3.0 | Sim | Sim |
| Windows 10<sup>1</sup> | SMB 3.0 | Sim | Sim |
| Canal semestral do Windows Server<sup>2</sup> | SMB 3.0 | Sim | Sim |
| Windows Server 2016    | SMB 3.0     | Sim                   | Sim                  |
| Windows 8.1            | SMB 3.0     | Sim                   | Sim                  |
| Windows Server 2012 R2 | SMB 3.0     | Sim                   | Sim                  |
| Windows Server 2012    | SMB 3.0     | Sim                   | Sim                  |
| Windows 7              | SMB 2.1     | Sim                   | Não                    |
| Windows Server 2008 R2 | SMB 2.1     | Sim                   | Não                    |

<sup>1</sup>Windows 10, versões 1507, 1607, 1703, 1709, 1803 e 1809.  
<sup>2</sup>Windows Server, versão 1709 e 1803.

> [!Note]  
> É sempre recomendável obter o KB mais recente para a sua versão do Windows.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
* **Nome da conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará do nome da conta de armazenamento.

* **Chave de conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará da chave de armazenamento primária (ou secundária). Atualmente, as chaves SAS não têm suporte para montagem.

* **Verifique se a porta 445 está aberta**: O protocolo SMB requer a porta TCP 445 aberta; haverá falha de conexão se a porta 445 estiver bloqueada. Você pode verificar se o firewall está bloqueando a porta 445 com o cmdlet `Test-NetConnection`. Você pode aprender sobre [várias maneiras para solucionar esse problema bloqueado a porta 445 aqui](https://docs.microsoft.com/en-us/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked).

    O PowerShell a seguir código pressupõe que você tenha o módulo do Azure PowerShell instalado, consulte [instalar o Azure PowerShell módulo](https://docs.microsoft.com/powershell/azure/install-az-ps) para obter mais informações. Lembre-se de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes referentes a sua conta de armazenamento.

    ```powershell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Se a conexão foi bem-sucedida, você verá a seguinte saída:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > O comando acima retorna o endereço IP atual da conta de armazenamento. Não há garantia de que esse endereço IP será sempre o mesmo; ele pode mudar a qualquer momento. Não codifique esse endereço IP em um script ou em uma configuração de firewall. 

## <a name="using-an-azure-file-share-with-windows"></a>Usando um compartilhamento de arquivos do Azure com o Windows
Para usar um compartilhamento de arquivos do Azure com o Windows, você deve montá-lo, ou seja, atribuir uma letra da unidade ou um caminho de ponto de montagem, ou acessá-lo por meio do [caminho UNC](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

Ao contrário de outros compartilhamentos de SMB com os quais você possa ter interagido, como aqueles hospedados em um Windows Server, em um servidor Linux Samba ou em um dispositivo NAS, os compartilhamentos de arquivos do Azure atualmente não dão suporte à autenticação Kerberos no Azure AD (Active Directory) ou à identidade do AAD (Azure Active Directory), embora estejamos [trabalhando nisso](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles). Sendo assim, você deve acessar o compartilhamento de arquivos do Azure com a chave da conta de armazenamento que contém o compartilhamento de arquivos do Azure. Uma chave de conta de armazenamento é uma chave de administrador para uma conta de armazenamento que inclui permissões de administrador para todos os arquivos e pastas no compartilhamento de arquivo que você está acessando e para todos os compartilhamentos de arquivos e outros recursos de armazenamento (blobs, filas, tabelas e afins) contidos na conta de armazenamento. Se isso não é suficiente para sua carga de trabalho, a [Sincronização de Arquivos do Azure](storage-files-planning.md#data-access-method) pode lidar com a falta de autenticação Kerberos e suporte a ACL nesse meio-tempo até que o suporte a ACL e a autenticação Kerberos com base em AAD fiquem disponíveis publicamente.

Um padrão comum para o lift and shift de aplicativos de LOB (linha de negócios) que esperam um compartilhamento de arquivos SMB para o Azure é usar um compartilhamento de arquivos do Azure como uma alternativa para a execução de um servidor de arquivos dedicado do Windows em uma VM do Azure. Uma consideração importante para a migração com êxito de um aplicativo de linha de negócios a fim de usar um compartilhamento de arquivos do Azure é que muitos aplicativos de linha de negócios são executados no contexto de uma conta de serviço dedicada com permissões do sistema limitadas em vez de usar a conta administrativa da VM. Portanto, você deve montar/salvar as credenciais do compartilhamento de arquivos do Azure a partir do contexto da conta de serviço em vez da conta administrativa.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Persistindo as credenciais de compartilhamento de arquivos do Azure no Windows  
O utilitário [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) permite que você armazene suas credenciais da conta de armazenamento no Windows. Isso significa que você não precisa especificar as credenciais quando tenta acessar um compartilhamento de arquivos do Azure por meio do caminho UNC ou montar o compartilhamento de arquivos do Azure. Para salvar as credenciais da conta de armazenamento, execute os seguintes comandos do PowerShell, substituindo `<your-storage-account-name>` e `<your-resource-group-name>` quando apropriado.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

Você pode verificar que o utilitário cmdkey armazenou a credencial da conta de armazenamento usando o parâmetro de lista:

```powershell
cmdkey /list
```

Se as credenciais do compartilhamento de arquivos do Azure forem armazenadas com êxito, a saída esperada será semelhante à seguinte (pode haver outras chaves armazenadas na lista):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Agora você deve conseguir montar ou acessar o compartilhamento sem ter que fornecer outras credenciais.

#### <a name="advanced-cmdkey-scenarios"></a>Cenários cmdkey avançados
Há dois cenários adicionais a serem considerados com cmdkey: armazenar credenciais de outro usuário na máquina, por exemplo, uma conta de serviço, e armazenar credenciais em um computador remoto com a comunicação remota do PowerShell.

O armazenamento de credenciais de outro usuário no computador é muito fácil: quando estiver conectado à sua conta, execute o seguinte comando do PowerShell:

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Isso abrirá uma nova janela do PowerShell no contexto do usuário de sua conta de serviço (ou conta de usuário). Você pode usar o utilitário cmdkey conforme descrito [acima](#persisting-azure-file-share-credentials-in-windows).

No entanto, não é possível armazenar as credenciais em um computador remoto usando a comunicação remota do PowerShell, já que cmdkey não permite o acesso ao repositório de credenciais, mesmo para adições, quando o usuário está conectado por meio da comunicação remota do PowerShell. Recomendamos fazer logon no computador com a [Área de Trabalho Remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>Como montar o compartilhamento de arquivos do Azure com o PowerShell
Execute os comandos a seguir de uma sessão regular (ou seja, sem privilégios elevados) do PowerShell para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>`, e `<desired-drive-letter>` pelas informações apropriadas.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> O uso da opção `-Persist` no cmdlet `New-PSDrive` só permitirá que o compartilhamento de arquivos seja remontado na inicialização se as credenciais tiverem sido salvas. Você pode salvar as credenciais usando o cmdkey como [descrito anteriormente](#persisting-azure-file-share-credentials-in-windows). 

Se desejar, você pode desmontar o compartilhamento de arquivos do Azure usando o cmdlet do PowerShell a seguir.

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Como montar o compartilhamento de arquivos do Azure com o Explorador de Arquivos
> [!Note]  
> Observe que as instruções a seguir são mostradas no Windows 10 e podem diferir ligeiramente em versões mais antigas. 

1. Abra o Explorador de Arquivos. Isso pode ser feito abrindo o Menu Iniciar ou pressionando o atalho Win + E.

2. Navegue até o item **Este Computador** no lado esquerdo da janela. Isso alterará os menus disponíveis na faixa de opções. No menu do computador, selecione **Mapear unidade de rede**.
    
    ![Uma captura de tela do menu suspenso "Mapear unidade de rede"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Copie o caminho UNC do painel **Conectar** no portal do Azure. 

    ![O caminho UNC do painel Conectar dos Arquivos do Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Selecione a letra da unidade e digite o caminho UNC. 
    
    ![Uma captura de tela da caixa de diálogo "Mapear unidade de rede"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Use o nome da conta de armazenamento anexado com `AZURE\` como o nome de usuário e uma chave de conta de armazenamento como a senha.
    
    ![Uma captura de tela da caixa de diálogo de credenciais de rede](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Use o compartilhamento de arquivos do Azure como quiser.
    
    ![O compartilhamento de arquivos do Azure já está montado](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Quando quiser desmontar o compartilhamento de arquivos do Azure, clique com o botão direito do mouse na entrada do compartilhamento em **Locais de rede** no Explorador de Arquivos e selecione **Desconectar**.

### <a name="accessing-share-snapshots-from-windows"></a>Acessar instantâneos de compartilhamento no Windows
Se você tirou um instantâneo de compartilhamento, manual ou automaticamente por meio de um script ou serviço como o Backup do Azure, veja as versões anteriores de um compartilhamento, um diretório ou um arquivo específico do compartilhamento de arquivos no Windows. Você pode tirar um instantâneo de compartilhamento no [Portal do Azure](storage-how-to-use-files-portal.md), [Azure PowerShell](storage-how-to-use-files-powershell.md) e [CLI do Azure](storage-how-to-use-files-cli.md).

#### <a name="list-previous-versions"></a>Listar versões anteriores
Navegue até o item ou o item pai que precisa ser restaurado. Clique duas vezes para ir até o diretório desejado. Clique com o botão direito do mouse e selecione **Propriedades** no menu.

![Clique com o botão direito do mouse no menu para um diretório selecionado](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Selecione **Versões Anteriores** para ver a lista de instantâneos de compartilhamento para esse diretório. A lista pode levar alguns segundos para carregar, dependendo da velocidade da rede e do número de instantâneos de compartilhamento no diretório.

![Guia Versões Anteriores](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Você pode selecionar **Abrir** para abrir um instantâneo específico. 

![Instantâneo aberto](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior
Selecione **Restaurar** para copiar o conteúdo do diretório inteiro recursivamente no momento da criação do instantâneo de compartilhamento para o local original.
 ![Botão Restaurar em mensagem de aviso](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Protegendo o Windows/Windows Server
Para montar um compartilhamento de arquivos do Azure no Windows, a porta 445 deve estar acessível. Muitas organizações bloqueiam a porta 445 devido aos riscos de segurança inerentes ao protocolo SMB 1. SMB 1, também conhecido como CIFS (Common Internet File System), é um protocolo de sistema de arquivos herdados incluído no Windows e no Windows Server. O SMB 1 é um protocolo desatualizado, ineficiente e, o mais importante, não seguro. A boa notícia é que os arquivos do Azure não dão suporte a SMB 1 e todas as versões com suporte do Windows e do Windows Server possibilitam a remoção ou desabilitação do SMB 1. [Recomendamos sempre](https://aka.ms/stopusingsmb1) remover ou desabilitar o cliente e o servidor SMB 1 no Windows antes de usar os compartilhamentos de arquivos do Azure na produção.

A tabela a seguir fornece informações detalhadas sobre o status do SMB 1 em cada versão do Windows:

| Versão do Windows                           | Status padrão de protocolos SMB 1 | Desabilitar/Remover método       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019 (versão prévia)             | Desabilitado             | Remover com recurso do Windows |
| Windows Server, versões 1709 e posteriores            | Desabilitado             | Remover com recurso do Windows |
| Windows 10, versões 1709 ou posterior                | Desabilitado             | Remover com recurso do Windows |
| Windows Server 2016                       | Enabled              | Remover com recurso do Windows |
| Windows 10 versões 1507, 1607 e 1703 | Enabled              | Remover com recurso do Windows |
| Windows Server 2012 R2                    | Enabled              | Remover com recurso do Windows | 
| Windows 8.1                               | Enabled              | Remover com recurso do Windows | 
| Windows Server 2012                       | Enabled              | Desabilitar no Registro       | 
| Windows Server 2008 R2                    | Enabled              | Desabilitar no Registro       |
| Windows 7                                 | Enabled              | Desabilitar no Registro       | 

### <a name="auditing-smb-1-usage"></a>Auditando o uso de protocolos SMB 1
> Aplica-se a Windows Server 2019 (versão prévia), canal semestral do Windows Server (versões 1709 e 1803), Windows Server 2016, Windows 10 (versões 1507, 1607, 1703, 1709 e 1803), Windows Server 2012 R2 e Windows 8.1

Antes de remover o protocolo SMB 1 de seu ambiente, audite o uso de SMB 1 para ver se algum cliente será interrompido pela alteração. Se alguma solicitação for feita em relação a compartilhamentos SMB com protocolos SMB 1, um evento de auditoria será registrado no log de eventos em `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Para habilitar o suporte a auditoria no Windows Server 2012 R2 e no Windows 8.1, instale pelo menos o [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Para habilitar a auditoria, execute o seguinte cmdlet em uma sessão do PowerShell com privilégios elevados:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Removendo SMB 1 do Windows Server
> Aplica-se a Windows Server 2019 (versão prévia), canal semestral do Windows Server (versões 1709 e 1803), Windows Server 2016 e Windows Server 2012 R2

Para remover o SMB 1 de uma instância do Windows Server, execute o seguinte cmdlet em uma sessão do PowerShell com privilégios elevados:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Para concluir o processo de remoção, reinicie o servidor. 

> [!Note]  
> A partir do Windows 10 e do Windows Server versão 1709, o SMB 1 não está instalado por padrão e tem recursos do Windows diferentes para o servidor SMB 1 e o cliente SMB 1. Recomendamos sempre deixar o servidor SMB 1 (`FS-SMB1-SERVER`) e o cliente SMB 1 (`FS-SMB1-CLIENT`) desinstalados.

### <a name="removing-smb-1-from-windows-client"></a>Removendo SMB 1 do cliente do Windows
> Aplica-se a Windows 10 (versões 1507, 1607, 1703, 1709 e 1803) e Windows 8.1

Para remover o SMB 1 do seu cliente Windows, execute o seguinte cmdlet em uma sessão do PowerShell com privilégios elevados:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Para concluir o processo de remoção, reinicie o computador.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Desabilitando SMB 1 em versões herdadas do Windows/Windows Server
> Aplica-se a Windows Server 2012, Windows Server 2008 R2 e Windows 7

O SMB 1 não pode ser completamente removidos em versões herdadas do Windows ou do Windows Server, mas pode ser desabilitado no Registro. Para desabilitar o SMB 1, crie uma nova chave do registro `SMB1` do tipo `DWORD` com um valor de `0` em `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Você pode fazer isso facilmente usando também o seguinte cmdlet do PowerShell:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Depois de criar essa chave do registro, você deverá reiniciar o servidor para desabilitar o SMB 1.

### <a name="smb-resources"></a>Recursos de SMB
- [Parar de usar protocolos SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Produto SMB 1 Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Descobrir SMB 1 em seu ambiente com DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [Desabilitando o SMB 1 com Política de Grupo](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure:
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Perguntas frequentes](../storage-files-faq.md)
- [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
