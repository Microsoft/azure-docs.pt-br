---
title: Usar um compartilhamento de arquivos do Azure com o Windows | Microsoft Docs
description: Aprenda a usar compartilhamentos de arquivos do Azure com o Windows e o Windows Server. Use compartilhamentos de arquivos do Azure com SMB 3,0 em instalações do Windows em execução localmente ou em VMs do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94626887"
---
# <a name="use-an-azure-file-share-with-windows"></a>Usar um compartilhamento de arquivos do Azure com o Windows
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser usados perfeitamente no Windows e no Windows Server. Este artigo aborda as considerações para usar um compartilhamento de arquivos do Azure com Windows e Windows Server.

Para usar um compartilhamento de Arquivos do Azure fora da região na qual o Azure está hospedado, por exemplo, localmente ou em uma região diferente do Azure, o sistema operacional deverá dar suporte a SMB 3.0. 

Você pode usar compartilhamentos de arquivos do Azure em uma instalação do Windows que esteja em execução em uma VM do Azure ou localmente. A tabela abaixo mostra quais versões do sistema operacional dão suporte ao acesso de compartilhamentos de arquivos em quais ambientes:

| Versão do Windows        | Versão do SMB | Montável na VM do Azure | Montável no local |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Sim | Sim |
| Windows 10<sup>1</sup> | SMB 3.0 | Sim | Sim |
| Canal semestral do Windows Server<sup>2</sup> | SMB 3.0 | Sim | Sim |
| Windows Server 2016 | SMB 3.0 | Sim | Sim |
| Windows 8.1 | SMB 3.0 | Sim | Sim |
| Windows Server 2012 R2 | SMB 3.0 | Sim | Sim |
| Windows Server 2012 | SMB 3.0 | Sim | Sim |
| Windows 7<sup>3</sup> | SMB 2.1 | Sim | Não |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Sim | Não |

<sup>1</sup> Windows 10, versões 1507, 1607, 1803, 1809, 1903, 1909 e 2004.  
<sup>2</sup> Windows Server, versões 1809, 1903, 1909, 2004.  
<sup>3</sup>O suporte regular da Microsoft para o Windows 7 e o Windows Server 2008 R2 terminou. É possível adquirir suporte adicional para patches de segurança somente por meio do [programa ESU (Patch de Segurança Estendido)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). É altamente recomendável migrar desses sistemas operacionais.

> [!Note]  
> É sempre recomendável obter o KB mais recente para a sua versão do Windows.

## <a name="prerequisites"></a>Pré-requisitos 

Verifique se a porta 445 está aberta: O protocolo SMB requer a porta TCP 445 aberta; haverá falha de conexão se a porta 445 estiver bloqueada. Você pode verificar se o firewall está bloqueando a porta 445 com o `Test-NetConnection` cmdlet. Para saber mais sobre as maneiras de contornar uma porta bloqueada 445, consulte a seção [causa 1: porta 445 está bloqueada](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) do nosso guia de solução de problemas do Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Usando um compartilhamento de arquivos do Azure com o Windows
Para usar um compartilhamento de arquivos do Azure com o Windows, você deve montá-lo, ou seja, atribuir uma letra da unidade ou um caminho de ponto de montagem, ou acessá-lo por meio do [caminho UNC](/windows/win32/fileio/naming-a-file). 

Este artigo usa a chave de conta de armazenamento para acessar o compartilhamento de arquivo. Uma chave de conta de armazenamento é uma chave de administrador para uma conta de armazenamento que inclui permissões de administrador para todos os arquivos e pastas no compartilhamento de arquivo que você está acessando e para todos os compartilhamentos de arquivos e outros recursos de armazenamento (blobs, filas, tabelas etc.) contidos na conta de armazenamento. Se isso não for suficiente para a carga de trabalho, use a [Sincronização de Arquivos do Azure](storage-sync-files-planning.md) ou a [autenticação baseada em identidade com SMB](storage-files-active-directory-overview.md).

Um padrão comum para o lift and shift de aplicativos de LOB (linha de negócios) que esperam um compartilhamento de arquivos SMB para o Azure é usar um compartilhamento de arquivos do Azure como uma alternativa para a execução de um servidor de arquivos dedicado do Windows em uma VM do Azure. Uma consideração importante para a migração com êxito de um aplicativo de linha de negócios a fim de usar um compartilhamento de arquivos do Azure é que muitos aplicativos de linha de negócios são executados no contexto de uma conta de serviço dedicada com permissões do sistema limitadas em vez de usar a conta administrativa da VM. Portanto, você deve montar/salvar as credenciais do compartilhamento de arquivos do Azure a partir do contexto da conta de serviço em vez da conta administrativa.

### <a name="mount-the-azure-file-share"></a>Montar o compartilhamento de arquivos do Azure

O portal do Azure fornece um script que você pode usar para montar o compartilhamento de arquivos diretamente em um host. É recomendável usar esse script fornecido.

Para obter esse script:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Navegue até a conta de armazenamento que contém o compartilhamento de arquivos que você deseja montar.
1. Selecionar **Compartilhamentos de arquivo**.
1. Selecione o compartilhamento de arquivos que você deseja montar.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="exemplo":::

1. Selecione **Conectar**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Captura de tela do ícone conectar para seu compartilhamento de arquivos.":::

1. Selecione a letra da unidade à qual o compartilhamento será montado.
1. Copie o script fornecido.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Texto de exemplo":::

1. Cole o script em um shell no host para o qual você gostaria de montar o compartilhamento de arquivos e execute-o.

Agora você montou o compartilhamento de arquivos do Azure.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Como montar o compartilhamento de arquivos do Azure com o Explorador de Arquivos
> [!Note]  
> Observe que as instruções a seguir são mostradas no Windows 10 e podem diferir ligeiramente em versões mais antigas. 

1. Abra o Explorador de Arquivos. Isso pode ser feito abrindo o Menu Iniciar ou pressionando o atalho Win + E.

1. Navegue até **este PC** no lado esquerdo da janela. Isso alterará os menus disponíveis na faixa de opções. No menu do computador, selecione **Mapear unidade de rede**.
    
    ![Uma captura de tela do menu suspenso "Mapear unidade de rede"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Selecione a letra da unidade e digite o caminho UNC de formato `\\<storageAccountName>.file.core.windows.net\<fileShareName>`. Por exemplo: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Uma captura de tela da caixa de diálogo "Mapear unidade de rede"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Use o nome da conta de armazenamento anexado com `AZURE\` como o nome de usuário e uma chave de conta de armazenamento como a senha.
    
    ![Uma captura de tela da caixa de diálogo de credenciais de rede](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Use o compartilhamento de arquivos do Azure como quiser.
    
    ![O compartilhamento de arquivos do Azure já está montado](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Quando quiser desmontar o compartilhamento de arquivos do Azure, clique com o botão direito do mouse na entrada do compartilhamento em **Locais de rede** no Explorador de Arquivos e selecione **Desconectar**.

### <a name="accessing-share-snapshots-from-windows"></a>Acessar instantâneos de compartilhamento no Windows
Se você tirou um instantâneo de compartilhamento, manual ou automaticamente por meio de um script ou serviço como o Backup do Azure, veja as versões anteriores de um compartilhamento, um diretório ou um arquivo específico do compartilhamento de arquivos no Windows. Você pode fazer um instantâneo de compartilhamento usando [Azure PowerShell](storage-how-to-use-files-powershell.md), [CLI do Azure](storage-how-to-use-files-cli.md)ou a [portal do Azure](storage-how-to-use-files-portal.md).

#### <a name="list-previous-versions"></a>Listar versões anteriores
Navegue até o item ou o item pai que precisa ser restaurado. Clique duas vezes para ir até o diretório desejado. Clique com o botão direito do mouse e selecione **Propriedades** no menu.

![Clique com o botão direito do mouse no menu para um diretório selecionado](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Selecione **Versões Anteriores** para ver a lista de instantâneos de compartilhamento para esse diretório. A lista pode levar alguns segundos para carregar, dependendo da velocidade da rede e do número de instantâneos de compartilhamento no diretório.

![Guia Versões Anteriores](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Você pode selecionar **Abrir** para abrir um instantâneo específico. 

![Instantâneo aberto](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior
Selecione **Restaurar** para copiar o conteúdo do diretório inteiro recursivamente no momento da criação do instantâneo de compartilhamento para o local original.

 ![Botão Restaurar na mensagem de aviso](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Protegendo o Windows/Windows Server
Para montar um compartilhamento de arquivos do Azure no Windows, a porta 445 deve estar acessível. Muitas organizações bloqueiam a porta 445 devido aos riscos de segurança inerentes ao protocolo SMB 1. SMB 1, também conhecido como CIFS (Common Internet File System), é um protocolo de sistema de arquivos herdados incluído no Windows e no Windows Server. O SMB 1 é um protocolo desatualizado, ineficiente e, o mais importante, não seguro. A boa notícia é que os arquivos do Azure não dão suporte a SMB 1 e todas as versões com suporte do Windows e do Windows Server possibilitam a remoção ou desabilitação do SMB 1. [Recomendamos sempre](https://aka.ms/stopusingsmb1) remover ou desabilitar o cliente e o servidor SMB 1 no Windows antes de usar os compartilhamentos de arquivos do Azure na produção.

A tabela a seguir fornece informações detalhadas sobre o status do SMB 1 em cada versão do Windows:

| Versão do Windows                           | Status padrão de protocolos SMB 1 | Desabilitar/Remover método       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Desabilitado             | Remover com recurso do Windows |
| Windows Server, versões 1709 e posteriores            | Desabilitado             | Remover com recurso do Windows |
| Windows 10, versões 1709 ou posterior                | Desabilitado             | Remover com recurso do Windows |
| Windows Server 2016                       | habilitado              | Remover com recurso do Windows |
| Windows 10 versões 1507, 1607 e 1703 | habilitado              | Remover com recurso do Windows |
| Windows Server 2012 R2                    | habilitado              | Remover com recurso do Windows | 
| Windows 8.1                               | habilitado              | Remover com recurso do Windows | 
| Windows Server 2012                       | habilitado              | Desabilitar no Registro       | 
| Windows Server 2008 R2                    | habilitado              | Desabilitar no Registro       |
| Windows 7                                 | habilitado              | Desabilitar no Registro       | 

### <a name="auditing-smb-1-usage"></a>Auditando o uso de protocolos SMB 1
> Aplica-se a Windows Server 2019, canal semestral do Windows Server (versões 1709 e 1803), Windows Server 2016, Windows 10 (versões 1507, 1607, 1703, 1709 e 1803), Windows Server 2012 R2 e Windows 8.1

Antes de remover o protocolo SMB 1 de seu ambiente, audite o uso de SMB 1 para ver se algum cliente será interrompido pela alteração. Se alguma solicitação for feita em relação a compartilhamentos SMB com protocolos SMB 1, um evento de auditoria será registrado no log de eventos em `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Para habilitar o suporte a auditoria no Windows Server 2012 R2 e no Windows 8.1, instale pelo menos o [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Para habilitar a auditoria, execute o seguinte cmdlet em uma sessão do PowerShell com privilégios elevados:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Removendo SMB 1 do Windows Server
> Aplica-se a Windows Server 2019, canal semestral do Windows Server (versões 1709 e 1803), Windows Server 2016 e Windows Server 2012 R2

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
- [Descobrir SMB 1 em seu ambiente com DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [Desabilitando o SMB 1 com Política de Grupo](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure:
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
- [perguntas frequentes](./storage-files-faq.md)
- [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)