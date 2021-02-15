---
title: Solucionar problemas de Arquivos do Azure no Windows
description: Solução de problemas de arquivos do Azure no Windows. Consulte problemas comuns relacionados aos arquivos do Azure quando você se conecta de clientes Windows e veja possíveis resoluções. Somente para compartilhamentos SMB
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 242c0819e916f3ea7912d4d57b7d3e338152e4d9
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878503"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Solucionar problemas de arquivos do Azure no Windows (SMB)

Este artigo lista os problemas comuns relacionados aos Arquivos do Microsoft Azure quando você se conecta de clientes Windows. Também fornece as possíveis causas e resoluções para esses problemas. Além das etapas de solução de problemas neste artigo, você também pode usar o [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para garantir que o ambiente de cliente do Windows possui os pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo e ajuda a configurar seu ambiente para obter um desempenho ideal.

> [!IMPORTANT]
> O conteúdo deste artigo se aplica somente a compartilhamentos SMB. Para obter detalhes sobre compartilhamentos NFS, consulte [solucionar problemas de compartilhamentos de arquivos NFS](storage-troubleshooting-files-nfs.md)

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 ao montar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos, pode receber o erro a seguir:

- Ocorreu um erro de sistema 5. O acesso foi negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não criptografado

Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo datacenter onde residem os compartilhamentos de arquivos do Azure. As conexões não criptografadas dentro do mesmo datacenter também podem ser bloqueadas se o [transferência segura obrigatória](../common/storage-require-secure-transfer.md) está habilitada na conta de armazenamento. Um canal de comunicação criptografado é fornecido somente quando o sistema operacional do cliente do usuário dá suporte à criptografia SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada solicitação de negociação de sistema que inclua o SMB 3.0, que dá suporte à criptografia.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

1. Conecte a partir de um cliente com suporte à criptografia SMB (Windows 8, Windows Server 2012 ou posterior) ou conecte a partir de uma máquina virtual que está no mesmo datacenter da conta de armazenamento do Azure usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [Transferência segura obrigatória](../common/storage-require-secure-transfer.md) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Se as regras de firewall e de VNET (rede virtual) estiverem configuradas na conta de armazenamento, o tráfego de rede terá acesso negado a menos que o endereço IP do cliente ou da rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Causa 3: permissões de nível de compartilhamento incorretas ao usar a autenticação baseada em identidade

Se os usuários estiverem acessando o compartilhamento de arquivos do Azure usando a autenticação Active Directory (AD) ou Azure Active Directory Domain Services (Azure AD DS), o acesso ao compartilhamento de arquivos falhará com o erro "acesso negado" se as permissões de nível de compartilhamento estiverem incorretas. 

### <a name="solution-for-cause-3"></a>Solução para a causa 3

Valide se as permissões estão configuradas corretamente:

- **Active Directory (AD)** consulte [atribuir permissões de nível de compartilhamento a uma identidade](./storage-files-identity-ad-ds-assign-permissions.md).

    As atribuições de permissão de nível de compartilhamento têm suporte para grupos e usuários que foram sincronizados do Active Directory (AD) para Azure Active Directory (AD do Azure) usando Azure AD Connect.  Confirme se os grupos e os usuários que estão sendo atribuídos às permissões de nível de compartilhamento não são grupos de "somente nuvem" sem suporte.
- **Azure Active Directory Domain Services (Azure AD DS)** consulte [atribuir permissões de acesso a uma identidade](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, Erro 67 ou Erro 87 ao montar ou desmontar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos do local ou de um datacenter diferente, você poderá receber os seguintes erros:

- Ocorreu um erro de sistema 53. O caminho da rede não foi encontrado.
- Ocorreu um erro de sistema 67. O nome de rede não foi encontrado.
- Ocorreu um erro de sistema 87. O parâmetro está incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: a porta 445 está bloqueada

Pode ocorrer um erro de sistema 53 ou erro de sistema 67 se a comunicação de saída na porta 445 para o datacenter de Arquivos do Azure estiver bloqueada. Para ver o resumo de ISPs que permitem ou proíbem o acesso a partir da porta 445, vá para [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se o firewall ou ISP está bloqueando a porta 445, use a ferramenta [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) ou o cmdlet `Test-NetConnection`. 

Para usar o `Test-NetConnection` cmdlet, o módulo Azure PowerShell deve ser instalado, consulte [instalar Azure PowerShell módulo](/powershell/azure/install-Az-ps) para obter mais informações. Lembre-se de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes referentes a sua conta de armazenamento.

   
```azurepowershell
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
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> O comando acima retorna o endereço IP atual da conta de armazenamento. Não há garantia de que esse endereço IP será sempre o mesmo; ele pode mudar a qualquer momento. Não codifique esse endereço IP em um script ou em uma configuração de firewall.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1 - Usar a Sincronização de Arquivos do Azure
Sincronização de Arquivos do Azure pode transformar seu Windows Server local em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. A Sincronização de Arquivos do Azure funciona na porta 443 e, portanto, pode ser usada como uma solução alternativa para acessar os arquivos do Azure de clientes que têm a porta 445 bloqueada. [Saiba como configurar o sincronização de arquivos do Azure](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Solução 2 - Usar a VPN
Ao configurar uma VPN para sua conta de armazenamento específica, o tráfego passará por um túnel seguro em oposição à Internet. Siga as [instruções para configurar a VPN](storage-files-configure-p2s-vpn-windows.md) para acessar os Arquivos do Azure no Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3 - Desbloquear a porta 445 com a ajuda de seu ISP / administrador de TI
Trabalhe com seu departamento de ti ou ISP para abrir a porta 445 de saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 – Usar a API REST com base em ferramentas como o Gerenciador de armazenamento/Powershell
Os arquivos do Azure também dão suporte ao REST, além do SMB. O acesso a REST funciona pela porta 443 (tcp padrão). Há várias ferramentas que são escritas usando a API REST que possibilitam uma experiência avançada de interface do usuário. [Gerenciador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) é um deles. [Baixe e instale o Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e conecte-se ao compartilhamento de arquivo respaldado pelos Arquivos do Azure. Você também pode usar o [PowerShell](./storage-how-to-use-files-powershell.md) que também a API REST do usuário.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está habilitado

O erro de sistema 53 ou erro de sistema 87 também pode ocorrer se a comunicação NTLMv1 está habilitada no cliente. Os Arquivos do Azure dão suporte apenas a autenticação NTLMv2. Ter NTLMv1 habilitado faz com que o cliente esteja menos seguro. Portanto, a comunicação será bloqueada para Arquivos do Azure. 

Para determinar se essa é a causa do erro, verifique se a seguinte subchave do registro não está definida com um valor menor que 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para saber mais, confira o tópico [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) no TechNet.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Reverter o valor **LmCompatibilityLevel** para o valor padrão de 3 na seguinte subchave do registro:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Erro 1816-não há cota suficiente disponível para processar este comando

### <a name="cause"></a>Causa

O erro 1816 ocorre quando você atinge o limite superior de identificadores abertos simultâneos permitidos para um arquivo ou diretório no compartilhamento de arquivos do Azure. Para obter mais informações, confira [Destinos de escala de Arquivos do Azure](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e tentando novamente. Para obter mais informações, consulte [armazenamento do Microsoft Azure lista de verificação de desempenho e escalabilidade](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para exibir identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) do PowerShell.  

Para fechar identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) do PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Erro "sem acesso" ao tentar acessar ou excluir um compartilhamento de arquivos do Azure  
Ao tentar acessar ou excluir um compartilhamento de arquivos do Azure no portal, você pode receber o seguinte erro:

Sem acesso  
Código de erro: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para a causa 1

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: sua conta de usuário não tem acesso à conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Navegue até a conta de armazenamento onde o compartilhamento de arquivos do Azure está localizado, clique em **Controle de acesso (IAM)** e verifique se sua conta de usuário tem acesso à conta de armazenamento. Para saber mais, confira [como proteger sua conta de armazenamento com o controle de acesso baseado em função do Azure (RBAC do Azure)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Não é possível modificar, mover/renomear ou excluir um arquivo ou diretório
Uma das principais finalidades de um compartilhamento de arquivos é que vários usuários e aplicativos podem interagir simultaneamente com arquivos e diretórios no compartilhamento. Para ajudá-lo nessa interação, os compartilhamentos de arquivos fornecem várias maneiras de mediar o acesso a arquivos e diretórios.

Quando você abre um arquivo de um compartilhamento de arquivos do Azure montado em SMB, seu aplicativo/sistema operacional solicita um identificador de arquivo, que é uma referência ao arquivo. Entre outras coisas, seu aplicativo especifica um modo de compartilhamento de arquivos quando solicita um identificador de arquivo, que especifica o nível de exclusividade de seu acesso ao arquivo imposto pelos arquivos do Azure: 

- `None`: você tem acesso exclusivo. 
- `Read`: outras pessoas podem ler o arquivo enquanto ele estiver aberto.
- `Write`: outras pessoas podem gravar no arquivo enquanto ele estiver aberto. 
- `ReadWrite`: uma combinação dos modos de `Read` `Write` compartilhamento e.
- `Delete`: outras pessoas podem excluir o arquivo enquanto você o abre. 

Embora como um protocolo sem estado, o protocolo filerest não tem um conceito de identificadores de arquivo, ele fornece um mecanismo semelhante para mediar o acesso a arquivos e pastas que seu script, aplicativo ou serviço pode usar: concessões de arquivo. Quando um arquivo é concedido, ele é tratado como equivalente a um identificador de arquivo com um modo de compartilhamento de arquivos de `None` . 

Embora os identificadores de arquivo e as concessões atendam a uma finalidade importante, às vezes, os identificadores e as concessões de arquivos podem ficar órfãos. Quando isso acontece, isso pode causar problemas na modificação ou exclusão de arquivos. Você poderá ver mensagens de erro como:

- O processo não pode acessar o arquivo porque ele está sendo usado por outro processo.
- A ação não pode ser concluída porque o arquivo está aberto em outro programa.
- O documento está bloqueado para edição por outro usuário.
- O recurso especificado está marcado para exclusão por um cliente SMB.

A resolução para esse problema depende se isso está sendo causado por um identificador de arquivo órfão ou concessão. 

### <a name="cause-1"></a>Causa 1
Um identificador de arquivo está impedindo que um arquivo/diretório seja modificado ou excluído. Você pode usar o cmdlet do PowerShell [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) para exibir identificadores abertos. 

Se todos os clientes SMB tiverem fechado seus identificadores abertos em um arquivo/diretório e o problema continuar a ocorrer, você poderá forçar o fechamento de um identificador de arquivo.

### <a name="solution-1"></a>Solução 1
Para forçar o fechamento de um identificador de arquivo, use o cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) do PowerShell. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Causa 2
Uma concessão de arquivo é impedir que um arquivo seja modificado ou excluído. Você pode verificar se um arquivo tem uma concessão de arquivo com o seguinte PowerShell, substituindo `<resource-group>` ,, `<storage-account>` `<file-share>` e `<path-to-file>` pelos valores apropriados para o seu ambiente:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Se um arquivo tiver uma concessão, o objeto retornado deverá conter as seguintes propriedades:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Solução 2
Para remover uma concessão de um arquivo, você pode liberar a concessão ou interromper a concessão. Para liberar a concessão, você precisa da concessão de leasing da concessão, que você define ao criar a concessão. Você não precisa da concessão de leasing para interromper a concessão.

O exemplo a seguir mostra como quebrar a concessão para o arquivo indicado na causa 2 (Este exemplo continua com as variáveis do PowerShell da causa 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Windows

Você pode ver o desempenho lento ao tentar transferir arquivos para o Serviço de Arquivos do Azure.

- Se você não tiver um requisito mínimo de tamanho de E / S específico, recomendamos usar 1 MiB como o tamanho de E / S para um desempenho ideal.
-   Se você sabe o tamanho final de um arquivo que você está estendendo com gravações e o seu software não tem problemas de compatibilidade com o final ainda não escrito desse arquivo que contém zeros, defina o tamanho do arquivo antecipadamente em vez de realizar cada gravação como uma gravação de extensão.
-   Use o método de cópia correto:
    -   Use [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre dois compartilhamentos de arquivos.
    -   Use o [Robocopy](./storage-how-to-create-file-share.md) entre compartilhamentos de arquivos e um computador local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para Windows 8.1 ou Windows Server 2012 R2

Para clientes que estão executando o Windows 8.1 ou o Windows Server 2012 R2, verifique se o hotfix [KB3114025](https://support.microsoft.com/help/3114025) está instalado. Esse hotfix melhora o desempenho dos identificadores de criação e fechamento.

Você pode executar o script abaixo para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix foi instalado, a seguinte saída será exibida:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão desde dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nenhuma pasta com uma letra de unidade em "Meu Computador" ou "este computador"

Se você mapear um compartilhamento de arquivos do Azure como administrador por meio do uso de rede, o compartilhamento parece estar ausente.

### <a name="cause"></a>Causa

Por padrão, o Explorador de Arquivos do Windows não é executado como administrador. Se você executar net use em um prompt de comando de administrador, mapeará a unidade de rede como um administrador. Como as unidades mapeadas são focadas no usuário, a conta de usuário que está conectada não exibe as unidades se eles estão montadas em uma conta de usuário diferente.

### <a name="solution"></a>Solução
Monte o compartilhamento de uma linha de comando de não administrador. Como alternativa, você pode seguir [Este tópico do TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) para configurar o valor do registro **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>O comando net use falha se a conta de armazenamento contém uma barra

### <a name="cause"></a>Causa

O comando net use interpreta uma barra (/) como uma opção de linha de comando. Se o nome da sua conta de usuário começa com uma barra, o mapeamento da unidade falhará.

### <a name="solution"></a>Solução

Você pode usar qualquer uma das seguintes etapas para contornar o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A partir de um arquivo em lotes, você pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Colocando aspas duplas em torno da chave para contornar esse problema, a menos que a barra seja o primeiro caractere. Se for, use o modo interativo e digite sua senha separadamente ou regenere as chaves para obter uma chave que não comece com uma barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>O aplicativo ou serviço não pode acessar uma unidade de Arquivos do Azure montada

### <a name="cause"></a>Causa

As unidades são montadas por usuário. Se o seu aplicativo ou serviço estiver em execução em uma conta de usuário diferente da que montou a unidade, o aplicativo não verá a unidade.

### <a name="solution"></a>Solução

use uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de usuário que contém o aplicativo. Você pode usar uma ferramenta como o PsExec.
- Passe o nome da conta de armazenamento e a chave nos parâmetros de nome do usuário e senha do comando net use.
- Use o comando cmdkey para adicionar as credenciais no Gerenciador de Credenciais. Execute-o em uma linha de comando no contexto da conta de serviço, seja por meio de um logon interativo ou usando `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapeie o compartilhamento diretamente sem usar uma letra de unidade mapeada. Alguns aplicativos podem não se reconectar à letra da unidade corretamente, portanto, usar o caminho UNC completo pode ser mais confiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir estas instruções, você pode receber a seguinte mensagem de erro quando você executa o net use para a conta de serviço de rede/sistema: "Ocorreu um erro de sistema 1312. Uma sessão de logon especificada não existe. Talvez ela já tenha sido finalizada.” Se isso ocorrer, verifique se o nome de usuário que é passado para net use inclui informações de domínio (por exemplo: "[nome da conta de armazenamento].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia"

Quando um arquivo é copiado pela rede, o arquivo é descriptografado no computador de origem, transmitido em texto não criptografado e criptografado novamente no destino. No entanto, você pode ver o seguinte erro quando estiver tentando copiar um arquivo criptografado: "Você está copiando o arquivo para um destino que não dá suporte à criptografia".

### <a name="cause"></a>Causa
Esse problema pode ocorrer se você estiver usando o sistema de arquivos com criptografia (EFS). Os arquivos criptografados pelo BitLocker podem ser copiados para os Arquivos do Azure. No entanto, os Arquivos do Azure não dão suporte a EFS NTFS.

### <a name="workaround"></a>Solução alternativa
Para copiar um arquivo pela rede, você deve primeiro descriptografá-lo. Use um dos métodos a seguir:

- Use o comando **copy /d**. Ele permite que os arquivos criptografados sejam salvos como arquivos descriptografados no destino.
- Defina a seguinte chave do registro:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

Observe que a definição da chave do registro afeta todas as operações de cópia que são feitas para compartilhamentos de rede.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumeração lenta dos arquivos e pastas

### <a name="cause"></a>Causa

Esse problema poderá ocorrer se não houver cache suficiente no computador cliente para diretórios grandes.

### <a name="solution"></a>Solução

Para resolver esse problema, ajuste o valor do registro **DirectoryCacheEntrySizeMax** para permitir o cache de listagens de diretório maiores no computador cliente:

- Local: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Emaranhado do valor: DirectoryCacheEntrySizeMax 
- Tipo de valor: DWORD
 
 
Por exemplo, você pode defini-lo como 0x100000 e ver se o desempenho se torna melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Erro AadDsTenantNotFound ao habilitar a autenticação do serviço de domínio Azure Active Directory (Azure AD DS) para arquivos do Azure "não é possível localizar locatários ativos com a ID de locatário AAD-Tenant-ID"

### <a name="cause"></a>Causa

O erro AadDsTenantNotFound ocorre quando você tenta [habilitar a autenticação Azure Active Directory Domain Services (Azure AD DS) em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) em uma conta de armazenamento em que o [Azure AD Domain Service (Active AD DS)](../../active-directory-domain-services/overview.md) não é criado no locatário do Azure ad da assinatura associada.  

### <a name="solution"></a>Solução

Habilite o Azure AD DS no locatário do Azure AD da assinatura na qual sua conta de armazenamento está implantada. Você precisa de privilégios de administrador do locatário do Azure AD para criar um domínio gerenciado. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [criar e configurar um domínio gerenciado Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Não é possível montar os arquivos do Azure com as credenciais do AD 

### <a name="self-diagnostics-steps"></a>Etapas de autodiagnóstico
Primeiro, certifique-se de ter seguido todas as quatro etapas para [habilitar a autenticação do AD dos arquivos do Azure](./storage-files-identity-auth-active-directory-enable.md).

Em segundo lugar, tente [montar o compartilhamento de arquivos do Azure com a chave da conta de armazenamento](./storage-how-to-use-files-windows.md). Se você não conseguiu montar, baixe [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para ajudá-lo a validar o cliente que está executando o ambiente, detectar a configuração de cliente incompatível que causaria falha de acesso para os arquivos do Azure, fornece orientação prescritiva sobre AutoCorreção e coleta dos rastreamentos de diagnóstico.

Em terceiro lugar, você pode executar o cmdlet Debug-AzStorageAccountAuth para conduzir um conjunto de verificações básicas em sua configuração do AD com o usuário conectado do AD. Esse cmdlet é compatível com a [versão AzFilesHybrid v0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). Você precisa executar esse cmdlet com um usuário do AD que tenha permissão de proprietário na conta de armazenamento de destino.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
O cmdlet executa essas verificações abaixo em sequência e fornece diretrizes para falhas:
1. CheckADObjectPasswordIsCorrect: Verifique se a senha configurada na identidade do AD que representa a conta de armazenamento corresponde à da conta de armazenamento kerb1 ou à chave kerb2. Se a senha estiver incorreta, você poderá executar [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) para redefinir a senha. 
2. CheckADObject: Confirme se há um objeto no Active Directory que representa a conta de armazenamento e tem o SPN (nome da entidade de serviço) correto. Se o SPN não estiver configurado corretamente, execute o cmdlet Set-AD retornado no cmdlet debug para configurar o SPN.
3. CheckDomainJoined: valide se o computador cliente está ingressado no domínio no AD. Se seu computador não estiver ingressado no domínio no AD, consulte este [artigo](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) para obter instruções de ingresso no domínio.
4. CheckPort445Connectivity: Verifique se a porta 445 está aberta para conexão SMB. Se a porta necessária não estiver aberta, consulte a ferramenta de solução de problemas [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) para problemas de conectividade com os arquivos do Azure.
5. CheckSidHasAadUser: Verifique se o usuário conectado do AD está sincronizado com o Azure AD. Se você quiser procurar se um usuário específico do AD está sincronizado com o Azure AD, você pode especificar o-UserName e-Domain nos parâmetros de entrada. 
6. CheckGetKerberosTicket: tentativa de obter um tíquete Kerberos para conectar-se à conta de armazenamento. Se não houver um token Kerberos válido, execute o cmdlet klist Get CIFS/Storage-Account-Name. File. Core. Windows. net e examine o código de erro para raiz, causando a falha de recuperação do tíquete.
7. CheckStorageAccountDomainJoined: Verifique se a autenticação do AD foi habilitada e se as propriedades do AD da conta estão preenchidas. Caso contrário, consulte a instrução [aqui](./storage-files-identity-ad-ds-enable.md) para habilitar a autenticação de AD DS nos arquivos do Azure. 
8. CheckUserRbacAssignment: Verifique se o usuário do AD tem a atribuição de função RBAC apropriada para fornecer a permissão de nível de compartilhamento para acessar os arquivos do Azure. Caso contrário, consulte a instrução [aqui](./storage-files-identity-ad-ds-assign-permissions.md) para configurar a permissão de nível de compartilhamento. (Com suporte em AzFilesHybrid v 0.2.3 + versão)
9. CheckUserFileAccess: Verifique se o usuário do AD tem a permissão de diretório/arquivo apropriada (ACLs do Windows) para acessar os arquivos do Azure. Caso contrário, consulte a instrução [aqui](./storage-files-identity-ad-ds-configure-permissions.md) para configurar a permissão de nível de diretório/arquivo. (Com suporte em AzFilesHybrid v 0.2.3 + versão)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Não é possível configurar permissões de nível de diretório/arquivo (ACLs do Windows) com o explorador de arquivos do Windows

### <a name="symptom"></a>Sintoma

Você pode ter qualquer um dos sintomas descritos abaixo ao tentar configurar ACLs do Windows com o explorador de arquivos em um compartilhamento de arquivos montado:
- Depois de clicar na permissão Editar na guia Segurança, o assistente de permissão não é carregado. 
- Quando você tenta selecionar um novo usuário ou grupo, o local do domínio não exibe a AD DS domínio correto. 

### <a name="solution"></a>Solução

Recomendamos que você use a [ferramenta icacls](/windows-server/administration/windows-commands/icacls) para configurar as permissões de nível de diretório/arquivo como uma solução alternativa. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Erros ao executar o cmdlet Join-AzStorageAccountForAuth

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Erro: "o serviço de diretório não pôde alocar um identificador relativo"

Esse erro pode ocorrer se um controlador de domínio que mantém a função FSMO mestre RID não estiver disponível ou foi removido do domínio e restaurado do backup.  Confirme se todos os controladores de domínio estão em execução e disponíveis.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Erro: "Não é possível associar parâmetros posicionais porque nenhum nome foi fornecido"

Este erro provavelmente é acionado por um erro de sintaxe no comando Join-AzStorageAccountforAuth.  Verifique o comando em busca de erros de ortografia ou de sintaxe e verifique se a versão mais recente do módulo AzFilesHybrid ( https://github.com/Azure-Samples/azure-files-samples/releases) está instalada.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Suporte de autenticação local de AD DS arquivos do Azure para criptografia Kerberos AES 256

Introduzimos o suporte de criptografia Kerberos do AES 256 para a autenticação de AD DS local dos arquivos do Azure com o [módulo AzFilesHybrid v 0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases). Se você tiver habilitado AD DS autenticação com uma versão de módulo inferior a v 0.2.2, será necessário baixar o módulo AzFilesHybrid mais recente (v 0.2.2 +) e executar o PowerShell abaixo. Se você ainda não habilitou a autenticação AD DS em sua conta de armazenamento, você pode seguir estas [diretrizes](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) para habilitação. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
