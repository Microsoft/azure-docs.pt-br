---
title: Usando a janela do Azure Cloud Shell | Microsoft Docs
description: Visão geral de como usar a janela do Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "70860312"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela do Azure Cloud Shell

Este documento explica como usar a janela do Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Alternar entre os ambientes Bash e PowerShell

Use o seletor de ambiente na barra de ferramentas do Cloud Shell para alternar entre os ambientes Bash e PowerShell.  
![Selecionar um ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
Clique no ícone de reinicialização na barra de ferramentas do Cloud Shell para redefinir o estado da máquina.  
![Reiniciar o Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar o Cloud Shell redefinirá o estado do computador, e quaisquer arquivos não persistentes no seu compartilhamento de arquivos do Azure serão perdidos.

## <a name="change-the-text-size"></a>Alterar o tamanho do texto
Clique no ícone de configurações na parte superior esquerda da janela, em seguida, passe o mouse sobre a opção "Tamanho do texto" e selecione o tamanho do texto desejado. Sua seleção persistirá entre as sessões.
![Tamanho do texto](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Alterar fonte
Clique no ícone de configurações na parte superior esquerda da janela, em seguida, passe o mouse sobre a opção "fonte" e selecione a fonte desejada.  Sua seleção persistirá entre as sessões.
![Fonte](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Carregar e baixar arquivos
Clique no ícone carregar/baixar arquivos na parte superior esquerda da janela e, em seguida, selecione carregar ou baixar.  
![Carregar/baixar arquivos](media/using-the-shell-window/uploaddownload.png)
* Para carregar arquivos, use o pop-up para navegar até o arquivo no computador local, selecione o arquivo desejado e clique no botão "abrir".  O arquivo será carregado no `/home/user` diretório.
* Para baixar o arquivo, insira o caminho do arquivo totalmente qualificado na janela pop-up (ou seja, basicamente um caminho sob o `/home/user` diretório que aparece por padrão) e selecione o botão "baixar".  
> [!NOTE] 
> Arquivos e caminhos de arquivo diferenciam maiúsculas de minúsculas no Cloud Shell. Verifique novamente sua capitalização no caminho do arquivo.

## <a name="open-another-cloud-shell-window"></a>Abrir outra janela de Cloud Shell
O Cloud Shell permite várias sessões simultâneas nas guias do navegador, permitindo que cada sessão exista como um processo separado.
Ao sair de uma sessão, saia de cada janela de sessão, pois cada processo é executado de forma independente apesar de serem executados no mesmo computador.  
Clique no ícone abrir nova sessão na parte superior esquerda da janela. Uma nova guia será aberta com outra sessão conectada ao contêiner existente.
![Abrir nova sessão](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor do Cloud Shell
* Consulte a página [usando o editor de Azure cloud Shell](using-cloud-shell-editor.md) .

## <a name="web-preview"></a>Visualização da Web
Clique no ícone de visualização da Web na parte superior esquerda da janela, selecione "configurar", especifique a porta desejada a ser aberta.  Selecione "abrir porta" para abrir apenas a porta ou "abrir e procurar" para abrir a porta e visualizar a porta em uma nova guia.  
![Visualização da Web](media/using-the-shell-window/preview.png)  
<br>
![Configurar porta](media/using-the-shell-window/preview-configure.png)  
Clique no ícone de visualização da Web na parte superior esquerda da janela, selecione "porta de visualização..." para visualizar uma porta aberta em uma nova guia. Clique no ícone de visualização da Web na parte superior esquerda da janela, selecione "fechar porta..." para fechar a porta aberta.  
![Visualizar/fechar porta](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar e maximizar a janela do Cloud Shell
Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique novamente no ícone do Cloud Shell para reexibir.
Clique no ícone de maximizar para definir a janela com a altura máxima. Para restaurar a janela ao tamanho anterior, clique em Restaurar.  
![Minimizar ou maximizar a janela](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela do Cloud Shell
Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela do Cloud Shell.

## <a name="scrolling-text-display"></a>Rolagem pela exibição de texto
Role com o mouse ou teclado para ir ao texto de terminal.

## <a name="exit-command"></a>Comando de saída
Executar `exit` encerra a sessão ativa. Esse comportamento ocorre por padrão após 20 minutos sem interação.

## <a name="next-steps"></a>Próximas etapas

[Bash no guia de início rápido Cloud Shell](quickstart.md) <br>
[PowerShell no guia de início rápido Cloud Shell](quickstart-powershell.md)
