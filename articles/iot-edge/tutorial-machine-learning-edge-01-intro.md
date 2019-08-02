---
title: Passo a passo detalhado do Machine Learning no Azure IoT Edge | Microsoft Docs
description: Um tutorial de alto nível que percorre as várias tarefas necessárias para criar um aprendizado de máquina de ponta a ponta no cenário de borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 916e48752431be41ff150c2ac84e66eb1e98e81f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064670"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Tutorial: Uma solução de ponta a ponta que usa o Azure Machine Learning e o IoT Edge

Com frequência, os aplicativos IoT desejam aproveitar a nuvem e a borda inteligentes. Neste tutorial, explicaremos como treinar um modelo de machine learning usando dados coletados de dispositivos de IoT na nuvem, implantando esse modelo no IoT Edge e mantendo e refinando o modelo periodicamente.

O objetivo principal deste tutorial é apresentar o processamento de dados de IoT com o aprendizado de máquina, especificamente na borda. Embora abordemos muitos aspectos de um fluxo de trabalho de aprendizado de máquina geral, este tutorial não pretende ser uma introdução aprofundada ao aprendizado de máquina. A título de exemplo, não tentamos criar um modelo altamente otimizado para o caso de uso – fazemos o suficiente para ilustrar o processo de criação e de uso de um modelo viável para o processamento de dados de IoT.

## <a name="target-audience-and-roles"></a>Público-alvo e funções

Esse conjunto de artigos é destinado a desenvolvedores sem experiência anterior em aprendizado de máquina ou em desenvolvimento de IoT. Implantar o aprendizado de máquina na borda requer conhecimento de como conectar uma ampla variedade de tecnologias. Portanto, este tutorial aborda um cenário inteiro de ponta a ponta para demonstrar uma maneira de unir essas tecnologias em conjunto para uma solução de IoT. Em um ambiente de mundo real, essas tarefas podem ser distribuídas entre várias pessoas com especializações diferentes. Por exemplo, os desenvolvedores se concentrariam no código do dispositivo ou da nuvem, enquanto os cientistas de dados criavam os modelos de análise. Para permitir que um desenvolvedor individual conclua este tutorial com êxito, fornecemos diretrizes suplementares com insights e links para mais informações que esperamos que sejam suficientes para compreender o que está sendo feito e por quê.

Como alternativa, você pode se juntar a colegas de diferentes funções para seguirem o tutorial juntos, exercendo todo seu conhecimento completo, aprenderem enquanto equipe como as coisas se encaixam.

Em ambos os casos, para ajudar a orientar os leitores, cada artigo deste tutorial indica a função do usuário. Essas funções incluem:

* Desenvolvimento de nuvem (incluindo um desenvolvedor de nuvem trabalhando em uma capacidade de DevOps)
* Análise de dados

## <a name="use-case-predictive-maintenance"></a>Caso de uso: Manutenção preditiva

Baseamos esse cenário em um caso de uso apresentado na Conferência sobre Prognóstico e Gerenciamento de Integridade (PHM08) em 2008. A meta é prever a RUL (vida útil restante) de um conjunto de motores de avião turbofan. Esses dados foram gerados usando o C-MAPSS, a versão comercial do software MAPSS (Simulação do Sistema de Propulsão a Ar Modular). Este software oferece um ambiente de simulação de motor turbofan flexível para simular convenientemente os parâmetros de integridade, de controle e do motor.

Os dados usados neste tutorial foram extraídos do [Conjunto de dados de simulação de degradação de motor turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Do arquivo leiame:

***Cenário experimental***

*Os conjuntos de dados são compostos por várias séries temporais multivariadas. Cada conjunto de dados é dividido em subconjuntos de treinamento e de teste. Cada série temporal provém de um motor diferente – por ex., os dados podem ser processados para serem provenientes de uma frota de motores do mesmo tipo. Cada motor começa com diferentes graus de desgaste inicial e variação de fabricação desconhecidos para o usuário. Esse desgaste e a variação são considerados normais, por ex., não é considerada uma condição de falha. Há três configurações operacionais que têm um efeito significativo no desempenho do motor. Essas configurações também estão incluídas nos dados. Os dados são contaminados com o ruído do sensor.*

*O motor está operando normalmente no início de cada série temporal e desenvolve uma falha em algum momento durante a série. No conjunto de treinamento, a falha aumenta em magnitude até a falha do sistema. No conjunto de teste, a série temporal termina algum tempo antes da falha do sistema. O objetivo da competição é prever o número de ciclos operacionais restantes antes da falha no conjunto de teste, por ex., o número de ciclos operacionais após o último ciclo que o motor continuará operando. Também foi fornecido um vetor de valores RUL (Vida Útil Restante) verdadeiros para os dados de teste.*

Como os dados foram publicados para uma competição, várias abordagens para derivar os modelos de machine learning foram publicadas independentemente. Descobrimos que estudar os exemplos é útil para entender o processo e o raciocínio envolvido na criação de um modelo de machine learning específico. Confira, por exemplo:

[Modelo de previsão de falha de motor de aeronave](https://github.com/jancervenka/turbofan_failure) do usuário do GitHub jancervenka.

[Degradação de motor turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) do usuário do GitHub hankroark.

## <a name="process"></a>Processo

A figura a seguir ilustra as etapas estimadas que seguimos este tutorial:

![Diagrama de arquitetura para as etapas do processo](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Coletar dados de treinamento**: o processo é iniciado coletando os dados de treinamento. Em alguns casos, os dados já foram coletados e estão disponíveis em um banco de dados ou na forma de arquivos de dados. Em outros casos, principalmente para cenários de IoT, os dados precisam ser coletados de sensores e de dispositivos IoT e armazenados na nuvem.

   Vamos supor que você não tem uma coleção de motores turbofan; portanto, os arquivos de projeto incluem um simulador de dispositivo simples que envia os dados de dispositivo da NASA para a nuvem.

1. **Preparar os dados**. Na maioria dos casos, os dados brutos conforme coletados de dispositivos e de sensores exigirão preparação para aprendizado de máquina. Essa etapa pode envolver a limpeza, a reformatação ou o pré-processamento de dados para injetar mais informações que o aprendizado de máquina pode aceitar.

   Para nossos dados de computador de motor de avião, a preparação de dados envolve calcular tempos de tempo de falha explícitos para cada ponto de dados no exemplo com base nas observações reais sobre os dados. Essas informações permitem que o algoritmo de aprendizado de máquina encontre correlações entre padrões de dados de sensor reais e o tempo de vida restante esperado do motor. Esta etapa é altamente específica do domínio.

1. **Criar um modelo de machine learning**. Com base nos dados preparados, agora podemos experimentar com diferentes algoritmos de aprendizado de máquina e parametrizações para treinar modelos e comparar os resultados uns com os outros.

   Nesse caso, para teste, comparamos o resultado previsto calculado pelo modelo com o resultado real observado em um conjunto de motores. No Azure Machine Learning, podemos gerenciar as diferentes iterações de modelos criados em um Registro e modelo.

1. **Implantar o modelo**. Quando tivermos um modelo que atenda aos nossos critérios de sucesso, poderemos passar para a implantação. Isso envolve encapsular o modelo em um aplicativo de serviço Web que pode ser alimentado com os dados usando chamadas REST e retornar resultados de análise. O aplicativo de serviço Web é empacotado em um contêiner do docker, que, por sua vez, pode ser implantado na nuvem ou como um módulo do IoT Edge. Neste exemplo, nosso foco é a implantação no IoT Edge.

1. **Manter e refinar o modelo**. Nosso trabalho não termina depois que o modelo é implantado. Em muitos casos, desejamos continuar coletando dados e fazer upload periodicamente desses dados na nuvem. Podemos, então, usar esses dados para treinar novamente nosso modelo e refiná-lo, que pode ser, então, implantado novamente no IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, é necessário ter acesso a uma assinatura do Azure na qual você tem direitos para criar recursos. Vários dos serviços usados neste tutorial incorrerão em encargos do Azure. Se você ainda não tiver uma assinatura do Azure, talvez possa começar com uma [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Também é necessário um computador com o PowerShell instalado, em que você pode executar scripts para configurar uma Máquina Virtual do Azure como seu computador de desenvolvimento.

Neste documento, usamos o seguinte conjunto de ferramentas:

* Um Hub IoT do Azure para captura de dados

* Azure Notebooks como nosso front-end principal para preparação de dados e experimentação de aprendizado de máquina. A execução do código python em um notebook em um subconjunto dos dados de exemplo é uma ótima maneira de obter um retorno iterativo e interativo rápido durante a preparação de dados. Os notebooks Jupyter podem ser usados também para preparar scripts para serem executados em escala em um back-end de computação.

* Azure Machine Learning como um back-end para aprendizado de máquina em escala e para a geração de imagens de aprendizado de máquina. Conduzimos o back-end do Azure Machine Learning usando scripts preparados e testados em notebooks Jupyter.

* Azure IoT Edge para aplicativos fora da nuvem de uma imagem de aprendizado de máquina

É óbvio que há outras opções disponíveis. Em determinados cenários, por exemplo, o IoT Central pode ser usado como uma alternativa sem código para capturar dados de treinamento iniciais de dispositivos IoT.

## <a name="next-steps"></a>Próximas etapas

Este tutorial é dividido nas seguintes seções:

1. Configurar seu computador de desenvolvimento e os serviços do Azure.
2. Gerar os dados de treinamento para o módulo de aprendizado de máquina.
3. Treinar e implantar o módulo de aprendizado de máquina.
4. Configurar um dispositivo IoT Edge para atuar como um gateway transparente.
5. Criar e implantar módulos do IoT Edge.
6. Enviar dados para seu dispositivo IoT Edge.

Passe para o próximo artigo para configurar um computador de desenvolvimento e provisionar recursos do Azure.

> [!div class="nextstepaction"]
> [Configurar um ambiente para aprendizado de máquina no IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
