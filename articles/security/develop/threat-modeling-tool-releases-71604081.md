---
title: Versão de Microsoft Threat Modeling Tool 4/9/2019
titleSuffix: Azure
description: Documentando as notas de versão da versão 7.1.60408.1 da ferramenta de modelagem de ameaças.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 69dd2c6cdba41779849b4eb6b889cde9b1d6e5c9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913561"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Versão de atualização do Threat Modeling Tool 7.1.60408.1-4/9/2019

A versão 7.1.60408.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em abril de 9 2019 e contém as seguintes alterações:

- Novos estênceis para Azure Key Vault e o Gerenciador de tráfego do Azure
- O número de versão do TMT agora é mostrado na tela inicial
- Links de suporte foram atualizados
- Correções de bug

## <a name="feature-changes"></a>Alterações de recurso

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Novos estênceis para Azure Key Vault e o Gerenciador de tráfego do Azure

![Captura de tela mostra ícones para Azure Key Vault e o Gerenciador de tráfego do Azure.](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Novos estênceis e ameaças para Azure Key Vault e o Gerenciador de tráfego do Azure foram adicionados ao conjunto de estênceis do Azure. Ao abrir modelos com base no conjunto do estêncil do Azure, os usuários serão solicitados a atualizar o modelo associado ao modelo. A atualização de um modelo baseado no conjunto de estênceis do Azure também pode ser iniciada manualmente usando o comando "aplicar modelo" no menu "arquivo" e reaplicando o arquivo. tb7 mais recente dos serviços de nuvem do Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>O número de versão do TMT agora é mostrado na tela inicial

A versão do cliente do Threat Modeling Tool agora é mostrada na tela inicial do aplicativo do para facilitar o acesso.

![Captura de tela mostra a Microsoft Threat Modeling Tool com o número de versão do cliente.](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Links de suporte foram atualizados

Todos os links de suporte dentro da ferramenta foram atualizados para direcionar os usuários para [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) o em vez de um fórum do MSDN.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas operacionais com suporte
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão necessária do .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](threat-modeling-tool.md) e inclui informações [sobre como usar a ferramenta](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
