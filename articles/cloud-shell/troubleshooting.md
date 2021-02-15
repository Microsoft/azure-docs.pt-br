---
title: Solução de problemas do Azure Cloud Shell | Microsoft Docs
description: Solucionando problemas do Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: eea64520dd5440467c911b6de42d8c8c31fc1bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543445"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Solução de problemas e limitações do Azure Cloud Shell

As resoluções conhecidas para solucionar problemas no Azure Cloud Shell incluem:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Solução de problemas gerais

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Erro ao executar cmdlets AzureAD no PowerShell

- **Detalhes**: ao executar cmdlets AzureAD como `Get-AzureADUser` no Cloud Shell, você poderá ver um erro: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets` . 
- **Resolução**: execute o `Connect-AzureAD` cmdlet. Anteriormente, Cloud Shell executou esse cmdlet automaticamente durante a inicialização do PowerShell. Para acelerar a hora de início, o cmdlet não é mais executado automaticamente. Você pode optar por restaurar o comportamento anterior adicionando `Connect-AzureAD` ao arquivo de $profile no PowerShell.

### <a name="early-timeouts-in-firefox"></a>Tempos limites iniciais no FireFox

- **Detalhes**: Cloud Shell utiliza um websocket aberto para passar a entrada/saída para o navegador. FireFox tem políticas predefinidas que podem fechar o websocket prematuramente, causando tempos limites iniciais no Cloud Shell.
- **Resolução**: Abra o FireFox e acesse "sobre: config" na caixa URL. Procure por "network.websocket.timeout.ping.request" e altere o valor de 0 para 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Desabilitando o Cloud Shell em um ambiente de rede bloqueado

- **Detalhes**: os administradores podem querer desabilitar o acesso ao Cloud Shell para seus usuários. Cloud Shell utiliza o acesso ao `ux.console.azure.com` domínio, que pode ser negado, interrompendo qualquer acesso aos ponto de entrada de Cloud Shell, incluindo Portal.Azure.com, Shell.Azure.com, Visual Studio Code extensão de conta do Azure e docs.Microsoft.com. Na nuvem do governo dos EUA, o EntryPoint é; não há `ux.console.azure.us` nenhum Shell.Azure.us correspondente.
- **Resolução**: restrinja o acesso a `ux.console.azure.com` ou `ux.console.azure.us` via configurações de rede ao seu ambiente. O ícone de Cloud Shell ainda existirá na portal do Azure, mas não se conectará com êxito ao serviço.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Caixa de diálogo Armazenamento – Erro: 403 RequestDisallowedByPolicy

- **Detalhes**: ao criar uma conta de armazenamento por meio do Cloud Shell, ela não é bem-sucedida devido a uma atribuição de Azure Policy colocada pelo administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: contate o administrador do Azure para remover ou atualizar a atribuição de Azure Policy que nega a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Caixa de diálogo – Erro: 400 DisallowedOperation

- **Detalhes**: Ao usar uma assinatura do Azure Active Directory, você não consegue criar armazenamento.
- **Solução**: use uma assinatura do Azure capaz de criar recursos de armazenamento. Assinaturas do Azure AD não são capazes de criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal de saída – Erro: Falha ao conectar o terminal: o websocket não pode ser estabelecido. Pressione `Enter` para reconectar.
- **Detalhes**: o Cloud Shell requer a capacidade de estabelecer uma conexão websocket à infraestrutura do Cloud Shell.
- **Resolução**: verifique se você definiu as configurações de rede para permitir o envio de solicitações HTTPS e solicitações de websocket para domínios em *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Definir sua conexão de Cloud Shell para dar suporte ao uso do TLS 1.2
 - **Detalhes**: para definir a versão do TLS para sua conexão com Cloud Shell, você deve definir configurações específicas do navegador.
 - **Resolução**: Navegue até as configurações de segurança do seu navegador e selecione a caixa de seleção ao lado de "Usar TLS 1.2".

## <a name="bash-troubleshooting"></a>Solução de problemas de Bash

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon do Docker

- **Detalhes**: O Cloud Shell utiliza um contêiner para hospedar o seu ambiente de shell e, como resultado, a execução do daemon deixa de ser permitida.
- **Resolução**: utilize o [Docker Machine](https://docs.docker.com/machine/overview/), que é instalado por padrão, para gerenciar contêineres do Docker de um host remoto do Docker.

## <a name="powershell-troubleshooting"></a>Solução de problemas do PowerShell

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI

- **Detalhes**: se um usuário inicia um aplicativo GUI, o prompt não retorna. Por exemplo, quando um usuário clona um repositório do GitHub privado com autenticação de dois fatores habilitada, uma caixa de diálogo é exibida para completar a autenticação de dois fatores.
- **Resolução**: feche e reabra o shell.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solucionando problemas de gerenciamento remoto de máquinas virtuais do Azure
> [!NOTE]
> As VMs do Azure devem ter um endereço IP voltado para o público.

- **Detalhes**: devido às configurações do Firewall do Windows para WinRM, o usuário pode ver o seguinte erro:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**: execute `Enable-AzVMPSRemoting` para habilitar todos os aspectos da comunicação remota do PowerShell no computador de destino.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` não atualiza o resultado na unidade do Azure

- **Detalhes**: por padrão, para otimizar a experiência do usuário, os resultados de `dir` é armazenado em cache na unidade do Azure.
- **Resolução**: depois de criar, atualizar ou remover um recurso do Azure, execute `dir -force` para atualizar os resultados na unidade do Azure.

## <a name="general-limitations"></a>Limitações gerais

O Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="quota-limitations"></a>Limitações de cota

Azure Cloud Shell tem um limite de 20 usuários simultâneos por locatário por região. Se você tentar abrir mais sessões simultâneas do que o limite, verá um erro "usuário do locatário sobre cota". Se você tiver uma necessidade legítima de ter mais sessões abertas do que isso (por exemplo, para sessões de treinamento), entre em contato com o suporte antes do uso antecipado para solicitar um aumento de cota.

O Cloud Shell é fornecido como um serviço gratuito e foi projetado para ser usado para configurar seu ambiente do Azure, não como uma plataforma de computação de uso geral. O uso automatizado excessivo pode ser considerado em violação aos termos de serviço do Azure e pode levar a Cloud Shell acesso bloqueado.

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece a sessão do Cloud Shell é temporário e é reciclado após a sessão ficar inativa por 20 minutos. O Cloud Shell exige a montagem de um compartilhamento de arquivos do Azure. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar o Cloud Shell. Outras considerações incluem:

- Com o armazenamento montado, apenas as modificações no diretório `clouddrive` persistem. No Bash, seu `$HOME` diretório também persiste.
- Os compartilhamentos de arquivo do Azure só podem ser montados em sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  - No Bash, execute `env` para localizar sua região definida como `ACC_LOCATION`.
- Os Arquivos do Azure oferece suporte apenas a armazenamento com redundância local e a contas de armazenamento com redundância geográfica.

### <a name="browser-support"></a>Suporte ao navegador

O Cloud Shell é compatível com as versões mais recentes dos navegadores a seguir:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Não há suporte para o Safari no modo particular.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites de uso

O Cloud Shell destina-se a casos de uso interativos. Como resultado, quaisquer sessões não interativo e de longa execução são finalizadas sem aviso.

### <a name="user-permissions"></a>Permissões de usuário

As permissões são definidas como usuários regulares sem acesso sudo. Qualquer instalação fora do seu diretório `$Home` não será persistida.

## <a name="bash-limitations"></a>Limitações do Bash

### <a name="editing-bashrc"></a>Edição de .bashrc

Tome cuidado ao editar .bashrc, uma vez que isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="preview-version-of-azuread-module"></a>Versão prévia do módulo AzureAD

Atualmente, `AzureAD.Standard.Preview`, uma versão prévia baseada no .NET Standard, o módulo está disponível. Esse módulo fornece funcionalidade como `AzureAD`.

## <a name="personal-data-in-cloud-shell"></a>Dados pessoais no Cloud Shell

O Azure Cloud Shell leva seus dados pessoais a sério, os dados capturados e armazenados pelo serviço do Azure Cloud Shell são usados para fornecer padrões para a sua experiência, como o seu shell usado mais recentemente, tamanho da fonte preferencial, tipo de fonte preferencial e detalhes de compartilhamento de arquivos que deem suporte à unidade de nuvem. Se desejar exportar ou excluir esses dados, use as instruções a seguir.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportação
Para **exportar** as configurações de usuário Cloud Shell salvas para você, como o shell preferencial, o tamanho da fonte e o tipo de fonte, execute os seguintes comandos.

1. [![Imagem mostrando um botão chamado iniciar Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Execute os seguintes comandos no PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Excluir
Para **excluir** as configurações de usuário Cloud Shell salvas para você, como o shell preferencial, o tamanho da fonte e o tipo de fonte, execute os seguintes comandos. Na próxima vez que você iniciar o Cloud Shell você deverá carregar um compartilhamento de arquivos novamente. 

>[!Note]
> Se você excluir as configurações de usuário, vá para arquivos do Azure para concluir essa ação. Vá para os seus arquivos do Azure para concluir essa ação.

1. [![Imagem mostrando um botão chamado iniciar Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Execute os seguintes comandos no PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Limitações do Azure governamental
Azure Cloud Shell no Azure governamental só é acessível por meio do portal do Azure.

>[!Note]
> Atualmente, não há suporte para conectar-se a nuvens GCC-High ou do governo do DoD para o Exchange Online.
