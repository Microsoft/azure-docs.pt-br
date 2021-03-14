---
title: 'ML Studio (clássico): Exibir & experimentos de nova execução-Azure'
description: Gerenciar execuções de experimento no Azure Machine Learning Studio (clássico). Você pode examinar as execuções anteriores dos seus testes a qualquer momento para desafiar, revisitar e, por fim, confirmar ou refinar suposições anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 419a696da1244afab7aa03cd8c4521ea819a5298
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515947"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Gerenciar execuções de experimento no Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Desenvolver um modelo de análise de previsão é um processo iterativo - como modificar as várias funções e parâmetros de seu teste, seus resultados convergem até você ficar satisfeito com um modelo treinado e eficiente. A chave para esse processo está em acompanhar várias iterações dos parâmetros e configurações do seu teste.

Você pode examinar as execuções anteriores dos seus testes a qualquer momento para desafiar, revisitar e, por fim, confirmar ou refinar suposições anteriores. Quando você executa um experimento, Machine Learning Studio (clássico) mantém um histórico da execução, incluindo as conexões de conjunto de módulos (DataSet), módulo e porta e parâmetros. Esse histórico também captura resultados, informações de runtime, como tempos de início e parada, mensagens de log e status de execução. Você pode observar qualquer uma dessas execuções a qualquer momento para examinar o cronograma de seu teste e os resultados intermediários. Você pode até usar uma execução anterior de seu teste para fazer a inicialização em uma nova fase de consulta e descoberta em seu caminho para criar soluções simples, complexas ou até mesmo de modelagem conjunta.

> [!NOTE]
> Quando você exibe uma execução anterior de um teste, essa versão do teste está bloqueada e não pode ser editada. No entanto, você pode salvar uma cópia dele clicando em **SALVAR COMO** e fornecendo um novo nome para a cópia. Machine Learning Studio (clássico) abre a nova cópia, que você pode editar e executar. Esta cópia do seu teste está disponível na lista **TESTES** junto com todos os seus testes.
> 
> 

## <a name="view-the-prior-run"></a>Exibir a execução anterior
Quando tiver um teste aberto que você tenha executado pelo menos uma vez, você pode exibir a execução anterior do teste clicando em **Execução anterior** no painel Propriedades.

Por exemplo, suponha que você crie um teste e execute versões dele às 11h23 11h42 e 11h55. Se você abrir a última execução do teste (11h55) e clicar em **Execução anterior**, a versão que você executou às 11h42 será aberta.

## <a name="view-the-run-history"></a>Exibir o histórico de execuções
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

A lista de experimentos na guia **experimentos** em Machine Learning Studio (clássico) sempre exibe a versão mais recente de um experimento. Se abrir uma execução anterior do teste (usando **Execução anterior** ou **EXIBIR O HISTÓRICO DE EXECUÇÃO**), você pode retornar para a versão de rascunho, clicando em **EXIBIR O HISTÓRICO DE EXECUÇÃO** e selecionando a iteração que tem um **ESTADO****Editável**.

## <a name="run-a-previous-experiment"></a>Executar um experimento anterior
Quando clicar em **Execução anterior** ou em **EXIBIR O HISTÓRICO DE EXECUÇÃO** e abrir uma execução anterior, você poderá exibir um teste concluído no modo somente leitura.

Se quiser iniciar uma iteração de seu teste começando com o modo como você o configurou para uma execução anterior, você pode fazer isso abrindo a execução e clicando em **SALVAR COMO**. Isso cria um novo teste, com um novo título, um histórico de execução vazio e todos os componentes e os valores de parâmetro de versões anteriores. Esse novo experimento é listado na guia **experimentos** do Machine Learning Studio (clássico) Home Page e você pode modificá-lo e executá-lo, iniciando um novo histórico de execução para essa iteração de seu experimento. 

Por exemplo, suponha que você tenha o teste executando o histórico mostrado na seção anterior. Você deseja observar o que acontece quando define o parâmetro **Taxa de aprendizagem** para 0,4 e testa valores diferentes para o parâmetro **Número de épocas de treinamento**.

1. Clique em **EXIBIR O HISTÓRICO DE EXECUÇÃO** e abra a iteração do teste que você executou às 16h28min36s (no qual você definiu o valor do parâmetro para 0,4).
2. Clique em **salvar como**.
3. Digite um novo título e clique na marca de seleção **OK** . É criada uma nova cópia do teste.
4. Modifique o parâmetro **Número de épocas de treinamento** .
5. Clique em **executar**.

Agora você pode continuar a modificar e executar esta versão do seu teste, criando um novo histórico de execução para registrar o seu trabalho.

<!-- Module References -->
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression