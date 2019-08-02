---
title: Solucionar problemas de Arquivos do Azure no Windows | Microsoft Docs
description: Solução de problemas de Arquivos do Azure no Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 1241a6ee5a49504619c377fa3f7006320def14ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805917"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Solucionar problemas de Arquivos do Azure no Windows

Este artigo lista os problemas comuns relacionados aos Arquivos do Microsoft Azure quando você se conecta de clientes Windows. Também fornece as possíveis causas e resoluções para esses problemas. Além das etapas de solução de problemas neste artigo, você também pode usar o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para garantir que o ambiente de cliente do Windows tenha os pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo e ajuda a configurar seu ambiente para obter um desempenho ideal. Também é possível encontrar essas informações na [solução de problemas de compartilhamentos de Arquivos do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece etapas para ajudá-lo a resolver problemas de conexão/mapeamento/montagem de compartilhamentos de Arquivos do Azure.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 ao montar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos, pode receber o erro a seguir:

- Ocorreu um erro de sistema 5. Acesso negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não criptografado

Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo datacenter onde residem os compartilhamentos de arquivos do Azure. As conexões não criptografadas dentro do mesmo datacenter também podem ser bloqueadas se o [transferência segura obrigatória](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está habilitada na conta de armazenamento. Um canal de comunicação criptografado é fornecido somente quando o sistema operacional do cliente do usuário dá suporte à criptografia SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada solicitação de negociação de sistema que inclua o SMB 3.0, que dá suporte à criptografia.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

1. Conecte a partir de um cliente com suporte à criptografia SMB (Windows 8, Windows Server 2012 ou posterior) ou conecte a partir de uma máquina virtual que está no mesmo datacenter da conta de armazenamento do Azure usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [Transferência segura obrigatória](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Se as regras de firewall e de VNET (rede virtual) estiverem configuradas na conta de armazenamento, o tráfego de rede terá acesso negado a menos que o endereço IP do cliente ou da rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, Erro 67 ou Erro 87 ao montar ou desmontar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos do local ou de um datacenter diferente, você poderá receber os seguintes erros:

- Ocorreu um erro de sistema 53. O caminho da rede não foi encontrado.
- Ocorreu um erro de sistema 67. O nome de rede não foi encontrado.
- Ocorreu um erro de sistema 87. O parâmetro está incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: A porta 445 está bloqueada

Pode ocorrer um erro de sistema 53 ou erro de sistema 67 se a comunicação de saída na porta 445 para o datacenter de Arquivos do Azure estiver bloqueada. Para ver o resumo de ISPs que permitem ou proíbem o acesso a partir da porta 445, vá para [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se o firewall ou ISP está bloqueando a porta 445, use a ferramenta [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ou o cmdlet `Test-NetConnection`. 

Para usar o `Test-NetConnection` cmdlet, o Azure PowerShell módulo deve ser instalado, consulte [instalar o Azure PowerShell módulo](/powershell/azure/install-Az-ps) para obter mais informações. Lembre-se de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes referentes a sua conta de armazenamento.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Se a conexão foi bem-sucedida, você verá a seguinte saída:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> O comando acima retorna o endereço IP atual da conta de armazenamento. Não há garantia de que esse endereço IP será sempre o mesmo; ele pode mudar a qualquer momento. Não codifique esse endereço IP em um script ou em uma configuração de firewall.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1: usar a sincronização de arquivos do Azure
A sincronização de arquivos do Azure pode transforma seu servidor do Windows no local em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. A sincronização de arquivos do Azure funciona pela porta 443 e, portanto, pode ser usada como uma solução alternativa para acessar arquivos do Azure dos clientes que têm a porta 445 bloqueado. [Saiba como configurar a sincronização de arquivos do Azure a](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solução 2: uso VPN
Ao configurar uma VPN para sua conta de armazenamento específico, o tráfego passará por meio de um túnel seguro e não pela internet. Siga as [instruções para configurar VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) para acessar os arquivos do Azure do Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3: desbloquear a porta 445 com a Ajuda de seu ISP / administrador de TI
Trabalhar com seu departamento de TI ou ISP para abrir a porta 445 com saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 – usar a API REST com base em ferramentas como o Gerenciador de armazenamento/Powershell
Os arquivos do Azure também dá suporte a REST, além de SMB. O acesso REST funciona pela porta 443 (tcp padrão). Há várias ferramentas que são escritas usando a API REST que permitem a rica experiência de interface do usuário. [O Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) é um deles. [Baixar e instalar o Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) e conecte-se ao compartilhamento de arquivo com suporte pelos arquivos do Azure. Você também pode usar [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) que também usuário API REST.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está habilitado

O erro de sistema 53 ou erro de sistema 87 também pode ocorrer se a comunicação NTLMv1 está habilitada no cliente. Os Arquivos do Azure dão suporte apenas a autenticação NTLMv2. Ter NTLMv1 habilitado faz com que o cliente esteja menos seguro. Portanto, a comunicação será bloqueada para Arquivos do Azure. 

Para verificar se essa é a causa do erro, verifique se a seguinte subchave do registro está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para saber mais, confira o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) no TechNet.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Reverter o valor **LmCompatibilityLevel** para o valor padrão de 3 na seguinte subchave do registro:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "Não há cota suficiente disponível para processar este comando" quando você copia para um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

O Erro 1816 ocorre quando você atingir o limite superior de identificadores abertos simultâneos permitidos para um arquivo no computador onde o compartilhamento de arquivos está sendo montado.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e tentando novamente. Para obter mais informações, consulte [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para exibir os identificadores abertos em um compartilhamento de arquivo, diretório ou arquivo, use o [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) cmdlet do PowerShell.  

Para fechar identificadores abertos em um compartilhamento de arquivo, diretório ou arquivo, use o [AzStorageFileHandle fechar](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) cmdlet do PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e fechar AzStorageFileHandle estão incluídos no Az módulo do PowerShell versão 2.4 ou posterior. Para instalar o módulo do PowerShell de Az mais recente, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Erro "Falha de autorização" durante a navegação em um compartilhamento de arquivos do Azure no portal

A navegar até um compartilhamento de arquivos do Azure no portal, você pode receber o erro a seguir:

Falha de autorização  
Você não tem acesso 

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 1: Sua conta de usuário não tem acesso à conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para a causa 1

Navegue até a conta de armazenamento onde o compartilhamento de arquivos do Azure está localizado, clique em **Controle de acesso (IAM)** e verifique se sua conta de usuário tem acesso à conta de armazenamento. Para saber mais, confira [Como proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Não é possível excluir um arquivo ou diretório em um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa
Esse problema normalmente ocorre se o arquivo ou diretório tem um identificador aberto. 

### <a name="solution"></a>Solução

Se os clientes SMB tem fechado todos os identificadores abertos e o problema persistir, execute o seguinte:

- Use o [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) cmdlet do PowerShell para exibir os identificadores abertos.

- Use o [AzStorageFileHandle fechar](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) cmdlet do PowerShell para fechar identificadores abertos. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e fechar AzStorageFileHandle estão incluídos no Az módulo do PowerShell versão 2.4 ou posterior. Para instalar o módulo do PowerShell de Az mais recente, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Windows

Você pode ver o desempenho lento ao tentar transferir arquivos para o Serviço de Arquivos do Azure.

- Se você não tiver um requisito mínimo de tamanho de E / S específico, recomendamos usar 1 MiB como o tamanho de E / S para um desempenho ideal.
-   Se você sabe o tamanho final de um arquivo que você está estendendo com gravações e o seu software não tem problemas de compatibilidade com o final ainda não escrito desse arquivo que contém zeros, defina o tamanho do arquivo antecipadamente em vez de realizar cada gravação como uma gravação de extensão.
-   Use o método de cópia correto:
    -   Use o [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para todas as transferências entre dois compartilhamentos de arquivo.
    -   Use o [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre um compartilhamento de arquivos e um computador local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para Windows 8.1 ou Windows Server 2012 R2

Para clientes que estão executando o Windows 8.1 ou o Windows Server 2012 R2, verifique se o hotfix [KB3114025](https://support.microsoft.com/help/3114025) está instalado. Esse hotfix melhora o desempenho dos identificadores de criação e fechamento.

Você pode executar o script abaixo para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix foi instalado, a seguinte saída será exibida:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão desde dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nenhuma pasta com uma letra de unidade em "Meu computador" ou "Este computador"

Se você mapear um compartilhamento de arquivos do Azure como administrador por meio do uso de rede, o compartilhamento parece estar ausente.

### <a name="cause"></a>Causa

Por padrão, o Explorador de Arquivos do Windows não é executado como administrador. Se você executar net use em um prompt de comando de administrador, mapeará a unidade de rede como um administrador. Como as unidades mapeadas são focadas no usuário, a conta de usuário que está conectada não exibe as unidades se eles estão montadas em uma conta de usuário diferente.

### <a name="solution"></a>Solução
Monte o compartilhamento de uma linha de comando de não administrador. Como alternativa, você pode seguir [este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor do registro **EnableLinkedConnections**.

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

Utilize uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de usuário que contém o aplicativo. Você pode usar uma ferramenta como o PsExec.
- Passe o nome da conta de armazenamento e a chave nos parâmetros de nome do usuário e senha do comando net use.
- Use o comando cmdkey para adicionar as credenciais no Gerenciador de Credenciais. Execute isso em uma linha de comando no contexto de conta de serviço, por meio de um logon interativo ou usando Executar como.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapeie o compartilhamento diretamente sem usar uma letra de unidade mapeada. Alguns aplicativos podem não se reconectar à letra da unidade corretamente, portanto, usar o caminho UNC completo pode ser mais confiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir essas instruções, você pode receber a seguinte mensagem de erro ao executar o net use para a conta de serviço de rede/sistema: "Ocorreu um erro de sistema 1312. Uma sessão de logon especificada não existe. Talvez ela já tenha sido finalizada.” Se isso ocorrer, verifique se o nome de usuário que é passado para net use inclui informações de domínio (por exemplo: "[nome da conta de armazenamento].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia"

Quando um arquivo é copiado pela rede, o arquivo é descriptografado no computador de origem, transmitido em texto não criptografado e criptografado novamente no destino. No entanto, você poderá ver o seguinte erro quando estiver tentando copiar um arquivo criptografado: "Você está copiando o arquivo para um destino que não dá suporte à criptografia."

### <a name="cause"></a>Causa
Esse problema pode ocorrer se você estiver usando o sistema de arquivos com criptografia (EFS). Os arquivos criptografados pelo BitLocker podem ser copiados para os Arquivos do Azure. No entanto, os Arquivos do Azure não dão suporte a EFS NTFS.

### <a name="workaround"></a>Solução alternativa
Para copiar um arquivo pela rede, você deve primeiro descriptografá-lo. Use um dos seguintes métodos:

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

- Localização: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Nome do valor: DirectoryCacheEntrySizeMax 
- Tipo de valor: DWORD
 
 
Por exemplo, você pode defini-lo como 0x100000 e ver se o desempenho se torna melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Erro AadDsTenantNotFound na habilitação da autenticação do Azure Active Directory para Arquivos do Azure, "Não é possível localizar locatários Active Directory com o locatário Id aad-tenant-id"

### <a name="cause"></a>Causa

O erro AadDsTenantNotFound acontece quando você tenta [habilitar a autenticação do AAD (Azure Active Directory) para Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) em uma conta de armazenamento em que o [AAD DS (Azure Active Directory Domain Services)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) não é criado no locatário do AAD da assinatura associada.  

### <a name="solution"></a>Solução

Habilite o AAD DS no locatário do AAD da assinatura na qual a conta de armazenamento é implantada. Você precisa de privilégios de administrador do locatário do AAD para criar um domínio gerenciado. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
