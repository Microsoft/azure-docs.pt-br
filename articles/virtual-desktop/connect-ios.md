---
title: Conectar-se à área de trabalho virtual do Windows do iOS-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente iOS.
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230034"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>Conectar-se à área de trabalho virtual do Windows com o cliente iOS

> Aplica-se a: iOS 13,0 ou posterior. Compatível com iPhone, iPad e iPod Touch.

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/connect-ios-2019.md).

Você pode acessar os recursos da área de trabalho virtual do Windows em seu dispositivo iOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente iOS.

## <a name="install-the-ios-client"></a>Instalar o cliente iOS

Para começar, [Baixe](https://aka.ms/rdios) e instale o cliente em seu dispositivo IOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed fornecido pelo administrador para obter a lista de recursos gerenciados que você pode acessar em seu dispositivo iOS.

Para assinar um feed:

1. No centro de conexões, toque em **+** e em **adicionar espaço de trabalho**.
2. Insira a URL do feed no campo **URL do feed** . A URL do feed pode ser uma URL ou um endereço de email.
   - Se você usar uma URL, use a que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Para usar email, insira seu endereço de email. Isso informa ao cliente para pesquisar uma URL associada ao seu endereço de email, caso o administrador tenha configurado o servidor dessa maneira.
   - Para se conectar por meio do portal de US Gov, use <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Toque em **Avançar**.
4. Forneça suas credenciais quando solicitado.
   - Para **nome de usuário**, dê ao nome de usuário permissão para acessar recursos.
   - Para **senha**, forneça a senha associada ao nome de usuário.
   - Você também poderá ser solicitado a fornecer fatores adicionais se o administrador configurou a autenticação dessa maneira.
5. Toque em **Salvar**.

Depois disso, a Central de Conexão deve exibir os recursos remotos.

Após a assinatura de um feed, o conteúdo dele será atualizado automaticamente de maneira regular. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente iOS, confira a documentação [introdução ao cliente Ios](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
