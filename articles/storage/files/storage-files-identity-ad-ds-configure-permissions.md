---
title: Controlar o que um usuário pode fazer no nível de arquivo-compartilhamentos de arquivos do Azure
description: Saiba como configurar permissões de ACLs do Windows para autenticação de AD DS local para compartilhamentos de arquivos do Azure. Permitindo que você aproveite o controle de acesso granular.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 02b8d72ab88f9eca2e1fac4858c14826dae57dbe
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629165"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Parte três: configurar permissões de diretório e de nível de arquivo sobre SMB 

Antes de começar este artigo, verifique se você concluiu o artigo anterior, [atribua permissões de nível de compartilhamento a uma identidade](storage-files-identity-ad-ds-assign-permissions.md) para garantir que suas permissões de nível de compartilhamento estejam em vigor.

Depois de atribuir permissões de nível de compartilhamento com o RBAC do Azure, você deve configurar ACLs apropriadas do Windows no nível de raiz, diretório ou arquivo, para tirar proveito do controle de acesso granular. Considere as permissões de nível de compartilhamento do RBAC do Azure como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento. Enquanto as ACLs do Windows operam em um nível mais granular para determinar quais operações o usuário pode fazer no nível do diretório ou do arquivo. As permissões no nível de compartilhamento e de arquivo/diretório são impostas quando um usuário tenta acessar um arquivo/diretório, portanto, se houver uma diferença entre qualquer uma delas, somente a mais restritiva será aplicada. Por exemplo, se um usuário tiver acesso de leitura/gravação no nível de arquivo, mas somente leitura em um nível de compartilhamento, ele só poderá ler esse arquivo. O mesmo seria verdadeiro se ele fosse invertido e um usuário tivesse acesso de leitura/gravação no nível de compartilhamento, mas somente leitura no nível de arquivo, ele ainda poderá ler o arquivo.

## <a name="azure-rbac-permissions"></a>Permissões do RBAC do Azure

A tabela a seguir contém as permissões RBAC do Azure relacionadas a esta configuração:


| Função interna  | Permissão NTFS  | Acesso resultante  |
|---------|---------|---------|
|Leitor de Compartilhamento SMB de Dados do Arquivo de Armazenamento | Controle total, modificar, ler, gravar, executar | Ler e executar  |
|     |   Ler |     Ler  |
|Colaborador de Compartilhamento SMB de Dados do Arquivo de Armazenamento  |  Controle total    |  Modificar, ler, gravar, executar |
|     |  Modificar         |  Modificar    |
|     |  Ler e executar |  Ler e executar |
|     |  Ler           |  Ler    |
|     |  Gravar          |  Gravar   |
|Colaborador com Privilégios Elevados do Compartilhamento SMB de Dados do Arquivo de Armazenamento | Controle total  |  Modificar, ler, gravar, editar, executar |
|     |  Modificar          |  Modificar |
|     |  Ler e executar  |  Ler e executar |
|     |  Ler            |  Ler   |
|     |  Gravar           |  Gravar  |



## <a name="supported-permissions"></a>Permissões com suporte

Os arquivos do Azure dão suporte ao conjunto completo de ACLs básicas e avançadas do Windows. Você pode exibir e configurar ACLs do Windows em diretórios e arquivos em um compartilhamento de arquivos do Azure montando o compartilhamento e usando o explorador de arquivos do Windows, executando o comando [icacls](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Para configurar ACLs com permissões de superusuário, você deve montar o compartilhamento usando sua chave de conta de armazenamento de sua VM ingressada no domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos do Azure no prompt de comando e configurar ACLs do Windows.

As seguintes permissões estão incluídas no diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|Usuários|Definição|
|---|---|
|BUILTIN\Administradores|Todos os usuários que são administradores de domínio do ambiente de AD DS local.
|BUILTIN\Users|Grupo de segurança interno no AD. Ele inclui usuários do NT AUTHORITY\Authenticated por padrão. Para um servidor de arquivos tradicional, você pode configurar a definição de associação por servidor. Para arquivos do Azure, não há um servidor de hospedagem, portanto, BUILTIN\Users inclui o mesmo conjunto de usuários que o NT AUTHORITY\Authenticated users.|
|NT AUTHORITY\SYSTEM|A conta de serviço do sistema operacional do servidor de arquivos. Essa conta de serviço não se aplica ao contexto de arquivos do Azure. Ele está incluído no diretório raiz para ser consistente com a experiência do servidor de arquivos do Windows para cenários híbridos.|
|Usuários do NT AUTHORITY\Authenticated|Todos os usuários no AD que podem obter um token Kerberos válido.|
|CREATOR OWNER|Cada objeto de diretório ou arquivo tem um proprietário para esse objeto. Se houver ACLs atribuídas a "proprietário do criador" nesse objeto, o usuário que for o proprietário desse objeto terá as permissões para o objeto definido pela ACL.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Montar um compartilhamento de arquivos do Azure no prompt de comando

Use o comando do Windows `net use` para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado no exemplo a seguir pelos seus próprios valores. Para obter mais informações sobre como montar compartilhamentos de arquivos, consulte [usar um compartilhamento de arquivos do Azure com o Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se você tiver problemas ao conectar-se aos arquivos do Azure, consulte [a ferramenta de solução de problemas que publicamos para erros de montagem de arquivos do Azure no Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Também fornecemos [orientações](./storage-files-faq.md#on-premises-access) para contornar cenários quando a porta 445 é bloqueada. 

## <a name="configure-windows-acls"></a>Configurar ACLs do Windows

Depois que o compartilhamento de arquivos tiver sido montado com a chave da conta de armazenamento, você deverá configurar as ACLs do Windows (também conhecidas como permissões NTFS). Você pode configurar as ACLs do Windows usando o explorador de arquivos do Windows ou o icacls.

Se você tiver diretórios ou arquivos em servidores de arquivos locais com DACLs do Windows configuradas em relação às identidades de AD DS, você poderá copiá-los para arquivos do Azure, mantendo as ACLs com ferramentas de cópia de arquivo tradicionais, como Robocopy ou [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Se seus diretórios e arquivos estiverem em camadas para arquivos do Azure por meio de Sincronização de Arquivos do Azure, suas ACLs serão transferidas e persistidas em seu formato nativo.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Configurar ACLs do Windows com o explorador de arquivos do Windows

Use o explorador de arquivos do Windows para conceder permissão total a todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz.

1. Abra o explorador de arquivos do Windows e clique com o botão direito do mouse no arquivo/diretório e selecione **Propriedades**.
1. Selecione a guia **Segurança**.
1. Selecione **Editar...** para alterar permissões.
1. Você pode alterar as permissões de usuários existentes ou selecionar **Adicionar...** para conceder permissões a novos usuários.
1. Na janela de prompt para adicionar novos usuários, insira o nome de usuário de destino ao qual você deseja conceder permissões na caixa **Inserir os nomes de objeto a serem selecionados** e selecione **verificar nomes** para localizar o nome UPN completo do usuário de destino.
1.    Selecione **OK**.
1.    Na guia **segurança** , selecione todas as permissões que você deseja conceder ao novo usuário.
1.    Escolha **Aplicar**.

### <a name="configure-windows-acls-with-icacls"></a>Configurar ACLs do Windows com icacls

Use o seguinte comando do Windows para conceder permissões completas para todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar o icacls para definir ACLs do Windows e sobre os diferentes tipos de permissões com suporte, consulte [a referência de linha de comando para icacls](/windows-server/administration/windows-commands/icacls).

## <a name="next-steps"></a>Próximas etapas

Agora que o recurso está habilitado e configurado, prossiga para o próximo artigo, no qual você monta o compartilhamento de arquivos do Azure de uma VM ingressada no domínio.

[Parte quatro: montar um compartilhamento de arquivos de uma VM ingressada no domínio](storage-files-identity-ad-ds-mount-file-share.md)