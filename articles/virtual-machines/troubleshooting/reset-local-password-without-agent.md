---
title: Redefinir uma senha local do Windows sem o agente do Azure | Microsoft Docs
description: Como redefinir a senha de uma conta de usuário local do Windows quando o agente convidado do Azure não estiver instalado ou funcionando em uma VM
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 5354ebc8c25125f86a0208382d176c84372cadc1
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369870"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Redefina a senha local do Windows para a VM do Azure offline
Você pode redefinir a senha local do Windows de uma VM no Azure usando o [portal do Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), desde que o agente convidado do Azure esteja instalado. Esse método é a principal maneira de redefinir uma senha para uma VM do Azure. Caso encontre problemas com o agente convidado do Azure, como não responder ou falhar na instalação após carregamento de uma imagem personalizada, você poderá redefinir manualmente uma senha do Windows. Este artigo fornece detalhes sobre como redefinir uma senha de conta local anexando o disco virtual do SO de origem a outra VM. As etapas descritas neste artigo não se aplicam aos controladores de domínio do Windows. 

> [!WARNING]
> Use esse processo apenas como último recurso. Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) primeiro.

## <a name="overview-of-the-process"></a>Visão geral do processo
As principais etapas para redefinir uma senha local para uma VM do Windows no Azure quando não há acesso ao agente convidado do Azure são as seguintes:

1. Exclua a VM de origem. Os discos virtuais são mantidos.

2. Anexe o disco do sistema operacional da VM de origem a outra VM na mesma localização em sua assinatura do Azure. Essa VM é conhecida como a VM para solução de problemas.

3. Usando a VM para solução de problemas, crie alguns arquivos de configuração no disco do SO da VM de origem.

4. Desanexe da VM para solução de problemas o disco do SO da VM.

5. Use um modelo do Resource Manager para criar uma VM usando o disco virtual original.

6. Quando a nova VM é inicializada, os arquivos de configuração que você cria atualizam a senha do usuário exigido.

> [!NOTE]
> Você pode automatizar os processos a seguir:
>
> - Criação da VM de solução de problemas
> - Anexação do disco de SO
> - Recriação da VM original
> 
> Para fazer isso, use os [Scripts de Recuperação de VM do Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Se você optar por usar os Scripts de Recuperação de VM do Azure, use o seguinte processo na seção “Etapas detalhadas”:
> 1. Ignore as etapas 1 e 2 usando os scripts para anexar o disco do SO da VM afetada a uma VM de recuperação.
> 2. Siga as etapas 3 a 6 para aplicar as mitigações.
> 3. Ignore as etapas 7 a 9 usando os scripts para recompilar a VM.
> 4. Siga as etapas 10 e 11.

## <a name="detailed-steps-for-resource-manager"></a>Etapas detalhadas para o Gerenciador de recursos

> [!NOTE]
> As etapas não se aplicam aos controladores de domínio do Windows. Ela só funciona no servidor autônomo ou em um servidor que seja membro de um domínio.

Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de tentar as etapas seguintes. Verifique se você tem um backup da VM antes de iniciar. 

1. Exclua a VM afetada no portal do Azure. A exclusão da VM exclui apenas os metadados, a referência da VM dentro do Azure. Os discos virtuais são mantidos quando a VM é excluída:
   
   * Escolha a VM no portal do Azure e clique em *Excluir*:
     
     ![Excluir VM existente](./media/reset-local-password-without-agent/delete-vm.png)

2. Anexe o disco do SO da VM de origem à VM para solução de problemas. A VM para solução de problemas deve estar na mesma região que o disco do SO da VM de origem (como `West US`):
   
   1. Escolha a VM para solução de problemas no portal do Azure. Clique em *Discos* | *Anexar existente*:
     
     ![Anexar disco existente](./media/reset-local-password-without-agent/disks-attach-existing.png)
     
   2. Escolha *Arquivo VHD* e, em seguida, a conta de armazenamento que contém sua VM de origem:
     
     ![Escolher conta de armazenamento](./media/reset-local-password-without-agent/disks-select-storage-account.png)
     
   3. Escolha o contêiner de origem. O contêiner de origem normalmente é *vhds*:
     
     ![Escolher contêiner de armazenamento](./media/reset-local-password-without-agent/disks-select-container.png)
     
   4. Escolha o vhd do SO a ser anexado. Clique em *Selecionar* para concluir o processo:
     
     ![Escolher o disco virtual de origem](./media/reset-local-password-without-agent/disks-select-source-vhd.png)

3. Conecte-se à VM para solução de problemas usando a Área de Trabalho Remota e assegure-se de que o disco do SO da VM de origem esteja visível:
   
   1. Escolha a VM para solução de problemas no portal do Azure e clique em *Conectar*.

   2. Abra o arquivo RDP que foi baixado. Insira o nome de usuário e a senha da VM para solução de problemas.

   3. No Explorador de Arquivos, procure o disco de dados anexado. Se o VHD da VM de origem for o único disco de dados anexado à VM para solução de problemas, ele deverá ser a unidade F:
     
     ![Exibir o disco de dados anexado](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer.png)

4. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` na unidade da VM de origem (se gpt.ini existir, renomeie para gpt.ini.bak):
   
   > [!WARNING]
   > Tome cuidado para não criar acidentalmente os arquivos a seguir em C:\Windows, a unidade do SO da VM para solução de problemas. Crie os arquivos a seguir na unidade do SO da VM de origem que está anexada como um disco de dados.
   
   * Adicione as seguintes linhas no arquivo `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

5. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machines\Scripts\`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as pastas`Machine` ou `Scripts`.
   
   * Adicione as seguintes linhas ao arquivo `scripts.ini` que você criou:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

6. Crie `FixAzureVM.cmd` em `\Windows\System32` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` por seus próprios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Criar FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Você deve atender aos requisitos de complexidade de senha configurada para sua VM ao definir a nova senha.

7. No portal do Azure, desanexe o disco da VM para solução de problemas:
   
   1. Escolha a VM para solução de problemas no portal do Azure e clique em *Discos*.

   2. Selecione o disco de dados anexado na etapa 2 e clique em *Desanexar*:
     
     ![Desanexar disco](./media/reset-local-password-without-agent/detach-disk.png)

8. Antes de criar uma VM, obtenha o URI para o disco do SO de origem:
   
   1. Escolha a conta de armazenamento no portal do Azure e clique em *Blobs*.

   2. Escolha o contêiner. O contêiner de origem normalmente é *vhds*:
     
     ![Selecionar o blob da conta de armazenamento](./media/reset-local-password-without-agent/select-storage-details.png)
     
   3. Escolha o VHD do SO da VM de origem e clique no botão *Copiar* ao lado do nome da *URL*:
     
     ![Copiar URI do disco](./media/reset-local-password-without-agent/copy-source-vhd-uri.png)

9. Crie uma VM usando o disco do SO da VM de origem:
   
   1. Use [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) para criar uma VM de um VHD especializado. Clique no botão `Deploy to Azure` para abrir o portal do Azure com os detalhes do modelo que você populou.

   2. Se quiser manter todas as configurações anteriores para a VM, escolha *Editar modelo* para fornecer a VNet, a sub-rede, o adaptador de rede ou o IP público existentes.

   3. Na caixa de texto do parâmetro `OSDISKVHDURI`, cole o URI do VHD de origem obtido na etapa anterior:
     
     ![Criar uma VM usando o modelo](./media/reset-local-password-without-agent/create-new-vm-from-template.png)

10. Depois que a nova VM estiver em execução, conecte-se à ela usando a Área de Trabalho Remota com a nova senha que você especificou no script `FixAzureVM.cmd`.

11. Na sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * De %windir%\System32
      * remova FixAzureVM.cmd
    * De%windir%\System32\GroupPolicy\Machine\Scripts
      * remova scripts.ini
    * De %windir%\System32\GroupPolicy
      * remova gpt.ini (se gpt.ini já existia e você o renomeou para gpt.ini.bak, renomeie o arquivo .bak de volta para gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Etapas detalhadas para a VM clássica

> [!NOTE]
> As etapas não se aplicam aos controladores de domínio do Windows. Ela só funciona no servidor autônomo ou em um servidor que seja membro de um domínio.

Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) antes de tentar as etapas seguintes. Verifique se você tem um backup da VM antes de iniciar. 

1. Exclua a VM afetada no portal do Azure. A exclusão da VM exclui apenas os metadados, a referência da VM dentro do Azure. Os discos virtuais são mantidos quando a VM é excluída:
   
   * Selecione a VM no portal do Azure, em seguida, clique em *excluir*:
     
     ![Excluir VM existente](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Anexe o disco do SO da VM de origem à VM para solução de problemas. A VM para solução de problemas deve estar na mesma região que o disco do SO da VM de origem (como `West US`):
   
   1. Escolha a VM para solução de problemas no portal do Azure. Clique em *Discos* | *Anexar existente*:
     
      ![Anexar disco existente](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Escolha *Arquivo VHD* e, em seguida, a conta de armazenamento que contém sua VM de origem:
     
      ![Selecione a conta de armazenamento](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Marque a caixa marcada *Mostrar contas de armazenamento clássicas*e, em seguida, selecione o contêiner de origem. O contêiner de origem normalmente é *vhds*:
     
      ![Escolher contêiner de armazenamento](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Escolher contêiner de armazenamento](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Escolha o vhd do SO a ser anexado. Clique em *Selecionar* para concluir o processo:
     
      ![Escolher o disco virtual de origem](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Clique em OK para anexar o disco

      ![Anexar disco existente](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Conecte-se à VM para solução de problemas usando a Área de Trabalho Remota e assegure-se de que o disco do SO da VM de origem esteja visível:

   1. Escolha a VM para solução de problemas no portal do Azure e clique em *Conectar*.

   2. Abra o arquivo RDP que foi baixado. Insira o nome de usuário e a senha da VM para solução de problemas.

   3. No Explorador de Arquivos, procure o disco de dados anexado. Se o VHD da VM de origem for o único disco de dados anexado à VM para solução de problemas, ele deverá ser a unidade F:
     
      ![Exibir o disco de dados anexado](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Crie `gpt.ini` `gpt.ini` `gpt.ini.bak`no `\Windows\System32\GroupPolicy` na unidade da VM de origem (se existir, renomeie para):
   
   > [!WARNING]
   > Certifique-se de não criar acidentalmente os seguintes arquivos no `C:\Windows`, a unidade do sistema operacional para a VM de solução de problemas. Crie os arquivos a seguir na unidade do SO da VM de origem que está anexada como um disco de dados.
   
   * Adicione as seguintes linhas no arquivo `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machines\Scripts\`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as pastas`Machine` ou `Scripts`.
   
   * Adicione as seguintes linhas ao arquivo `scripts.ini` que você criou:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Crie `FixAzureVM.cmd` em `\Windows\System32` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` por seus próprios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Criar FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Você deve atender aos requisitos de complexidade de senha configurada para sua VM ao definir a nova senha.

7. No portal do Azure, desanexe o disco da VM para solução de problemas:
   
   1. Escolha a VM para solução de problemas no portal do Azure e clique em *Discos*.
   
   2. Selecione o disco de dados anexado na etapa 2, clique em *desanexar:* e, em seguida, clique em *OK*.

     ![Desanexar disco](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Desanexar disco](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Crie uma VM usando o disco do SO da VM de origem:
   
     ![Criar uma VM usando o modelo](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Criar uma VM usando o modelo](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Criar uma VM usando o modelo](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Concluir a experiência de criação de máquina virtual

1. Depois que a nova VM estiver em execução, conecte-se à ela usando a Área de Trabalho Remota com a nova senha que você especificou no script `FixAzureVM.cmd`.

2. Na sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * De`%windir%\System32`
      * exclu`FixAzureVM.cmd`
    * De`%windir%\System32\GroupPolicy\Machine\Scripts`
      * exclu`scripts.ini`
    * De`%windir%\System32\GroupPolicy`
      * Remova `gpt.ini` (se `gpt.ini` existia antes, e você o renomeou `gpt.ini.bak`como, renomeie o `gpt.ini` `.bak` arquivo de volta para)

## <a name="next-steps"></a>Próximas etapas
Se você ainda não conseguir conectar usando a Área de Trabalho Remota, confira o [guia de solução de problemas do RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O [guia para solução de problemas detalhada de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) explica os métodos da solução de problemas, e não etapas específicas. Você também pode [abrir uma solicitação de suporte do Azure](https://azure.microsoft.com/support/options/) para obter assistência prática.
