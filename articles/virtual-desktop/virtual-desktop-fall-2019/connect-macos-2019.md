---
title: Conectar-se à área de trabalho virtual do Windows (clássico) do macOS – Azure
description: Como se conectar à área de trabalho virtual do Windows (clássico) usando o cliente macOS.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a0d2bd6a07cfc9e5ebe205322550c9e898022846
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008433"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-macos-client"></a>Conectar-se à área de trabalho virtual do Windows (clássico) com o cliente macOS

> Aplica-se a: macOS 10,12 ou posterior

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../connect-macos.md).

Você pode acessar os recursos da área de trabalho virtual do Windows de seus dispositivos macOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente do.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [Baixe](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e instale o cliente em seu dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed que o administrador lhe deu para obter a lista de recursos gerenciados disponíveis para você em seu dispositivo macOS.

Para assinar um feed:

1. Selecione **adicionar espaço de trabalho** na página principal para se conectar ao serviço e recuperar seus recursos.
2. Insira a URL do Feed. Ela pode ser uma URL ou endereço de email:
   - Se você usar uma URL, use a que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com>.
   - Para usar email, insira seu endereço de email. Isso informa ao cliente para pesquisar uma URL associada ao seu endereço de email, caso o administrador tenha configurado o servidor dessa maneira.
3. Selecione **Adicionar**.
4. Entre com sua conta quando solicitado.

Depois de entrar, você deverá ver uma lista de recursos disponíveis.

Após a assinatura de um feed, o conteúdo dele será atualizado automaticamente de maneira regular. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o cliente macOS, confira a documentação [introdução ao cliente MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .