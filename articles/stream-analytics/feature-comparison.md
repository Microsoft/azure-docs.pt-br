---
title: Comparação de recursos do Azure Stream Analytics
description: Este artigo compara os recursos com suporte para trabalhos Azure Stream Analytics nuvem e IoT Edge no portal do Azure, no Visual Studio e no Visual Studio Code.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 037bd8bc823cd8c77241d0ca25174e29d25149b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020529"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de recursos do Azure Stream Analytics

Com o Azure Stream Analytics, você pode criar soluções de streaming na nuvem e no IoT Edge usando [portal do Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)e [Visual Studio Code](quick-create-visual-studio-code.md). As tabelas neste artigo mostram quais recursos têm suporte em cada plataforma para ambos os tipos de trabalho.

> [!NOTE]
> As ferramentas do Visual Studio e do Visual Studio Code não são compatíveis com trabalhos nas regiões Leste da China, Norte da China, Alemanha Central e Nordeste da Alemanha.

## <a name="cloud-job-features"></a>Recursos de trabalho de nuvem


|Recurso  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plataforma cruzada     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Criação de script     |Sim         |Sim         |Sim         |
|Script IntelliSense     |Realce de sintaxe         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |
|Definir todos os tipos de entradas, saídas e configurações de trabalho     |Sim         |Sim         |Sim         |
|Controle do código-fonte     |Não         |Sim         |Sim         |
|Suporte a CI/CD     |Parcial         |Sim         |Sim         |
|Compartilhar entradas e saídas em várias consultas     |Não         |Sim         |Sim         |
|Teste de consulta com um arquivo de exemplo     |Sim         |Sim        |Sim         |
|Testes locais de dados dinâmicos     |Não         |Sim       |Sim      |
|Listar trabalhos e exibir entidades de trabalho     |Sim         |Sim        |Sim         |
|Exportar um trabalho para um projeto local     |Não         |Sim         |Sim         |
|Enviar, iniciar e parar trabalhos     |Sim         |Sim         |Sim         |
|Exibir as métricas e o diagrama do trabalho     |Sim         |Sim         |Abrir no portal         |
|Exibir erros de tempo de execução de trabalho     |Sim         |Sim         |Não         |
|Logs de recursos     |Sim         |Não         |Não         |
|Propriedades da mensagem personalizada     |Sim         |Sim         |Não       |
|Função de código personalizado C# e desserializador|Modo somente leitura|Sim|Não|
|UDF e UDA do JavaScript     |Sim         |Sim         |Somente Windows         |
|Serviço Machine Learning     |Sim        |Sim         |Não         |
|Azure Machine Learning Studio (clássico)|Sim, mas a consulta não pode ser testada        |Sim |Não         |
|Nível de Compatibilidade     |1.0</br>1,1</br>1,2 (padrão)         |1.0</br>1,1</br>1,2 (padrão)           |1.0</br>1,1</br>1,2 (padrão)           |
|Funções internas de detecção de anomalias baseadas em ML     |Sim         |Sim         |Sim         |
|Funções geoespaciais internas     |Sim         |Sim         |Sim         |



## <a name="iot-edge-job-features"></a>IoT Edge recursos de trabalho

|Recurso  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Criação de trabalho     |Sim         |Sim         |Não         |
|Controle do código-fonte     |Não         |Sim         |Não         |
|Exportar um trabalho para um projeto local     |Não         |Sim         |Não         |
|Teste de consulta com um arquivo de exemplo     |Sim         |Sim         |Não         |
|Compartilhar entradas e saídas em várias consultas     |Não         |Sim         |Não         |
|UDF do C#     |Não         |Sim         |Não         |
|Enviar trabalhos     |Sim         |Sim         |Não         |
|Listar trabalhos e exibir entidades de trabalho     |Sim         |Sim         |Não         |
|Exibir as métricas e o diagrama do trabalho     |Sim         |Parcial         |Não         |
|Exibir erros de tempo de execução de trabalho     |Sim         |Parcial         |Não         |
|Suporte a CI/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Próximas etapas

* [O Stream Analytics do Azure no IoT Edge](stream-analytics-edge.md)
* [Tutorial: gravar uma função de C# definida pelo usuário para Azure Stream Analytics trabalho de IoT Edge (versão prévia)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver Stream Analytics trabalhos de IoT Edge usando as ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explorar Azure Stream Analytics com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)


