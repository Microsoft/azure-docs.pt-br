---
title: "Perguntas frequentes sobre a Versão Prévia do Azure Machine Learning 2017 | Microsoft Docs"
description: "Este artigo contém perguntas frequentes e respostas para recursos de versão prévia do Azure Machine Learning"
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 4b3720019ba396d90be5586fa8fff4fa7e7d6e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Machine Learning

## <a name="general-product-questions"></a>Perguntas gerais sobre o produto

**O que é Azure Machine Learning?**

O Azure Machine Learning é um serviço do Azure totalmente gerenciado que permite criar, testar, gerenciar e implantar modelos de AI e aprendizado de máquina. Nossos novos serviços e aplicativo para download oferecem uma abordagem primeiro de código que aproveita a nuvem, o local e a borda para oferecer modelos de treinamento, implantação, gerenciamento e monitoramento com eficiência, velocidade e flexibilidade. Como alternativa, Azure Machine Learning Studio oferece um ambiente de criação visual do tipo "arrastar e soltar" baseado em navegador em que nenhuma codificação é necessária. 

**Como faço para começar a utilizar a versão prévia?**

É a maneira mais fácil de começar é inscrever-se para o Serviço de Experimentação no Azure. No portal, você pode também baixar nosso aplicativo Workbench gratuito ou inscrever-se para uma conta de Gerenciamento de Modelos. Além disso, você pode acessar nosso site de documentação para artigos de instruções, vídeos, código de demonstração e muito mais. 

**Eu não tenho uma assinatura do Azure. Ainda posso experimentar os novos serviços?**

Como parte do portfólio do Microsoft Azure, nossos novos serviços exigem uma assinatura do Azure. Além disso, você deve ter as permissões adequadas para criar ativos, como Grupos de Recursos, Máquinas Virtuais etc. 

**Em quais regiões os novos serviços estão disponíveis?**

Procure regiões do Azure com suporte para serviço de gerenciamento de modelos e experimentação em Azure Machine Learning na página [Produtos do Azure por região](https://azure.microsoft.com/regions/services/).

Mais regiões serão adicionadas à medida que desenvolvermos o produto.  Você pode nos ajudar a priorizar em quais regiões para implantar no site [Comentários sobre o Azure Machine Learning](https://feedback.azure.com/forums/257792-machine-learning). 

**Que outros serviços do Azure que são necessários?**

Para aproveitar completamente todos os recursos do Azure Machine Learning, o Azure Machine Learning usará a conta do Visual Studio VSTS, Armazenamento de Blobs do Azure, Registro de Contêiner do Azure, computação do HDInsight ou VM de Ciência de Dados e serviço de Contêiner do Azure.

**Como o Azure Machine Learning está relacionado aos Serviços Machine Learning da Microsoft no SQL Server 2017?**   

Os Serviços de Machine Learning no SQL Server 2017 são uma plataforma extensível e escalonável para integrar tarefas de aprendizado de máquina a fluxos de trabalho do banco de dados. São ideais para situações em que uma solução local é necessária, por exemplo, quando a movimentação de dados é cara ou inviável. Em contraste, cargas de trabalho de nuvem ou híbrida são uma excelente opção para nossos novos serviços do Azure. 

**Há suporte para Python e R? E quanto a outras linguagens de programação, como C++**

No momento, damos suporte somente a Python. Estamos trabalhando na integração do R e esperamos que ele fique disponível em breve. 

**Como o Azure Machine Learning está relacionado ao Machine Learning da Microsoft para Spark?**

R: O MMLSpark oferece ferramentas de ciência de dados e aprendizado profundo para o Apache Spark, com ênfase em produtividade, facilidade de experimentação e algoritmos de última geração. O MMLSpark oferece integração de pipelines de Machine Learning do Spark com o Kit de Ferramentas Cognitivas da Microsoft e OpenCV. Você pode criar eficientes modelos de previsão e análise eficientes e altamente escaláveis para dados de imagem e texto. O MMLSpark está disponível em uma licença de software livre e está incluído no AML Workbench como um conjunto de modelos e algoritmos para consumo. Para obter mais informações sobre o MMLSpark, acesse nossa documentação de produto. 

**Quais versões do Spark têm suporte nas novas ferramentas e serviços? Seção superior**

No momento, o Workbench inclui e dá suporte a MMLSpark versão 0.8, que é compatível com o Apache Spark 2.1. Você também tem uma opção para usar a imagem do Docker habilitada para GPU do MMLSpark 0.8 em máquinas virtuais Linux.

## <a name="experimentation-service"></a>Serviço de Experimentação

**O que é o serviço de Experimentação do Azure Machine Learning?**

O Serviço de Experimentação é um serviço gerenciado do Azure que leva a experimentação de aprendizado de máquina para o próximo nível. Os experimentos podem ser criadas localmente ou na nuvem. Crie rapidamente protótipos em uma área de trabalho, então expanda para máquinas virtuais ou clusters do Spark. VMs do Azure com a tecnologia mais recente de GPU permitem que você participe de aprendizado profundo com rapidez e eficiência. Também incluímos integração profunda com o Git para que você possa conectar-se facilmente aos fluxos de trabalho existentes para acompanhamento, configuração e colaboração de código. 

**Como serei ser cobrado pelo Serviço de Experimentação?**

Os dois primeiros usuários associados ao seu Serviço de Experimentação do Azure Machine Learning são gratuitos. Usuários adicionais serão cobrados a taxa de Visualização Pública de US$ 50/mês. Para obter mais informações sobre preços e faturamento, acesse nossa página de Preços.

**Posso ser cobrado com base em quantos experimentos eu executo?**

Não, o Serviço de Experimentação permite tantos experimentos quantos você precisar e as cobranças são baseadas somente no número de usuários. Os recursos de computação de experimentação são cobrados separadamente. Recomendamos que você execute vários experimentos para que possa encontrar o modelo mais adequado à sua solução.   

**Que tipos específicos de recursos de computação e armazenamento posso usar?**

O serviço Experimentação pode executar seus experimentos em computadores locais (diretos ou baseado em Docker), [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/) e [HDInsight](https://azure.microsoft.com/services/hdinsight/). O serviço também acessa uma conta de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para armazenar as saídas de execução e pode aproveitar uma conta do [Serviço do Visual Studio Team](https://azure.microsoft.com/services/visual-studio-team-services/) para controle de versão e armazenamento de Git. Note que você será cobrado independentemente por qualquer recurso de computação e de armazenamento consumido com base no preço individual.  


## <a name="model-management"></a>Gerenciamento de Modelos

**O que é o Gerenciamento de Modelos do Azure Machine Learning?**

O Gerenciamento de Modelos do Azure Machine Learning é um serviço gerenciado do Azure que permite a cientistas de dados e equipes de operações de desenvolvimento implantar modelos preditivos de maneira confiável em uma ampla variedade de ambientes. Repositórios Git e contêineres do Docker proporcionam capacidade de acompanhamento e de repetição. Os modelos podem ser implantados de maneira confiável na nuvem, localmente ou na borda. Uma vez em produção, você pode gerenciar o desempenho do modelo e treinar novamente de modo proativo se o desempenho diminuir. Você pode implantar modelos em computadores locais, como [VMs do Azure](https://azure.microsoft.com/services/virtual-machines/), Spark em [HDInsight](https://azure.microsoft.com/services/hdinsight/) ou clusters do [Serviço de Contêiner do Azure](https://azure.microsoft.com/services/container-service/) orquestrados por Kubernetes.  

**O que é um “modelo”?**

Um modelo é a saída de uma execução de experimentação que foi promovida para gerenciamento de modelos. Um modelo registrado na conta de hospedagem é contado em relação ao seu plano, incluindo modelos atualizados por meio de novo treinamento ou iteração de versão.

**O que é um “modelo gerenciado?”**

Um modelo é a saída de um processo de treinamento e é a aplicação de um algoritmo de aprendizado de máquina para treinar dados. Gerenciamento de Modelos permite implantar modelos como serviços Web, gerenciar várias versões dos seus modelos e monitorar seu desempenho e métricas. Modelos “gerenciados” são registrados com uma conta de Gerenciamento de Modelos do Azure Machine Learning. Como um exemplo, considere um cenário em que você esteja tentando prever vendas. Durante a fase de experimentação, você gerará muitos modelos usando diferentes conjuntos de dados ou algoritmos. Você gerou quatro modelos com diferentes precisões, mas escolhe registrar somente o modelo com a precisão mais alta. O modelo registrado se torna o seu primeiro modelo gerenciado.
 
**O que é uma “implantação”?**

Gerenciamento de Modelos permite que você implante modelos como contêineres de serviço Web empacotados no Azure. Esses serviços Web podem ser invocados usando APIs REST. Cada serviço Web é contado como uma implantação única e o número total de implantações ativas é contado para o seu plano. Usando o exemplo de previsão de vendas, ao implantar seu modelo de melhor desempenho, seu plano é incrementado em uma implantação. Se você então treinar novamente e implantar outra versão, terá duas implantações. Se você determinar que o modelo mais recente é melhor e excluir o original, sua contagem de implantação diminuirá em uma.  

**Quais recursos de computação específicos estão disponíveis para as minhas implantações?** 

O Gerenciamento de Modelos pode executar suas implantações como contêineres do Docker registrados para o [Serviço de Contêiner do Azure](https://azure.microsoft.com/services/container-service/), como [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/) ou em computadores locais. Mais destinos de implantação serão adicionados em breve. Note que você será cobrado independentemente por qualquer recurso de computação consumido com base no seu preço individual.     

**Posso usar o Gerenciamento de Modelos do Azure Machine Learning para implantar modelos criados usando ferramentas diferentes daquelas do Serviço de Experimentação?**

Você obtém a melhor experiência quando implanta modelos criados usando o Serviço de Experimentação. No entanto, você pode implantar os modelos criados usando outras ferramentas e estruturas. Há suporte para uma variedade de modelos, incluindo MMLSpark, TensorFlow, Kit de Ferramentas Cognitivas Microsoft, scikit-learn, Keras etc. 

**Posso usar meus próprios recursos do Azure?**

Sim, o Serviço de Experimentação e Gerenciamento de Modelos funcionam junto com vários repositórios de dados do Azure, cargas de trabalho de computação e outros serviços. Consulte a nossa documentação técnica para obter mais detalhes sobre os serviços do Azure necessários.

**Há suporte para cenários de implantação em nuvem e local?**

Sim. Damos suporte a cenários de implantação local e na nuvem por meio de contêineres do Docker. Destinos de execução locais incluem: implantações do Docker de nó único, [Microsoft SQL Server com Serviços ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop ou Spark. Também há suporte para implantações na nuvem por meio do Docker, incluindo: implantações em cluster por meio do Serviço de Contêiner do Azure e clusters Kubernetes, HDInsight ou Spark. Cenários de borda têm suporte por meio de contêineres do Docker e Borda de IOT do Azure. 

**É possível executar uma imagem do Docker criada usando a CLI do Azure Machine Learning em outro host?**

Sim. Você pode usar a imagem como um serviço Web em qualquer host do docker, desde que o host tenha recursos de computação suficientes para hospedar a imagem do docker.

**Há suporte para novo treinamento dos modelos implantados?**

Sim, você pode implantar diversas versões do mesmo modelo. O Gerenciamento de Modelos dará suporte a atualizações de serviço para todos os modelos e imagens atualizados.

## <a name="workbench"></a>Workbench

**O que é o Azure Machine Learning Workbench?**

O Azure Machine Learning Workbench é um aplicativo complementar criado para cientistas de dados profissionais. Disponível para Windows e Mac, o Machine Learning Workbench oferece visão geral, gerenciamento e controle para soluções de aprendizado de máquina. O Machine Learning Workbench inclui acesso a estruturas de AI de ponta da Microsoft e da comunidade de software livre. Incluímos os kits de ferramentas de ciência de dados mais populares, incluindo TensorFlow, Kit de Ferramentas Cognitivas da Microsoft, Spark ML, scikit-learn e muito mais. Também habilitamos integração com IDEs de ciência de dados populares, como blocos de anotações do Jupyter, PyCharm e do Visual Studio Code. O Machine Learning Workbench tem recursos de preparação de dados internos para rapidamente amostrar, entender e preparar dados, sejam estruturados ou não. Nossa nova ferramenta de preparação de dados, chamada [PROSE](https://microsoft.github.io/prose/), se baseia na tecnologia de última geração da Microsoft Research.  

**O Workbench é um IDE?**

Não. O Machine Learning Workbench foi projetado como um complemento a IDEs populares, como Blocos de Anotações do Jupyter, Visual Studio Code e PyCharm, mas não é um IDE totalmente funcional. O Machine Learning Workbench oferece alguns recursos básicos de edição de texto, mas depuração, intellisense e outros recursos de IDE comumente usados não têm suporte. É recomendável que você use seu IDE favorito para o desenvolvimento, edição e depuração de código. 

**Há um custo para usar o Azure Machine Learning Workbench?**

Não. O Azure Machine Learning Workbench é um aplicativo gratuito. Você pode baixá-lo em tantos computadores e usuários quantos precisar. Para usar o Azure Machine Learning Workbench, você deve ter uma conta de Experimentação. .  

**Há suporte para funcionalidades de linha de comando?**

R: Sim, o Azure Machine Learning oferece uma interface de CLI completa. A CLI de Machine Learning é instalada por padrão com Azure Machine Learning Workbench. Ele também é fornecido como parte da máquina virtual de Ciência de Dados Linux no Azure e será integrado à [CLI do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)


**Posso usar Blocos de Anotações do Jupyter com o Workbench?**

Sim! Você pode executar blocos de anotações do Jupyter no Workbench, sendo o Workbench o aplicativo de host do cliente, assim como você usaria um navegador como cliente. 

**Há suporte para quais kernels do Bloco de Anotações do Jupyter?**

R: A versão atual do Jupyter incluída no Workbench inicializa um kernel Python 3 e um kernel adicional para cada arquivo "runconfig" na pasta aml_config. As configurações com suporte incluem:
- Python local
- Python no Docker local ou remoto

## <a name="data-formats-and-capabilities"></a>Formatos e funcionalidades de dados

**Quais formatos de arquivo têm suporte no momento para ingestão de dados no Workbench?**

As ferramentas de preparação de dados no Workbench atualmente dão suporte para ingestão dos seguintes formatos: 
- Arquivos delimitados, como CSV, TSV etc.  
- Arquivos de largura fixa
- Arquivos de texto sem formatação
- Excel (.xls/xlsx)
- Arquivos JSON
- Arquivos Parquet 
- Arquivos personalizados (scripts) Se sua solução exigir uma ingestão de dados de outras fontes, o código Python poderá ser usado para… 

**Atualmente, há suporte para quais locais de armazenamento de dados?**

Para visualização pública, o Workbench dá suporte à ingestão de dados de: 
- Disco rígido local ou local de armazenamento de rede mapeado
- O Armazenamento do Azure ou BLOB do Azure (requer uma assinatura do Azure)
- Azure Data Lake ou Azure Data Lake Storage (requer uma assinatura do Azure)
- Azure SQL Server
- Microsoft SQL Server


**Quais tipos de agrupamento, preparação e transformações de dados estão disponíveis?**

Para visualização pública, o Workbench dá suporte a "Derivar Coluna por Exemplo", "Dividir Coluna por Exemplo", "Clustering de Texto", "Tratar Valores Ausentes" e muitos outros.  O Workbench também dá suporte à conversão de tipo de dados, agregação de dados (CONTAGEM, MÉDIA, VARIÂNCIA etc.) e associações de dados complexos. Para obter uma lista completa de funcionalidades com suporte, acesse nossa documentação de produto. 

**Há qualquer limites de tamanho de dados imposto pelo Azure Machine Learning Workbench, Experimentação ou Gerenciamento de Modelos?**

R: Não, os novos serviços não impõem limitações de dados. No entanto, há limitações introduzidas pelo ambiente no qual você está executando sua preparação de dados, treinamento de modelo, experimentação ou implantação. Por exemplo, se você estiver almejando um ambiente local para treinamento, estará limitado pelo espaço disponível no seu disco rígido. Como alternativa, se você estiver almejando o HDInsight, estará limitado por qualquer tamanho associado ou restrições de computação. 

## <a name="algorithms-and-libraries"></a>Algoritmos e bibliotecas

**Quais algoritmos têm suporte no Azure Machine Learning Workbench?**

Nossos produtos e serviços de versão prévia incluem o melhor da comunidade de software livre. Há suporte para uma ampla variedade de algoritmos e bibliotecas, incluindo TensorFlow, scikit-learn, Apache Spark e o Kit de Ferramentas Cognitivas da Microsoft. O Azure Machine Learning Workbench também inclui o pacote [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy).

**Como o Azure Machine Learning se relacionam com o Kit de Ferramentas Cognitivas da Microsoft?**

O [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/cognitive-toolkit/) é uma das várias estruturas com suporte em nossas novas ferramentas e serviços. O Kit de Ferramentas Cognitivas é um kit de ferramentas de aprendizado profundo unificado que permite consumir e combinar modelos de aprendizado de máquina populares, incluindo Redes Neurais Profundas de Feed-Forward, Redes Convolucionais, Sequência a Sequência e Redes Recorrentes. Para obter mais informações sobre o Kit de Ferramentas Cognitivas da Microsoft, acesse nossa [documentação de produto](https://docs.microsoft.com/cognitive-toolkit/). 

## <a name="pricing-and-billing"></a>Preços e cobrança

**Vocês cobram pelo Azure Machine Learning durante a versão prévia?** 

R: O aplicativo Azure Machine Learning Workbench está disponível gratuitamente para assinantes do Azure. O Serviço de Experimentação e a oferta de Gerenciamento de Modelos oferecem camadas gratuitas, além de camadas pagas, disponíveis com um desconto durante a Visualização Pública.

**Posso ser cobrado com base em quantos experimentos eu executo?**

Não, o Serviço de Experimentação permite tantos experimentos quantos você precisar e as cobranças são baseadas somente no número de usuários. Os recursos de computação de experimentação são cobrados separadamente.  Recomendamos que você execute vários experimentos para definir o melhor modelo para a sua solução. 

**Serei cobrado com base em quantas vezes meus serviços Web forem chamados?**

Não. Os serviços Web podem ser chamados quantas vezes forem necessárias, sem nenhuma implicação de cobrança do Gerenciamento de Modelos. Você terá controle completo para dimensionar suas implantações para que atendam às necessidades dos seus aplicativos.

**Como posso dimensionar o número de unidades comprados no Gerenciamento de Modelos do Azure Machine Learning?**

Você pode mudar o número de unidades, para mais ou para menos, usando o portal do Azure ou a CLI. 

**Como será minha fatura?**

As faturas são produzidas diariamente. Para fins de cobrança, um dia começa à meia-noite UTC. As faturas são geradas mensalmente. Encargos separados são gerados para quaisquer serviços do Azure consumidos em conjunto com o Azure Machine Learning. Os encargos incluem, mas não são limitados a: 
- Encargos de computação
- HDInsight
- Serviço de Contêiner do Azure
- Registro de Contêiner do Azure 
- Armazenamento do Blobs do Azure
- Application Insights
- Cofre da Chave do Azure
- Visual Studio Team Services
- Hub de Eventos do Azure
- Azure Stream Analytics Para obter mais detalhes ou para exibir uma lista de exemplo, acesse nossa página de preços. 

## <a name="support-and-training"></a>Suporte e treinamento

**Onde posso obter treinamento para o Azure Machine Learning?**

O [Centro de Documentação do Azure Machine Learning](./overview-what-is-azure-ml.md) hospeda tutoriais em vídeo e guias de instruções. Esses guias passo a passo apresentam os serviços e explicam o ciclo de vida de ciência de dados. Incluímos novo material continuamente no Machine Learning Center. Você pode enviar solicitações de material de aprendizado adicional no fórum de comentários dos usuários.

**Como fazer para obter suporte ao Azure Machine Learning?**

Para obter suporte técnico, acesse o Suporte do Azure e selecione Machine Learning. O Azure Machine Learning também tem uma comunidade ativa em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) marcada com "Azure-Machine-Learning", monitorado pela equipe.  Sugestões e solicitações de recurso podem ser enviadas no site [Comentários sobre o Azure Machine Learning](https://feedback.azure.com/forums/257792-machine-learning). 
