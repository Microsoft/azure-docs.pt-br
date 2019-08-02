---
title: Solucionar problemas da extensão do painel de acesso do Azure para o IE | Microsoft Docs
description: Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723911"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Solucionar problemas da extensão de painel de acesso para o Internet Explorer

Este artigo ajuda você a solucionar os problemas a seguir:

* Você não consegue acessar seus aplicativos por meio do portal de meus aplicativos ao usar o Internet Explorer.
* Consulte a mensagem "Instalação de Software", mesmo que você já tenha instalado o software.

Se você for um administrador, consulte [como implantar a extensão do painel de acesso do Internet Explorer usando a diretiva de grupo](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Execute a ferramenta de diagnóstico

Você pode diagnosticar problemas de instalação com a extensão do painel de acesso fazendo download e executando a ferramenta de diagnóstico do painel de acesso. 

Para baixar e instalar a ferramenta de diagnóstico:

1. [Selecione este link para baixar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Abra o arquivo e extraia o conteúdo em seu computador.
1. Para executar a ferramenta, clique no arquivo denominado *AccessPanelExtensionDiagnosticTool.js* e selecione **abrir com** > **Microsoft Windows com base em Script Host** .

    ![Abrir com > Microsoft Windows com base em Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Examine os resultados de diagnóstico que são exibidos e selecione **Sim** para corrigir os problemas. O **resultados da verificação** caixa de diálogo é exibida com informações sobre o que fazer se a extensão não funciona.  
1. Leia a mensagem e selecione **Okey**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está habilitada

Para verificar se você habilitou a extensão do painel de acesso no Internet Explorer:

1. No Internet Explorer, selecione o **ícone de engrenagem** no canto superior direito da janela e selecione **opções da Internet**.
1. Vá para o **programas** e selecione **gerenciar complementos**.
1. Selecione **extensão do painel de acesso** na **Microsoft Corporation** seção e selecione **habilitar**.
1. Para salvar as alterações, feche todas as janelas de navegador do Internet Explorer, você tiver aberto. A alteração entra em vigor na próxima vez que você abra o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Habilitar as extensões para a navegação InPrivate

Para habilitar as extensões para a navegação InPrivate:

1. No Internet Explorer, selecione o **ícone de engrenagem** no canto superior direito da janela e selecione **opções da Internet**.
1. Vá para o **privacidade** guia e verificar se o **desabilitar barras de ferramentas e extensões quando a navegação InPrivate** caixa de seleção estiver desmarcada.
1. Para salvar as alterações, feche todas as janelas de navegador do Internet Explorer, você tiver aberto. A alteração entra em vigor na próxima vez que você abra o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso do seu computador:

1. No painel de controle, pesquise *desinstalar*.
1. Nos resultados da pesquisa, selecione **desinstalar um programa**.

    ![Selecione a opção de um programa de desinstalação no painel de controle](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Na lista, selecione **extensão do painel de acesso** e selecione **desinstalar**.

    ![Desinstalar a extensão do painel de acesso](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Você pode tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se você enfrentar problemas ao desinstalar a extensão, você também pode removê-lo usando o [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) ferramenta.

## <a name="related-articles"></a>Artigos relacionados

* [Acesso a aplicativos e logon único com o Active Directory do Azure](what-is-single-sign-on.md)
* [Como implantar a extensão do painel de acesso do Internet Explorer usando a diretiva de grupo](deploy-access-panel-browser-extension.md)
