---
title: Configurar dados em escala para a State Configuration da Automação do Azure
description: Este artigo informa como configurar dados em escala para a State Configuration da Automação do Azure.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc55a4c5ab20cac041a00a0f924b207eb256ae8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186513"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Configurar dados em escala para a State Configuration da Automação do Azure

> Aplica-se a: Windows PowerShell 5.1

Gerenciar centenas ou milhares de servidores pode ser um desafio.
Os clientes comentaram que o aspecto mais difícil é realmente gerenciar os [dados de configuração](/powershell/scripting/dsc/configurations/configdata).
Organizar informações em constructos lógicos, como localização, tipo e ambiente.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade do Open Source.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-datum"></a>Projeto da Comunidade: Datum

Uma solução mantida pela comunidade chamada [Datum](https://github.com/gaelcolas/Datum) foi criada para resolver esse desafio.
A Datum se baseia em ideias incríveis de outras plataformas de gerenciamento de configuração e implementa o mesmo tipo de solução para o DSC PowerShell.
As informações são [organizadas em arquivos de texto](https://github.com/gaelcolas/Datum#3-intended-usage) com base em ideias lógicas.
Exemplos seriam:

- Configurações que devem ser aplicadas globalmente
- Configurações que devem ser aplicadas a todos os servidores em um local
- Configurações que devem ser aplicadas a todos os servidores de banco de dados
- Configurações do servidor individual

Essas informações são organizadas no formato de arquivo que você preferir (JSON, YAML ou PSD1).
Os cmdlets são fornecidos para gerar arquivos de dados de configuração [consolidando as informações](https://github.com/gaelcolas/Datum#datum-tree) de cada arquivo no modo de exibição único de um servidor ou uma função de servidor.

Uma vez que os arquivos de dados forem gerados, você poderá usá-los com [scripts de Configuração de DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação do Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores de [local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) ou [no Azure](./automation-dsc-onboarding.md#enable-azure-vms) para efetuar pull das configurações.

Para experimentar a Datum, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/datum/) e baixe a solução ou clique em "Site do Projeto" para exibir a [documentação](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o PowerShell DSC, consulte [Visão geral das configurações de Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Saiba mais sobre os recursos de DSC do PowerShell em [Recursos de DSC](/powershell/scripting/dsc/resources/resources).
- Para obter detalhes sobre as definições do Configuration Manager local, consulte [Definir o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig).
