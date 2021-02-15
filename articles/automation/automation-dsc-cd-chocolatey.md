---
title: Configurar a implantação contínua de DSC de Automação do Azure com Chocolatey
description: Este artigo informa como configurar a implantação contínua com a State Configuration e o gerenciador de pacotes de Chocolatey.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896318"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Configurar a implantação contínua com o Chocolatey

Em um mundo de DevOps, há várias ferramentas para ajudá-lo em vários pontos no pipeline de integração contínua. A [State Configuration](automation-dsc-overview.md) da Automação do Azure é uma nova adição bem-vinda às opções que as equipes do DevOps pode empregar. 

A Automação do Azure é um serviço gerenciado no Microsoft Azure que permite automatizar várias tarefas usando runbooks, nós e recursos compartilhados, como credenciais, agendamentos e variáveis globais. A Configuração de Estado da Automação do Azure amplia esse recurso de automação para incluir ferramentas da DSC (Desired State Configuration) do PowerShell. Confira uma excelente [visão geral](automation-dsc-overview.md).

Este artigo demonstra como configurar a CD (Implantação Contínua) para um computador com Windows. Você pode facilmente ampliar a técnica para incluir tantos computadores com Windows quantos forem necessários na função, por exemplo, um site e para funções adicionais.

![Implantação contínua para VMs do IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Em um alto nível

Há bem pouco acontecendo aqui, mas, felizmente, é possível dividir tudo em dois processos principais:

- Escrever código e testá-lo e, depois, criar e publicar pacotes de instalação para as versões principais e secundárias do sistema.
- Criar e gerenciar VMs que instalam e executam o código nos pacotes.  

Quando esses dois processos principais estiverem em vigor, será fácil atualizar automaticamente o pacote na sua VM específica à medida que novas versões são criadas e implantadas.

## <a name="component-overview"></a>Visão geral do componente

Os gerenciadores de pacotes como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são conhecidos no mundo do Linux, mas nem tanto no mundo do Windows.
O [Chocolatey](https://chocolatey.org/) é um deles e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o tópico é uma excelente introdução. Resumindo, o Chocolatey permite usar a linha de comando para instalar pacotes de um repositório central para um sistema operacional Windows. Você pode criar e gerenciar seu próprio repositório, e o Chocolatey pode instalar pacotes de qualquer repositório que você designar.

A [DSC do PowerShell](/powershell/scripting/dsc/overview/overview) é uma ferramenta do PowerShell que permite declarar a configuração que você deseja usar em um computador. Por exemplo, se você quiser que o Chocolatey esteja instalado, o IIS instalado, a porta 80 aberta e a versão 1.0.0 do seu site instalado, o LCM (Local Configuration Manager) de DSC implementa essa configuração. Um servidor de recepção da DSC mantém um repositório de configurações para seus computadores. O LCM em cada computador verifica periodicamente se sua configuração corresponde à configuração armazenada. Ele pode relatar o status ou tentar realinhar o computador com a configuração armazenada. Você pode editar a configuração armazenada no servidor de recepção para alinhar um computador ou um conjunto de computadores com a configuração alterada.

Um recurso DSC é um módulo de código que tem recursos específicos, como gerenciar redes, o Active Directory ou o SQL Server. O Recurso DSC do Chocolatey sabe como acessar um Servidor do NuGet (entre outros), baixar e instalar pacotes e assim por diante. Há muitos outros Recursos de DSC na [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Você instala esses módulos no servidor de pull de State Configuration da Automação do Azure para que possam ser usados por suas configurações.

Os modelos do Resource Manager fornecem uma maneira declarativa de gerar sua infraestrutura, por exemplo, redes, sub-redes, roteamento e segurança de rede, balanceadores de carga, NICs, VMs e assim por diante. Confira um [artigo](../azure-resource-manager/management/deployment-models.md) que compara o modelo de implantação do Resource Manager (declarativo) ao modelo de implantação do ASM (Gerenciamento de Serviços do Azure) ou clássico (imperativo). Este artigo inclui uma discussão sobre os principais provedores de recursos, computação, armazenamento e rede.

Um recurso principal de um modelo do Resource Manager é a capacidade de instalar uma extensão de VM na VM quando ela é provisionada. Uma extensão de VM tem recursos específicos, como executar um script personalizado, instalar software antivírus e executar um script de configuração de DSC. Há muitos outros tipos de extensões de VM.

## <a name="quick-trip-around-the-diagram"></a>Explicação rápida do diagrama

Começando pela parte superior, você escreve o código, compila e testa e, depois, cria um pacote de instalação. O Chocolatey pode manipular vários tipos de pacotes de instalação, como MSI, MSU e ZIP. Você conta com toda a capacidade do PowerShell para realizar a instalação real, caso os recursos nativos do Chocolatey não sejam suficientes. Coloque o pacote em algum lugar acessível – um repositório de pacotes. Este exemplo de uso usa uma pasta pública em uma conta de armazenamento de blob do Azure, mas pode estar em qualquer outro lugar. O Chocolatey funciona nativamente com servidores do NuGet e alguns outras para o gerenciamento de metadados de pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. Este exemplo de uso usa o NuGet. Um Nuspec consiste em metadados sobre seus pacotes. As informações do Nuspec são compiladas em um NuPkg e armazenadas em um servidor NuGet. Quando a configuração solicita um pacote por nome e faz referência a um servidor do NuGet, o Recurso DSC do Chocolatey (na VM) obtém o pacote e o instala. Você também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo do Azure Resource Manager. Nesse exemplo de uso, a extensão da VM registra a VM com o servidor de pull de State Configuration da Automação do Azure como um nó. A configuração é armazenada no servidor de pull duas vezes: uma vez como texto sem formatação e uma vez compilada como um arquivo MOF. No portal do Microsoft Azure, o MOF representa uma configuração de nó, em vez de simplesmente uma configuração. É o artefato associado a um nó para que o nó conheça a configuração. Os detalhes a seguir mostram como atribuir a configuração de nó ao nó.

Criar o Nuspec, compilá-lo e armazená-lo em um servidor do NuGet é uma tarefa pequena. E você já está gerenciando VMs. 

O próximo passo para a implantação contínua requer a configuração do servidor de pull uma vez, o registro dos nós uma vez e a criação e o armazenamento da configuração inicial neles. À medida que os pacotes forem atualizados e implantados no repositório, você só precisa atualizar a configuração e a configuração de nó no servidor de pull, conforme necessário.

Se você não está iniciando com um modelo do Resource Manager, está tudo bem. Há comandos do PowerShell projetados para ajudá-lo a registrar suas VMs no servidor de pull. Para saber mais, veja [Máquinas de integração para o gerenciamento pela State Configuration de Automação do Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Sobre o exemplo de uso

O exemplo de uso neste artigo começa com uma VM de uma imagem genérica do Windows Server 2012 R2 da galeria do Azure. Você poderá iniciar de qualquer imagem armazenada e ajustá-la com a configuração da DSC.
No entanto, é muito mais difícil alterar a configuração incorporada a uma imagem do que atualizar de forma dinâmica a configuração usando a DSC.

Não é necessário usar um modelo do Resource Manager e a extensão da VM para usar essa técnica com as VMs. E as VMs não precisam estar no Azure para estar sob gerenciamento de CD. Tudo o que é necessário é que o Chocolatey seja instalado e o LCM configurado na VM para que reconheça onde está o servidor de pull.

Ao atualizar um pacote em uma VM que está em produção, você precisa colocar a VM fora de rotação enquanto a atualização é instalada. A maneira de fazer isso varia muito. Por exemplo, com uma VM por trás de um Balanceador de Carga do Azure, você pode adicionar uma Sonda Personalizada. Ao atualizar a VM, faça com que o ponto de extremidade da sonda retorne um 400. O ajuste necessário para fazer essa alteração pode ser dentro de sua configuração, da mesma forma como o ajuste para que 200 volte a ser retornado depois que a atualização for concluída.

O código-fonte completo deste exemplo de uso está [neste projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Etapa 1: Configure o servidor de pull e a conta de automação

Em uma linha de comando do PowerShell autenticada (`Connect-AzAccount`): (pode demorar alguns minutos enquanto o servidor de pull é configurado)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar sua conta de automação em uma das seguintes regiões (também conhecidas como localizações): Leste dos EUA 2, Centro-Sul dos EUA, US Gov – Virgínia, Europa Ocidental, Sudeste Asiático, Leste do Japão, Índia Central e Sudeste da Austrália, Canadá Central e Norte da Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Etapa 2: Faça ajustes de extensão da VM para o modelo do Resource Manager

Detalhes do registro de VM (usando a extensão de VM de DSC do PowerShell) são fornecidos neste [Modelo de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Esta etapa registra a nova VM com o servidor de pull na lista de Nós da Configuração de Estado. Parte do registro especifica a configuração de nó a ser aplicada ao nó. Essa configuração de nó não precisa existir ainda no servidor pull, portanto, não há problemas se isso for feito pela primeira vez apenas na Etapa 4. Mas aqui na Etapa 2, é necessário decidir o nome do nó e o nome da configuração. Neste exemplo de uso, o nó é “isvbox” e a configuração é “ISVBoxConfig”. Portanto, o nome da configuração de nó (a ser especificado em DeploymentTemplate.json) é “ISVBoxConfig.isvbox”.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Etapa 3: Adicione recursos de DSC necessários para o servidor de pull

A Galeria do PowerShell é instrumentada para instalar recursos de DSC em sua conta da Automação do Azure.
Navegue até o recurso desejado e clique no botão "Implantar na Automação do Azure".

![Exemplo da Galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao portal do Azure permite criar novos módulos ou atualizar módulos existentes. Clique no recurso da conta de Automação, no bloco Ativos e, por fim, no bloco Módulos. O ícone Procurar na Galeria permite ver a lista de módulos na galeria, filtrar para ver detalhes e, por fim, importar para sua conta de Automação. Isso é uma ótima maneira de manter atualizados os módulos periodicamente. Além disso, o recurso de importação verifica dependências com outros módulos para garantir que nada esteja fora de sincronização.

Há também uma abordagem manual, usada apenas uma vez por recurso, a menos que você queira atualizá-la mais tarde. Para saber mais sobre como criar módulos de integração do PowerShell, confira [Criando Módulos de Integração para a Automação do Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>A estrutura de pastas de um módulo de integração do PowerShell para um computador com Windows é um pouco diferente da estrutura de pastas esperada pela Automação do Azure. 

1. Instale o [Windows Management Framework, v5](https://aka.ms/wmf5latest) (não é necessário para o Windows 10).

2. Instale o módulo de integração.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copie a pasta do módulo de **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** para uma pasta temporária.

4. Exclua os exemplos e a documentação da pasta principal.

5. Compacte a pasta principal, nomeando o arquivo ZIP com o nome da pasta.

6. Coloque o arquivo ZIP em um local http acessível, como o armazenamento de blobs em uma conta do Armazenamento do Microsoft Azure.

7. Execute o comando a seguir.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

O exemplo incluído implementa estas etapas para cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Etapa 4: Adicione a configuração de nó ao servidor de pull

Não há nada de especial na primeira vez que você importa a configuração para o servidor de pull e compila. Todas as importações/compilações posteriores da mesma configuração têm exatamente a mesma aparência. Sempre que atualiza o pacote e precisa enviá-lo para produção, você realiza esta etapa após garantir que o arquivo de configuração está correto, incluindo a nova versão do pacote. Aqui está o arquivo de configuração **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Aqui está o script de **New-ConfigurationScript.ps1** (modificado para usar o módulo AZ):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Essas etapas resultam em uma nova configuração de nó chamada **ISVBoxConfig.isvbox** colocada no servidor de pull. O nome da configuração do nó é criado como `configurationName.nodeName`.

## <a name="step-5-create-and-maintain-package-metadata"></a>Etapa 5: Criar e manter metadados de pacote

Para cada pacote que você coloca no repositório de pacotes, é necessário um nuspec que o descreve. Isso deve ser compilado e armazenado em seu servidor do NuGet. Este processo é descrito [aqui](https://docs.nuget.org/create/creating-and-publishing-a-package). 

Você pode usar **MyGet.org** como um servidor do NuGet. Você pode comprar esse serviço, mas há um SKU de início gratuito. No [NuGet](https://www.nuget.org/), você encontrará instruções sobre como instalar seu próprio servidor NuGet para os pacotes privados.

## <a name="step-6-tie-it-all-together"></a>Etapa 6: Coloque tudo isso junto

Sempre que uma versão passar na garantia de qualidade e for aprovada para implantação, o pacote será criado e o nuspec e o nupkg serão atualizados e implantados no servidor do NuGet. A configuração (etapa 4) também deve ser atualizada de acordo com o novo número de versão. Ela deve ser enviada para o servidor de pull e compilada.

Daí em diante, as VMs que dependem dessa configuração serão responsáveis por receber a atualização e instalá-la. Cada uma dessas atualizações é simples - apenas uma ou duas linhas do PowerShell. No caso do Azure DevOps, algumas delas são encapsuladas em tarefas de compilação que podem ser encadeadas juntas em uma compilação. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [repositório GitHub](https://github.com/Microsoft/vso-agent-tasks) fornece detalhes das várias tarefas de compilação disponíveis.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md)
* [Integração de computadores para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Visão geral da State Configuration da Automação do Azure](automation-dsc-overview.md).
- Para começar a usar o recurso, confira [Introdução ao State Configuration da Automação do Azure](automation-dsc-getting-started.md).
- Para saber como compilar configurações DSC para que possam ser atribuídas a nós de destino, confira [Compilar configurações DSC no State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [Preço da State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
