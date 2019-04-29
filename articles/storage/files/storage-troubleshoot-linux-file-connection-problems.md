---
title: Solucionar problemas de Arquivos do Azure no Linux | Microsoft Docs
description: Solução de problemas de Arquivos do Azure no Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 09898ac7dd4a6f3ee9cf0ea26ded607a8673b9f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438237"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Solucionar problemas de Arquivos do Azure no Linux

Este artigo lista os problemas comuns relacionados aos Arquivos do Azure quando você se conecta de clientes Linux. Também fornece as possíveis causas e resoluções para esses problemas. 

Além das etapas de solução de problemas deste artigo, você pode usar [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) para garantir que o cliente Linux tenha pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo. Isso ajuda a configurar seu ambiente para obter um desempenho ideal. Você também pode encontrar essas informações na solução de problemas do [Compartilhamento de arquivos do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). A solução de problemas fornece etapas para ajudá-lo com problemas de conexão, o mapeamento e montar os compartilhamentos de arquivos do Azure.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Error de montagem(13): Permissão negada" quando você monta um compartilhamento de arquivos do Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não criptografado

Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo datacenter onde residem os compartilhamentos de arquivos do Azure. As conexões não criptografadas dentro do mesmo datacenter também podem ser bloqueadas se o [transferência segura obrigatória](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está habilitada na conta de armazenamento. Um canal de comunicação criptografado é fornecido somente quando o sistema operacional do cliente do usuário dá suporte à criptografia SMB.

Para saber mais, confira [Pré-requisitos para montar um compartilhamento de arquivos do Azure com o Linux e o pacote cifs-utils](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Solução para a causa 1

1. Conecte-se de um cliente com suporte à criptografia SMB ou conecte a partir de uma máquina virtual que está no mesmo datacenter da conta de armazenamento do Azure usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [Transferência segura obrigatória](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Se as regras de firewall e de VNET (rede virtual) estiverem configuradas na conta de armazenamento, o tráfego de rede terá acesso negado a menos que o endereço IP do cliente ou da rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>“[permissão negada] Cota de disco excedida” ao tentar abrir um arquivo

No Linux, você recebe uma mensagem de erro semelhante à seguinte:

**\<nome do arquivo > [permissão negada] cota de disco excedida**

### <a name="cause"></a>Causa

Você atingiu o limite máximo de identificadores abertos simultâneos permitidos para um arquivo.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns deles e repita a operação. Para obter mais informações, consulte [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Linux

- Se você não tiver um requisito mínimo de tamanho de E / S específico, recomendamos usar 1 MiB como o tamanho de E / S para um desempenho ideal.
- Se você sabe o tamanho final de um arquivo que está sendo estendido usando gravações, e seu software não apresenta problemas de compatibilidade quando uma cauda não escrita no arquivo contém zeros, defina o tamanho do arquivo antecipadamente em vez de tornar cada gravação estendendo a gravação.
- Use o método de cópia correto:
    - Use o [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para todas as transferências entre dois compartilhamentos de arquivo.
    - Use o [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre compartilhamentos de arquivos e um computador local.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Erro de montagem(112): o host está inativo" devido a um tempo limite de reconexão

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
- [CIFS: corrija um possível bloqueio duplo de mutex durante a reconexão (para kernel v4.9 e posterior)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, essas alterações ainda podem não ter sido portadas para todas as distribuições do Linux. Esta correção e outras correções de reconexão estão nos seguintes kernels populares do Linux: 4.4.40, 4.8.16 e 4.9.1. Obtenha essa correção fazendo upgrade para uma dessas versões recomendadas de kernel.

### <a name="workaround"></a>Solução alternativa

Resolva esse problema especificando uma montagem rígida. Uma montagem rígida força o cliente a esperar até que uma conexão seja estabelecida ou até que seja explicitamente interrompida. Você pode usá-lo para evitar erros devido a tempos limite da rede. No entanto, essa solução alternativa pode causar esperas indefinidas. Esteja preparado para interromper conexões, conforme necessário.

Se você não pode atualizar para as versões mais recentes do kernel, você pode contornar esse problema mantendo um arquivo no compartilhamento de arquivos do Azure que você grava a cada 30 segundos ou menos. Essa deve ser uma operação de gravação, como regravar a data de criação ou de modificação no arquivo. Caso contrário, você poderá obter resultados em cache e a operação poderá não disparar a reconexão.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Erro de montagem(115): a operação está em andamento" durante a montagem dos Arquivos do Azure usando o SMB 3.0

### <a name="cause"></a>Causa

Algumas distribuições Linux ainda não suportam recursos de criptografia no SMB 3.0. Os usuários podem receber uma mensagem de erro "115" se tentarem montar arquivos do Azure usando o SMB 3.0 devido a um recurso ausente. O SMB 3.0 com criptografia completa é suportado apenas quando você está usando o Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

O recurso de criptografia para SMB 3.0 para Linux foi introduzido no kernel 4.11. Esse recurso permite a montagem de um compartilhamento de arquivos do Azure de local ou de uma região diferente do Azure. No momento da publicação deste artigo, essa funcionalidade foi retrocompatibilizada para o Ubuntu 17.04 e Ubuntu 16.10. 

Se o seu cliente SMB do Linux não oferecer suporte à criptografia, monte os arquivos do Azure usando o SMB 2.1 de uma VM do Azure Linux que esteja no mesmo datacenter do compartilhamento de arquivos. Verifique se a configuração [Transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desativada na conta de armazenamento. 

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Erro "Acesso negado" ao procurar um compartilhamento de arquivos do Azure no portal

A navegar até um compartilhamento de arquivos do Azure no portal, você pode receber o erro a seguir:

Acesso negado  
Você não tem acesso  
Parece que você não tem acesso a este conteúdo. Para obter acesso, contate o proprietário.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 1: Sua conta de usuário não tem acesso à conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para a causa 1

Navegue até a conta de armazenamento onde o compartilhamento de arquivos do Azure está localizado, clique em **Controle de acesso (IAM)** e verifique se sua conta de usuário tem acesso à conta de armazenamento. Para saber mais, confira [Como proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: As regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento em um compartilhamento de arquivos do Azure montado em uma VM Linux

### <a name="cause"></a>Causa

Uma possível causa da lentidão no desempenho é o cache desabilitado.

### <a name="solution"></a>Solução

Para verificar se o cache está desabilitado, procure a entrada **cache=**. 

**Cache=none** indica que o cache está desabilitado. Remonte o compartilhamento usando o comando de montagem padrão ou adicionando explicitamente a opção **cache=strict** ao comando de montagem para garantir que o modo de cache padrão ou de cache “strict” seja habilitado.

Em alguns cenários, a opção de montagem **serverino** pode fazer com que o comando **ls** execute stat em cada entrada do diretório. Esse comportamento resulta na degradação do desempenho quando você está listando um diretório grande. Verifique as opções de montagem na entrada **/etc/fstab**:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Você também pode verificar se as opções corretas estão sendo usadas executando o **sudo mount | grep cifs** comando e verificando sua saída. A seguir está a saída de exemplo:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Se a opção **cache=strict** ou **serverino** não estiver presente, desmonte e monte os Arquivos do Azure novamente executando o comando de montagem da [documentação](../storage-how-to-use-files-linux.md). Em seguida, verifique novamente se a entrada **/etc/fstab** tem as opções corretas.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Os carimbos de data/hora foram perdidos durante a cópia de arquivos do Windows para o Linux

Em plataformas Linux / Unix, o comando **cp -p** falha se usuários diferentes possuírem o arquivo 1 e o arquivo 2.

### <a name="cause"></a>Causa

O sinalizador de força **f** em COPYFILE resulta na execução de **cp -p -f** no Unix. Esse comando também não preserva o carimbo de data/hora do arquivo que você não possui.

### <a name="workaround"></a>Solução alternativa

Use o usuário da conta de armazenamento para copiar os arquivos:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Não é possível se conectar a ou montar um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

Causas comuns para esse problema são:


- Você está usando um cliente de distribuição Linux incompatível. Recomendamos que você use as seguintes distribuições do Linux para se conectar a um compartilhamento de arquivos do Azure:

    |   | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens de local e entre regiões) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Os utilitários CIFS (cfs-utils) não estão instalados no cliente.
- A versão mínima do SMB / CIFS, 2.1, não está instalada no cliente.
- A criptografia SMB 3.0 não é suportada no cliente. A criptografia SMB 3.0 está disponível no Ubuntu 16.4 e em versões posteriores, junto com o SUSE 12.3 e versões posteriores. Outras distribuições requerem o kernel 4.11 e versões posteriores.
- Você está tentando se conectar a uma conta de armazenamento pela porta TCP 445, que não é suportada.
- Você está tentando se conectar a um compartilhamento de arquivos do Azure de uma VM do Azure, e a VM não está na mesma região que a conta de armazenamento.
- Se a configuração [Transferência segura necessária]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) estiver ativada na conta de armazenamento, os Arquivos do Azure só permitirão conexões que usem o SMB 3.0 com criptografia.

### <a name="solution"></a>Solução

Para resolver o problema, use o [ferramenta de solução de problemas para os arquivos do Azure erros de montagem no Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Essa ferramenta:

* Ajuda a validar o ambiente de execução de cliente.
* Detecta a configuração de cliente incompatível que causaria falha de acesso para arquivos do Azure.
* Dá orientação prescritiva sobre auto-fixação.
* Coleta os rastreamentos de diagnóstico.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>Is: não é possível acessar "&lt;caminho&gt;": Erro de entrada/saída

Quando você tenta listar arquivos em um compartilhamento de arquivos do Azure usando o comando ls, o comando trava ao listar arquivos. Você obterá o seguinte erro:

**Is: não é possível acessar "&lt;caminho&gt;": Erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel do Linux para as seguintes versões que possuem uma correção para esse problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que são maiores ou iguais a 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Não é possível criar links simbólicos - ln: falha ao criar link simbólico "t": Operação sem suporte

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

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
