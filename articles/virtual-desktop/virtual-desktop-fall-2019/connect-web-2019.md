---
title: Conectar o cliente Web da área de trabalho virtual do Windows (clássico)-Azure
description: Como se conectar à área de trabalho virtual do Windows (clássico) usando o cliente Web.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a55e35ba89caefc362024fc871ac5eb800447b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008416"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Conectar-se à área de trabalho virtual do Windows (clássico) com o cliente Web

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../connect-web.md).

O cliente Web permite acessar os recursos da Área de Trabalho Virtual do Windows em um navegador da Web sem o lento processo de instalação.

>[!NOTE]
>No momento, o cliente Web não dá suporte a sistemas operacionais de dispositivos móveis.

## <a name="supported-operating-systems-and-browsers"></a>Navegadores e sistemas operacionais compatíveis

Por mais que qualquer navegador compatível com HTML5 funcione, oficialmente damos suporte aos seguintes navegadores e sistemas operacionais.

| Navegador           | SO com suporte                     | Observações               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Acessar feed de recursos remotos

Em um navegador, vá até o cliente Web da Área de Trabalho Virtual do Windows em <https://rdweb.wvd.microsoft.com/webclient> e entre com sua conta de usuário.

>[!NOTE]
>Se você estiver usando a área de trabalho virtual do Windows com a integração Azure Resource Manager, conecte-se aos seus recursos em <https://rdweb.wvd.microsoft.com/arm/webclient> vez disso.

>[!NOTE]
>Se já tiver entrado com uma conta do Azure Active Directory diferente da que deseja usar para a Área de Trabalho Virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Depois de entrar, você verá uma lista de recursos. Você pode iniciar os recursos selecionando-os da mesma maneira que faria com um aplicativo normal na guia **Todos os Recursos**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o cliente Web em [Introdução ao cliente Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
