---
title: Converter configurações em recursos compostos na State Configuration de Automação do Azure
description: Este artigo mostra como converter configurações em recursos compostos na State Configuration de Automação do Azure.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563565"
---
# <a name="convert-configurations-to-composite-resources"></a>Converter configurações em recursos de composição

> Aplica-se a: Windows PowerShell 5.1

Depois de começar a criar as configurações, você pode criar rapidamente "cenários" que gerenciam grupos de configurações.
Exemplos seriam:

- criar um servidor Web
- criar um servidor DNS
- criar um servidor do SharePoint
- configurar um cluster do SQL
- gerenciar configurações de firewall
- gerenciar configurações de senha

Caso você esteja interessado em compartilhar esse trabalho com outras pessoas, a melhor opção é acondicionar a configuração como um [Recurso composto](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Criar recursos compostos pela primeira vez pode ser complicado.

> [!NOTE]
> Este artigo faz referência a uma solução mantida pela comunidade do Open Source.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-compositeresource"></a>Projeto da comunidade: CompositeResource

Uma solução mantida pela comunidade chamada [CompositeResource](https://github.com/microsoft/compositeresource) foi criada para resolver esse desafio.

O CompositeResource automatiza o processo de criação de um novo módulo a partir de sua configuração.
Você começa usando [fontes de pontos](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) no script de configuração na sua estação de trabalho (ou servidor de build) para que ele seja carregado na memória.
Em seguida, em vez de executar a configuração para gerar um arquivo MOF, use a função fornecida pelo módulo CompositeResource para automatizar uma conversão.
O cmdlet vai carregar o conteúdo de sua configuração, obter a lista de parâmetros e gerar um novo módulo com tudo o que você precisa.

Depois de gerar um módulo, você pode incrementar a versão e adicionar notas de versão cada vez que fizer alterações e publicá-las em seu próprio [repositório PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Depois de criar um módulo de recurso de composição que contém sua configuração (ou várias configurações), você pode usá-los na [experiência de criação combinável](./compose-configurationwithcompositeresources.md) no Azure ou adicioná-los aos [scripts de configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar arquivos MOF e [carregar os arquivos MOF para a automação do Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores do [local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) ou [no Azure](./automation-dsc-onboarding.md#enable-azure-vms) para efetuar o pull das configurações.
A atualização mais recente do projeto também publicou [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) para a Automação do Azure a fim de automatizar o processo de importação de configurações da Galeria do PowerShell.

Para experimentar a automação da criação de recursos compostos do DSC, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/compositeresource/) e baixe a solução ou clique em "Site do projeto" para exibir a [documentação](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o PowerShell DSC, consulte [Visão geral das configurações de Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Saiba mais sobre os recursos de DSC do PowerShell em [Recursos de DSC](/powershell/scripting/dsc/resources/resources).
- Para obter detalhes sobre as definições do Configuration Manager local, consulte [Definir o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig).
