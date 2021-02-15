---
title: Enviar email quando a porta for aberta usando Azure Functions
description: Monitore o sensor magnético para detectar quando uma porta é aberta e use o Azure Functions para enviar uma notificação por email.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e3649ac786c07459695a1e6ca6ad52620e23d59f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148794"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitor de porta--usando Azure Functions e SendGrid, enviar email quando uma porta é aberta           

O Kit de Desenvolvimento de IoT MXChip contém um sensor magnético integrado. Neste projeto, você detecta a presença ou ausência de um campo de magnético forte próximo – nesse caso, proveniente de um imã pequeno, permanente.

## <a name="what-you-learn"></a>O que você aprenderá

Neste projeto, você aprenderá a:
- Como usar o sensor magnético do Kit de Desenvolvimento de IoT MXChip para detectar a movimentação de um ímã próximo.
- Como usar o serviço SendGrid para enviar uma notificação para seu endereço de email.

> [!NOTE]
> Para um uso prático deste projeto, execute as seguintes tarefas:
> - Fixe um ímã na borda de uma porta.
> - Monte o Kit de Desenvolvimento no batente da porta próximo ao ímã. A abertura ou o fechamento da porta irá disparar o sensor, resultando no recebimento de uma notificação por email do evento.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma assinatura ativa do Azure. Se você não tiver uma, você pode registrar por meio de um desses métodos:

* Ative uma [avaliação gratuita de 30 dias Microsoft Azure conta](https://azure.microsoft.com/free/).
* Declare seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for assinante do MSDN ou o Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Implementar o serviço SendGrid no Azure

[SendGrid](https://sendgrid.com/) é uma plataforma de entrega de email com base na nuvem. Esse serviço será usado para enviar notificações por email.

> [!NOTE]
> Se você já tiver implantado um serviço SendGrid, poderá continuar diretamente em [Implantar Hub IoT no Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implantação do SendGrid

Para provisionar os serviços do Azure, use o botão **Implantar no Azure**. Esse botão permite a implantação rápida e fácil de seus projetos de código-fonte aberto no Microsoft Azure.

Clique no botão **implantar no Azure** abaixo. 

[![Implantar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Se ainda não estiver autenticado em sua conta do Azure, faça logon agora. 

Agora você verá o formulário de inscrição do SendGrid.

![Implantação do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Preencha o formulário de inscrição:

   * **Grupo de recursos**: criar um grupo de recursos para hospedar serviço SendGrid ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nome**: O nome do serviço SendGrid. Escolha um nome exclusivo, diferente de outros serviços que você possa ter.

   * **Senha**: O serviço requer uma senha, a qual não será usada para nada mais neste projeto.

   * **Email**: O serviço SendGrid enviará a verificação para esse endereço de email.

Marque a opção **Fixar no painel** para facilitar a localização do aplicativo no futuro, em seguida clique em **Comprar** para enviar o formulário de assinatura.
 
### <a name="sendgrid-api-key-creation"></a>Criação da Chave de API do SendGrid

Depois que implantação for concluída, clique nela e, em seguida, clique no botão **Gerenciar**. A página de conta do SendGrid é exibida, onde você precisa verificar seu endereço de email.

![Gerenciamento do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na página SendGrid, clique em **configurações**  >  **chaves de API**  >  **criar chave de API**.

![Criar primeira API do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Insira a página **Criar chave da API**, insira o nome da **Chave da API** e clique em **Criar e exibir**.

![Criar segunda API do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Sua chave de API é exibida apenas uma vez. Certifique-se de copiá-la e armazená-la com segurança, pois ela será usada na próxima etapa.

## <a name="deploy-iot-hub-in-azure"></a>Implantar um Hub IoT no Azure

As etapas a seguir provisionarão outros serviços relacionados a IoT do Azure e implantarão Azure Functions para esse projeto.

Clique no botão **implantar no Azure** abaixo. 

[![Implantar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

O formulário de inscrição aparece.

![Implantação de Hub IOT](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Preencha os campos no formulário de inscrição.

   * **Grupo de recursos**: criar um grupo de recursos para hospedar serviço SendGrid ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nome do Hub IoT**: O nome para o hub IoT. Escolha um nome exclusivo, diferente de outros serviços que você possa ter.

   * **Sku do Hub IOT**: F1 (limitado a um por assinatura) é gratuito. Você pode ver mais informações de preço na [página de preços](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Do Email**: Este campo deve ser o mesmo endereço de email que você usou ao configurar o serviço SendGrid.

Marque a opção **Fixar no painel** para tornar este aplicativo mais fácil de encontrar no futuro, em seguida clique em **Comprar** quando estiver pronto para continuar para a próxima etapa.
 
## <a name="build-and-upload-the-code"></a>Compilação e carregamento do código

Em seguida, carregue o código de exemplo no VS Code e provisione os serviços do Azure necessários.

### <a name="start-vs-code"></a>Iniciar o VS Code

- Verifique se o Kit de Desenvolvimento **não** está conectado ao computador.
- Iniciar o VS Code.
- Conecte o DevKit ao computador.

> [!NOTE]
> Ao iniciar o VS Code, você receberá uma mensagem de erro que informa que não é possível localizar o Arduino IDE ou o pacote de quadro relacionado. Se você receber esse erro, feche o VS Code e inicialize o Arduino IDE uma vez, assim o VS Code localizará o caminho do Arduino IDE corretamente.

### <a name="open-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Expanda a seção **EXEMPLOS DO ARDUINO** no canto esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **DoorMonitor**. Essa ação abrirá uma nova janela do VS Code com a pasta de projeto nela.

![Exemplos de minisolução](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Você também pode abrir o aplicativo de exemplo a partir da paleta de comandos. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

### <a name="provision-azure-services"></a>Provisionamento dos serviços do Azure

Na janela de solução, execute a tarefa de provisionamento de nuvem:
- Digite `Ctrl+P` (macOS: `Cmd+P`).
- Digite `task cloud-provision` na caixa de texto fornecida.

No terminal do VS Code, uma linha de comando interativa orienta você durante o provisionamento dos serviços do Azure necessários. Selecione todos os mesmos itens da lista solicitada que você forneceu anteriormente em [Implantar Hub IoT no Azure](#deploy-iot-hub-in-azure).

![Provisionamento de nuvem](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se a página trava no status de carregamento ao tentar entrar no Azure, consulte a seção [“páginas pendentes ao logar”](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver esse problema. 

### <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

Em seguida, carregue o código para o dispositivo.

#### <a name="windows"></a>Windows

1. Use `Ctrl+P` para executar `task device-upload`.

2. O terminal solicita que você entre no modo de configuração. Para fazer isso, mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibe o número de identificação do Kit de Desenvolvimento e a palavra *Configuração*.

#### <a name="macos"></a>macOS

1. Coloque o Kit de Desenvolvimento no modo de configuração: mantenha o botão A pressionado e, em seguida, pressione e solte o botão Reiniciar. A tela exibe “Configuração”.

2. Clicar `Cmd+P` para executar `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Verificar, carregar e executar o aplicativo de amostra

A cadeia de caracteres de conexão que é recuperada da etapa [Provisionar Serviços do Azure](#provision-azure-services). 

O VS Code então inicia verificando e carregando o esboço Arduino para o Kit de Desenvolvimento.

![A captura de tela mostra Visual Studio Code verificar e carregar o esboço Arduino.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O DevKit reinicia e inicia a execução do código.

> [!NOTE]
> Ocasionalmente, você pode receber uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse erro, consulte a [seção de desenvolvimento de perguntas frequentes sobre o Kit de Desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

O programa primeiro inicializa quando o Kir de Desenvolvimento está na presença de um campo magnético estável.

Após a inicialização, `Door closed` é exibido na tela. Quando há uma alteração no campo magnético, o estado muda para `Door opened`. Cada vez que o estado da porta mudar, você receberá uma notificação por email. (Essas mensagens de email podem levar até cinco minutos para serem recebidas.)

![Ímã perto do sensor: porta fechada](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Ímã perto do sensor: porta fechada")

![Ímã afastado do sensor: porta aberta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Ímã afastado do sensor: porta aberta")

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [as perguntas frequentes do Kit de Desenvolvedores de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como conectar um dispositivo de Kit de Desenvolvimento ao acelerador de solução de Monitoramento Remoto do Azure IoT e usar o serviço SendGrid para enviar um email. Esta é a próxima etapa sugerida:[visão geral do acelerador de solução de monitoramento remoto do Azure IOT](/azure/iot-suite/)