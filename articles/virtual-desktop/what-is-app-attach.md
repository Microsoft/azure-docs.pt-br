---
title: Visão geral da anexação do aplicativo MSIX do Windows Virtual Desktop – Azure
description: O que é a anexação de aplicativo MSIX? Descubra neste artigo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88556204"
---
# <a name="what-is-msix-app-attach"></a>O que é a anexação de aplicativo MSIX?

O MSIX é um novo formato de empacotamento que oferece muitos recursos destinados a melhorar a experiência de empacotamento para todos os aplicativos do Windows. Para saber mais sobre o MSIX, consulte a [visão geral do MSIX](/windows/msix/overview).

O MSIX app Attach é uma maneira de fornecer aplicativos MSIX para máquinas físicas e virtuais. No entanto, o anexo do aplicativo MSIX é diferente do MSIX normal porque é feito especialmente para a área de trabalho virtual do Windows. Este artigo descreverá o que é o anexo do aplicativo MSIX e o que ele pode fazer por você.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Opções de entrega de aplicativos na área de trabalho virtual do Windows

Você pode entregar aplicativos na área de trabalho virtual do Windows por meio de um dos seguintes métodos:

- Colocar aplicativos em uma imagem mestra
- Use ferramentas como o SCCM ou o Intune para gerenciamento central
- Provisionamento de aplicativo dinâmico (AppV, VMWare AppVolumes ou Citrix AppLayering)
- Crie ferramentas ou scripts personalizados usando a Microsoft e uma ferramenta de terceiros

## <a name="what-does-msix-app-attach-do"></a>O que o aplicativo MSIX anexa?

Em uma implantação de área de trabalho virtual do Windows, o MSIX app Attach pode:

- Crie separação entre os dados do usuário, o sistema operacional e os aplicativos usando [contêineres MSIX](/windows/msix/msix-container).
- Remova a necessidade de reempacotamento ao entregar aplicativos dinamicamente.
- Reduza o tempo que leva para um usuário entrar.
- Reduza os requisitos de infraestrutura e o custo.

A anexação do aplicativo MSIX deve ser aplicável fora do VDI ou do SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Camada de aplicativo tradicional em comparação com anexação de aplicativo MSIX

A tabela a seguir compara o principal recurso de anexação de aplicativo MSIX e camada de aplicativo.

| Recurso | Disposição em camadas do aplicativo tradicional  | Anexação de aplicativo MSIX  |
|-----|-----------------------------|--------------------|
| Formatar               | Tecnologias de camadas de aplicativo diferentes exigem formatos proprietários diferentes. | Funciona com o formato de empacotamento MSIX nativo.        |
| Reempacotando a sobrecarga | Os formatos proprietários exigem sequenciamento e reempacotamento por atualização.         | Os aplicativos publicados como MSIX não exigem reempacotamento. No entanto, se o pacote MSIX não estiver disponível, a sobrecarga de reempacotamento ainda se aplicará. |
| Ecossistema            | N/A (por exemplo, os fornecedores não enviam o App-V)  | A MSIX é a principal tecnologia da Microsoft que os principais parceiros ISV e aplicativos internos, como o Office, estão adotando. Você pode usar o MSIX em áreas de trabalho virtuais e em computadores físicos com Windows. |
| Infraestrutura       | Infraestrutura adicional necessária (servidores, clientes e assim por diante) | Somente armazenamento   |
| Administração       | Requer manutenção e atualização   | Simplifica as atualizações do aplicativo |
| Experiência do usuário      | Afeta o tempo de entrada do usuário. O limite existe entre o estado do sistema operacional, o estado do aplicativo e os dados do usuário.  | Os aplicativos entregues são indistinguíveis de aplicativos instalados localmente. |

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre o anexo de aplicativo do MSIX, confira nosso [Glossário](app-attach-glossary.md) e [perguntas frequentes](app-attach-faq.md). Caso contrário, comece a [Configurar a anexação de aplicativo](app-attach.md).
