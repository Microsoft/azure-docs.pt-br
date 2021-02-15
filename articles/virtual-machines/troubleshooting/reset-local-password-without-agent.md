---
title: Redefinir uma senha local do Windows sem o agente do Azure | Microsoft Docs
description: Como redefinir a senha de uma conta de usuário local do Windows quando o agente convidado do Azure não estiver instalado ou funcionando em uma VM
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 2cc6ef9b1d9ca8336162b524356ea6e0d1bf5fd2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197652"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Redefina a senha local do Windows para a VM do Azure offline
Você pode redefinir a senha local do Windows de uma VM no Azure usando o [portal do Azure ou Azure PowerShell](reset-rdp.md), desde que o agente convidado do Azure esteja instalado. Esse método é a principal maneira de redefinir uma senha para uma VM do Azure. Caso encontre problemas com o agente convidado do Azure, como não responder ou falhar na instalação após carregamento de uma imagem personalizada, você poderá redefinir manualmente uma senha do Windows. Este artigo fornece detalhes sobre como redefinir uma senha de conta local anexando o disco virtual do SO de origem a outra VM. As etapas descritas neste artigo não se aplicam aos controladores de domínio do Windows. 

> [!WARNING]
> Use esse processo apenas como último recurso. Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](reset-rdp.md) primeiro.

## <a name="overview-of-the-process"></a>Visão geral do processo
As principais etapas para redefinir uma senha local para uma VM do Windows no Azure quando não há acesso ao agente convidado do Azure são as seguintes:

1. Pare a VM afetada.
1. Crie um instantâneo para o disco do sistema operacional da VM.
1. Crie uma cópia do disco do sistema operacional com base no instantâneo.
1. Anexe e monte o disco do sistema operacional copiado em outra VM do Windows e, em seguida, crie alguns arquivos de configuração no disco. Os arquivos ajudarão a redefinir a senha.
1. Desmonte e desanexe o disco copiado do sistema operacional da VM de solução de problemas.
1. Troque o disco do sistema operacional da VM afetada.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Etapas detalhadas para a VM com implantação do Resource Manager

> [!NOTE]
> As etapas não se aplicam aos controladores de domínio do Windows. Ela só funciona no servidor autônomo ou em um servidor que seja membro de um domínio.

Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](reset-rdp.md) antes de tentar as etapas seguintes. Verifique se você tem um backup da VM antes de iniciar.

1. Obtenha um instantâneo do disco do sistema operacional da VM afetada, crie um disco com base no instantâneo e, em seguida, anexe o disco a uma VM de solução de problemas. Para obter mais informações, confira [Solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Conecte-se à VM de solução de problemas usando a Área de Trabalho Remota.
3. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` na unidade da VM de origem (se gpt.ini existir, renomeie para gpt.ini.bak):
   
   > [!WARNING]
   > Tome cuidado para não criar acidentalmente os arquivos a seguir em C:\Windows, a unidade do SO da VM para solução de problemas. Crie os arquivos a seguir na unidade do SO da VM de origem que está anexada como um disco de dados.
   
   * Adicione as seguintes linhas no arquivo `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini.png" alt-text="Captura de tela que mostra as atualizações feitas no arquivo de gpt.ini.":::

4. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts\`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as pastas`Machine` ou `Scripts`. 
   
   * Adicione as seguintes linhas ao arquivo `scripts.ini` que você criou:
     
     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-1.png" alt-text="Captura de tela que mostra as atualizações feitas no arquivo de script.ini.":::

5. Crie `FixAzureVM.cmd` em `\Windows\System32\GroupPolicy\Machine\Scripts\Startup\` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` por seus próprios valores:
   
    ```
    net user <username> <newpassword> /add /Y
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Captura de tela que mostra o arquivo recém-criado FixAzureVM. cmd no qual você atualiza o nome de usuário e a senha.":::
   
    Você deve atender aos requisitos de complexidade de senha configurada para sua VM ao definir a nova senha.

6. No portal do Azure, desanexe o disco da VM de solução de problemas.

7. [Altere o disco do sistema operacional da VM afetada](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Depois que a nova VM estiver em execução, conecte-se à ela usando a Área de Trabalho Remota com a nova senha que você especificou no script `FixAzureVM.cmd`.

9. Na sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * De%windir%\System32\GroupPolicy\Machine\Scripts\Startup
      * remova FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\Scripts
      * remova scripts.ini
    * De %windir%\System32\GroupPolicy
      * remova gpt.ini (se gpt.ini já existia e você o renomeou para gpt.ini.bak, renomeie o arquivo .bak de volta para gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Etapas detalhadas para VM Clássica

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> As etapas não se aplicam aos controladores de domínio do Windows. Ela só funciona no servidor autônomo ou em um servidor que seja membro de um domínio.

Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) antes de tentar as etapas seguintes. Verifique se você tem um backup da VM antes de iniciar. 

1. Exclua a VM afetada no portal do Azure. A exclusão da VM exclui apenas os metadados, a referência da VM dentro do Azure. Os discos virtuais são mantidos quando a VM é excluída:
   
   * Selecione a VM no portal do Azure e, em seguida, clique em *Excluir*:
     
     :::image type="content" source="./media/reset-local-password-without-agent/delete-vm-classic.png" alt-text="Excluir VM clássica existente":::

2. Anexe o disco do SO da VM de origem à VM para solução de problemas. A VM para solução de problemas deve estar na mesma região que o disco do SO da VM de origem (como `West US`):
   
   1. Escolha a VM para solução de problemas no portal do Azure. Clique em *Discos* | *Anexar existente*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-existing-classic.png" alt-text="Anexar disco existente-clássico":::
     
   2. Escolha *Arquivo VHD* e, em seguida, a conta de armazenamento que contém sua VM de origem:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-storage-account-classic.png" alt-text="Selecionar conta de armazenamento-clássico":::
     
   3. Marque a caixa *Mostrar contas de armazenamento clássicas* e, em seguida, selecione o contêiner de origem. O contêiner de origem normalmente é *vhds*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-classic.png" alt-text="Selecionar contêiner de armazenamento-clássico":::

      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png" alt-text="Selecionar contêiner de armazenamento-VHD-clássico":::
     
   4. Escolha o vhd do SO a ser anexado. Clique em *Selecionar* para concluir o processo:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png" alt-text="Selecionar disco virtual de origem-clássico":::

   5. Clicar em OK para anexar o disco

      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-okay-classic.png" alt-text="Anexar disco existente-OK caixa de diálogo-clássico":::

3. Conecte-se à VM para solução de problemas usando a Área de Trabalho Remota e assegure-se de que o disco do SO da VM de origem esteja visível:

   1. Escolha a VM para solução de problemas no portal do Azure e clique em *Conectar*.

   2. Abra o arquivo RDP que foi baixado. Insira o nome de usuário e a senha da VM para solução de problemas.

   3. No Explorador de Arquivos, procure o disco de dados anexado. Se o VHD da VM de origem for o único disco de dados anexado à VM para solução de problemas, ele deverá ser a unidade F:
     
      :::image type="content" source="./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png" alt-text="Exibir o disco de dados anexado":::

4. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` na unidade da VM de origem (se `gpt.ini` existir, renomeie para `gpt.ini.bak`):
   
   > [!WARNING]
   > Tome cuidado para não criar acidentalmente os arquivos a seguir em `C:\Windows`, a unidade do sistema operacional da VM de solução de problemas. Crie os arquivos a seguir na unidade do SO da VM de origem que está anexada como um disco de dados.
   
   * Adicione as seguintes linhas no arquivo `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini-classic.png" alt-text="Criar gpt.ini-clássico":::

5. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts\`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as pastas`Machine` ou `Scripts`.
   
   * Adicione as seguintes linhas ao arquivo `scripts.ini` que você criou:

     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-classic-1.png" alt-text="Criar scripts.ini-clássico":::

6. Crie `FixAzureVM.cmd` em `\Windows\System32\GroupPolicy\Machine\Scripts\Startup\` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` por seus próprios valores:
   
    ```
    net user <username> <newpassword> /add /Y
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Criar FixAzureVM. cmd-clássico":::
   
    Você deve atender aos requisitos de complexidade de senha configurada para sua VM ao definir a nova senha.

7. No portal do Azure, desanexe o disco da VM para solução de problemas:
   
   1. Escolha a VM para solução de problemas no portal do Azure e clique em *Discos*.
   
   2. Selecione o disco de dados anexado na etapa 2, clique em **Desanexar** e, em seguida, clique em **OK**.

     :::image type="content" source="./media/reset-local-password-without-agent/data-disks-classic.png" alt-text="Desanexar disco-Solucionando problemas de VM-clássico":::
     
     :::image type="content" source="./media/reset-local-password-without-agent/detach-disk-classic.png" alt-text="Desanexar disco-solução de problemas de VM-Ok caixa de diálogo-clássico":::

8. Crie uma VM usando o disco do SO da VM de origem:
   
     :::image type="content" source="./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png" alt-text="Criar uma VM do modelo-clássico":::

     :::image type="content" source="./media/reset-local-password-without-agent/choose-subscription-classic.png" alt-text="Criar uma VM do modelo-escolher assinatura-clássico":::

     :::image type="content" source="./media/reset-local-password-without-agent/create-vm-classic.png" alt-text="Criar uma VM do modelo-criar VM-clássico":::

## <a name="complete-the-create-virtual-machine-experience"></a>Concluir a experiência Criar máquina virtual

1. Depois que a nova VM estiver em execução, conecte-se à ela usando a Área de Trabalho Remota com a nova senha que você especificou no script `FixAzureVM.cmd`.

2. Na sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * Em `%windir%\System32\GroupPolicy\Machine\Scripts\Startup\`
      * remova `FixAzureVM.cmd`
    * Em `%windir%\System32\GroupPolicy\Machine\Scripts`
      * remova `scripts.ini`
    * Em `%windir%\System32\GroupPolicy`
      * remova `gpt.ini` (se `gpt.ini` existia antes e você o renomeou como `gpt.ini.bak`, renomeie o arquivo `.bak` de volta para `gpt.ini`)

## <a name="next-steps"></a>Próximas etapas
Se você ainda não conseguir conectar usando a Área de Trabalho Remota, confira o [guia de solução de problemas do RDP](troubleshoot-rdp-connection.md). O [guia para solução de problemas detalhada de RDP](detailed-troubleshoot-rdp.md) explica os métodos da solução de problemas, e não etapas específicas. Você também pode [abrir uma solicitação de suporte do Azure](https://azure.microsoft.com/support/options/) para obter assistência prática.
