---
title: Usando o Visual Studio, habilitar Área de Trabalho Remota para uma função (serviços de nuvem do Azure clássicos)
description: Como configurar seu aplicativo de serviço de nuvem do Azure para permitir conexões de Área de Trabalho Remota
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743229"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Habilitar Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure (clássico) usando o Visual Studio

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar a conexão da área de trabalho remota para solucionar e diagnosticar problemas com seu aplicativo durante a execução.

O assistente de publicação que o Visual Studio fornece para serviços de nuvem inclui uma opção para habilitar a Área de Trabalho Remota durante o processo de publicação, usando as credenciais fornecidas por você. O uso dessa opção é adequado ao usar o Visual Studio 2017 versão 15.4 e versões anteriores.

No entanto, com o Visual Studio 2017 versão 15.5 e posterior, é recomendável que você evite habilitar a Área de Trabalho Remota por meio do assistente de publicação, a menos que você esteja trabalhando somente como um único desenvolvedor. Para qualquer situação em que o projeto pode ser aberto por outros desenvolvedores, em vez disso, habilite a Área de Trabalho Remota por meio do portal do Azure, por meio do PowerShell ou de um pipeline de lançamento em um fluxo de trabalho de implantação contínua. Essa recomendação é devido a uma alteração em como Visual Studio se comunica com a Área de Trabalho Remota com o serviço de nuvem VM, como explicado neste artigo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configurar Área de Trabalho Remota por meio do Visual Studio 2017 versão 15.4 e anteriores

Ao usar o Visual Studio 2017 versão 15.4 e anteriores, você pode usar a opção **Habilitar a Área de Trabalho Remota para todas as funções** no assistente de publicação. Você ainda pode usar o assistente com o Visual Studio 2017 versão 15.5 e posterior, mas não use a opção de Área de Trabalho Remota.

1. No Visual Studio, inicie o assistente de publicação clicando com o botão direito no seu projeto de serviço de nuvem no Gerenciador de Soluções e escolhendo **Publicar**.

2. Entre em sua assinatura do Azure, se necessário e selecione **Próximo**.

3. Na página de **Configurações**, selecione **Habilitar a Área de Trabalho Remota para todas as funções**, em seguida, selecione o link **Configurações...** para abrir  caixa de diálogo **Configuração da Área de Trabalho Remota**.

4. Na parte inferior da caixa de diálogo, selecione **Mais opções**. Esse comando exibe lista suspensa na qual você cria ou escolhe um certificado para que você possa criptografar informações de credenciais ao conectar-se via área de trabalho remota.

   > [!Note]
   > Os certificados necessários para uma conexão de área de trabalho remota são diferentes dos certificados que você usa para outras operações do Azure. O certificado de acesso remoto deve ter uma chave privada.

5. Selecione um certificado na lista ou escolha **&lt; criar... &gt;**. Se criar um novo certificado, forneça um nome amigável para o novo certificado quando solicitado e selecione **OK**. O novo certificado é exibido na caixa de listagem suspensa.

6. Forneça um nome de usuário e uma senha. Você não pode usar uma conta existente. Não use "Administrador" como o nome de usuário para a nova conta.

7. Escolha uma data em que a conta vai expirar e após a qual conexões de Área de Trabalho Remota serão bloqueadas.

8. Depois de ter fornecido todas as informações necessárias, selecione **OK**. O Visual Studio adiciona as configurações de Área de Trabalho Remota aos arquivos `.cscfg` e `.csdef` do seu projeto, inclusive a senha que é criptografada usando o certificado escolhido.

9. Conclua as etapas restantes usando o botão **Próximo** e, em seguida, selecione **Publicar** quando estiver pronto para publicar seu serviço de nuvem. Se você não estiver pronto para publicar, selecione **Cancelar** e resposta **Sim** quando for solicitado a salvar as alterações. Você pode publicar seu serviço de nuvem mais tarde com essas configurações.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configurar Área de Trabalho Remota ao usar o Visual Studio 2017 versão 15.5 e posteriores

Com o Visual Studio 2017 versão 15.5 e posteriores, você ainda pode usar o assistente de publicação com um projeto de serviço de nuvem. Você também pode usar a opção **Habilitar a Área de Trabalho Remota para todas as funções** se você estiver trabalhando apenas como um único desenvolvedor.

Se você estiver trabalhando como parte de uma equipe, em vez disso, você deve habilitar a área de trabalho remota no serviço de nuvem do Azure usando o [portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Essa recomendação é devido a uma alteração em como o Visual Studio 2017 versão 15.5 e posteriores se comunica com o serviço de nuvem VM. Ao habilitar a Área de Trabalho Remota por meio do assistente de publicação, as versões anteriores do Visual Studio se comunicam com a VM por meio do que chamamos de "plug-in RDP". Visual Studio 2017 versão 15.5 e posteriores, por sua vez, se comunicam usando a "extensão RDP", que é mais segura e mais flexível. Essa alteração também alinha com o fato de que o portal do Azure e os métodos do PowerShell para habilitar a Área de Trabalho Remota também usam a extensão RDP.

Quando o Visual Studio se comunica com a extensão RDP, ele transmite uma senha de texto sem formatação por TLS. No entanto, os arquivos de configuração do projeto armazenam apenas uma senha criptografada, que pode ser descriptografada em texto sem formatação somente com o certificado local que foi usado originalmente para criptografá-lo.

Se você implantar o projeto de serviço de nuvem no mesmo computador de desenvolvimento a cada vez, esse certificado local estará disponível. Nesse caso, você ainda pode usar a opção **Habilitar a Área de Trabalho Remota para todas as funções** no assistente de publicação.

No entanto, se você ou outros desenvolvedores quiserem implantar o projeto de serviço de nuvem de diferentes computadores, esses outros computadores não terão o certificado necessário para descriptografar a senha. Como resultado, se você vir a seguinte mensagem de erro:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Você pode alterar a senha sempre que você implantar o serviço de nuvem, mas essa ação torna-se inconveniente para qualquer pessoa que precise usar a Área de Trabalho Remota.

Se você estiver compartilhando o projeto com uma equipe, será melhor limpar a opção no assistente de publicação e, em vez disso, habilitar a Área de Trabalho Remota diretamente por meio do [portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou usando [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implantação de um servidor de compilação com o Visual Studio 2017 versão 15.5 e posterior

Você pode implantar um projeto de serviço de nuvem de um servidor de build (por exemplo, com o Azure DevOps Services) no qual o Visual Studio 2017 versão 15.5 ou posterior está instalado no agente de build. Com essa organização, a implantação ocorre no mesmo computador no qual o certificado de criptografia está disponível.

Para usar a extensão RDP do Azure DevOps Services, inclua os seguintes detalhes na pipeline de build:

1. Incluir `/p:ForceRDPExtensionOverPlugin=true` em seus argumentos de MSBuild para verificar se a implantação funciona com a extensão RDP em vez de plug-in do RDP. Por exemplo:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Após as etapas de compilação, adicione a etapa **Implantação de Serviço de Nuvem do Azure** e defina suas propriedades.

1. Após a etapa da implantação, adicione uma etapa **Azure Powershell**, defina sua propriedade **Nome de exibição** para "Implantação do Azure: Habilitar extensão RDP" (ou outro nome adequado) e selecione a assinatura do Azure apropriada.

1. Defina o **Tipo de Script** para "Embutido" e cole o código abaixo no campo **Script embutido**. (Você também pode criar um arquivo `.ps1` em seu projeto com esse script, defina **Tipo de Script** para "Caminho do arquivo de Script" e defina **Caminho de Script** para apontar para o arquivo.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Conectar a uma Função do Azure usando a Área de Trabalho Remota

Depois de publicar seu serviço de nuvem no Azure e habilitar a Área de Trabalho Remota, você pode usar o Gerenciador de Servidores do Visual Studio para fazer logon na VM do serviço de nuvem:

1. No Gerenciador de Servidores, expanda o nó **Azure** e, em seguida, expanda o nó para um serviço de nuvem e uma de suas funções para exibir uma lista de instâncias.

2. Clique com o botão direito em um nó da instância e selecione **Conectar-se usando a Área de Trabalho Remota**.

3. Insira o nome de usuário e a senha que você criou anteriormente. Agora você está conectado na sessão remota.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar serviços de nuvem](cloud-services-how-to-configure-portal.md)
