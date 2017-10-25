---
title: "Analisando a Variação do Cliente usando o Machine Learning | Microsoft Docs"
description: "Estudo de caso do desenvolvimento de um modelo integrado para analisar e pontuar a insatisfação do cliente"
services: machine-learning
documentationcenter: 
author: jeannt
manager: jhubbard
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeannt
ms.openlocfilehash: b3dca9e75df2d057d7ee1b314faac490e5f10a08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analisando a Variação do Cliente usando o Machine Learning do Microsoft Azure
## <a name="overview"></a>Visão geral
Este artigo apresenta uma implementação de referência de um projeto de análise de variação de cliente que é criado usando-se o Azure Machine Learning. Discutimos aqui modelos genéricos associados para resolver holisticamente o problema de variação do cliente industrial. Medimos também a precisão dos modelos criados usando o Machine Learning e avaliamos as direções para maior desenvolvimento.  

### <a name="acknowledgements"></a>Confirmações
Esse experimento foi desenvolvido e testado por Serge Berger, principal cientista de dados na Microsoft, e Roger Barga, ex-gerente de produto para o Microsoft Azure Machine Learning. A equipe de documentação do Azure confirma reconhecidamente a experiência que eles têm e os agradece por compartilhar este white paper.

> [!NOTE]
> Os dados usados neste experimento não estão publicamente disponíveis. Para ver um exemplo de como construir um modelo de aprendizagem por máquina para análise de variação, confira o [modelo de variação de varejo](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) em [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>O problema da variação do cliente
Empresas no mercado consumidor e em todos os setores empresariais precisam lidar com a variação. Algumas vezes a variação é excessiva e influencia decisões de política. A solução tradicional é prever índices de variação de alta propensão e tratar de suas necessidades por meio de um serviço de concierge, campanhas de marketing ou fazendo uso de isenções especiais. Essas abordagens podem variar de setor para setor e até mesmo de um cluster de consumidor específico para outro dentro de um setor (por exemplo, telecomunicações).

O fator comum é que as empresas precisam minimizar esses esforços especiais para a retenção de clientes. Assim, uma metodologia natural seria classificar cada cliente com a probabilidade de variação e o endereço dos N maiores. Os maiores clientes podem ser os mais lucrativos, por exemplo, em cenários mais sofisticados, uma função de lucro é aplicada durante a seleção de candidatos para isenção especial. Todavia, essas considerações são somente uma parte da estratégia holística para gerenciar a variação. As empresas também precisam levar em consideração o risco (e a tolerância a risco associada), o nível e o custo de intervenção, além da segmentação plausível de clientes.  

## <a name="industry-outlook-and-approaches"></a>Abordagens e perspectiva industriais
O gerenciamento sofisticado da variação é um sinal de uma indústria madura. O exemplo clássico é o da indústria de telecomunicações, na qual sabe-se que os assinantes frequentemente mudam de um provedor para outro. Essa variação voluntária é uma preocupação primária. Além disso, os provedores acumularam conhecimento significativo sobre *motivadores de variação*, que são os fatores que levam os clientes a mudar.

Por exemplo, a escolha de telefone ou dispositivo é um motivador bem conhecido no setor de telefonia móvel. Como resultado, uma política popular é subsidiar o preço de um aparelho para novos assinantes e cobrar o preço total para os clientes existentes para uma atualização. Historicamente, essa política fez com que clientes mudassem de um provedor para outro para obter um novo desconto, o que, por sua vez, fez com que os provedores refinassem suas estratégias.

A alta volatilidade nas ofertas de aparelhos é um fator que invalida muito rapidamente modelos de variação baseados em modelos de aparelhos atuais. Além disso, celulares não são apenas dispositivos de telecomunicações; eles também são acessórios de moda (considere iPhone) e essas previsões sociais estão fora do escopo de conjuntos de dados de telecomunicações regulares.

O resultado final para a criação de um modelo é que você não pode criar uma política sólida apenas eliminando as causas da variação. Na verdade, uma estratégia de modelagem contínua, incluindo modelos clássicos que quantificam variáveis categóricas (como árvores de decisão), é **obrigatória**.

Usando conjuntos de big data com seus clientes, as organizações estão realizando análises de big data, em particular a detecção de variação baseada em big data, como uma abordagem eficaz para o problema. Você pode saber mais sobre a abordagem de big data para o problema da variação na seção Recomendações de ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia para modelagem da variação do cliente
Um processo comum de solução de problemas para resolver variação do cliente é descrito nas Figuras 1 a 3:  

1. Um modelo de risco permite que você considere como as ações afetam as probabilidades e o risco.
2. Um modelo de intervenção permite que você considere como o nível de intervenção poderia afetar a probabilidade de variação e a quantidade de CLV (valor de vida útil do cliente).
3. Essa análise se presta a uma análise qualitativa que será escalada para uma campanha de marketing proativa destinada a segmentos de clientes para oferecer a oferta ideal.  

![][1]

Essa abordagem visionária é a melhor maneira de tratar a variação, mas ela traz complexidade: precisamos desenvolver um arquétipo de vários modelos e rastrear dependências entre os modelos. A interação entre os modelos pode ser encapsulada conforme mostrado no diagrama a seguir:  

![][2]

*Figura 4: Arquétipo multimodelo unificado*  

As interações entre os diferentes modelos são a chave para fornecermos uma abordagem holística para a retenção de clientes. Cada modelo obrigatoriamente degrada-se com o tempo; portanto, a arquitetura é um loop implícito (semelhante ao arquétipo definido pelo padrão CRISP-DM de mineração de dados, [***3***]).  

O ciclo total de segmentação/decomposição de risco-decisão-marketing ainda é uma estrutura generalizada, que é aplicável a muitos problemas empresariais. A análise de variação é somente um exemplo altamente representativo desse grupo de problemas, porque demonstra todas as características de um problema empresarial complexo que não permite uma solução preditiva simplificada. Os aspectos sociais da abordagem moderna para a variação não são especialmente destacados na abordagem, mas os aspectos sociais são encapsulados no arquétipo de modelagem, do mesmo modo que seriam em qualquer modelo.  

Uma adição interessante nesse caso é a análise de big data. As empresas de telecomunicações e varejo coletam dados sobre seus clientes de modo exaustivo, de modo que podemos prever facilmente que a necessidade de uma conectividade multimodelo se tornará uma tendência comum, dadas as tendências emergentes como a “Internet das coisas” e os dispositivos ubíquos, que permitem que a empresa aplique soluções inteligentes em múltiplos níveis.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementando o arquétipo modelagem no Machine Learning Studio
Dado o problema descrito acima, qual é a melhor maneira de implementar uma abordagem integrada de modelagem e pontuação? Nesta seção, demonstraremos como conseguimos isso usando o Estúdio do Azure Machine Learning.  

A abordagem multimodelo é essencial ao projetar um arquétipo global para variação. Até mesmo a parte de pontuação (preditiva) da abordagem deve ser multimodelo.  

O diagrama a seguir mostra o protótipo que criamos, que emprega quatro algoritmos de pontuação no Machine Learning Studio para prever a variação. O motivo para usar uma abordagem multimodelo não é somente criar um classificador de conjunto para aumentar a precisão, mas também proteger contra over-fitting e aprimorar a seleção prescritiva de recursos.  

![][3]

*Figura 5: Protótipo de uma abordagem de modelagem de variação*  

As seções a seguir fornecem mais detalhes sobre o protótipo de modelo de pontuação que implementamos usando o Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Seleção e preparação de dados
Os dados usados para criar os modelos e atribuir pontuação aos clientes foram obtidos por meio de uma solução vertical de CRM, com os dados ofuscados para proteger a privacidade dos clientes. Os dados contém informações sobre 8.000 assinaturas nos EUA e combinam três fontes: dados de provisionamento (metadados de assinatura), dados de atividade (uso do sistema) e dados de atendimento ao cliente. Os dados não incluem nenhuma informação relacionada à empresa sobre os clientes; por exemplo, não inclui metadados de fidelidade ou pontuação de crédito.  

Para simplificar, os processos de ETL e limpeza dos dados estão fora do escopo porque presumimos que a preparação dos dados já foi realizada em outra instância.   

A seleção de recursos para modelagem baseia-se na importância preliminar da pontuação do conjunto de prognosticadores, incluídos no processo que usa o módulo de floresta aleatório. Para a implementação no Machine Learning Studio, calculamos a média, mediana e intervalos de recursos relevantes. Por exemplo, adicionamos agregações aos dados qualitativos, tais como os valores mínimo e máximo para a atividade do usuário.    

Também capturamos informações temporais para os últimos seis meses. Analisamos dados por um ano e estabelecemos que mesmo que houvesse tendências estatisticamente significativas, o efeito sobre a variação é bastante reduzido depois de seis meses.  

O ponto mais importante é que o processo inteiro, incluindo ETL, a seleção de recursos e a modelagem foi implementado Machine Learning Studio, usando fontes de dados no Microsoft Azure.   

Os diagramas a seguir ilustram os dados que foram usados.  

![][4]

*Figura 6: Extrato de fonte de dados (ofuscado)*  

![][5]

*Figura 7: Recursos extraídos da fonte de dados*
 

> Observe que esses dados são particulares e, portanto, o modelo e os dados não podem ser compartilhados.
> No entanto, para um modelo semelhante usando dados publicamente disponíveis, confira este experimento de exemplo na [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/): [Variação do cliente de telecomunicações](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como você pode implementar um modelo de análise de variação usando o Cortana Intelligence Suite, também recomendamos [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) do gerente de programa sênior Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos usados no protótipo
Usamos os quatro algoritmos de aprendizado de máquina a seguir para criar o protótipo (sem personalização):  

1. Regressão logística (LR)
2. Árvore de decisão aumentada (BT)
3. Perceptron médio (AP)
4. Máquina de vetor de suporte (SVM)  

O diagrama a seguir ilustra uma parte da superfície de design do teste, que indica a sequência na qual os modelos foram criados:  

![][6]  

*Figura 8: Criando modelos no Machine Learning Studio*  

### <a name="scoring-methods"></a>Métodos de pontuação
Pontuamos quatro modelos usando um conjunto de dados de treinamento rotulado.  

Também enviamos o conjunto de dados para um modelo comparável criado usando a edição de área de trabalho do SAS Enterprise Miner 12. Medimos a precisão do modelo SAS e todos os quatro modelos do Machine Learning Studio.  

## <a name="results"></a>Resultados
Nesta seção, apresentamos nossas descobertas sobre a precisão dos modelos, com base no conjunto de dados de pontuação.  

### <a name="accuracy-and-precision-of-scoring"></a>Precisão e exatidão da pontuação
Em geral, a implementação no Azure Machine Learning é inferior ao SAS em precisão em aproximadamente 10% a 15% (Área sob a Curva ou AUC).  

No entanto, a métrica mais importante na variação é a taxa de classificação incorreta, ou seja, dos maiores N variantes previstos pelo classificador, quais deles **não** variaram de fato e ainda assim receberam tratamento especial? O diagrama a seguir compara essa taxa de classificação incorreta em todos os modelos:  

![][7]

*Figura 9: Área do protótipo de Passau sob a curva*

### <a name="using-auc-to-compare-results"></a>Usando AUC para comparar resultados
A área sob a curva (AUC) é uma métrica que representa uma medição global da *separabilidade* entre as distribuições de pontos para populações positivas e negativas. É semelhante ao gráfico ROC (Característica de Operador do Receptor) tradicional, mas uma diferença importante é que a métrica AUC não exige que você escolha um valor de limite. Em vez disso, ela resume os resultados relativos a **todas** as escolhas possíveis. Em contraste, o gráfico ROC tradicional exibe a taxa de resultados positivos no eixo vertical e a taxa de falsos positivos no eixo horizontal, sendo que o limite de classificação varia.   

AUC geralmente é usada como uma medida de valor para algoritmos diferentes (ou sistemas diferentes), porque permite que os modelos sejam comparados por meio do valor de AUC que apresentam. Essa é uma abordagem popular em setores como meteorologia e biociência. Assim, a AUC representa uma ferramenta popular para avaliar o desempenho de um classificador.  

### <a name="comparing-misclassification-rates"></a>Comparando as taxas de classificação incorreta
Comparamos as taxas de erro na classificação no conjunto de dados em questão usando os dados de CRM de aproximadamente 8.000 assinaturas.  

* A taxa de erro na classificação da SAS foi de 10 a 15%.
* A taxa de classificação incorreta do Estúdio do Machine Learning foi 15% a 20% para os maiores 200 a 300 variadores.  

Na indústria de telecomunicações, é importante tratar somente daqueles clientes que apresentam o maior risco de variação, oferecendo a eles um serviço de concierge ou outro tratamento especial. Nesse ponto, a implementação do Machine Learning Studio obtém resultados no modelo de SAS.  

Pelo mesmo token, a exatidão é mais importante que a precisão, porque estamos interessados, principalmente, em classificar corretamente os índices de variação em potencial.  

O diagrama a seguir, da Wikipédia, representa a relação em um gráfico vívido, fácil de entender:  

![][8]

*Figura 10: Troca entre exatidão e precisão*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e exatidão para modelo de árvore de decisão aprimorado
O gráfico a seguir exibe os resultados brutos de pontuação usando o protótipo de Machine Learning para o modelo de árvore de decisão aprimorado, que vem a ser o mais preciso dentre os quatro modelos:  

![][9]

*Figura 11: Características do modelo de árvore de decisão aprimorado*

## <a name="performance-comparison"></a>Comparação de desempenho
Comparamos a velocidade na qual os dados foram pontuados usando os modelos do Machine Learning Studio e um modelo comparável criado usando a edição de área de trabalho do SAS Enterprise Miner 12.1.  

A tabela a seguir resume o desempenho dos algoritmos:  

*Tabela 1. Desempenho geral (precisão) dos algoritmos*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo médio |O melhor modelo |Subdesempenhando |Modelo médio |

Os modelos hospedados no Estúdio do Machine Learning superaram o desempenho de SAS em 15% a 25% para velocidade de execução, mas a precisão permaneceu amplamente no mesmo nível.  

## <a name="discussion-and-recommendations"></a>Discussão e recomendações
Na indústria de telecomunicações, diversas práticas surgiram para analisar a variação, incluindo:  

* As métricas se derivam de quatro categorias essenciais:
  * **Entidade (por exemplo, uma assinatura)**. Provisione informações básicas sobre a assinatura e/ou cliente sujeito à variação.
  * **Atividade**. Obter todas as informações de uso possíveis relacionadas à entidade, por exemplo, o número de logons.
  * **Atendimento ao cliente**. Colete informações por meio dos logs de suporte do cliente para indicar se a assinatura teve problemas ou interações com o atendimento ao cliente.
  * **Dados empresariais e de concorrência**. Obter todas as informações possíveis sobre o cliente (por exemplo, ele pode estar indisponível ou ser difícil de rastrear).
* Use a importância como critério para acelerar a seleção de recursos. Isso implica que o modelo de árvore de decisão aprimorado sempre é uma abordagem promissora.  

O uso das quatro categorias cria a ilusão de que uma simples abordagem *determinística* , baseada em índices formados por fatores razoáveis divididos por categoria, seria suficiente para identificar os clientes apresentando risco de variação. Infelizmente, apesar de esse conceito parecer plausível, ele não é verdadeiro. O motivo é que a variação é um efeito temporal e os fatores que contribuem para a variação estão, geralmente, em estados transientes. O que leva um cliente a considerar a possibilidade de sair hoje pode ser diferente do motivo de amanhã e, com certeza, será diferente do motivo que ele terá para isso daqui a seis meses. Assim, um modelo *probabilístico* é necessário.  

Essa observação importante muitas vezes é ignorada pela empresa, que geralmente prefere uma abordagem orientada por Business Intelligence para a análise, principalmente porque é algo mais fácil de vender e permite automação simples.  

No entanto, a promessa de análises de autoatendimento usando Machine Learning Studio significa que as quatro categorias de informação, classificadas por divisão ou departamento, tornam-se uma fonte valiosa de aprendizado de máquina sobre variação.  

Outro recurso interessante do Azure Machine Learning é a capacidade de adicionar um módulo personalizado ao repositório de módulos predefinidos que já estão disponíveis. Essa capacidade, essencialmente, cria uma oportunidade de selecionar bibliotecas e criar modelos para mercados verticais. Ela é um diferencial importante do Azure Machine Learning no mercado.  

Esperamos continuar com este tópico no futuro, especialmente relacionado à análise de big data.
  

## <a name="conclusion"></a>Conclusão
Este documento descreve uma abordagem sensata para lidar com o problema comum de variação do cliente usando uma estrutura genérica. Consideramos um protótipo para modelos de pontuação e o implementamos usando o Azure Machine Learning. Por fim, avaliamos a exatidão e o desempenho do protótipo da solução com relação a algoritmos comparáveis em SAS.  

**Para obter mais informações:**  

Este documento foi útil para você? Por favor, nos dê seu feedback. Avalie em uma escala de 1 (ruim) a 5 (excelente), como você classificaria este documento e o porquê de ter dado essa classificação? Por exemplo:  

* Você está dando uma classificação alta devido a ter bons exemplos, excelentes capturas de tela, escrita clara ou por outro motivo?
* Você está dando uma classificação baixa devido a exemplos ruins, capturas de tela distorcidas ou escrita confusa?  

Seu feedback nos ajudará a melhorar a qualidade dos white papers que produzirmos.   

[Enviar comentários](mailto:sqlfback@microsoft.com).
 

## <a name="references"></a>Referências
[1] Predictive Analytics: Beyond the Predictions, W. McKnight, Information Management, julho/agosto de 2011, p. 18 a 20.  

[2] Artigo na Wikipédia: [Precisão e exatidão](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Step-by-Step Data Mining Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing de Big Data: envolva seus clientes com mais eficiência e agregue valor](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Modelo de variação de telecomunicações](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) na [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Apêndice
![][10]

*Figura 12: Instantâneo de uma apresentação de protótipo de variação*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
