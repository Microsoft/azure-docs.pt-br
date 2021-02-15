---
title: Conectar-se à área de trabalho virtual do Windows com o cliente Web-Azure
description: Como se conectar à Área de Trabalho Virtual do Windows usando o cliente Web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400629"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Conectar-se à área de trabalho virtual do Windows com o cliente Web

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).

O cliente Web permite acessar os recursos da Área de Trabalho Virtual do Windows em um navegador da Web sem o lento processo de instalação.

>[!NOTE]
>No momento, o cliente Web não dá suporte a sistemas operacionais de dispositivos móveis.

## <a name="supported-operating-systems-and-browsers"></a>Navegadores e sistemas operacionais compatíveis

Por mais que qualquer navegador compatível com HTML5 funcione, oficialmente damos suporte aos seguintes navegadores e sistemas operacionais.

| Navegador           | SO com suporte                     | Observações               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versão 11 ou posterior |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Acessar feed de recursos remotos

Em um navegador, vá até a versão integrada do Azure Resource Manager do cliente Web da Área de Trabalho Virtual do Windows em <https://rdweb.wvd.microsoft.com/arm/webclient> e entre com sua conta de usuário.

>[!NOTE]
>Se você estiver usando a área de trabalho virtual do Windows (clássica) sem integração Azure Resource Manager, conecte-se aos seus recursos em <https://rdweb.wvd.microsoft.com/webclient> vez disso.
>
> Se você estiver usando o portal de US Gov, use <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Se já tiver entrado com uma conta do Azure Active Directory diferente da que deseja usar para a Área de Trabalho Virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Depois de entrar, você verá uma lista de recursos. Você pode iniciar os recursos selecionando-os da mesma maneira que faria com um aplicativo normal na guia **Todos os recursos**.

## <a name="using-an-input-method-editor"></a>Usando um editor de método de entrada

O cliente Web dá suporte ao uso de um IME (editor de método de entrada) na sessão remota na versão **1.0.21.16 ou posterior**. O pacote de idiomas do teclado que você deseja usar na sessão remota deve ser instalado na máquina virtual do host. Para saber mais sobre como configurar pacotes de idiomas na sessão remota, confira [adicionar pacotes de idiomas a uma imagem de várias sessões do Windows 10](language-packs.md).

Para habilitar a entrada do IME usando o cliente Web:

1. Antes de se conectar à sessão remota, vá para o painel de **configurações** do cliente Web.

2. Alterne a configuração **habilitar editor de método de entrada** para **ativado**.

3. No menu suspenso, selecione o teclado que será usado na sessão remota.

4. Conecte-se à sessão remota.

O cliente Web irá suprimir a janela local do IME quando você estiver concentrado na sessão remota. Alterar as configurações do IME depois que você já tiver se conectado à sessão remota não terá nenhum efeito.

>[!NOTE]
>Se o pacote de idiomas não estiver instalado na máquina virtual do host, a sessão remota usará como padrão o teclado inglês (Estados Unidos).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o cliente Web em [Introdução ao cliente Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
