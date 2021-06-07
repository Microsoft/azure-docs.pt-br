---
title: Versão Microsoft Threat Modeling Tool 03/22/2020 – Azure
description: Documentando as notas de versão da versão 7.3.00316.1 da ferramenta de modelagem de ameaças.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516893"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Versão de atualização do Threat Modeling Tool 7.3.00316.1-03/22/2020

A versão 7.3.00316.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em março de 22 2020 e contém as seguintes alterações:

- Aprimoramentos de acessibilidade
- Correções de bug
- Novo recurso DiagramReader

## <a name="notable-bug-fixes"></a>Correções de bugs notáveis

### <a name="exporting-the-threat-list-to-csv"></a>Exportando a lista de ameaças para CSV

A função exportar para CSV estava selecionando de forma inconsistente quais campos da lista de ameaças seriam exportados. Agora, todos os campos da lista de ameaças serão exportados para o arquivo CSV. 

### <a name="ux-bugs"></a>Bugs de UX

- Os menus de ajuda no fluxo de trabalho primário (criar/abrir/analisar) e a experiência do editor de modelos agora têm opções de menu consistentes.
- A barra de pesquisa no painel estênceis agora tem um cursor padrão e os rótulos apropriados foram adicionados.

## <a name="new-features"></a>Novos recursos

### <a name="diagramreader-feature-has-been-added"></a>O recurso DiagramReader foi adicionado

Um novo recurso DiagramReader foi adicionado no menu principal enquanto um modelo está aberto. Esse recurso converterá a representação gráfica do modelo em uma narração baseada em texto. 

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas operacionais com suporte:
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão do .NET necessária:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais:
  - Uma conexão com a Internet para receber atualizações para a ferramenta, bem como modelos

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](./threat-modeling-tool.md) e inclui informações [sobre como usar a ferramenta](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).