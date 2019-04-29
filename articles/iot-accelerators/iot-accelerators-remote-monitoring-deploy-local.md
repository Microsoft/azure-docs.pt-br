---
title: Implantar a solução de monitoramento remoto localmente (via Visual Studio IDE) – Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto no computador local usando o Visual Studio para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: fdb9b6c09e81ce29d2587fb0faca1aa80d28ea38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450589"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Implantar o acelerador da solução de monitoramento remoto localmente - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de Monitoramento Remoto no computador local para teste e desenvolvimento. Você aprenderá como executar os microsserviços no Visual Studio. Uma implantação de microsserviços local usa os seguintes serviços de nuvem: Hub IoT, Cosmos DB, Azure Stream Analytics e serviços na nuvem do Azure Time Series Insights.

Se você quiser executar o acelerador de solução de Monitoramento Remoto no Docker em seu computador local, confira [Implantar o acelerador de solução de Monitoramento Remoto localmente – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de Monitoramento Remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisa ter as seguintes ferramentas instaladas no computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) – este software é um pré-requisito para a CLI do PCS que os scripts usam para criar recursos do Azure. Não use o Node.js v10.

> [!NOTE]
> O Visual Studio está disponível para Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microsserviços

Nesta seção, você executa os microsserviços do Monitoramento Remoto. Execute a interface do usuário da Web nativamente, o serviço de Simulação de Dispositivo no Docker e os microsserviços no Visual Studio.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação de dispositivo

Abra uma nova janela do prompt de comando para ter certeza de que você tem acesso às variáveis de ambiente definidas pelo script **start.cmd** na seção anterior.

Execute o seguinte comando para iniciar o contêiner do Docker para o serviço de simulação de dispositivo. O serviço simula os dispositivos para a solução de monitoramento remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implantar todos os outros microsserviços na máquina local

As etapas a seguir mostram como executar os microsserviços de Monitoramento Remoto no Visual Studio 2017:

1. Inicie o Visual Studio 2017
1. Abra a solução **remoto monitoring.sln** na pasta **serviços** em sua cópia local do repositório.
1. No **Gerenciador de Soluções**, clique com o botão direito na solução e clique em **Propriedades**.
1. Selecione **Propriedades Comuns > Projeto de Inicialização**.
1. Selecione **Vários projetos de inicialização** e defina **Ação** como **Iniciar** para os projetos a seguir:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Clique em **OK** para salvar suas opções.
1. Clique em **Depurar > Iniciar Depuração** para compilar e executar os serviços Web no computador local.

Cada serviço Web abre uma janela do navegador e do prompt de comando. No prompt de comando, há uma saída do serviço em execução, e a janela do navegador permite que você monitore o status. Não feche os prompts de comando ou páginas da Web, essa ação interrompe o serviço Web.

### <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho do Stream Analytics

Execute estas etapas para iniciar o trabalho do Stream Analytics:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até o **Grupo de recursos** criado para sua solução. O nome do grupo de recursos é o nome da solução que você escolheu quando executou o script **start.cmd****.
1. Clique no **trabalho do Stream Analytics** na lista de recursos.
1. Na página **visão geral** do trabalho do Stream Analytics, clique no botão **Iniciar**. Clique em **Iniciar** para iniciar o trabalho.

### <a name="run-the-web-ui"></a>Executar a interface do usuário da Web

Nesta etapa, você inicia a interface do usuário da Web. Abra uma nova janela do prompt de comando para ter certeza de que você tem acesso às variáveis de ambiente definidas pelo script **start.cmd**. Navegue até a pasta **webui** em sua cópia local do repositório e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início for concluído, o navegador exibe a página **http:\//localhost:3000 / dashboard**. São esperados erros nessa página. Para exibir o aplicativo sem erros, conclua a etapa a seguir.

### <a name="configure-and-run-nginx"></a>Configurar e executar o NGINX

Configure um servidor proxy reverso para vincular o aplicativo Web e os microsserviços em execução no computador local:

* Copie o arquivo **nginx.conf** da pasta **webui\scripts\localhost** em sua cópia local do repositório para o diretório de instalação **nginx\conf**.
* Execute **nginx**.

Para saber mais sobre como executar **nginx**, confira [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Conectar-se ao painel

Para acessar o painel de solução de monitoramento remoto, navegue para http:\//localhost:9000 no seu navegador.

## <a name="clean-up"></a>Limpar

Para evitar encargos desnecessários, quando terminar o teste, remova os serviços de nuvem de sua assinatura do Azure. Para remover os serviços, navegue até o [portal do Azure](https://ms.portal.azure.com) e exclua o grupo de recursos que o script **start.cmd** criou.

Você também pode excluir a cópia local do repositório de Monitoramento Remoto criada quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel de solução](quickstart-remote-monitoring-deploy.md).
