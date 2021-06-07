---
title: 'Tutorial: Configurar um ambiente – Machine Learning no Azure IoT Edge'
description: 'Tutorial: Prepare seu ambiente para o desenvolvimento e a implantação de módulos de aprendizado de máquina na borda.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 723d7b6ae5b96cf6dc622be1b1bb98065151787f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463139"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Configurar um ambiente para aprendizado de máquina no IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Este artigo ajuda você a preparar seu ambiente para desenvolvimento e implantação. Primeiro, configure um computador de desenvolvimento com todas as ferramentas necessárias. Em seguida, crie os recursos de nuvem necessários no Azure.

Nesta seção do tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Configurar uma máquina virtual para desenvolvimento.
> * Configurar um hub IoT e o armazenamento em nuvem para uso do seu ambiente de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, acesse o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="set-up-the-development-vm"></a>Configurar a VM de desenvolvimento

Esta etapa normalmente é executada por um desenvolvedor de nuvem. Alguns dos softwares também podem ser úteis para um cientista de dados.

Criamos um script do PowerShell que cria uma máquina virtual do Azure com muitos dos pré-requisitos já configurados. A VM criada precisa ser capaz de lidar com a [virtualização aninhada](../virtual-machines/windows/nested-virtualization.md), que é o motivo de termos escolhido o tamanho [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md).

A VM de desenvolvimento será configurada com:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop para Windows](https://www.docker.com/products/docker-desktop)
* [Git para Windows](https://gitforwindows.org/)
* [Gerenciador de credenciais do Git para Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK do .NET Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [PowerShell do Azure](/powershell/azure/)
* [Extensões do VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A VM de desenvolvedor não é estritamente necessária – todas as ferramentas de desenvolvimento podem ser executadas em um computador local. No entanto, é altamente recomendável usar a VM para garantir um campo de atuação uniforme.

São necessários cerca de 30 minutos para criar e configurar a máquina virtual.

1. Clone ou baixe o repositório de exemplo do [Machine Learning e do IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) no computador local.

1. Abra o PowerShell como administrador e navegue até o diretório **\IoTEdgeAndMlSample\DevVM** localizado no diretório raiz em que você baixou o código. Vamos nos referir ao diretório raiz de sua fonte como `srcdir`.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   O diretório DevVM contém os arquivos necessários para criar uma máquina virtual do Azure adequada para concluir este tutorial.

1. Execute o seguinte comando para permitir a execução de scripts. Escolha **Sim para tudo** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Execute Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Quando solicitado, forneça as seguintes informações:

    * **ID de assinatura do Azure**: sua ID da assinatura, que pode ser encontrada em [Assinaturas do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal.
    * **Nome do Grupo de Recursos**: o nome de um grupo de recursos novo ou existente no Azure.
    * **Localização**: escolha a localização do Azure em que a máquina virtual será criada. Por exemplo, 'Oeste dos EUA 2' ou 'Norte da Europa'. Para obter mais informações, confira [Locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Nome de usuário**: forneça um nome fácil de memorizar para a conta de administrador da VM.
    * **Senha**: defina uma senha para a conta de administrador da VM.

   O script é executado por vários minutos conforme executa as seguintes etapas:

    1. Instala o [módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).
    1. Solicita que você entre no Azure.
    1. Confirma as informações para a criação da VM. Pressione **y** ou **ENTER** para continuar.
    1. Cria o grupo de recursos caso ele não exista.
    1. Implanta a máquina virtual.
    1. Habilita o Hyper-V na VM.
    1. Instala o software necessário para desenvolvimento e clona o repositório de exemplo.
    1. Reinicia a VM.
    1. Criar um arquivo RDP na área de trabalho para conexão com a VM.

   Se precisar fornecer o nome da VM para reiniciá-la, copie o nome usando a saída do script. A saída também mostra o caminho para o arquivo RDP para conexão com a VM.

### <a name="set-auto-shutdown-schedule"></a>Definir a agenda de desligamento automático

Para ajudar você a reduzir os custos, a VM de desenvolvimento foi criada com uma agenda de desligamento automático definida como 1900 PST. Talvez você precise atualizar essa configuração dependendo da localização e da agenda. Para atualizar a agenda de desligamento:

1. No portal do Azure, navegue até a VM criada pelo script.

1. No menu do painel esquerdo, em **Operações**, selecione **Desligamento automático**.

1. Ajuste o **Desligamento agendado** e o **Fuso horário** conforme desejado e selecione **Salvar**.

## <a name="connect-to-the-development-vm"></a>Conectar-se à VM de desenvolvimento

Agora que criamos uma VM, precisamos concluir a instalação do software necessário para concluir o tutorial.

1. Clique duas vezes no arquivo RDP criado pelo script na área de trabalho.

1. Você verá uma caixa de diálogo informando que o editor da conexão remota é desconhecido. Isso é aceitável, portanto, selecione **Conectar**.

1. Forneça a senha de administrador que você forneceu para criar a VM e clique em **OK**.

1. Será solicitado que você aceite o certificado para a VM. Selecione **Sim** na barra superior.

## <a name="install-visual-studio-code-extensions"></a>Instalar extensões do Visual Studio Code

Agora que você se conectou ao computador de desenvolvimento, adicione algumas extensões úteis ao Visual Studio Code para facilitar a experiência de desenvolvimento.

1. Conecte-se à VM de desenvolvimento, abra uma janela do PowerShell e navegue até o diretório **C:\source\IoTEdgeAndMlSample\DevVM**. Esse diretório foi criado pelo script que criou a VM.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Execute o seguinte comando para permitir a execução de scripts. Escolha **Sim para tudo** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Execute o script de extensões do Visual Studio Code.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. O script será executado por alguns minutos, instalando extensões do VS Code:

    * Ferramentas do Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurar o Hub IoT e o Armazenamento

Estas etapas normalmente são executadas por um desenvolvedor de nuvem.

O Hub IoT do Azure é o núcleo de qualquer aplicativo de IoT, pois ele lida com a comunicação segura entre os dispositivos IoT e a nuvem. Ele é o ponto de coordenação principal para a operação da solução de aprendizado de máquina do IoT Edge.

* O Hub IoT usa rotas para encaminhar dados de entrada de dispositivos de IoT para outros serviços downstream. Aproveitaremos as rotas do Hub IoT para enviar dados do dispositivo para o Armazenamento do Azure. No Armazenamento do Azure, os dados do dispositivo são consumidos pelo Azure Machine Learning para treinar nosso classificador de RUL (vida útil restante).

* Mais adiante no tutorial, usaremos o Hub IoT para configurar e gerenciar nosso dispositivo do Azure IoT Edge.

Nesta seção, você pode usar um script para criar um Hub IoT do Azure e uma conta do Armazenamento do Azure. Em seguida, no portal do Azure, você configurará uma rota que encaminha os dados recebidos pelo hub para um contêiner do Armazenamento do Azure. Essas etapas levam cerca de 10 minutos para serem concluídas.

1. Conecte-se à VM de desenvolvimento, abra uma janela do PowerShell e navegue até o diretório **IoTHub**.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Execute o script de criação. Use os mesmos valores de grupo de recursos, localização e ID de assinatura que você usou ao criar a VM de desenvolvimento.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Você será solicitado a entrar no Azure.
    * O script confirma as informações para a criação de seu Hub e sua conta de armazenamento. Pressione **y** ou **ENTER** para continuar.

O script leva cerca de dois minutos para ser executado. Depois de concluído, o script gera o nome do hub IoT e a conta de armazenamento.

## <a name="review-route-to-storage-in-iot-hub"></a>Examine a rota para o armazenamento no Hub IoT

Como parte da criação do Hub IoT, o script que executamos na seção anterior também criou um ponto de extremidade personalizado e uma rota. As rotas do Hub IoT são compostas por uma expressão de consulta e um ponto de extremidade. Se uma mensagem corresponder à expressão, os dados serão enviados pela rota até o ponto de extremidade associado. Pontos de extremidade podem ser Hubs de Eventos, Filas do Barramento de Serviço e Tópicos. Neste caso, o ponto de extremidade é um contêiner de blob em uma conta de armazenamento. Vamos usar o portal do Azure para examinar a rota criada por nosso script.

1. Abra o [portal do Azure](https://portal.azure.com) e acesse o grupo de recursos que você está usando neste tutorial.

1. Na lista de recursos, selecione o Hub IoT criado pelo script. Ele terá um nome que termina com caracteres aleatórios, como `IotEdgeAndMlHub-jrujej6de6i7w`.

1. No menu do painel esquerdo, em **Mensagens**, selecione **Roteiros de mensagens**.

1. Na página **Roteiros de mensagens**, selecione a guia **Pontos de extremidade personalizados**.

1. Expanda a seção **Armazenamento**:

   ![Verifique se turbofanDeviceStorage está na lista de pontos de extremidade personalizados](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Observamos que **turbofanDeviceStorage** está na lista de pontos de extremidade personalizados. Observe as seguintes características sobre este ponto de extremidade:

   * Ele aponta para o contêiner de Armazenamento de Blobs que você criou chamado `devicedata`, conforme indicado pelo **Nome do contêiner**.
   * O **Formato de nome de arquivo** tem a partição como o último elemento no nome. Achamos que esse formato é mais conveniente para as operações de arquivo que faremos com o Azure Notebooks mais adiante no tutorial.
   * O **Status** deve ser íntegro.

1. Selecione a guia **Rotas**.

1. Selecione a rota denominada **turbofanDeviceDataToStorage**.

1. Na página **Detalhes das rotas**, observe que o ponto de extremidade da rota é o ponto de extremidade **turbofanDeviceStorage**.

   ![Examinar os detalhes sobre a rota turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Examine a **Consulta de roteiros**, que está definida como **true**. Essa configuração indica que todas as mensagens de telemetria do dispositivo corresponderão a essa rota e, portanto, todas as mensagens serão enviadas para o ponto de extremidade **turbofanDeviceStorage**.

1. Como nenhuma edição foi feita, basta fechar esta página.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto em que cada artigo se baseia no trabalho feito nos anteriores. Aguarde para limpar todos os recursos até concluir o tutorial final.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, criamos um Hub IoT e configuramos uma rota para uma conta do Armazenamento do Azure. Em seguida, enviaremos dados de um conjunto de dispositivos simulados por meio do Hub IoT para a conta de armazenamento. Mais adiante no tutorial, após configuramos o dispositivo e os módulos do IoT Edge, vamos revisitar as rotas e examinar um pouco mais a consulta de roteamento.

Continue para o próximo artigo para criar um dispositivo simulado para monitoramento.

> [!div class="nextstepaction"]
> [Gerar dados do dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
