---
title: Faça um tour da interface do usuário do Azure IoT Central | Microsoft Docs
description: Familiarize-se com as áreas principais da interface do usuário do Azure IoT Central que você usa para criar, gerenciar e usar uma solução de IoT.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c07d247358d980152648fdf7f702c8efcb72bab9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90020066"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Faça um tour da interface do usuário do Azure IoT Central

Este artigo apresenta a interface do usuário do Microsoft Azure IoT Central. Você pode usar a interface do usuário para criar, gerenciar e usar uma solução do Azure IoT Central e seus dispositivos conectados.

Como um _construtor de soluções_, você usa a interface do usuário do Azure IoT Central para definir sua solução do Azure IoT Central. Você pode usar a interface do usuário para:

* Definir os tipos de dispositivos que se conectam à sua solução.
* Configurar as regras e ações para seus dispositivos. 
* Personalizar a interface do usuário para um _operador_ que usa sua solução.

Como um _operador_, você pode usar a interface do usuário do Azure IoT Central para gerenciar sua solução do Azure IoT Central. Você pode usar a interface do usuário para:

* Monitorar seus dispositivos.
* Configurar seus dispositivos.
* Solucionar e corrigir problemas com dispositivos.
* Provisione novos dispositivos.

## <a name="iot-central-homepage"></a>Página inicial do IoT Central

Na [página inicial do IoT Central](https://aka.ms/iotcentral-get-started), você pode saber mais sobre as notícias e os recursos mais recentes disponíveis no IoT Central, criar novos aplicativos e ver e iniciar o aplicativo existente.

> [!div class="mx-imgBorder"]
> ![Página inicial do IoT Central](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Criar um aplicativo

Na seção Build, você pode navegar na lista de modelos IoT Central relevantes para o setor para ajudar você a começar rapidamente, ou começar do zero usando um modelo de aplicativo personalizado.  
> [!div class="mx-imgBorder"]
> ![Página de build do IoT Central](media/overview-iot-central-tour/iot-central-build-pnp.png)

Para saber mais, confira o início rápido [Criar um aplicativo Azure IoT Central](quick-deploy-iot-central.md).

### <a name="launch-your-application"></a>Iniciar seu aplicativo

Você pode iniciar o aplicativo IoT Central acessando a URL que você ou o criador de soluções escolheu durante a criação do aplicativo. Você também pode ver uma lista de todos os aplicativos aos quais você tem acesso no [Gerenciador de aplicativos do IoT Central](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![Gerenciador de aplicativos do IoT Central](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navegar em seu aplicativo

Quando você estiver em seu aplicativo IoT, use o painel esquerdo para acessar as diferentes áreas. Expanda ou recolha o painel esquerdo selecionando o ícone de três linhas na parte superior do painel:

> [!NOTE]
> Os itens que você verá no painel esquerdo dependerão da sua função de usuário. Saiba mais sobre [como gerenciar usuários e funções](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![painel esquerdo](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Painel** exibe o painel de seu aplicativo. Como um *construtor de soluções*, você pode personalizar o painel global para seus operadores. Dependendo de sua função de usuário, os operadores também podem criar os próprios painéis pessoais.
     
     A opção **Dispositivos** permite que você gerencie seus dispositivos conectados, tanto reais quanto simulados.

     A opção **Grupos de dispositivos** permite exibir e criar coleções lógicas de dispositivos especificados por uma consulta. Você pode salvar essa consulta e usar grupos de dispositivos por meio do aplicativo para executar operações em massa.

     A opção **Regras** permite criar e editar regras para monitorar dispositivos. As regras são avaliadas com base na telemetria do dispositivo e disparam ações personalizáveis.

     A opção **Análise** permite criar exibições personalizadas sobre dados do dispositivo para derivar insights de seu aplicativo.

     A opção **Trabalhos** permite que você gerencie seus dispositivos em escala executando operações em massa.

     A opção **Modelos de dispositivo** permite criar e gerenciar as características dos dispositivos que se conectam ao seu aplicativo.

     A opção **Exportação de dados** permite configurar uma exportação contínua para serviços externos, como armazenamento e filas.

     A opção **Administração** permite gerenciar as configurações, a personalização, a cobrança, os usuários e as funções do seu aplicativo.

     A opção **IoT Central** permite que os *administradores* retornem ao gerenciador de aplicativos do IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Pesquisa, ajuda, tema e suporte

O menu superior aparece em cada página:

> [!div class="mx-imgBorder"]
> ![Barra de ferramentas](media/overview-iot-central-tour/toolbar-pnp.png)

* Para procurar dispositivos e modelos de dispositivo, insira um valor **Pesquisar**.
* Para alterar o idioma da interface do usuário ou o tema, escolha o ícone **Configurações**. Saiba mais sobre [como gerenciar suas preferências de aplicativo](howto-manage-preferences.md)
* Para sair do aplicativo, escolha o ícone **Conta**.
* Para obter ajuda e suporte, escolha o menu suspenso da **Ajuda** para ver uma lista de recursos. Você pode [obter informações sobre seu aplicativo](./howto-get-app-info.md) no link **Sobre seu aplicativo**. Em um aplicativo no plano de preço gratuito, os recursos de suporte incluem o acesso ao [chat ao vivo](howto-show-hide-chat.md).

Você pode escolher entre um tema claro ou escuro para a interface do usuário:

> [!NOTE]
> A opção de escolher entre os temas claro e escuro não estará disponível se o administrador tiver configurado um tema personalizado para o aplicativo.

> [!div class="mx-imgBorder"]
> ![Escolher um tema para a interface do usuário](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Painel
> [!div class="mx-imgBorder"]
> ![Painel](media/overview-iot-central-tour/dashboard-pnp.png)

* O painel é a primeira página que você vê quando entra no seu aplicativo do Azure IoT Central. Como um *construtor de soluções*, você pode criar e personalizar vários painéis de aplicativos globais para outros usuários. Saiba mais sobre [como adicionar blocos ao seu painel](howto-add-tiles-to-your-dashboard.md)

* Como um *operador*, se sua função de usuário permitir, você poderá criar painéis pessoais para monitorar o que importa. Para saber mais, confira o artigo de instruções [Criar painéis pessoais do Azure IoT Central](howto-create-personal-dashboards.md).

### <a name="devices"></a>Dispositivos

> [!div class="mx-imgBorder"]
> ![Página Dispositivos](media/overview-iot-central-tour/devices-pnp.png)

A página Explorer mostra os _dispositivos_ no aplicativo do Azure IoT Central agrupados por _modelo de dispositivo_. 

* Um modelo do dispositivo define um tipo de dispositivo que pode se conectar ao seu aplicativo.
* Um dispositivo representa um dispositivo real ou simulado no seu aplicativo.

Para saber mais, confira o início rápido [Monitorar seus dispositivos](./quick-monitor-devices.md). 

### <a name="device-groups"></a>Grupos de dispositivos

> [!div class="mx-imgBorder"]
> ![Página Grupos de dispositivos](media/overview-iot-central-tour/device-groups-pnp.png)

Um grupo de dispositivos consiste em uma coleção de dispositivos relacionados. Um *construtor de soluções* define uma consulta para identificar os dispositivos que estão incluídos em um grupo de dispositivos. Você usa grupos de dispositivos para executar operações em massa em seu aplicativo. Para saber mais, confira o artigo [Usar grupos de dispositivos no aplicativo do Azure IoT Central](tutorial-use-device-groups.md).

### <a name="rules"></a>Regras
> [!div class="mx-imgBorder"]
> ![Página Regras](media/overview-iot-central-tour/rules-pnp.png)

A página Regras permite definir regras com base na telemetria, estado ou eventos dos dispositivos. Quando uma regra é acionada, ela pode disparar uma ou mais ações, como enviar um email, notificar um sistema externo por meio de alertas de webhook, etc. Para saber mais, confira o tutorial [Como configurar regras](tutorial-create-telemetry-rules.md). 

### <a name="analytics"></a>Análise

> [!div class="mx-imgBorder"]
> ![Página Análise](media/overview-iot-central-tour/analytics-pnp.png)

A análise permite criar exibições personalizadas sobre dados do dispositivo para derivar insights de seu aplicativo. Para saber mais, confira o artigo [Criar análises para seu aplicativo do Azure IoT Central](howto-create-analytics.md).

### <a name="jobs"></a>Trabalhos

> [!div class="mx-imgBorder"]
> ![Página Trabalhos](media/overview-iot-central-tour/jobs-pnp.png)

A página Trabalhos permite que você execute operações de gerenciamento de dispositivo em massa em seus dispositivos. Você pode atualizar propriedades e configurações do dispositivo e executar comandos em grupos de dispositivos. Para saber mais, consulte o artigo [Executar um trabalho](howto-run-a-job.md).

### <a name="device-templates"></a>Modelos de dispositivo

> [!div class="mx-imgBorder"]
> ![Página Modelos de Dispositivo](media/overview-iot-central-tour/templates-pnp.png)

A página Modelos de Dispositivo é o local em que um construtor cria e gerencia os modelos de dispositivo no aplicativo. Um modelo de dispositivo especifica as características dos dispositivos, como:

* medidas de telemetria, estado e evento
* Propriedades
* Comandos
* Exibições

O *construtor de soluções* também pode criar formulários e painéis para uso dos operadores no gerenciamento de dispositivo.

Para saber mais, consulte o tutorial [Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Exportação de dados
> [!div class="mx-imgBorder"]
> ![Página Exportação de dados](media/overview-iot-central-tour/export-pnp.png)

A exportação de dados permite configurar fluxos de dados, como telemetria, do aplicativo para sistemas externos. Para saber mais, confira o artigo [Exportar seus dados no Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administração
> [!div class="mx-imgBorder"]
> ![Página Administração](media/overview-iot-central-tour/administration-pnp.png)

A página Administração permite configurar e personalizar o aplicativo IoT Central. Aqui você pode alterar o nome do aplicativo, a URL, o tema, o gerenciamento de usuários e as funções, criar tokens de API e exportar seu aplicativo. Para saber mais, consulte o artigo [Administrar seu aplicativo do Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Azure IoT Central e está familiarizado com o layout da interface do usuário, a próxima etapa sugerida é concluir o início rápido [Criar um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).
