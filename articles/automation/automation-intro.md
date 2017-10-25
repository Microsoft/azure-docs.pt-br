---
title: "O que é a Automação do Azure | Microsoft Docs"
description: "Saiba qual valor a Automação do Azure fornece e obtenha respostas para perguntas comuns para que você pode começar a criar usando runbooks e DSC de Automação do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "o que é automação, automação do azure, exemplos de automação do azure"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
ms.openlocfilehash: c9f5c346350a62540ba29444b1b00cc9dc398d56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-overview"></a>Visão geral da Automação do Azure
A Automação do Microsoft Azure fornece uma maneira para os usuários automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que normalmente são executadas em um ambiente de nuvem e corporativo. Ele economiza tempo e aumenta a confiabilidade das tarefas administrativas regulares e até mesmo agenda a sua execução automática em intervalos regulares. Você pode automatizar processos usando runbooks ou automatizar o gerenciamento de configuração usando Configuração de Estado Desejado. Este artigo fornece uma visão geral da Automação do Azure e responde a perguntas comuns. Você pode consultar outros artigos desta biblioteca para obter informações mais detalhadas sobre os diferentes tópicos.

## <a name="automating-processes-with-runbooks"></a>Automatizando processos com runbooks
Um runbook é um conjunto de tarefas que executa um processo automatizado na Automação do Azure. Pode ser um processo simples, como iniciar uma máquina virtual e criar uma entrada de log, ou você pode ter um runbook complexo que combina outros runbooks menores para executar um processo complexo em vários recursos ou até mesmo várias nuvens e em ambientes locais.  

Por exemplo, você pode ter um processo manual existente para truncar um banco de dados SQL, que esteja prestes a atingir o volume máximo, que inclui várias etapas, como conectar-se ao servidor, conectar-se ao banco de dados, obter o tamanho atual do banco de dados, verificar se o limite foi excedido a fim de truncar e notificar o usuário. Em vez de executar cada uma dessas etapas manualmente, você pode criar um runbook que executa todas essas tarefas como um único processo. Você inicia o runbook, fornece as informações necessárias, como o nome do servidor SQL, o nome do banco de dados e o email do destinatário, e aguarda enquanto o processo é concluído. 

## <a name="what-can-runbooks-automate"></a>O que os runbooks podem automatizar?
Os runbooks na Automação do Azure são baseados no Windows PowerShell ou Fluxo de Trabalho do Windows PowerShell, portanto eles fazem tudo o que o PowerShell pode fazer. Se um aplicativo ou serviço tiver uma API, um runbook pode trabalhar com ele. Se você tiver um módulo do PowerShell para o aplicativo, pode carregar esse módulo na Automação do Azure e incluir esses cmdlets em seu runbook. Os runbooks da Automação do Azure são executados na nuvem do Azure e podem acessar qualquer recurso de nuvem ou recursos externos que podem ser acessados da nuvem. Usando o [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), os runbooks podem ser executados em seu data center local para gerenciar recursos locais. 

## <a name="getting-runbooks-from-the-community"></a>Obtendo runbooks da comunidade
A [Galeria de Runbooks](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contém runbooks da Microsoft e da comunidade que você pode usar inalterados em seu ambiente ou personalizá-los para suas próprias finalidades. Eles também são úteis como referências para aprender a criar seus próprios runbooks. Você ainda pode contribuir na galeria com seus próprios runbooks que acredita que os outros usuários podem considerar úteis. 

## <a name="creating-runbooks-with-azure-automation"></a>Criação de Runbooks com a Automação do Azure
Você pode [criar seus próprios runbooks](automation-creating-importing-runbook.md) do zero ou modificar runbooks da [Galeria de Runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) para seus próprios requisitos. Há quatro [tipos de runbook](automation-runbook-types.md) diferentes entre os quais você pode escolher com base em seus requisitos e experiência do PowerShell. Se você preferir trabalhar diretamente com o código do PowerShell, use um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou [runbook de Fluxo de Trabalho](automation-runbook-types.md#powershell-workflow-runbooks) do PowerShell que você edita offline ou com o [editor de texto](http://msdn.microsoft.com/library/azure/dn879137.aspx) no Portal do Azure. Se preferir editar um runbook sem ser exposto ao código subjacente, crie um [Runbook gráfico](automation-runbook-types.md#graphical-runbooks) usando o [editor gráfico](automation-graphical-authoring-intro.md) no portal do Azure. 

Prefere assistir do que ler? Examine o vídeo abaixo da sessão do Microsoft Ignite de maio de 2015. Observação: embora os conceitos e recursos discutidos neste vídeo estejam corretos, a Automação do Azure avançou muito desde que esse vídeo foi gravado, e agora possui uma interface de usuário mais ampla no Portal do Azure e oferece suporte a recursos adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatizando o gerenciamento de configuração com a Configuração de Estado Desejado
[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) é uma plataforma de gerenciamento que permite gerenciar, implantar e impor a configuração em hosts físicos e máquinas virtuais usando uma sintaxe declarativa do PowerShell. Você pode definir configurações em um servidor de recepção DSC central que podem ser recuperadas e aplicadas automaticamente por computadores de destino. O DSC fornece um conjunto de cmdlets do PowerShell que você pode usar para gerenciar configurações e recursos.  

[DSC de Automação do Azure](automation-dsc-overview.md) é uma solução baseada em nuvem para DSC do PowerShell que fornece os serviços necessários para ambientes corporativos.  Você pode gerenciar seus recursos de DSC na Automação do Azure e aplicar as configurações a máquinas virtuais ou físicas que as recuperam de um servidor de recepção DSC na nuvem do Azure.  Ele também fornece serviços de relatório que informam sobre eventos importantes, como nós que desviam de sua configuração atribuída e quando uma nova configuração foi aplicada. 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Criando suas próprias configurações DSC com a Automação do Azure
[Configurações DSC](automation-dsc-overview.md) especifica o estado desejado de um nó.  Vários nós podem aplicar a mesma configuração para garantir que todos eles mantenham um estado idêntico.  Você pode criar uma configuração que usa qualquer editor de texto em seu computador local e importá-la para Automação do Azure, onde poderá compilá-la e aplicar nós.

## <a name="getting-modules-and-configurations"></a>Obtendo módulos e configurações
Você pode obter [módulos do PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) contendo cmdlets que você pode usar em seus runbooks e configurações de DSC da [Galeria do PowerShell](http://www.powershellgallery.com/). Você pode iniciar esta galeria no portal do Azure e importar módulos diretamente na Automação do Azure ou pode baixá-los e instalá-los manualmente. Não é possível instalar os módulos diretamente do portal do Azure, mas você pode baixá-los e instalá-los como o faria com qualquer módulo. 

## <a name="example-practical-applications-of-azure-automation"></a>Aplicações de exemplo da Automação do Azure
A seguir temos alguns exemplos de quais são os tipos de cenários de automação com a Automação do Azure. 

* Criar e copiar máquinas virtuais em diferentes assinaturas do Azure. 
* Agendar cópias de arquivos de um computador local para um contêiner de armazenamento de Blob do Azure. 
* Automatizar funções de segurança, como negar solicitações de um cliente, quando um ataque de negação de serviço é detectado. 
* Verifique se as máquinas se alinham continuamente com a política de segurança configurada.
* Gerencie a implantação contínua do código do aplicativo na nuvem e na infraestrutura local. 
* Crie uma floresta do Active Directory no Azure para seu ambiente de laboratório. 
* Truncar uma tabela em um banco de dados SQL se ele estiver se aproximando do tamanho máximo. 
* Atualize remotamente as configurações do ambiente de um Site do Azure 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Como a Automação do Azure se relaciona com outras ferramentas de automação?
[SMA (Service Management Automation)](http://technet.microsoft.com/library/dn469260.aspx) destina-se a automatizar tarefas de gerenciamento na nuvem privada. Ele é instalado localmente em seu data center como um componente do [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). O SMA e a Automação do Azure usam o mesmo formato de runbook com base no fluxo de trabalho do Windows PowerShell e no Fluxo de Trabalho do Windows PowerShell, mas o SMA não dá suporte para [runbooks gráficos](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) destina-se à automação de recursos locais. Ele usa um formato de runbook diferente da Automação do Azure e do Service Management Automation e tem uma interface gráfica para criar runbooks sem a necessidade de qualquer script. Seus runbooks são compostos por atividades de Pacotes de Integração que são escritos especificamente para o Orchestrator. 

## <a name="where-can-i-get-more-information"></a>Onde posso obter mais informações?
Uma variedade de recursos está disponível para você aprender mais sobre a Automação do Azure e criar seus próprios runbooks. 

* **Biblioteca da Automação do Azure** é onde você está agora. Os artigos nessa biblioteca fornecem a documentação completa sobre a configuração e administração da Automação do Azure e para a criação de seus próprios runbooks. 
* [Cmdlets do Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fornece informações para automatizar operações do Azure usando o Windows PowerShell. Os runbooks usam esses cmdlets para trabalhar com recursos do Azure. 
* [Blog de Gerenciamento](https://azure.microsoft.com/blog/tag/azure-automation/) fornece as informações mais recentes sobre a Automação do Azure e outras tecnologias de gerenciamento da Microsoft. Você deve assinar esse blog para se manter atualizado com o que há de mais recente da equipe de Automação do Azure. 
* [Fórum de automação](http://go.microsoft.com/fwlink/p/?LinkId=390561) permite que você poste dúvidas sobre a Automação do Azure a serem resolvidas pela Microsoft e pela comunidade de automação. 
* [Cmdlets de Automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx) fornece informações sobre como automatizar tarefas administrativas. Ele contém cmdlets para gerenciar contas de automação, ativos, runbooks, DSC.

## <a name="can-i-provide-feedback"></a>Posso fornecer comentários?
**Envie-nos comentários!** Se você estiver procurando por uma solução de runbook de Automação do Azure ou por um módulo de integração, poste uma Solicitação de script no Script Center. Se você tiver comentários ou solicitações de recurso para a Automação do Azure, poste-os em [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Obrigado! 

