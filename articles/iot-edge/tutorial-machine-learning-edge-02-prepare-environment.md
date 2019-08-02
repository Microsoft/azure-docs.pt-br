---
title: Configurar um ambiente – Machine Learning no Azure IoT Edge | Microsoft Docs
description: Prepare seu ambiente para o desenvolvimento e a implantação de módulos de aprendizado de máquina na borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fd3b5766ec2bd8d1babf847598f1fbe5b6511ce7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432841"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Tutorial: Configurar um ambiente para aprendizado de máquina no IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Se você acessou este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para ter os melhores resultados.

Este artigo do tutorial de ponta a ponta do Azure Machine Learning no IoT Edge ajuda você a preparar seu ambiente para desenvolvimento e implantação. Primeiro, configure um computador de desenvolvimento com todas as ferramentas necessárias. Em seguida, crie os recursos de nuvem necessários no Azure.

## <a name="set-up-a-development-machine"></a>Configurar um computador de desenvolvimento

Esta etapa normalmente é executada por um desenvolvedor de nuvem. Alguns dos softwares também podem ser úteis para um cientista de dados.

No decorrer deste artigo, executaremos várias tarefas de desenvolvedor, incluindo a codificação, a compilação, a configuração e a implantação de módulos do IoT Edge e de dispositivos de IoT. Para facilidade de uso, criamos um script do PowerShell que cria uma máquina virtual do Azure com muitos dos pré-requisitos já configurados. A VM criada precisa ser capaz de lidar com a [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), que é o motivo pelo qual escolhemos o tamanho da máquina DS8V3.

A VM de desenvolvimento será configurada com:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop para Windows](https://www.docker.com/products/docker-desktop)
* [Git para Windows](https://gitforwindows.org/)
* [Gerenciador de credenciais do Git para Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK do .Net Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Extensões do VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A VM de desenvolvedor não é estritamente necessária – todas as ferramentas de desenvolvimento podem ser executadas em um computador local. No entanto, é altamente recomendável usar a VM para garantir um campo de atuação uniforme.

São necessários cerca de 30 minutos para criar e configurar a máquina virtual.

### <a name="get-the-script"></a>Obter o script

Clone ou baixe o script do PowerShell do repositório de amostras [Machine Learning e IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample).

### <a name="create-an-azure-virtual-machine"></a>Criar uma máquina virtual do Azure

O diretório DevVM contém os arquivos necessários para criar uma máquina virtual do Azure adequada para concluir este tutorial.

1. Abra o PowerShell como administrador e navegue até o diretório em que você baixou o código. Vamos nos referir ao diretório raiz de sua fonte como `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Execute o seguinte comando para permitir a execução de scripts. Escolha **Sim para tudo** quando solicitado.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Execute Create-AzureDevVM.ps1 nesse diretório.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Quando solicitado, forneça as seguintes informações:
      * **ID de assinatura do Azure**: sua ID da assinatura, que pode ser encontrada no portal do Azure
      * **Nome do Grupo de Recursos**: o nome de um grupo de recursos novo ou existente no Azure
      * **Localização**: escolha a localização do Azure em que a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para obter mais informações, confira [Locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: forneça um nome fácil de lembrar para a conta do administrador que deseja criar e usar na máquina virtual.
      * **AdminPassword**: defina uma senha para a conta do administrador na máquina virtual.

    * Se você não tiver o Azure PowerShell instalado, o script instalará o [módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Você será solicitado a entrar no Azure.

    * O script confirma as informações para a criação de sua VM. Pressione `y` ou `Enter` para continuar.

O script é executado por vários minutos conforme executa as seguintes etapas:

* Criar o grupo de recursos se ele não existir
* Implantar a máquina virtual
* Habilitar o Hyper-V na VM
* Instalar o software necessário para o desenvolvimento e clonar o repositório de exemplo
* Reinicie a VM
* Criar um arquivo RDP em sua área de trabalho para conectar-se à VM

### <a name="set-auto-shutdown-schedule"></a>Definir a agenda de desligamento automático

Para ajudar a reduzir custos, a VM foi criada com uma agenda de desligamento automático definida para 1900 PST. Talvez você precise atualizar esse horário com base em sua localização e em sua agenda. Para atualizar a agenda de desligamento:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Navegue até sua máquina virtual no grupo de recursos que você forneceu na seção anterior.

3. Selecione **Desligamento automático** no navegador lateral.

4. Insira um novo horário de desligamento em **Desligamento agendado** ou alterar o **Fuso horário** e, em seguida, clique em **Salvar**.

### <a name="connect-and-configure-development-machine"></a>Conectar e configurar o computador de desenvolvimento

Agora que criamos uma VM, precisamos concluir a instalação do software necessário para concluir o tutorial.

#### <a name="start-a-remote-desktop-session"></a>Iniciar uma sessão da área de trabalho remota

1. O script de criação da VM criou um arquivo RDP em sua área de trabalho.

2. Clique duas vezes no arquivo **\<nome da VM do Azure\>.rdp**.

3. Você verá uma caixa de diálogo informando que o editor da conexão remota é desconhecido. Marque a caixa de seleção **Não perguntar novamente sobre conexões com este computador** e, em seguida, selecione **Conectar**.

4. Quando solicitado, forneça a AdminPassword que você usou ao executar o script para configurar a VM e clique em **OK**.

5. Será solicitado que você aceite o certificado para a VM. Selecione **Não perguntar novamente sobre conexões com este computador** e escolha **Sim**.

#### <a name="install-visual-studio-code-extensions"></a>Instalar extensões do Visual Studio Code

Agora que você se conectou ao computador de desenvolvimento, adicione algumas extensões úteis ao Visual Studio Code para facilitar a experiência de desenvolvimento.

1. Em uma janela do PowerShell, navegue até **C:\\source\\IoTEdgeAndMlSample\\DevVM**.

2. Permita que scripts sejam executados na máquina virtual digitando.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Execute o script.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. O script será executado por alguns minutos, instalando extensões do VS Code:

    * Ferramentas do Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurar o Hub IoT e o Armazenamento

Estas etapas normalmente são executadas por um desenvolvedor de nuvem.

O Hub IoT do Azure é o coração de qualquer aplicativo de IoT. Ele manipula a comunicação segura entre os dispositivos IoT e a nuvem. Ele é o ponto de coordenação principal para a operação da solução de aprendizado de máquina do IoT Edge.

* O Hub IoT usa rotas para encaminhar dados de entrada de dispositivos de IoT para outros serviços downstream. Tiraremos proveito das rotas do Hub IoT para enviar dados do dispositivo para o Armazenamento do Azure, em que eles podem ser consumidos pelo Azure Machine Learning para treinar nosso classificador de RUL (vida útil) restante.

* Mais adiante no tutorial, usaremos o Hub IoT para configurar e gerenciar nosso dispositivo do Azure IoT Edge.

Nesta seção, você pode usar um script para criar um Hub IoT do Azure e uma conta do Armazenamento do Azure. Em seguida, você configura uma rota que encaminha dados recebidos pelo hub para um contêiner do Azure Storage Blob usando o portal do Azure. Essas etapas levam cerca de 10 minutos para serem concluídas.

### <a name="create-cloud-resources"></a>Criar recursos de nuvem

1. No computador de desenvolvimento, abra uma janela do PowerShell.

1. Vá para o diretório IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Execute o script de criação. Use os mesmos valores de grupo de recursos, localização e ID de assinatura que você usou ao criar a VM de desenvolvimento.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Você será solicitado a entrar no Azure.
    * O script confirma as informações para a criação de seu Hub e sua conta de armazenamento. Pressione `y` ou `Enter` para continuar.

O script leva cerca de dois minutos para ser executado. Uma vez concluído, o script gera o nome do hub e a conta de armazenamento.

### <a name="review-route-to-storage-in-iot-hub"></a>Examine a rota para o armazenamento no Hub IoT

Como parte da criação do Hub IoT, o script que executamos na seção anterior também criou um ponto de extremidade personalizado e uma rota. As rotas do Hub IoT são compostas por uma expressão de consulta e um ponto de extremidade. Se uma mensagem corresponder à expressão, os dados serão enviados pela rota até o ponto de extremidade associado. Pontos de extremidade podem ser Hubs de Eventos, Filas do Barramento de Serviço e Tópicos. Neste caso, o ponto de extremidade é um contêiner de Blob em uma conta de armazenamento. Vamos usar o portal do Azure para examinar a rota criada por nosso script.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Escolha todos os serviços no navegador esquerdo, digite IoT na caixa de pesquisa e selecione **Hub IoT**.

1. Escolha o Hub IoT criado na etapa anterior.

1. No navegador lateral do Hub IoT, escolha **Roteamento de mensagens**.

1. A página de roteamento de mensagens tem duas guias, **Rotas** e **Pontos de extremidade personalizados**. Selecione a guia **Pontos de extremidade personalizados**.

1. Em **Armazenamento de Blobs**, selecione **turbofanDeviceStorage**.

1. Observe que esse ponto de extremidade aponta para um contêiner de blob chamado **devicedata** na conta de armazenamento criada na última etapa, que é denominado **iotedgeandml\<sufixo exclusivo\>** .

1. Observe também que o **Formato do nome de arquivo de blob** foi alterado do formato padrão para colocar a partição como o último elemento do nome. Achamos que esse formato é mais conveniente para as operações de arquivo que faremos com o Azure Notebooks mais adiante no tutorial.

1. Feche a folha de detalhes do ponto de extremidade para voltar para a página **Roteamento de mensagens**.

1. Selecione a guia **Rotas**.

1. Selecione a rota denominada **turbofanDeviceDataToStorage**.

1. Observe que o ponto de extremidade da rota é o ponto de extremidade personalizado **turbofanDeviceStorage**.

1. Examine a consulta de roteamento, que está definida como **true**. Isso significa que todas as mensagens de telemetria do dispositivo corresponderão a essa rota e, portanto, todas as mensagens serão enviadas para o ponto de extremidade **turbofanDeviceStorage**.

1. Feche os detalhes da rota.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, criamos um Hub IoT e configuramos uma rota para uma conta do Armazenamento do Azure. No próximo artigo, enviaremos dados de um conjunto de dispositivos simulados por meio do Hub IoT para a conta de armazenamento. Mais adiante no tutorial, após configuramos o dispositivo e os módulos do IoT Edge, vamos revisitar as rotas e examinar um pouco mais a consulta de roteamento.

Para obter mais informações sobre as etapas abordadas nesta parte do tutorial de Machine Learning no IoT Edge, confira:

* [Conceitos básicos do Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurar o roteamento de mensagens com o Hub IoT](../iot-hub/tutorial-routing.md)
* [Criar um Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md)

Continue para o próximo artigo para criar um dispositivo simulado para monitoramento.

> [!div class="nextstepaction"]
> [Gerar dados do dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
