---
title: Configurar dados com base no STIG para a State Configuration da Automação do Azure
description: Este artigo informa como configurar dados com base no DoD STIG para configuração de estado de automação do Azure.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015129"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>Configurar dados com base no guia de informações técnicas de segurança (STIG)

> Aplica-se a: Windows PowerShell 5.1

Criar conteúdo de configuração pela primeira vez pode ser desafiador.
Em muitos casos, o objetivo é automatizar a configuração de servidores, seguindo uma "linha de base" que esperamos que se alinhe a uma recomendação do setor.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade do Open Source.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-powerstig"></a>Projeto da comunidade: PowerSTIG

Um projeto de comunidade chamado [PowerSTIG](https://github.com/microsoft/powerstig) tem o objetivo de resolver esse problema gerando conteúdo DSC com base em [informações públicas](https://public.cyber.mil/stigs/) fornecidas sobre o STIG (guia de implementação técnica de segurança).

Lidar com linhas de base é mais complicado do que parece.
Muitas organizações precisam [documentar exceções](https://github.com/microsoft/powerstig#powerstigdata) às regras e gerenciar esses dados em escala.
O PowerSTIG resolve o problema fornecendo [recursos compostos](https://github.com/microsoft/powerstig#powerstigdsc) para tratar de cada área da configuração, em vez de tentar resolver todo o intervalo de configurações em um arquivo grande.

Uma vez que as configurações tiverem sido geradas, você poderá usá-las com [scripts de Configuração de DSC](/powershell/scripting/dsc/configurations/configurations) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação do Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores de [local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) ou [no Azure](./automation-dsc-onboarding.md#enable-azure-vms) para efetuar pull das configurações.

Para experimentar o PowerSTIG, visite a [Galeria do PowerShell](https://www.powershellgallery.com) e baixe a solução ou clique em "Site do Projeto" para exibir a [documentação](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o PowerShell DSC, consulte [Visão geral das configurações de Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Saiba mais sobre os recursos de DSC do PowerShell em [Recursos de DSC](/powershell/scripting/dsc/resources/resources).
- Para obter detalhes sobre as definições do Configuration Manager local, consulte [Definir o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig).
