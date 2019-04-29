---
title: Gerenciar iterações de teste
titleSuffix: Azure Machine Learning Studio
description: Como gerenciar iterações de teste no Azure Machine Learning Studio. Você pode examinar as execuções anteriores dos seus testes a qualquer momento para desafiar, revisitar e, por fim, confirmar ou refinar suposições anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 34a72f2e7b6be90654c0f053d5b8978b0283d56c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860230"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Gerenciar iterações de teste no Machine Learning Studio do Microsoft Azure
Desenvolver um modelo de análise de previsão é um processo iterativo - como modificar as várias funções e parâmetros de seu teste, seus resultados convergem até você ficar satisfeito com um modelo treinado e eficiente. A chave para esse processo está em acompanhar várias iterações dos parâmetros e configurações do seu teste.



Você pode examinar as execuções anteriores dos seus testes a qualquer momento para desafiar, revisitar e, por fim, confirmar ou refinar suposições anteriores. Quando você executa um teste, o Machine Learning Studio mantém um histórico de execução, incluindo o conjunto de dados, o módulo e as conexões de porta e parâmetros. Esse histórico também captura resultados, informações de tempo de execução, como tempos de início e parada, mensagens de log e status de execução. Você pode observar qualquer uma dessas execuções a qualquer momento para examinar o cronograma de seu teste e os resultados intermediários. Você pode até usar uma execução anterior de seu teste para fazer a inicialização em uma nova fase de consulta e descoberta em seu caminho para criar soluções simples, complexas ou até mesmo de modelagem conjunta.

> [!NOTE]
> Quando você exibe uma execução anterior de um teste, essa versão do teste está bloqueada e não pode ser editada. No entanto, você pode salvar uma cópia dele clicando em **SALVAR COMO** e fornecendo um novo nome para a cópia. O Machine Learning Studio abre a nova cópia, que você pode editar e executar. Esta cópia do seu teste está disponível na lista **TESTES** junto com todos os seus testes.
> 
> 

## <a name="viewing-the-prior-run"></a>Exibindo a execução anterior
Quando tiver um teste aberto que você tenha executado pelo menos uma vez, você pode exibir a execução anterior do teste clicando em **Execução anterior** no painel Propriedades.

Por exemplo, suponha que você crie um teste e execute versões dele às 11h23 11h42 e 11h55. Se você abrir a última execução do teste (11h55) e clicar em **Execução anterior**, a versão que você executou às 11h42 será aberta.

## <a name="viewing-the-run-history"></a>Exibindo o Histórico de execução
Você pode exibir todas as execuções anteriores de um teste clicando em **Exibir Histórico de execução** em um teste aberto.

Por exemplo, suponha que você crie um teste com o módulo [Regressão Linear][linear-regression] e queira observar o efeito da alteração no valor da **Taxa de aprendizado** em seus resultados do teste. Você executa o teste várias vezes com valores diferentes para esse parâmetro, conforme segue:

| Valor da Taxa de aprendizado | Hora de início da execução |
| --- | --- |
| 0,1 |11/09/2014 16h18min58s |
| 0,2 |11/09/2014 16h24min33s |
| 0,4 |11/09/2014 16h28min36s |
| 0,5 |11/09/2014 16h33min31s |

Se clicar em **EXIBIR O HISTÓRICO DE EXECUÇÃO**, você verá uma lista de todas essas execuções:

![Exemplo de histórico de execução](./media/manage-experiment-iterations/viewrunhistory.jpg)

Clique em qualquer uma dessas execuções para exibir um instantâneo do teste no momento em que você o executou. A configuração, os valores de parâmetro, comentários e resultados são preservados para que você tenha um registro completo da execução do seu teste.

> [!TIP]
> Para documentar suas iterações do teste, você pode modificar o título cada vez que executá-lo, pode atualizar o **Resumo** do teste no painel de propriedades e pode adicionar ou atualizar os comentários em módulos individuais para registrar as alterações. Os comentários do título, do resumo e do módulo são salvos com cada execução do teste.
> 
> 

A lista de testes na guia **TESTES** no Machine Learning Studio sempre exibe a versão mais recente de um teste. Se abrir uma execução anterior do teste (usando **Execução anterior** ou **EXIBIR O HISTÓRICO DE EXECUÇÃO**), você pode retornar para a versão de rascunho, clicando em **EXIBIR O HISTÓRICO DE EXECUÇÃO** e selecionando a iteração que tem um **ESTADO** **Editável**.

## <a name="iterating-on-a-previous-run"></a>Iterando sobre uma Execução anterior
Quando clicar em **Execução anterior** ou em **EXIBIR O HISTÓRICO DE EXECUÇÃO** e abrir uma execução anterior, você poderá exibir um teste concluído no modo somente leitura.

Se quiser iniciar uma iteração de seu teste começando com o modo como você o configurou para uma execução anterior, você pode fazer isso abrindo a execução e clicando em **SALVAR COMO**. Isso cria um novo teste, com um novo título, um histórico de execução vazio e todos os componentes e os valores de parâmetro de versões anteriores. Esse novo teste é listado na guia **TESTES** na home page do Machine Learning Studio e você pode modificá-lo e executá-lo, iniciando um novo histórico de execução para essa iteração do seu teste. 

Por exemplo, suponha que você tenha o teste executando o histórico mostrado na seção anterior. Você deseja observar o que acontece quando define o parâmetro **Taxa de aprendizagem** para 0,4 e testa valores diferentes para o parâmetro **Número de épocas de treinamento**.

1. Clique em **EXIBIR O HISTÓRICO DE EXECUÇÃO** e abra a iteração do teste que você executou às 16h28min36s (no qual você definiu o valor do parâmetro para 0,4).
2. Clique em **SALVAR COMO**.
3. Digite um novo título e clique na marca de seleção **OK** . É criada uma nova cópia do teste.
4. Modifique o parâmetro **Número de épocas de treinamento** .
5. Clique em **EXECUTAR**.

Agora você pode continuar a modificar e executar esta versão do seu teste, criando um novo histórico de execução para registrar o seu trabalho.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
