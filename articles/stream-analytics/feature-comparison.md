---
title: Comparação de recursos do Azure Stream Analytics
description: Este artigo compara os recursos com suporte para a nuvem do Azure Stream Analytics e trabalhos de borda de IoT no portal do Azure, Visual Studio e o Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509775"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de recursos do Azure Stream Analytics

Com o Azure Stream Analytics, você pode criar soluções de transmissão na nuvem e no IoT Edge usando [portal do Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md), e [Visual Studio Code](quick-create-vs-code.md). As tabelas neste artigo mostram quais recursos têm suporte por cada plataforma para ambos os tipos de trabalho.

## <a name="cloud-job-features"></a>Recursos de trabalho de nuvem


|Recurso  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plataforma cruzada     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Script de criação     |Sim         |sim         |Sim         |
|Intellisense do script     |Realce de sintaxe         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |
|Definir entradas, saídas e as configurações de trabalho     |Sim         |sim         |Sim         |
|Particionamento de saída de blob     |Sim         |sim         |Sim         |
|Power BI como saída     |Sim         |sim         |Não         |
|Dados de referência de banco de dados SQL     |Sim         |sim         |Sim         |
|Propriedades de mensagem personalizada     |Sim         |Não         |Não         |
|Compartilhar entradas e saídas entre várias consultas     |Não         |sim         |Sim         |
|UDF do JavaScript e UDA     |Sim         |Sim         |Somente Windows         |
|Textos explicativos de aprendizado de máquina     |Sim, mas a consulta não pode ser testado        |Sim, mas não pode ser testado localmente         |Não         |
|Nível de compatibilidade     |1.0</br>1,1</br>1.2         |1.0</br>1,1</br>1.2          |1.0</br>1,1</br>1.2          |
|Funções internas de detecção de anomalias com base em ML     |Sim         |sim         |Sim         |
|Funções geoespaciais internas     |Sim         |sim         |Sim         |
|Testes de consulta com um arquivo de exemplo     |Sim         |sim         |Sim         |
|Live testes locais de dados     |Não         |Sim         |Não         |
|Listar trabalhos e exibir as entidades de trabalho     |Sim         |sim         |Sim         |
|Exportar um trabalho para um projeto local     |Não         |sim         |Sim         |
|Enviar, iniciar e parar trabalhos     |Sim         |sim         |Sim         |
|Controle do código-fonte     |Não         |sim         |Sim         |
|Suporte de CI/CD     |Parcial         |Sim         |Sim         |
|Exibir métricas do trabalho e diagrama     |Sim         |Sim         |Abrir no portal         |
|Exibir erros de tempo de execução do trabalho     |Sim         |sim         |Não         |
|Logs de diagnóstico     |Sim         |Não         |Não         |


## <a name="iot-edge-job-features"></a>Recursos de trabalho de IoT Edge

|Recurso  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Criação de trabalho     |Sim         |sim         |Não         |
|Controle do código-fonte     |Não         |Sim         |Não         |
|Exportar um trabalho para um projeto local     |Não         |Sim         |Não         |
|Testes de consulta com um arquivo de exemplo     |Sim         |sim         |Não         |
|Compartilhar entradas e saídas entre várias consultas     |Não         |Sim         |Não         |
|C#UDF     |Não         |Sim         |Não         |
|Enviar, iniciar e parar trabalhos     |Sim         |sim         |Não         |
|Listar trabalhos e exibir as entidades de trabalho     |Sim         |sim         |Não         |
|Exibir métricas do trabalho e diagrama     |Sim         |Parcial         |Não         |
|Exibir erros de tempo de execução do trabalho     |Sim         |Parcial         |Não         |
|Suporte de CI/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Próximas etapas

* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [Tutorial: Escrever um C# função definida pelo usuário para o trabalho de borda de IoT do Azure Stream Analytics (visualização)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver trabalhos de borda de IoT do Stream Analytics usando as ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explorar o Azure Stream Analytics com o código do Visual Studio (versão prévia)](vscode-explore-jobs.md)


