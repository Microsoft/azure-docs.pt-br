---
title: Criar configurações de servidores existentes para a State Configuration da Automação do Azure
description: Este artigo mostra como criar configurações de servidores existentes para a State Configuration da Automação do Azure.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186496"
---
# <a name="create-configurations-from-existing-servers"></a>Criar configurações de servidores existentes

> Aplica-se a: Windows PowerShell 5.1

A criação de configurações de servidores existentes pode ser uma tarefa desafiadora.
Talvez você não queira *todas* as configurações, apenas aquelas com que você se importa.
Mesmo assim, você precisa saber em qual ordem as definições precisam ser aplicadas para que a configuração tenha êxito.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade do Open Source.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-reversedsc"></a>Projeto da comunidade: ReverseDSC

Uma solução mantida pela comunidade chamada [ReverseDSC](https://github.com/microsoft/reversedsc) foi criada para funcionar nessa área, iniciando o SharePoint.

A solução se baseia no [recurso SharePointDSC](https://github.com/powershell/sharepointdsc) e o estende para orquestrar a [coleta de informações](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) de servidores do SharePoint existentes.
A versão mais recente tem vários [modos de extração](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) para determinar qual nível de informações deve ser incluído.

O resultado do uso da solução é a geração de [Dados de Configuração](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) a serem usados com os scripts de configuração do SharePointDSC.

Uma vez que os arquivos de dados forem gerados, você poderá usá-los com [scripts de Configuração de DSC](/powershell/scripting/dsc/overview/overview) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação do Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores de [local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) ou [no Azure](./automation-dsc-onboarding.md#enable-azure-vms) para efetuar pull das configurações.

Para experimentar o ReverseDSC, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) e baixe a solução ou clique em "Site do Projeto" para exibir a [documentação](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o PowerShell DSC, consulte [Visão geral das configurações de Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Saiba mais sobre os recursos de DSC do PowerShell em [Recursos de DSC](/powershell/scripting/dsc/resources/resources).
- Para obter detalhes sobre as definições do Configuration Manager local, consulte [Definir o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig).
