---
title: Capturar um rastreamento de navegador para solucionar problemas
description: Capturar informações de rede de um rastreamento de navegador para ajudar a solucionar problemas com o portal do Azure.
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.openlocfilehash: 504a32fb63022f97e8049c7c1ccc7721bbac31ab
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745988"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Capturar um rastreamento de navegador para solucionar problemas

Se você estiver solucionando um problema com o portal do Azure e precisar entrar em contato com o Suporte da Microsoft, recomendamos que você capture primeiro um rastreamento de navegador e algumas informações adicionais. As informações coletadas podem fornecer detalhes importantes sobre o portal no momento em que o problema ocorre. Siga as etapas neste artigo para as ferramentas para desenvolvedores no navegador que você usa: Google Chrome ou Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML), Apple Safari ou Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Chromium)

O Google Chrome e o Microsoft Edge (Chromium) são baseados no [projeto de software livre Chromium](https://www.chromium.org/Home). As etapas a seguir mostram como usar as ferramentas para desenvolvedores, que são muito semelhantes nos dois navegadores. Para obter mais informações, confira [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) e [Ferramentas para Desenvolvedores do Microsoft Edge (Chromium)](/microsoft-edge/devtools-guide-chromium).

1. Entre no [portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Inicie a gravação das etapas executadas no portal usando o [Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Pressione F12 ou selecione ![ a captura de tela que mostra o ícone configurações do navegador.](media/capture-browser-trace/chromium-icon-settings.png) > **Mais ferramentas**  >  **Ferramentas de desenvolvedor**.

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está sendo carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija ir para mais de uma página:

    1. Selecione a guia **Rede** e, em seguida, selecione **Preservar log**.

          ![Captura de tela que realça a opção preservar log na guia rede.](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecione a guia **Console**, selecione **Configurações do console** e, em seguida, selecione **Preservar log**. Selecione **Configurações do console** novamente para fechar o painel de configurações.

          ![Captura de tela que realça a opção preservar log na guia Console.](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecione a guia **Rede**, em seguida, selecione **Parar gravação do log de rede** e **Limpar**.

    ![Captura de tela de "Parar gravação do log de rede" e "Limpar"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecione **Registrar log de rede** e, em seguida, reproduza o problema no portal.

    ![Captura de tela que mostra como registrar o log de rede.](media/capture-browser-trace/chromium-start-session.png)

    Você verá saídas de sessão semelhantes à imagem a seguir.

    ![Captura de tela que mostra a saída da sessão.](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Depois de reproduzir o comportamento de portal inesperado, selecione **Parar gravação do log de rede**, em seguida, selecione **Exportar HAR** e salve o arquivo.

    ![Captura de tela que mostra como exportar o HAR na guia rede.](media/capture-browser-trace/chromium-network-export-har.png)

1. Pare o Gravador de Passos e salve a gravação.

1. De volta ao painel de ferramentas para desenvolvedores do navegador, selecione a guia **Console**. Clique com o botão direito do mouse em uma das mensagens e selecione **Salvar como...** e salve a saída do console em um arquivo de texto.

    ![Captura de tela que realça a guia Console e salvar como... AdicionarMenu](media/capture-browser-trace/chromium-console-select.png)

1. Agrupe o arquivo HAR, a saída do console e a gravação de tela em um formato compactado (como .zip) e compartilhe isso com o Suporte da Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

As etapas a seguir mostram como usar as ferramentas para desenvolvedores no Microsoft Edge (EdgeHTML). Para obter mais informações, confira [Ferramentas para Desenvolvedores do Microsoft Edge (EdgeHTML)](/microsoft-edge/devtools-guide).

1. Entre no [portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Inicie a gravação das etapas executadas no portal usando o [Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Pressione F12 ou selecione ![ captura de tela do ícone de configurações do navegador.](media/capture-browser-trace/edge-icon-settings.png) > **Mais ferramentas**  >  **Ferramentas de desenvolvedor**.

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está sendo carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija ir para mais de uma página:

    1. Selecione a guia **Rede**, em seguida, desmarque a opção **Limpar entradas ao navegar**.

          ![Captura de tela de "Limpar entradas ao navegar"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecione a guia **Console** e, em seguida, selecione **Preservar Log**.

          ![Captura de tela de "Preservar Log"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecione a guia **Rede**, em seguida, selecione **Parar a sessão de criação de perfil** e **Limpar sessão**.

    ![Captura de tela de "Parar sessão de criação de perfil" e "Limpar sessão"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecione **Iniciar a sessão de criação de perfil**, em seguida, reproduza o problema no portal.

    ![Captura de tela de "Iniciar a sessão de criação de perfil"](media/capture-browser-trace/edge-start-session.png)

    Você verá saídas de sessão semelhantes à imagem a seguir.

    ![Captura de tela que mostra a saída da sessão de criação de perfil.](media/capture-browser-trace/edge-browser-trace-results.png)

1. Depois de reproduzir o comportamento de portal inesperado, selecione **Parar sessão de criação de perfil**, em seguida, selecione **Exportar HAR** e salve o arquivo.

    ![Captura de tela de "Exportar como HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Pare o Gravador de Passos e salve a gravação.

1. De volta ao painel de ferramentas para desenvolvedores do navegador, selecione a guia **Console** e expanda a janela. Coloque o cursor no início da saída do console e, em seguida, arraste e selecione todo o conteúdo da saída. Clique com o botão direito do mouse, selecione **Copiar** e salve a saída do console em um arquivo de texto.

    ![Captura de tela que realça a opção de menu Copiar.](media/capture-browser-trace/edge-console-select.png)

1. Agrupe o arquivo HAR, a saída do console e a gravação de tela em um formato compactado (como .zip) e compartilhe isso com o Suporte da Microsoft.

## <a name="apple-safari"></a>Apple Safari

As etapas a seguir mostram como usar as ferramentas para desenvolvedores no Apple Safari. Para obter mais informações, confira [Visão geral das Ferramentas para Desenvolvedores do Safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Habilite as ferramentas para desenvolvedores no Apple Safari:

    1. Selecione **Safari** e, em seguida, selecione **Preferências**.

        ![Captura de tela das preferências do Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selecione a guia **Avançado** e, em seguida, selecione **Mostrar menu Desenvolver na barra de menus**.

        ![Captura de tela das preferências avançadas do Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Entre no [portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Inicie a gravação das etapas executadas no portal. Para obter mais informações, confira [Como registrar a tela em seu Mac](https://support.apple.com/HT208721).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Selecione **Desenvolver** e, em seguida, selecione **Mostrar Inspetor da Web**.

    ![Captura de tela de "Mostrar Inspetor da Web"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está sendo carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija ir para mais de uma página:

    1. Selecione a guia **Rede** e, em seguida, selecione **Preservar Log**.

          ![Captura de tela que mostra a opção preservar log.](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecione a guia **Console** e, em seguida, selecione **Preservar Log**.

          ![Captura de tela que mostra a preservação de log na guia Console.](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecione a guia **Rede** e, em seguida, selecione **Limpar Itens de Rede**.

    ![Captura de tela de "Limpar Itens de Rede"](media/capture-browser-trace/safari-clear-session.png)

1. Reproduza o problema no portal. Você verá saídas de sessão semelhantes à imagem a seguir.

    ![Captura de tela que mostra a saída depois que você reproduziu o problema.](media/capture-browser-trace/safari-browser-trace-results.png)

1. Depois de reproduzir o comportamento de portal inesperado, selecione **Exportar** e salve o arquivo.

    ![Captura de tela de "Exportar"](media/capture-browser-trace/safari-network-export-har.png)

1. Pare o gravador de tela e salve a gravação.

1. De volta ao painel de ferramentas para desenvolvedores do navegador, selecione a guia **Console** e expanda a janela. Coloque o cursor no início da saída do console e, em seguida, arraste e selecione todo o conteúdo da saída. Use Command + C para copiar a saída e salvá-la em um arquivo de texto.

    ![Captura de tela que realça você pode exibir e copiar a saída.](media/capture-browser-trace/safari-console-select.png)

1. Agrupe o arquivo HAR, a saída do console e a gravação de tela em um formato compactado (como .zip) e compartilhe isso com o Suporte da Microsoft.

## <a name="firefox"></a>Firefox

As etapas a seguir mostram como usar as ferramentas para desenvolvedores no Firefox. Para mais informações, confira [Ferramentas para Desenvolvedores do Firefox](https://developer.mozilla.org/docs/Tools).

1. Entre no [portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Inicie a gravação das etapas executadas no portal. Use o [Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps) no Windows ou consulte [Como gravar a tela no Mac](https://support.apple.com/HT208721).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Pressione F12 ou selecione ![Captura de tela do ícone de configurações do navegador](media/capture-browser-trace/firefox-icon-settings.png) > **Desenvolvedor Web** > **Alternar Ferramentas**.

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está sendo carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija ir para mais de uma página:

    1. Selecione a guia **Rede** e, em seguida, selecione **Persistir Logs**.

          ![Captura de tela que realça a opção de logs de persistência.](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Selecione a guia **Console**, selecione **Configurações do console** e, em seguida, selecione **Persistir Logs**.

          ![Captura de tela de "Persistir Logs"](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Selecione a guia **Rede** e, em seguida, selecione **Limpar**.

    ![Captura de tela de "Limpar"](media/capture-browser-trace/firefox-clear-session.png)

1. Reproduza o problema no portal. Você verá saídas de sessão semelhantes à imagem a seguir.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Depois de reproduzir o comportamento de portal inesperado, selecione **Exportar/Importar HAR** e **Salvar Tudo como HAR**.

    ![Captura de tela de "Exportar HAR"](media/capture-browser-trace/firefox-network-export-har.png)

1. Pare o Gravador de Passos no Windows ou a gravação de tela no Mac e salve a gravação.

1. De volta ao painel de ferramentas para desenvolvedores do navegador, selecione a guia **Console**. Clique com o botão direito do mouse em uma das mensagens e selecione **Exportar Mensagem Visível para** e salve a saída do console em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/firefox-console-select.png)

1. Agrupe o arquivo HAR, a saída do console e a gravação de tela em um formato compactado (como .zip) e compartilhe isso com o Suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

[Visão geral do portal do Azure](azure-portal-overview.md)