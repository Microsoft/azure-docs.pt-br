---
title: Instruções detalhadas do Azure Status Monitor v2 | Microsoft Docs
description: Instruções detalhadas para introdução ao Status Monitor v2. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 02f4fa45cbfa619825478520961b6411459973e4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326269"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Status Monitor v2: Instruções detalhadas

Este artigo descreve como carregar o Galeria do PowerShell e baixar o módulo ApplicationMonitor.
Estão incluídos os parâmetros mais comuns que você precisará para começar.
Também fornecemos instruções de download manual caso você não tenha acesso à Internet.

## <a name="get-an-instrumentation-key"></a>Obter uma chave de instrumentação

Para começar, você precisa de uma chave de instrumentação. Para obter mais informações, consulte [criar um recurso de Application insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Executar o PowerShell como administrador com uma política de execução elevada

### <a name="run-as-admin"></a>Executar como administrador

O PowerShell precisa de permissões de nível de administrador para fazer alterações em seu computador.
### <a name="execution-policy"></a>Política de execução
- Descrição: Por padrão, a execução de scripts do PowerShell está desabilitada. É recomendável permitir scripts RemoteSigned apenas para o escopo atual.
- Referência: [Sobre as políticas](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) de [execução e Set](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)-ExecutionPolicy.
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parâmetro opcional:
    - `-Force`. Ignora o prompt de confirmação.

**Erros de exemplo**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

Faça auditoria da instância do PowerShell executando o `$PSVersionTable` comando.
Esse comando produz a seguinte saída:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Essas instruções foram escritas e testadas em um computador que executa o Windows 10 e as versões listadas acima.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para Galeria do PowerShell

Essas etapas prepararão o servidor para baixar módulos de Galeria do PowerShell.

> [!NOTE] 
> Galeria do PowerShell tem suporte no Windows 10, no Windows Server 2016 e no PowerShell 6.
> Para obter informações sobre versões anteriores, consulte Instalando o [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Execute o PowerShell como administrador com uma política de execução elevada.
2. Instale o provedor de pacote NuGet.
    - Descrição: Você precisa desse provedor para interagir com os repositórios baseados em NuGet, como Galeria do PowerShell.
    - Referência: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.
        - `-Force`. Ignora o prompt de confirmação.
    
    Você receberá essa solicitação se o NuGet não estiver configurado:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configure Galeria do PowerShell como um repositório confiável.
    - Descrição: Por padrão, Galeria do PowerShell é um repositório não confiável.
    - Referência: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parâmetro opcional:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.

    Você receberá essa solicitação se Galeria do PowerShell não for confiável:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Você pode confirmar essa alteração e auditar todos os PSRepositories executando `Get-PSRepository` o comando.

4. Instale a versão mais recente do PowerShellGet.
    - Descrição: Esse módulo contém as ferramentas usadas para obter outros módulos de Galeria do PowerShell. A versão 1.0.0.1 é fornecida com o Windows 10 e o Windows Server. A versão 1.6.0 ou superior é necessária. Para determinar qual versão está instalada, execute o `Get-Command -Module PowerShellGet` comando.
    - Referência: [Instalando o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.
        - `-Force`. Ignora o aviso "já instalado" e instala a versão mais recente.

    Você receberá esse erro se não estiver usando a versão mais recente do PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reinicie o PowerShell. Não é possível carregar a nova versão na sessão atual. Novas sessões do PowerShell carregarão a versão mais recente do PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Baixe e instale o módulo por meio do Galeria do PowerShell

Essas etapas baixarão o módulo AZ. ApplicationMonitor de Galeria do PowerShell.

1. Certifique-se de que todos os pré-requisitos para Galeria do PowerShell sejam atendidos.
2. Execute o PowerShell como administrador com uma política de execução elevada.
3. Instale o módulo AZ. ApplicationMonitor.
    - Referência: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.
        - `-AllowPrerelease`. Permite a instalação de versões alfa e beta.
        - `-AcceptLicense`. Ignora o prompt "aceitar licença"
        - `-Force`. Ignora o aviso de "repositório não confiável".

## <a name="download-and-install-the-module-manually-offline-option"></a>Baixar e instalar o módulo manualmente (opção offline)

Se por algum motivo você não puder se conectar ao módulo do PowerShell, poderá baixar e instalar manualmente o módulo AZ. ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Baixar manualmente o arquivo nupkg mais recente

1. Vá para https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecione a versão mais recente do arquivo na tabela de **histórico de versão** .
3. Em **Opções de instalação**, selecione **download manual**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opção 1: Instalar em um diretório de módulos do PowerShell
Instale o módulo do PowerShell baixado manualmente em um diretório do PowerShell para que ele possa ser descoberto por sessões do PowerShell.
Para obter mais informações, consulte [instalando um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Descompacte nupkg como um arquivo zip usando Expand-Archive (v 1.0.1.0)

- Descrição: A versão base do Microsoft. PowerShell. Archive (v 1.0.1.0) não pode descompactar arquivos nupkg. Renomeie o arquivo com a extensão. zip.
- Referência: [Expand-arquivo](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Descompactar nupkg usando Expand-Archive (v 1.1.0.0)

- Descrição: Use uma versão atual de Expand-Archive para descompactar arquivos nupkg sem alterar a extensão.
- Referência: [Expanda-arquivo](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opção 2: Descompactar e importar nupkg manualmente
Instale o módulo do PowerShell baixado manualmente em um diretório do PowerShell para que ele possa ser descoberto por sessões do PowerShell.
Para obter mais informações, consulte [instalando um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Se você estiver instalando o módulo em qualquer outro diretório, importe manualmente o módulo usando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> As DLLs serão instaladas por meio de caminhos relativos.
> Armazene o conteúdo do pacote em seu diretório de tempo de execução pretendido e confirme se as permissões de acesso permitem leitura, mas não gravação.

1. Altere a extensão para ". zip" e extraia o conteúdo do pacote em seu diretório de instalação pretendido.
2. Localize o caminho do arquivo AZ. ApplicationMonitor. psd1.
3. Execute o PowerShell como administrador com uma política de execução elevada.
4. Carregue o módulo usando o `Import-Module Az.ApplicationMonitor.psd1` comando.
    

## <a name="route-traffic-through-a-proxy"></a>Rotear o tráfego por meio de um proxy

Ao monitorar um computador em sua intranet privada, você precisará rotear o tráfego HTTP por meio de um proxy.

Os comandos do PowerShell para baixar e instalar o AZ. ApplicationMonitor da galeria do PowerShell dão `-Proxy` suporte a um parâmetro.
Examine as instruções anteriores ao escrever seus scripts de instalação.

O SDK do Application Insights precisará enviar a telemetria do aplicativo à Microsoft. Recomendamos que você defina as configurações de proxy para seu aplicativo em seu arquivo Web. config. Para obter mais informações, [consulte perguntas frequentes Application insights: Passagem](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)de proxy.


## <a name="enable-monitoring"></a>Habilitar o monitoramento

Use o `Enable-ApplicationInsightsMonitoring` comando para habilitar o monitoramento.

Consulte a [referência da API](status-monitor-v2-api-enable-monitoring.md) para obter uma descrição detalhada de como usar esse cmdlet.



## <a name="next-steps"></a>Próximas etapas

 Exiba sua telemetria:

- [Explore](../../azure-monitor/app/metrics-explorer.md) as métricas para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use a análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Crie painéis](../../azure-monitor/app/overview-dashboard.md).

 Adicione mais telemetria:

- [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
- [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.

Faça mais com Status Monitor v2:

- Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
