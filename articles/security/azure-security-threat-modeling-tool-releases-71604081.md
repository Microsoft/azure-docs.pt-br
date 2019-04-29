---
title: Versões da Threat Modeling Tool – Microsoft Threat Modeling Tool Azure | Microsoft Docs
description: Documentando as notas sobre a versão da Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610645"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Versão Threat Modeling Tool atualização 7.1.60408.1 - 9/4/2019

Versão 7.1.60408.1 da Microsoft Threat Modeling ferramenta (TMT) foi lançado em 9 de abril de 2019 e contém as seguintes alterações:

- Estênceis novos para o Cofre de chaves do Azure e o Azure Traffic Manager
- Número de versão TMT agora é mostrado na tela inicial
- Links de suporte foram atualizadas
- Correções de bug

## <a name="feature-changes"></a>Alterações de recurso

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Estênceis novos para o Cofre de chaves do Azure e o Azure Traffic Manager

![Estêncil de Cofre de chaves do Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Ameaças para o Azure Key Vault e o Gerenciador de tráfego do Azure e estênceis novos foram adicionadas ao conjunto de estêncil do Azure. Ao abrir os modelos com base no conjunto de estêncil do Azure, os usuários deverão atualizar o modelo associado com o modelo. Atualizando um modelo com base no conjunto de estêncil do Azure pode também ser iniciada manualmente usando o comando "Aplicar modelo" no menu "Arquivo" e reaplicar o arquivo Services.tb7 de nuvem do Azure mais recente.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Número de versão TMT agora é mostrado na tela inicial

A versão de cliente do Threat Modeling Tool agora é mostrada na tela inicial do aplicativo de para facilitar o acesso.

![Estêncil de Cofre de chaves do Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Links de suporte foram atualizadas

Todos os links de suporte dentro da ferramenta foram atualizados para direcionar os usuários para [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) em vez de um fórum do MSDN.

## <a name="system-requirements"></a>Requisitos do sistema

- Sistemas operacionais com suporte
  - [Atualização de Aniversário do Microsoft Windows 10 ou posterior](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)
- Versão necessária do .NET
  - [.Net 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e inclui informações [sobre como usar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).