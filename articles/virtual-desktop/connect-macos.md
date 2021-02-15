---
title: Conectar-se à área de trabalho virtual do Windows do macOS – Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente macOS.
author: Heidilohr
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f437c43c1e9ad960910e7576db4b3ddf3f6623ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230867"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Conectar-se à área de trabalho virtual do Windows com o cliente macOS

> Aplica-se a: macOS 10,12 ou posterior

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/connect-macos-2019.md).

Você pode acessar os recursos da área de trabalho virtual do Windows de seus dispositivos macOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente do.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [Baixe](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e instale o cliente em seu dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed que o administrador lhe deu para obter a lista de recursos gerenciados disponíveis para você em seu dispositivo macOS.

Para assinar um feed:

1. Selecione **adicionar espaço de trabalho** na página principal para se conectar ao serviço e recuperar seus recursos.
2. Insira a URL do Feed. Ela pode ser uma URL ou endereço de email:
   - Se você usar uma URL, use a que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Para usar email, insira seu endereço de email. Isso informa ao cliente para pesquisar uma URL associada ao seu endereço de email, caso o administrador tenha configurado o servidor dessa maneira.
   - Para se conectar por meio do portal de US Gov, use <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Selecione **Adicionar**.
4. Entre com sua conta quando solicitado.

Depois de entrar, você deverá ver uma lista de recursos disponíveis.

Após a assinatura de um feed, o conteúdo dele será atualizado automaticamente de maneira regular. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o cliente macOS, confira a documentação [introdução ao cliente MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
