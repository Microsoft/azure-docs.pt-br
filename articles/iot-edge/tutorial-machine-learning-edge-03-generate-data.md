---
title: Gerar dados de dispositivo simulado – Machine Learning no Azure IoT Edge | Microsoft Docs
description: Crie dispositivos virtuais que geram a telemetria simulada que depois pode ser usada para treinar um modelo de machine learning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 666172e3685b923ca0d0e5fa02878341fcd0a216
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543865"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Gerar dados de dispositivo simulado

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Se você acessou este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para ter os melhores resultados.

Neste artigo, usamos os dados de treinamento de machine learning para simular um dispositivo enviando telemetria ao Hub IoT. Conforme mencionado na introdução, este tutorial de ponta a ponta usa o [Conjunto de dados de simulação de degradação do motor Turbofan](https://c3.nasa.gov/dashlink/resources/139/) para simular os dados de um conjunto de motores de avião para treinamento e teste.

Do readme.txt que acompanha este artigo, sabemos que:

* Os dados consistem em várias séries temporais multivariadas
* Cada conjunto de dados é dividido em subconjuntos de treinamento e teste
* Cada série temporal é de um motor diferente
* Cada motor começa com graus diferentes de desgaste inicial e variação de fabricação

Para este tutorial, usamos o subconjunto de dados de treinamento de um único conjunto de dados (FD003).

Na realidade, cada motor seria um dispositivo de IoT independente. Supondo que você não tenha uma coleção de motores turbofan conectados à Internet disponíveis, vamos criar um substituto de software para esses dispositivos.

O simulador é um programa C# que usa as APIs do Hub IoT para registrar de modo programático dispositivos virtuais com o Hub IoT. Em seguida, lemos os dados para cada dispositivo do subconjunto de dados fornecido pela NASA e os enviamos ao hub IoT usando um dispositivo de IoT simulado. Todo o código desta parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto DeviceHarness é um projeto do .NET Core escrito em C# e consiste em quatro classes:

* **Programa:** o ponto de entrada para a execução responsável por processar a entrada do usuário e a coordenação geral.
* **TrainingFileManager:** responsável por ler e analisar o arquivo de dados selecionado.
* **CycleData:** representa uma única linha de dados em um arquivo convertido no formato de mensagem.
* **TurbofanDevice:** responsável pela criação de um Dispositivo IoT que corresponde a um único dispositivo (série temporal) nos dados e transmitindo os dados para o Hub IoT por meio do Dispositivo IoT.

A conclusão das tarefas descritas neste artigo deve levar cerca de 20 minutos.

O equivalente do mundo real para o trabalho nesta etapa provavelmente seria executado por desenvolvedores de dispositivos e desenvolvedores de nuvem.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configurar o Visual Studio Code e compilar o projeto DeviceHarness

1. Abra uma sessão de área de trabalho remota para sua máquina virtual, conforme demonstrado no artigo anterior.

1. Abra o Visual Studio Code.

1. No Visual Studio Code, selecione **Arquivo** > **Abrir Pasta...** .

1. Na caixa de texto **Pasta**, insira `C:\source\IoTEdgeAndMlSample\DeviceHarness` e clique no botão **Selecionar Pasta**.

   Se aparecerem erros OmniSharp na janela de saída, você precisará desinstalar a extensão do C#, fechar e reabrir o VS Code, instalar a extensão do C# e, em seguida, recarregar a janela.

1. Uma vez que você está usando extensões neste computador pela primeira vez, algumas extensões vão atualizar e instalar as respectivas dependências. Você será solicitado a atualizar a extensão. Se isso acontecer, selecione **Recarregar Janela**.

1. Você será solicitado a adicionar os ativos necessários para DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação poderá levar alguns segundos para aparecer.
   * Caso você tenha perdido essa notificação, verifique o ícone de "sino" no canto inferior direito.

   ![Pop-up de extensão do VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecione **Restaurar** para restaurar as dependências do pacote.

   ![Prompt de restauração do VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Valide que seu ambiente está configurado corretamente disparando um build `Ctrl + Shift + B` ou **Terminal** > **Executar Tarefa de Build**.

1. Você é solicitado a selecionar a tarefa de build a ser executada. Selecione **Compilar**.

1. O build é executado e gera uma mensagem de êxito.

   ![Mensagem de saída de build bem-sucedido](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Você pode tornar esse build a tarefa de build padrão selecionando **Terminal** > **Configurar a Tarefa de Build Padrão…** e escolhendo **Build** no prompt.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Conectar-se ao Hub IoT e executar o DeviceHarness

Agora que temos a compilação do projeto, conecte-se ao seu hub IoT para acessar a cadeia de conexão e monitorar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Entre no Azure no Visual Studio Code

1. Entre em sua assinatura do Azure no Visual Studio Code, abra a paleta de comandos `Ctrl + Shift + P` ou **Exibir** > **Paleta de Comandos...** .

1. No prompt, pesquise e selecione **Azure: entrar**.

1. Uma janela do navegador é aberta e solicita suas credenciais. Quando você for redirecionado para uma página de êxito, poderá fechar o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Conectar-se a seu hub IoT e recuperar a cadeia de conexão do hub

1. Na seção inferior do gerenciador do Visual Studio Code, selecione o quadro **Dispositivos do Hub IoT do Azure** para expandi-lo.

1. No quadro expandido, clique em **Selecionar Hub IoT**.

1. Quando solicitado, selecione sua assinatura do Azure e então o hub IoT.

1. Clique no quadro **Dispositivos do Hub IoT do Azure** e clique em **...** para mais ações. Selecione **Copiar cadeia de conexão do Hub IoT**.

   ![Copiar cadeia de conexão do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Executar o projeto DeviceHarness

1. Selecione **Exibir** > **Terminal** para abrir o terminal do Visual Studio Code.

   Caso não veja um prompt, pressione Enter.

1. Insira `dotnet run` no terminal.

1. Quando solicitado a fornecer a cadeia de conexão do Hub IoT, cole a cadeia de conexão copiada da seção anterior.

1. No quadro **Dispositivos do Hub IoT do Azure**, clique no botão de atualização.

   ![Atualizar lista de dispositivos do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observe que os dispositivos são adicionados ao Hub IoT e aparecem em verde para indicar que os dados estão sendo enviados por meio desse dispositivo.

1. Você pode exibir as mensagens que estão sendo enviadas para o hub clicando com o botão direito do mouse em qualquer dispositivo e selecionando **Iniciar Monitoramento do Ponto de Extremidade de Evento Interno**. As mensagens serão exibidas no painel de saída no Visual Studio Code.

1. Interrompa o monitoramento clicando no painel de saída **Kit de Ferramentas do Hub IoT do Azure** e escolha **Interromper o Monitoramento do Ponto de Extremidade de Evento Interno**.

1. Permita que a execução do aplicativo seja concluída, o que leva alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verifique o Hub IoT para a atividade

Os dados enviados pelo DeviceHarness foram para seu hub IoT. É fácil verificar que os dados atingiram seu hub usando o portal do Azure.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até seu hub IoT.

1. Na página de visão geral, você verá que os dados foram enviados ao hub:  

   ![Exibir mensagens do dispositivo para a nuvem no Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no Armazenamento do Azure

Os dados que acabamos de enviar ao hub IoT foram encaminhados para o contêiner de armazenamento que criamos no artigo anterior. Vamos examinar os dados em nossa conta de armazenamento.

1. No portal do Azure, navegue até sua conta de armazenamento.

1. No navegador da conta de armazenamento, selecione **Gerenciador de Armazenamento (versão prévia)** .

1. No Gerenciador de Armazenamento, selecione **Contêineres de Blob** e, em seguida, **devicedata**.

1. Na painel de conteúdo, clique na pasta para o nome do hub IoT e, em seguida, no ano, no mês, no dia e na hora. Você verá várias pastas representando os minutos de quando os dados foram gravados.

   ![Exibir as pastas no Armazenamento de Blobs](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique em uma destas pastas para localizar os arquivos de dados rotulados como **00** e **01** correspondendo à partição.

1. Os arquivos são escritos em formato [Avro](https://avro.apache.org/), mas clicar duas vezes em um desses arquivos abrirá outra guia do navegador e renderizará os dados parcialmente. Se, em vez disso, você for solicitado a abrir o arquivo em um programa, poderá escolher o VS Code e ele será renderizado corretamente.

1. Não é necessário tentar ler nem interpretar os dados no momento. Faremos isso no próximo artigo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, usamos um projeto do .NET Core para criar um conjunto de dispositivos virtuais e enviar dados por meio desses dispositivos usando nosso Hub IoT para um contêiner de Armazenamento do Azure. Este projeto simula um cenário do mundo real em que dispositivos físicos enviam dados, incluindo leituras do sensor, configurações operacionais, sinais e modos de falha e assim por diante, para um Hub IoT e seguindo para um armazenamento coletado. Depois de coletar dados suficientes, usamos esses dados para treinar modelos que preveem a RUL (vida útil restante) do dispositivo, o que vamos demonstrar no próximo artigo.

Prossiga para o próximo artigo para treinar um modelo de machine learning com os dados.

> [!div class="nextstepaction"]
> [Treinar e implantar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
