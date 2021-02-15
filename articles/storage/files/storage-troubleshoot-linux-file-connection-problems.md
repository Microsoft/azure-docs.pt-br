---
title: Solucionar problemas de Arquivos do Azure no Linux | Microsoft Docs
description: Solução de problemas de arquivos do Azure no Linux. Consulte problemas comuns relacionados aos arquivos do Azure quando você se conecta de clientes Linux e veja possíveis resoluções.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e680ba10c507ef83591b56652ee8e95c4d665dda
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492056"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>Solucionar problemas de arquivos do Azure no Linux (SMB)

Este artigo lista os problemas comuns relacionados aos Arquivos do Azure quando você se conecta de clientes Linux. Também fornece as possíveis causas e resoluções para esses problemas. 

Além das etapas de solução de problemas deste artigo, você pode usar [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) para garantir que o cliente Linux tenha pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo. Isso ajuda a configurar seu ambiente para obter um desempenho ideal. Você também pode encontrar essas informações na solução de problemas do [Compartilhamento de arquivos do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). A solução de problemas fornece etapas para ajudá-lo com problemas de conexão, o mapeamento e montar os compartilhamentos de arquivos do Azure.

> [!IMPORTANT]
> O conteúdo deste artigo se aplica somente a compartilhamentos SMB. Para obter detalhes sobre compartilhamentos NFS, consulte [solucionar problemas de compartilhamentos de arquivos NFS](storage-troubleshooting-files-nfs.md)

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Não é possível se conectar a ou montar um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

Causas comuns para esse problema são:

- Você está usando um cliente de distribuição Linux incompatível. Recomendamos que você use as seguintes distribuições do Linux para se conectar a um compartilhamento de arquivos do Azure:

|   | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens de local e entre regiões) |
| --- | :---: | :---: |
| **Ubuntu Server** | 14.04+ | 16.04+ |
| **RHEL** | 7+ | 7.5+ |
| **CentOS** | 7+ |  7.5+ |
| **Debian** | Mais de 8 |   |
| **openSUSE** | 13.2+ | 42.3+ |
| **SUSE Linux Enterprise Server** | 12 | 12 SP3+ |

- Os utilitários CIFS (CIFS-utils) não estão instalados no cliente.
- A versão mínima do SMB / CIFS, 2.1, não está instalada no cliente.
- A criptografia SMB 3.0 não é suportada no cliente. A tabela anterior fornece uma lista de distribuições do Linux que dão suporte à montagem do local e entre regiões usando criptografia. Outras distribuições requerem o kernel 4.11 e versões posteriores.
- Você está tentando se conectar a uma conta de armazenamento pela porta TCP 445, que não é suportada.
- Você está tentando se conectar a um compartilhamento de arquivos do Azure de uma VM do Azure, e a VM não está na mesma região que a conta de armazenamento.
- Se a configuração [Transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento, os Arquivos do Azure só permitirão conexões que usem o SMB 3.0 com criptografia.

### <a name="solution"></a>Solução

Para resolver o problema, use o [ferramenta de solução de problemas para os arquivos do Azure erros de montagem no Linux](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux). Essa ferramenta:

* Ajuda a validar o ambiente de execução de cliente.
* Detecta a configuração de cliente incompatível que causaria falha de acesso para arquivos do Azure.
* Dá orientação prescritiva sobre auto-fixação.
* Coleta os rastreamentos de diagnóstico.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Erro de montagem (13): permissão negada" ao montar um compartilhamento de arquivos do Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não criptografado

Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo datacenter onde residem os compartilhamentos de arquivos do Azure. As conexões não criptografadas dentro do mesmo datacenter também podem ser bloqueadas se o [transferência segura obrigatória](../common/storage-require-secure-transfer.md) está habilitada na conta de armazenamento. Um canal de comunicação criptografado é fornecido somente quando o sistema operacional do cliente do usuário dá suporte à criptografia SMB.

Para saber mais, confira [Pré-requisitos para montar um compartilhamento de arquivos do Azure com o Linux e o pacote cifs-utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Solução para a causa 1

1. Conecte-se de um cliente com suporte à criptografia SMB ou conecte a partir de uma máquina virtual que está no mesmo datacenter da conta de armazenamento do Azure usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [Transferência segura obrigatória](../common/storage-require-secure-transfer.md) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Se as regras de firewall e de VNET (rede virtual) estiverem configuradas na conta de armazenamento, o tráfego de rede terá acesso negado a menos que o endereço IP do cliente ou da rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](../common/storage-network-security.md).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>“[permissão negada] Cota de disco excedida” ao tentar abrir um arquivo

No Linux, você recebe uma mensagem de erro semelhante à seguinte:

**\<filename> [permissão negada] Cota de disco excedida**

### <a name="cause"></a>Causa

Você atingiu o limite máximo de identificadores abertos simultâneos que são permitidos para um arquivo ou diretório.

Há uma cota de 2.000 identificadores abertos em um único arquivo ou diretório. Quando você tem 2.000 identificadores abertos, uma mensagem de erro é exibida informando que a cota foi atingida.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns deles e repita a operação.

Para exibir identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) do PowerShell.  

Para fechar identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) do PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Linux

- Se você não tiver um requisito mínimo de tamanho de E / S específico, recomendamos usar 1 MiB como o tamanho de E / S para um desempenho ideal.
- Use o método de cópia correto:
    - Use [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre dois compartilhamentos de arquivos.
    - Usar CP ou DD com Parallel pode melhorar a velocidade de cópia, o número de threads depende do seu caso de uso e da carga de trabalho. Os exemplos a seguir usam seis: 
    - exemplo de CP (CP usará o tamanho de bloco padrão do sistema de arquivos como o tamanho da parte): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &` .
    - exemplo de DD (este comando define explicitamente o tamanho da parte como 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Ferramentas de terceiros de código aberto, como:
        - [GNU Parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) -classifica os arquivos e os compacta em partições.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -usa fpart e uma ferramenta de cópia para gerar várias instâncias para migrar dados de src_dir para dst_url.
        - [Vários](https://github.com/pkolano/mutil) multithreaded CP e md5sum com base no GNU coreutils.
- Definir o tamanho do arquivo com antecedência, em vez de fazer cada gravação de uma gravação de extensão, ajuda a melhorar a velocidade de cópia em cenários em que o tamanho do arquivo é conhecido. Se for necessário evitar gravações estendidas, você poderá definir um tamanho de arquivo de destino com o `truncate - size <size><file>` comando. Depois disso, `dd if=<source> of=<target> bs=1M conv=notrunc` o comando copiará um arquivo de origem sem precisar atualizar repetidamente o tamanho do arquivo de destino. Por exemplo, você pode definir o tamanho do arquivo de destino para cada arquivo que deseja copiar (Suponha que um compartilhamento seja montado em/mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - e, em seguida, copiar arquivos sem estender gravações em paralelo: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>“Erro de montagem (115): operação em andamento” durante a montagem dos Arquivos do Azure usando o SMB 3.0

### <a name="cause"></a>Causa

Algumas distribuições Linux ainda não suportam recursos de criptografia no SMB 3.0. Os usuários podem receber uma mensagem de erro "115" se tentarem montar arquivos do Azure usando o SMB 3.0 devido a um recurso ausente. O SMB 3.0 com criptografia completa é suportado apenas quando você está usando o Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

O recurso de criptografia para SMB 3.0 para Linux foi introduzido no kernel 4.11. Esse recurso permite a montagem de um compartilhamento de arquivos do Azure de local ou de uma região diferente do Azure. Algumas distribuições do Linux podem ter alterações reportadas do kernel 4,11 para versões mais antigas do kernel do Linux que eles mantêm. Para ajudar a determinar se sua versão do Linux dá suporte a SMB 3,0 com criptografia, consulte [usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md). 

Se o seu cliente SMB do Linux não oferecer suporte à criptografia, monte os arquivos do Azure usando o SMB 2.1 de uma VM do Azure Linux que esteja no mesmo datacenter do compartilhamento de arquivos. Verifique se a configuração [Transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desativada na conta de armazenamento. 

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
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Não é possível excluir um arquivo ou diretório em um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa
Esse problema normalmente ocorre se o arquivo ou diretório tiver um identificador aberto. 

### <a name="solution"></a>Solução

Se os clientes SMB tiverem fechado todos os identificadores abertos e o problema continuar ocorrendo, execute o seguinte:

- Use o cmdlet do PowerShell [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) para exibir identificadores abertos.

- Use o cmdlet do PowerShell [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) para fechar identificadores abertos. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e Close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento em um compartilhamento de arquivos do Azure montado em uma VM Linux

### <a name="cause-1-caching"></a>Causa 1: Caching

Uma possível causa da lentidão no desempenho é o cache desabilitado. O Caching pode ser útil se você estiver acessando um arquivo repetidamente, caso contrário, pode ser uma sobrecarga. Verifique se você está usando o cache antes de desabilitá-lo.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

Para verificar se o cache está desabilitado, procure a entrada **cache=**.

**Cache=none** indica que o cache está desabilitado. Remonte o compartilhamento usando o comando de montagem padrão ou adicionando explicitamente a opção **cache=strict** ao comando de montagem para garantir que o modo de cache padrão ou de cache “strict” seja habilitado.

Em alguns cenários, a opção de montagem **serverino** pode fazer com que o comando **ls** execute stat em cada entrada do diretório. Esse comportamento resulta em degradação de desempenho quando você está listando um diretório grande. Verifique as opções de montagem na entrada **/etc/fstab**:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Você também pode verificar se as opções corretas estão sendo usadas executando o **sudo mount | grep cifs** comando e verificando sua saída. A seguir está a saída de exemplo:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Se a opção **cache=strict** ou **serverino** não estiver presente, desmonte e monte os Arquivos do Azure novamente executando o comando de montagem da [documentação](./storage-how-to-use-files-linux.md). Em seguida, verifique novamente se a entrada **/etc/fstab** tem as opções corretas.

### <a name="cause-2-throttling"></a>Causa 2: limitação

É possível que você esteja enfrentando a limitação e que suas solicitações estejam sendo enviadas para uma fila. Você pode verificar isso aproveitando as [métricas de armazenamento do Azure no Azure monitor](../blobs/monitor-blob-storage.md).

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se seu aplicativo está dentro dos [destinos de escala de arquivos do Azure](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Os carimbos de data/hora foram perdidos durante a cópia de arquivos do Windows para o Linux

Em plataformas Linux / Unix, o comando **cp -p** falha se usuários diferentes possuírem o arquivo 1 e o arquivo 2.

### <a name="cause"></a>Causa

O sinalizador de força **f** em CopyFile resulta na execução de **CP-p-f** no UNIX. Esse comando também não preserva o carimbo de data/hora do arquivo que você não possui.

### <a name="workaround"></a>Solução alternativa

Use o usuário da conta de armazenamento para copiar os arquivos:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: não é possível acessar '&lt;caminho&gt;': erro de entrada/saída

Quando você tenta listar arquivos em um compartilhamento de arquivos do Azure usando o comando ls, o comando trava ao listar arquivos. Você obterá o seguinte erro:

**ls: não é possível acessar '&lt;caminho&gt;': erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel do Linux para as seguintes versões que possuem uma correção para esse problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que são maiores ou iguais a 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Não é possível criar links simbólicos - ln: falhou ao criar link simbólico 't': Operação não suportada

### <a name="cause"></a>Causa
Por padrão, a montagem de compartilhamentos de arquivos do Azure no Linux usando o CIFS não habilita o suporte a links simbólicos (links simbólicos). Você verá um erro como este:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Solução
O cliente CIFS do Linux não suporta a criação de links simbólicos no estilo do Windows sobre o protocolo SMB 2 ou 3. Atualmente, o cliente Linux suporta outro estilo de links simbólicos chamado [Minshall + francês symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) para criar e seguir operações. Os clientes que precisam de links simbólicos podem usar a opção de montagem "mfsymlinks". Recomendamos "mfsymlinks" porque também é o formato que os Macs usam.

Para usar links simbólicos, adicione o seguinte ao final do seu comando de montagem CIFS:

```
,mfsymlinks
```

Então o comando parece algo como:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Em seguida, você pode criar links simbólicos como sugerido na [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>“Erro de montagem (112): o host está inativo” devido a um tempo limite de reconexão

Um erro de montagem “112” ocorre no cliente Linux quando o cliente ficou ocioso por um longo período. Após um tempo ocioso estendido, o cliente se desconecta e a conexão atinge o tempo limite.  

### <a name="cause"></a>Causa

A conexão pode ficar ociosa pelos seguintes motivos:

-   Falhas de comunicação de rede que impedem o restabelecimento de uma conexão TCP com o servidor quando a opção de montagem "soft" padrão é usada
-   Correções de reconexão recentes que não estão presentes nos kernels mais antigos

### <a name="solution"></a>Solução

Esse problema de reconexão no kernel do Linux agora foi corrigido como parte das seguintes alterações:

- [Corrigir a reconexão para não adiar a sessão smb3 por muito tempo após a reconexão do soquete](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Chamar o serviço de eco imediatamente após a reconexão do soquete](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: corrija uma possível corrupção de memória durante a reconexão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: corrigir um possível bloqueio duplo de mutex durante a reconexão (para kernel v4.9 e posterior)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, essas alterações ainda podem não ter sido portadas para todas as distribuições do Linux. Se você estiver usando uma distribuição do Linux popular, poderá verificar em [usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md) para ver qual versão da sua distribuição tem as alterações de kernel necessárias.

### <a name="workaround"></a>Solução alternativa

Resolva esse problema especificando uma montagem rígida. Uma montagem rígida força o cliente a esperar até que uma conexão seja estabelecida ou até que seja explicitamente interrompida. Você pode usá-lo para evitar erros devido a tempos limite da rede. No entanto, essa solução alternativa pode causar esperas indefinidas. Esteja preparado para interromper conexões, conforme necessário.

Se você não pode atualizar para as versões mais recentes do kernel, você pode contornar esse problema mantendo um arquivo no compartilhamento de arquivos do Azure que você grava a cada 30 segundos ou menos. Essa deve ser uma operação de gravação, como regravar a data de criação ou de modificação no arquivo. Caso contrário, você poderá obter resultados em cache e a operação poderá não disparar a reconexão.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: Error-22 no IOCTL para obter a lista de interfaces" ao montar um compartilhamento de arquivos do Azure usando SMB 3,0

### <a name="cause"></a>Causa
Esse erro é registrado porque os arquivos do Azure [atualmente não dão suporte ao SMB multicanal](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Solução
Este erro pode ser ignorado.


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>Não é possível acessar pastas ou arquivos cujo nome tem um espaço ou um ponto no final

Você não pode acessar pastas ou arquivos do compartilhamento de arquivos do Azure durante a montagem no Linux, comandos como du e ls e/ou aplicativos de terceiros podem falhar com um erro "não existe um arquivo ou diretório" ao acessar o compartilhamento, no entanto, você pode carregar arquivos para as pastas citadas por meio do Portal.

### <a name="cause"></a>Causa

As pastas ou arquivos foram carregados de um sistema que codifica os caracteres no final do nome para um caractere diferente, os arquivos carregados de um computador Macintosh podem ter um caractere "0xF028" ou "0xF029" em vez de 0x20 (espaço) ou 0X2E (ponto).

### <a name="solution"></a>Solução

Use a opção mapchars no compartilhamento ao montar o compartilhamento no Linux: 

Em vez de:

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

utilizá

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.