---
title: "Solução de problemas do Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: Solucionando problemas do Azure Cloud Shell
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/2/2017
ms.author: damaerte
ms.openlocfilehash: a23023649474d4b4a36a362593dd7affde49b1a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Solucionando problemas do Azure Cloud Shell
As soluções conhecidas para problemas no Azure Cloud Shell incluem:

## <a name="error-400-disallowedoperation"></a>Erro: 400 DisallowedOperation
 - **Detalhes**: Ao usar uma assinatura do Azure Active Directory, você não consegue criar armazenamento.
 - **Solução**: use uma assinatura do Azure capaz de criar recursos de armazenamento. Assinaturas do Azure AD não são capazes de criar recursos do Azure.

## <a name="powershell-resolutions"></a>Soluções do PowerShell

### <a name="no-home-directory-persistence"></a>Sem persistência do diretório $Home
  - **Detalhes**: qualquer aplicativo (como git, vim e outros) que grava dados em $Home não serão mantidos em sessões do PowerShell.
  - **Solução**: no seu perfil do PowerShell, crie um link simbólico para a pasta de aplicativo específico em `clouddrive` para $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C não faz sair do prompt Cmdlet
 - **Detalhes**: ao tentar sair de um prompt Cmdlet, `Ctrl+C` não faz sair do prompt.
 - **Solução**: para sair do prompt, pressione `Ctrl+C` e, em seguida, `Enter`.

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI
  - **Detalhes**: se um usuário inicia um aplicativo GUI, o prompt não retorna.  Por exemplo, quando um usuário utiliza `git` para fechar um repositório privado com autenticação de dois fatores habilitada, uma caixa de diálogo é exibida para o código de autenticação de dois fatores.
  - **Solução**: `Ctrl+C` para sair do comando.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online não abre a página de ajuda
 - **Detalhes**: se um usuário digita `Get-Help Find-Module -online`, ele vê uma mensagem de erro, como:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
 - **Solução**: copie a url e a abra manualmente no navegador.
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solucionando problemas de gerenciamento remoto de máquinas virtuais do Azure
 - **Detalhes**: devido às configurações do Firewall do Windows para WinRM, o usuário pode ver o seguinte erro:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
 - **Solução**: verifique se a VM está em execução. Você pode executar `Get-AzureRmVM -Status` para descobrir o Status da VM.  Em seguida, adicione uma nova regra de firewall na VM remota para permitir conexões de WinRM de qualquer sub-rede; por exemplo,

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Você pode usar [extensão de script personalizado do Azure] [customex] para evitar o logon em sua VM remota para adicionar a nova regra de firewall.
 Você pode salvar o script acima em um arquivo, digamos `addfirerule.ps1`, e carregá-lo em seu contêiner de armazenamento do Azure.
 Em seguida, tente o seguinte comando:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

 ### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` armazena em cache o resultado na unidade do Azure
 - **Detalhes**: o resultado de `dir` é armazenado em cache na unidade do Azure.
 - **Resolução**: depois de criar ou remover um recurso no modo de exibição da unidade do Azure, execute `dir -force` para atualizar.