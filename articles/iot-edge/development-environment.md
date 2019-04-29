---
title: Ambiente de desenvolvimento do Azure IoT Edge | Microsoft Docs
description: Saiba mais sobre os sistemas com suporte e ferramentas de desenvolvimento de primeira parte que o ajudará a criar módulos do IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3d1fb393138aeb590bc0645d6abe93652a52a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247534"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Prepare o ambiente de desenvolvimento e teste para o IoT Edge

O Azure IoT Edge move a lógica de negócios existente para dispositivos operando na borda. Para preparar seus aplicativos e cargas de trabalho para executar como [Módulos do IoT Edge](iot-edge-modules.md), você precisa criá-los como contêineres. Este artigo fornece orientação sobre como configurar seu ambiente de desenvolvimento para que você possa criar uma solução IoT Edge com êxito. Depois de configurar seu ambiente de desenvolvimento, em seguida, você pode aprender como [Desenvolver seus próprios módulos do IoT Edge](module-development.md).

Em qualquer solução de IoT Edge, há pelo menos duas máquinas a serem consideradas. Uma é o dispositivo do IoT Edge (ou dispositivos) em si, que executa o módulo do IoT Edge. A outra é o computador de desenvolvimento que você usa para criar, testar e implantar módulos. Este artigo se concentra principalmente na máquina de desenvolvimento. Para fins de teste, as duas máquinas podem ser a mesma. Você pode executar o IoT Edge em seu computador de desenvolvimento e implantar módulos para ele.

## <a name="operating-system"></a>Sistema operacional

O Azure IoT Edge é executado em um conjunto específico de [sistemas operacionais com suporte](support.md). Para o desenvolvimento do IoT Edge, você pode usar a maioria dos sistemas operacionais que podem executar um mecanismo de contêiner. O mecanismo de contêiner é um requisito na máquina de desenvolvimento para compilar seus módulos como contêineres e enviar por push a um registro de contêiner. 

Se seu computador de desenvolvimento não puder executar o Azure IoT Edge, continue neste artigo para saber mais sobre [ferramentas de teste](#testing-tools) que ajudam a testar e depurar localmente. 

O sistema operacional do computador de desenvolvimento não precisa corresponder ao sistema operacional do dispositivo IoT Edge. No entanto, o sistema operacional do contêiner deve ser consistente entre o computador de desenvolvimento e o dispositivo do IoT Edge. Por exemplo, você pode desenvolver módulos em um computador Windows e implantá-los em um dispositivo Linux. O computador do Windows precisa executar contêineres do Linux para criar os módulos para o dispositivo Linux. 

## <a name="container-engine"></a>Mecanismo de contêiner

O conceito central do IoT Edge é que você remotamente pode implantar sua lógica de negócios e de nuvem para dispositivos, empacotando-o em contêineres. Para criar contêineres, você precisa de um mecanismo de contêiner no computador de desenvolvimento. 

O único mecanismo de contêiner com suporte para dispositivos do IoT Edge na produção é o Moby. No entanto, qualquer mecanismo de contêiner compatível com a iniciativa de contêiner aberto, como o Docker, é capaz de compilar as imagens do módulo do IoT Edge. 

## <a name="development-tools"></a>Ferramentas de desenvolvimento

Visual Studio e Visual Studio Code têm extensões de complemento para ajudar a desenvolver soluções do IoT Edge. Essas extensões fornecem modelos específicos do idioma para ajudar a criar e implantar novos cenários de IoT Edge. As extensões do Azure IoT Edge para Visual Studio e Visual Studio Code ajudam você a codificar, compilar, implantar e depurar suas soluções do IoT Edge. Você pode criar uma solução de IoT Edge inteira que contenha vários módulos e as extensões de atualizar automaticamente um modelo de manifesto de implantação com cada nova adição do módulo. Com as extensões, você também pode gerenciar seus dispositivos de IoT de dentro do Visual Studio ou Visual Studio Code. Implantar módulos em um dispositivo, monitorar o status e exibir as mensagens que chegam ao Hub IoT. Ambas as extensões usam a [ferramenta de desenvolvimento de IoT EdgeHub](#iot-edgehub-dev-tool) para habilitar o local em execução e depuração de módulos no seu computador de desenvolvimento. 

Se você preferir desenvolver com outros editores ou a partir da CLI, a ferramenta de desenvolvimento do Azure IoT Edge fornece comandos para que você possa desenvolver e testar a partir da linha de comando. Você pode criar novos cenário do IoT Edge, compilar imagens do módulo, executar módulos em um simular e monitorar mensagens enviadas para o Hub IoT. 

### <a name="visual-studio-code-extension"></a>Extensão Visual Studio Code

A extensão do Azure IoT Edge para Visual Studio Code fornece os modelos do módulo IoT Edge compilados em linguagens de programação incluindo C, C#, Java, Node.js, e Python, bem como funções do Azure no C#. 

Para obter mais informações e fazer o download, consulte [Ferramentas do Azure IoT para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Além de extensões do IoT Edge, talvez você considere útil instalar extensões adicionais para o desenvolvimento. Por exemplo, você pode usar o [Suporte do Docker para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para gerenciar suas imagens, contêineres e registros. Além disso, todas as linguagens com suporte principais têm extensões para o Visual Studio Code que podem ajudar quando você estiver desenvolvendo módulos. 

#### <a name="prerequisites"></a>Pré-requisitos

Os modelos de módulo para algumas linguagens e serviços têm os pré-requisitos que são necessários para criar as pastas de projeto em seu computador de desenvolvimento com o Visual Studio Code.

| Modelo do módulo | Pré-requisito |
| --------------- | ------------ |
| Funções do Azure | [SDK do .NET Core 2.1](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [SDK do .NET Core 2.1](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Defina a variável de ambiente JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Gerador de módulo Node. js do IoT Edge do Azure](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-2017-extension"></a>Extensão do Visual Studio 2017

As ferramentas do Azure IoT Edge para Visual Studio fornecem um IoT Edge criado no modelo do módulo de C#. 

Para obter mais informações e fazer o download, consulte [Ferramentas do Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

### <a name="iot-edge-dev-tool"></a>Ferramenta de desenvolvimento IoT Edge

A ferramenta de desenvolvimento do Azure IoT Edge simplifica o desenvolvimento de IoT Edge com capacidades de linha de comando. Essa ferramenta fornece comandos da CLI para desenvolver, depurar e testar módulos. A ferramenta de desenvolvimento do IoT Edge funciona com o sistema de desenvolvimento, se você instalou manualmente as dependências em seu computador ou está usando o contêiner de desenvolvimento do IoT Edge. 

Para obter mais informações e começar a usar, consulte [wiki de ferramenta de desenvolvimento de IoT Edge](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Ferramentas de teste

Existem várias ferramentas de testes para ajudá-lo a simular dispositivos IoT Edge ou depurar módulos com mais eficiência. A tabela a seguir mostra uma comparação entre as ferramentas de alto nível e, em seguida, as seções individuais descrevem cada ferramenta mais especificamente. 

Apenas o tempo de execução do IoT Edge tem suporte para implantações de produção, mas as ferramentas a seguir permitem que você simule ou crie facilmente dispositivos para fins de teste e desenvolvimento de IoT Edge. Essas ferramentas não são mutuamente exclusivas, mas podem trabalhar juntas para uma experiência de desenvolvimento completo. 

| Ferramenta | Também conhecida como | Plataformas com suporte | Mais adequado para |
| ---- | ------------- | ------------------- | --------- |
| Ferramenta de desenvolvimento de IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Simular um dispositivo para depurar os módulos. |
| Contêiner de desenvolvimento de IoT Edge | microsoft/iotedgedev | Windows, Linux, MacOS | Desenvolvimento sem instalar as dependências. |
| Tempo de execução do IoT Edge em um contêiner | iotedgec | Windows, Linux, MacOS, ARM | Testar em um dispositivo que pode não oferecer suporte ao tempo de execução. |
| Contêiner de desenvolvimento de IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testar um cenário com vários dispositivos de IoT Edge em escala. |

### <a name="iot-edgehub-dev-tool"></a>Ferramenta de desenvolvimento de IoT EdgeHub

A ferramenta de desenvolvimento do Azure IoT EdgeHub fornece uma experiência de desenvolvimento e depuração local. A ferramenta ajuda a iniciar módulos do IoT Edge sem o tempo de execução do IoT Edge para que você possa criar, desenvolver, testar, executar e depurar os módulos IoT Edge e soluções localmente. Você não precisa enviar imagens por push para um registro de contêiner e implantá-las em um dispositivo para teste.

A ferramenta de desenvolvimento de IoT EdgeHub foi projetada para trabalhar em tandem com as extensões do Visual Studio e Visual Studio Code, bem como com a ferramenta de desenvolvimento de IoT Edge. Dá suporte ao desenvolvimento de loop interno, bem como testes de loop externo, portanto, integra-se às ferramentas de DevOps também. 

Para obter mais informações e para instalar, consulte [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Contêiner de desenvolvimento de IoT Edge

O contêiner de desenvolvimento do Azure IoT Edge é um contêiner do Docker que tem todas as dependências que você precisa para desenvolvimento de IoT Edge. Esse contêiner torna mais fácil começar com a linguagem que você deseja desenvolver, inclusive C#, Python, Node. js e Java. Você só precisa instalar um mecanismo de contêiner, como Docker ou Moby, para efetuar pull no contêiner para seu computador de desenvolvimento. 

Para obter mais informações, consulte[ Contêiner de desenvolvimento do Azure IoT Edge](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Tempo de execução do IoT Edge em um contêiner

O tempo de execução do IoT Edge em um contêiner fornece um tempo de execução completo que usa a cadeia de conexão do dispositivo como uma variável de ambiente. Esse contêiner permite que você teste módulos e cenários do IoT Edge em um sistema que pode não suportar o tempo de execução nativo, como MacOS. Todos os módulos que você implantar serão iniciados fora do contêiner de tempo de execução. Se você quiser que o tempo de execução e todos os módulos implantados existam dentro do mesmo contêiner, considere o contêiner de dispositivo do IoT Edge.

Para obter mais informações, consulte[ Executar o Azure IoT Edge em um contêiner](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Contêiner de desenvolvimento de IoT Edge

O contêiner de dispositivo do IoT Edge é um dispositivo de IoT Edge concluído, pronto para ser iniciado em qualquer computador com um mecanismo de contêiner. O contêiner de dispositivo inclui o tempo de execução do IoT Edge e um mecanismo de contêiner em si. Cada instância do contêiner é um dispositivo de IoT Edge de autoprovisionamento totalmente funcional. O contêiner do dispositivo dá suporte à depuração remota de módulos, desde que exista uma rota de rede para o módulo. O contêiner de dispositivo é bom para a criação rápida de grandes quantidades de dispositivos do IoT Edge para testar cenários em escala ou pipelines de DevOps. Ele também dá suporte à implantação de kubernetes por meio do helm. 

Para obter mais informações, consulte[ Contêiner de desenvolvimento do Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Ferramentas DevOps

Quando estiver pronto para desenvolver soluções em escala para cenários de produção extenso, aproveite os princípios de DevOps modernos incluindo processos de engenharia de software simplificada, monitoramento e automação. O IoT Edge tem extensões para dar suporte a ferramentas de DevOps, incluindo DevOps do Azure, Azure DevOps Projects e Jenkins. Se você quiser personalizar um pipeline existente ou usar outra ferramenta DevOps como CircleCI ou TravisCI, você pode fazer isso com os recursos CLI incluídos na ferramenta de desenvolvimento de IoT Edge.

Para obter mais informações, diretrizes e exemplos, consulte as seguintes páginas:
* [Integração contínua e implantação contínua no Azure IoT Edge](how-to-ci-cd.md)
* [Criar um pipeline de CI/CD para IoT Edge com o Azure DevOps Projects](how-to-devops-project.md)
* [Plug-in do Jenkins do Azure IoT Edge](https://plugins.jenkins.io/azure-iot-edge)
* [Repositório GitHub de DevOps do IoT Edge](https://github.com/toolboc/IoTEdge-DevOps)
