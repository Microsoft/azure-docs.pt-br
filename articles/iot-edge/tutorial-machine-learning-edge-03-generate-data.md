---
title: 'Tutorial: Gerar dados de dispositivo simulado – Machine Learning no Azure IoT Edge'
description: Tutorial – Crie dispositivos virtuais que geram a telemetria simulada que depois pode ser usada para treinar um modelo de machine learning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fdd762834d351119116c5e4854dd4233671c29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463122"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Gerar dados de dispositivo simulado

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, usaremos os dados de treinamento de aprendizado de máquina para simular um dispositivo enviando telemetria ao Hub IoT do Azure. Conforme mencionado na introdução, este tutorial usa o [conjunto de dados de simulação de degradação do motor Turbofan](https://c3.nasa.gov/dashlink/resources/139/) para simular os dados de um conjunto de motores de avião para treinamento e teste.

Em nosso cenário experimental, sabemos que:

* Os dados consistem em várias séries temporais multivariadas.
* Cada conjunto de dados é dividido em subconjuntos de treinamento e teste.
* Cada série temporal é proveniente de um motor diferente.
* Cada motor começa com diferentes graus de desgaste inicial e variação de fabricação.

Para este tutorial, usamos o subconjunto de dados de treinamento de um único conjunto de dados (FD003).

Na realidade, cada motor seria um dispositivo de IoT independente. Supondo que você não tenha uma coleção de motores turbofan conectados à Internet disponíveis, vamos criar um substituto de software para esses dispositivos.

O simulador é um programa C# que usa as APIs do Hub IoT para registrar de modo programático dispositivos virtuais com o Hub IoT. Em seguida, lemos os dados para cada dispositivo do subconjunto de dados fornecido pela NASA e os enviamos ao hub IoT usando um dispositivo de IoT simulado. Todo o código desta parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto DeviceHarness é um projeto do .NET Core escrito em C# e consiste em quatro classes:

* **Programa:** o ponto de entrada para a execução responsável por processar a entrada do usuário e a coordenação geral.
* **TrainingFileManager:** responsável por ler e analisar o arquivo de dados selecionado.
* **CycleData:** representa uma só linha de dados em um arquivo convertido no formato da mensagem.
* **TurbofanDevice:** responsável pela criação de um dispositivo IoT que corresponde a um só dispositivo (série temporal) nos dados e por transmitir os dados para o Hub IoT.

A conclusão das tarefas descritas neste artigo deve levar cerca de 20 minutos.

O equivalente do mundo real para o trabalho nesta etapa provavelmente seria executado por desenvolvedores de dispositivos e desenvolvedores de nuvem.

Nesta seção do tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Incorporar um projeto externo ao seu ambiente de desenvolvimento.
> * Usar o projeto DeviceHarness de exemplo para gerar dados simulados do dispositivo IoT.
> * Exibir os dados gerados no hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, acesse o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configurar o Visual Studio Code e compilar o projeto DeviceHarness

1. Abra uma sessão da Área de Trabalho Remota na VM de desenvolvimento.

1. No Visual Studio Code, abra a pasta `C:\source\IoTEdgeAndMlSample\DeviceHarness`.

1. Uma vez que você está usando extensões neste computador pela primeira vez, algumas extensões vão atualizar e instalar as respectivas dependências. Você será solicitado a atualizar a extensão. Se isso acontecer, selecione **Recarregar Janela**.

   Se forem exibidos erros do OmniSharp na Janela de Saída, você precisará desinstalar a extensão C#.

1. Você será solicitado a adicionar os ativos necessários para DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação poderá levar alguns segundos para aparecer.
   * Caso você tenha perdido essa notificação, verifique o ícone de sino no canto inferior direito.

   ![Pop-up de extensão do VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecione **Restaurar** para restaurar as dependências do pacote.

   ![Prompt de restauração do VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Caso você não receba essas notificações, feche o Visual Studio Code, exclua os diretórios bin e obj em `C:\source\IoTEdgeAndMlSample\DeviceHarness`, abra o Visual Studio Code e reabra a pasta DeviceHarness.

1. Confirme se o ambiente está configurado corretamente disparando um build, **CTRL** + **SHIFT** + **B**, ou **Terminal** > **Executar Tarefa de Build**.

1. Você precisará selecionar a tarefa de build a ser executada. Selecione **Compilar**.

1. O build é executado e gera uma mensagem de êxito.

   ![Mensagem de saída de build bem-sucedido](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Você pode tornar esse build a tarefa de build padrão selecionando **Terminal** > **Configurar a Tarefa de Build Padrão…** e escolhendo **Build** no prompt.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Conectar-se ao Hub IoT e executar o DeviceHarness

Agora que temos a compilação do projeto, conecte-se ao seu hub IoT para acessar a cadeia de conexão e monitorar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Entre no Azure no Visual Studio Code

1. Entre em sua assinatura do Azure no Visual Studio Code abrindo a paleta de comandos, `Ctrl + Shift + P` ou **Exibir** > **Paleta de Comandos**.

1. Pesquise o comando **Azure: Entrar**.

   Uma janela do navegador é aberta e solicita suas credenciais. Quando for redirecionado para uma página de êxito, feche o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Conectar-se a seu hub IoT e recuperar a cadeia de conexão do hub

1. Na seção inferior do gerenciador do Visual Studio Code, selecione o quadro **Hub IoT do Azure** para expandi-lo.

1. No quadro expandido, clique em **Selecionar Hub IoT**.

1. Quando solicitado, selecione sua assinatura do Azure e então o hub IoT.

1. Clique nas **...** à direita do **Hub IoT do Azure** para obter mais ações. Selecione **Copiar cadeia de conexão do Hub IoT**.

   ![Copiar cadeia de conexão do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Executar o projeto DeviceHarness

1. Selecione **Exibir** > **Terminal** para abrir o terminal do Visual Studio Code.

   Se um aviso não for exibido, pressione ENTER.

1. Insira `dotnet run` no terminal.

1. Quando solicitado a fornecer a cadeia de conexão do Hub IoT, cole a cadeia de conexão copiada da seção anterior.

1. No quadro **Dispositivos do Hub IoT do Azure**, clique no botão de atualização.

   ![Atualizar lista de dispositivos do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observe que os dispositivos são adicionados ao Hub IoT e aparecem em verde para indicar que os dados estão sendo enviados por meio desse dispositivo. Depois que os dispositivos enviam mensagens ao hub IoT, eles se desconectam e são exibidos em azul.

1. Veja as mensagens enviadas ao hub clicando com o botão direito do mouse em qualquer dispositivo e selecionando **Iniciar Monitoramento do Ponto de Extremidade de Evento Interno**. As mensagens serão exibidas no painel de saída no Visual Studio Code.

1. Interrompa o monitoramento clicando no painel de saída **Hub IoT do Azure** e escolha **Interromper o Monitoramento do Ponto de Extremidade de Evento Interno**.

1. Permita que a execução do aplicativo seja concluída, o que leva alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verifique o Hub IoT para a atividade

Os dados enviados pelo DeviceHarness entraram no hub IoT, em que você pode verificar no portal do Azure.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o hub IoT criado para este tutorial.

1. No menu do painel esquerdo, em **Monitoramento**, selecione **Métricas**.

1. Na página de definição do gráfico, clique no menu suspenso **Métrica**, role a página para baixo na lista e selecione **Roteiros: dados entregues ao armazenamento**. O gráfico deverá mostrar o pico de quando os dados foram roteados para o armazenamento.

   ![O gráfico mostra o pico de quando os dados foram entregues ao armazenamento](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no Armazenamento do Azure

Os dados que acabamos de enviar ao hub IoT foram encaminhados para o contêiner de armazenamento que criamos no artigo anterior. Vamos examinar os dados em nossa conta de armazenamento.

1. No portal do Azure, navegue até sua conta de armazenamento.

1. No navegador da conta de armazenamento, selecione **Gerenciador de Armazenamento (versão prévia)** .

1. No Gerenciador de Armazenamento, selecione **Contêineres de Blob** e, em seguida, `devicedata`.

1. No painel de conteúdo, clique na pasta do nome do hub IoT, seguido por ano, mês, dia e hora. Você verá várias pastas representando os minutos de quando os dados foram gravados.

   ![Exibir as pastas no Armazenamento de Blobs](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique em uma destas pastas para localizar os arquivos de dados rotulados como **00** e **01** correspondendo à partição.

1. Os arquivos são gravados no formato [Avro](https://avro.apache.org/). Clique duas vezes em um desses arquivos para abrir outra guia do navegador e renderizar parcialmente os dados. Se precisar abrir o arquivo em um programa, escolha o VS Code e ele será renderizado corretamente.

1. Não é necessário tentar ler nem interpretar os dados no momento. Faremos isso no próximo artigo.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto em que cada artigo se baseia no trabalho feito nos anteriores. Aguarde para limpar todos os recursos até concluir o tutorial final.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, usamos um projeto .NET Core para criar um conjunto de dispositivos IoT virtuais e enviar dados por meio deles ao nosso hub IoT e para um contêiner do Armazenamento do Azure. Este projeto simula um cenário do mundo real em que os dispositivos IoT físicos enviam dados para um Hub IoT e adiante para um armazenamento coletado. Esses dados incluem leituras de sensor, configurações operacionais, sinais de falha, modos etc. Depois que dados suficientes forem coletados, usaremos esses dados para treinar modelos que preveem a RUL (vida útil restante) do dispositivo. Demonstraremos este aprendizado de máquina no próximo artigo.

Prossiga para o próximo artigo para treinar um modelo de machine learning com os dados.

> [!div class="nextstepaction"]
> [Treinar e implantar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
