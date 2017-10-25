---
title: "Implantação Contínua de DSC de Automação do Azure com Chocolatey | Microsoft Docs"
description: "Implantação contínua de DevOps usando DSC de Automação do Azure e o gerenciador de pacotes Chocolatey.  Exemplo com modelo ARM JSON completo e fonte do PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: c0baa411-eb76-4f91-8d14-68f68b4805b6
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/29/2016
ms.author: golive
ms.openlocfilehash: f23d7374a8954a0a95853fa9e00b54a8d9c468c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Exemplo de uso: implantação contínua em Máquinas Virtuais usando a DSC de Automação e o Chocolatey
Em um mundo de DevOps, há várias ferramentas para ajudá-lo em vários pontos no pipeline de Integração Contínua.  A DSC (Configuração do Estado Desejado) da Automação do Azure é uma nova adição bem-vinda para as opções que podem ser utilizadas por equipes de DevOps.  Este artigo demonstra a configuração da CD (Implantação Contínua) para um computador com Windows.  Você pode facilmente ampliar a técnica para incluir tantos computadores com Windows quantos forem necessários na função (um site, por exemplo) e também para funções adicionais.

![Implantação contínua para VMs do IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Em um alto nível
Há bem pouco acontecendo aqui, mas, felizmente, é possível dividir tudo em dois processos principais: 

* Escrever código e testá-lo e, depois, criar e publicar pacotes de instalação para as versões principais e secundárias do sistema. 
* Criar e gerenciar VMs que vão instalar e executar o código nos pacotes.  

Depois que esses dois processos principais são realizados, leva pouco tempo para atualizar automaticamente o pacote em execução em qualquer VM específica, à medida que novas versões são criadas e implantadas.

## <a name="component-overview"></a>Visão geral do componente
Os gerenciadores de pacotes como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são muito conhecidos no mundo do Linux, mas nem tanto no mundo do Windows.  O [Chocolatey](https://chocolatey.org/) é um deles, e o [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre esse tópico é uma excelente introdução.  Resumindo, o Chocolatey permite instalar pacotes de um repositório central de pacotes em um sistema do Windows usando a linha de comando.  Você pode criar e gerenciar seu próprio repositório, e o Chocolatey pode instalar pacotes de qualquer repositório que você designar.

A DSC (Configuração de Estado Desejado) ([visão geral](https://technet.microsoft.com/library/dn249912.aspx)) é uma ferramenta do PowerShell que permite declarar a configuração que você deseja usar em um computador.  Por exemplo, você pode dizer: "Quero o Chocolatey instalado, quero o IIS instalado, quero a porta 80 aberta, quero a versão 1.0.0 de meu site instalada".  O LCM (Gerenciador de configuração Local) da DSC implementa essa configuração. Um Servidor de recepção da DSC mantém um repositório de configurações para seus computadores. O LCM em cada computador verifica periodicamente se sua configuração corresponde à configuração armazenada. Ele pode relatar o status ou tentar realinhar o computador com a configuração armazenada. Você pode editar a configuração armazenada no servidor de recepção para alinhar um computador ou um conjunto de computadores com a configuração alterada.

A Automação do Azure é um serviço gerenciado no Microsoft Azure que permite automatizar várias tarefas usando runbooks, nós, credenciais, recursos e ativos, como agendamentos e variáveis globais. A DSC de Automação do Azure amplia esse recurso de automação para incluir ferramentas da DSC do PowerShell.  Confira uma excelente [visão geral](automation-dsc-overview.md).

Um Recurso DSC é um módulo de código que tem recursos específicos, como gerenciar redes, o Active Directory ou o SQL Server.  O Recurso DSC do Chocolatey sabe como acessar um Servidor do NuGet (entre outros), baixar e instalar pacotes e assim por diante.  Há muitos outros Recursos de DSC na [Galeria do PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Esses módulos são instalados no Servidor de Recepção da DSC de Automação do Azure (por você) para que possam ser usados por suas configurações.

Os modelos do Resource Manager fornecem uma maneira declarativa de gerar sua infraestrutura (itens como redes, sub-redes, roteamento e segurança de rede, balanceadores de carga, NICs, VMs e assim por diante).  Aqui está um [artigo](../azure-resource-manager/resource-manager-deployment-model.md) que compara o modelo de implantação de Resource Manager (declarativo) com o modelo de implantação do Azure Service Management (ASM ou clássico) (imperativo) e discute os principais provedores de recursos, computação, armazenamento e rede.

Um recurso principal de um modelo do Resource Manager é sua capacidade de instalar uma extensão de VM na VM quando ela é provisionada.  Uma extensão de VM tem recursos específicos, como executar um script personalizado, instalar software antivírus ou executar um script de configuração de DSC.  Há muitos outros tipos de extensões de VM.

## <a name="quick-trip-around-the-diagram"></a>Explicação rápida do diagrama
Começando pela parte superior, você escreve o código, compila e testa e, depois, cria um pacote de instalação.  O Chocolatey pode manipular vários tipos de pacotes de instalação, como MSI, MSU e ZIP.  Você conta com toda a capacidade do PowerShell para realizar a instalação real, caso os recursos nativos do Chocolatey não sejam suficientes.  Coloque o pacote em algum lugar acessível – um repositório de pacotes.  Este exemplo de uso usa uma pasta pública em uma conta de armazenamento de blob do Azure, mas pode estar em qualquer outro lugar.  O Chocolatey funciona nativamente com servidores do NuGet e alguns outras para o gerenciamento de metadados de pacote.  [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções.  Este exemplo de uso usa o NuGet.  Um Nuspec consiste em metadados sobre seus pacotes.  O Nuspec é "compilado" no NuPkg e armazenado em um servidor do NuGet.  Quando a configuração solicita um pacote por nome e faz referência a um servidor do NuGet, o Recurso DSC do Chocolatey (agora na VM) obtém o pacote e o instala para você.  Você também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo do ARM (Gerenciador de Recursos do Azure).  Neste exemplo de uso, a extensão de VM registra a VM no Servidor de Recepção da DSC de Automação do Azure (ou seja, um servidor de recepção) como um Nó.  A configuração é armazenada no servidor de recepção.  Na verdade, ela é armazenada duas vezes: uma vez como texto sem formatação e uma vez compilada como um arquivo MOF (para aqueles que os conhecem.)  No portal, o MOF é uma "configuração de nó" (em vez de simplesmente uma "configuração").  É o artefato associado a um nó para que o nó saiba sua configuração.  Os detalhes a seguir mostram como atribuir a configuração de nó ao nó.

Provavelmente, você já está realizando a ação na parte superior, ou a maior parte dela.  Criar o nuspec, compilá-lo e armazená-lo em um servidor do NuGet é uma tarefa pequena.  E você já está gerenciando VMs.  O próximo passo para a implantação contínua requer a configuração do servidor de recepção (uma vez), o registro dos nós (uma vez) e a criação e o armazenamento da configuração neles (inicialmente).  Em seguida, à medida que os pacotes forem atualizados e implantados no repositório, atualize a Configuração e a Configuração de Nó no servidor de recepção (repita conforme necessário).

Se você não estiver começando com um modelo do ARM, isso também está OK.  Há cmdlets do PowerShell projetados para ajudá-lo a registrar suas VMs no servidor de recepção e todo o restante. Para obter mais detalhes, confira este artigo: [Integrando máquinas para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Etapa 1: configurar o servidor de recepção e a conta de automação
Em uma linha de comando do PowerShell (Add-AzureRmAccount) autenticada: (pode demorar alguns minutos enquanto o servidor pull é configurado)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Você pode colocar sua conta de automação em qualquer uma das seguintes regiões (também conhecido como local): Leste dos EUA 2, Centro-Sul dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Sudeste Asiático, Leste do Japão, Índia Central e Sudeste da Austrália, Canadá Central e Europa Setentrional.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Etapa 2: ajustes da extensão de VM para o modelo ARM
Detalhes do registro de VM (usando a extensão de VM de DSC do PowerShell) são fornecidos neste [Modelo de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Esta etapa registra sua nova VM no servidor de recepção na lista de Nós DSC.  Parte do registro especifica a configuração de nó a ser aplicada ao nó.  Essa configuração de nó não precisa existir ainda no servidor pull, portanto, não há problemas se isso for feito pela primeira vez apenas na Etapa 4.  Mas aqui na Etapa 2, é necessário decidir o nome do nó e o nome da configuração.  Neste exemplo de uso, o nó é “isvbox” e a configuração é “ISVBoxConfig”.  Portanto, o nome da configuração de nó (a ser especificado em DeploymentTemplate.json) é “ISVBoxConfig.isvbox”.  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Etapa 3: adicionar recursos de DSC necessários para o servidor de recepção
A Galeria do PowerShell é instrumentada para instalar recursos de DSC em sua conta da Automação do Azure.  Navegue até o recurso desejado e clique no botão "Implantar na Automação do Azure".

![Exemplo da Galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao Portal do Azure permite criar novos módulos ou atualizar módulos existentes. Clique no recurso Conta de Automação, no bloco Ativos e, por fim, no bloco Módulos.  O ícone Procurar na Galeria permite ver a lista de módulos na galeria, filtrar para ver detalhes e, por fim, importar para sua Conta de Automação. Isso é uma ótima maneira de manter atualizados os módulos periodicamente. Além disso, o recurso de importação verifica dependências com outros módulos para garantir que nada esteja fora de sincronização.

Ou então, há a abordagem manual.  A estrutura de pastas de um Módulo de Integração do PowerShell para um computador com Windows é um pouco diferente da estrutura de pastas esperada pela Automação do Azure.  Isso exige que você faça alguns ajustes.  Porém, não é difícil, e isso só precisa ser feito uma vez por recurso (a menos que você deseje atualizá-lo no futuro).  Para saber mais sobre como criar Módulos de Integração do PowerShell, confira este artigo: [Criando Módulos de Integração para a Automação do Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Instale o módulo necessário na estação de trabalho, da seguinte maneira:
  * Instale o [Windows Management Framework, v5](http://aka.ms/wmf5latest) (não é necessário para o Windows 10)
  * `Install-Module –Name MODULE-NAME`    <—captura o módulo da Galeria do PowerShell 
* Copie a pasta de módulo de `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` em uma pasta temporária 
* Exclua os exemplos e a documentação da pasta principal 
* Compacte a pasta principal, nomeando o arquivo ZIP exatamente como a pasta 
* Coloque o arquivo ZIP em um local http acessível, como o armazenamento de blobs em uma Conta do Armazenamento do Azure.
* Execute este PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

O exemplo incluído executa estas etapas para cChoco e xNetworking. Veja as [Observações](#notes) para obter o tratamento especial para o cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Etapa 4: adicionar a configuração de nó ao servidor de recepção
Não há nada de especial na primeira vez que você importa a configuração para o servidor de recepção e realiza a compilação.  Todas as importações/compilações subsequentes da mesma configuração têm exatamente a mesma aparência.  Sempre que atualiza o pacote e precisa enviá-lo para produção, você realiza esta etapa após garantir que o arquivo de configuração está correto, incluindo a nova versão do pacote.  Aqui estão o arquivo de configuração e o PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

    Import-AzureRmAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force

    $jobData = Start-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 

    $compilationJobId = $jobData.Id

    Get-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Essas etapas resultam em uma nova configuração de nó chamada “ISVBoxConfig.isvbox” colocada no servidor de recepção.  O nome da configuração de nó é compilado como “configurationName.nodeName”.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Etapa 5: criar e manter metadados de pacote
Para cada pacote que você coloca no repositório de pacotes, é necessário um nuspec que o descreve.  Esse nuspec deve ser compilado e armazenado em seu servidor do NuGet. Este processo é descrito [aqui](http://docs.nuget.org/create/creating-and-publishing-a-package).  Você pode usar MyGet.org como um servidor do NuGet.  Eles vendem este serviço, mas há uma SKU inicial que é gratuita.  Em NuGet.org, você encontrará instruções sobre como instalar seu próprio servidor do NuGet para seus pacotes privados.

## <a name="step-6-tying-it-all-together"></a>Etapa 6: reunir tudo isso
Sempre que uma versão passar na garantia de qualidade e for aprovada para implantação, o pacote será criado e o nuspec e o nupkg serão atualizados e implantados no servidor do NuGet.  Além disso, a configuração (Etapa 4 acima) deve ser atualizada de acordo com o novo número de versão.  Ela deve ser enviada para o servidor de recepção e compilada.  Daí em diante, as VMs que dependem dessa configuração serão responsáveis por receber a atualização e instalá-la.  Cada uma dessas atualizações é simples - apenas uma ou duas linhas do PowerShell.  No caso do Visual Studio Team Services, algumas delas são encapsuladas em tarefas de compilação que podem ser encadeadas juntas em uma compilação.  Este [artigo](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes.  Este [repositório GitHub](https://github.com/Microsoft/vso-agent-tasks) fornece detalhes das várias tarefas de compilação disponíveis.

## <a name="notes"></a>Observações
Este exemplo de uso começa com uma VM de uma imagem genérica do Windows Server 2012 R2 da galeria do Azure.  Você poderá iniciar de qualquer imagem armazenada e ajustá-la com a configuração da DSC.  No entanto, é muito mais difícil alterar a configuração incorporada a uma imagem do que atualizar de forma dinâmica a configuração usando a DSC.

Você não precisa usar um modelo ARM e a extensão de VM para usar essa técnica com suas VMs.  E suas VMs não precisam estar no Azure para estar no gerenciamento de CD.  Basta que o Chocolatey seja instalado e o LCM seja configurado na máquina virtual para que ele saiba onde está o servidor de recepção.  

É claro que, ao atualizar um pacote em uma VM que está em produção, você precisa colocar a VM fora de rotação enquanto a atualização é instalada.  A maneira de fazer isso varia muito.  Por exemplo, com uma VM por trás de um Balanceador de Carga do Azure, você pode adicionar uma Sonda Personalizada.  Ao atualizar a VM, faça com que o ponto de extremidade da sonda retorne um 400.  O ajuste necessário para fazer essa alteração pode ser dentro de sua configuração, da mesma forma como o ajuste para que 200 volte a ser retornado depois que a atualização for concluída.

O código-fonte completo deste exemplo de uso está [neste projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral da DSC da Automação do Azure](automation-dsc-overview.md)
* [cmdlets da DSC de Automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)

