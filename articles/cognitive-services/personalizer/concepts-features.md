---
title: 'Recursos: Ação e contexto – Personalizador'
titleSuffix: Azure Cognitive Services
description: O Personalizador usa recursos, informações sobre ações e contexto, para oferecer melhores sugestões de classificação. Os recursos podem ser muitos genéricos ou específicos a um item.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 1960856ce2f15945d1b1bfa093f349771d481ffc
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663837"
---
# <a name="features-are-information-about-actions-and-context"></a>Recursos são informações sobre ações e contexto

O serviço Personalizador funciona aprendendo o que o seu aplicativo deve mostrar aos usuários em determinado contexto.

O Personalizador usa **recursos**, que são informações sobre o **contexto atual** para a escolha da melhor **ação**. Os recursos representam todas as informações que você acha que podem ajudar na personalização para alcançar maiores recompensas. Os recursos podem ser muitos genéricos ou específicos a um item. 

Por exemplo, você pode ter um **recurso** sobre:

* O _usuário_, como uma `UserID`. 
* O _conteúdo_, por exemplo, se um vídeo é um `Documentary`, um `Movie` ou uma `TV Series`, ou se um item de varejo está disponível na loja.
* O período _atual_, por exemplo, qual é o dia da semana.

O personalizador não prescreve, limita ou corrige quais recursos você pode enviar para ações e contexto:

* Você pode enviar alguns recursos para algumas ações e não para outras, caso não os tenha. Por exemplo, uma série de TV pode ter atributos que filmes não têm.
* Você pode ter alguns recursos disponíveis apenas algumas vezes. Por exemplo, um aplicativo móvel pode fornecer mais informações do que uma página da Web. 
* Ao longo do tempo, você pode adicionar e remover recursos sobre ações e contexto. O Personalizador continua aprendendo com as informações disponíveis.
* Deve haver, pelo menos, um recurso para o contexto. O Personalizador não dá suporte a um contexto vazio. Se você enviar apenas um contexto fixo sempre, o Personalizador escolherá a ação para classificações somente referente aos recursos nas ações.
* Para recursos categóricos, não é necessário definir os valores possíveis, e você não precisa definir os intervalos predefinidos para valores numéricos.

## <a name="supported-feature-types"></a>Tipos de recursos compatíveis

O Personalizador dá suporte a recursos de tipos de cadeia de caracteres, numéricos e boolianos.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Como a escolha do tipo de recurso afeta Machine Learning no Personalizador

* **Cadeias de caracteres**: Para tipos de cadeia de caracteres, cada combinação de chave e valor cria novos pesos no modelo personalizado de aprendizado de máquina. 
* **Numérico**: Você deve usar valores numéricos quando o número deve afetar proporcionalmente o resultado da personalização. Isso depende muito do cenário. Em um exemplo simplificado, por exemplo, ao personalizar uma experiência de varejo, o NumberOfPetsOwned pode ser um recurso que é numérico, pois você pode querer que as pessoas com 2 ou 3 animais de estimação influenciem o resultado da personalização duas vezes ou três vezes por até um animal de estimação. Recursos que são baseados em unidades numéricas, mas em que o significado não é linear, como idade, temperatura ou altura da pessoa, são mais bem codificados como cadeias de caracteres, e a qualidade do recurso pode ser normalmente melhorada usando intervalos. Por exemplo, age pode ser codificada como "Age": "0-5", "Age": "6-10", etc.
* Valores Boolianos enviados com o valor "false" funcionam como se não tivessem sido enviados.

Os recursos que não estão presentes devem ser omitidos da solicitação. Evite o envio de recursos com um valor nulo, pois ele será processado como existente e com um valor igual a "nulo" ao treinar o modelo.

## <a name="categorize-features-with-namespaces"></a>Categorizar recursos com namespaces

O Personalizador aceita recursos organizados em namespaces. Você determina, em seu aplicativo, se os namespaces são usados e quais eles devem ser. Os namespaces são usados para agrupar recursos sobre um tópico semelhante ou recursos provenientes de uma fonte específica.

Estes são exemplos de namespaces de recurso usados por aplicativos:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Clima
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Você pode nomear os namespaces de recurso seguindo suas próprias convenções, desde que elas sejam chaves JSON válidas. Os namespaces são usados para organizar recursos em conjuntos distintos e para desambiguar recursos com nomes semelhantes. Você pode considerar os namespaces como um ' prefix ' que é adicionado aos nomes de recursos. Namespaces não podem ser aninhados.


No JSON a seguir, `user`, `state` e `device` são namespaces de recurso. Observação de visualização pública: No momento, é altamente recomendável usar nomes para namespaces de recursos que são baseados em UTF-8 e começar com letras diferentes. Por `user`exemplo `device` `u`,, e comece com ,`s`e .`d` `state` Atualmente, ter namespaces com os mesmos primeiros caracteres pode resultar em colisões em índices usados para aprendizado de máquina.

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só poderá ser incluída se os itens de matriz forem números. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Como tornar os conjuntos de recursos mais efetivos para o Personalizador

Um bom conjunto de recursos ajuda o Personalizador a aprender a prever a ação que gerará a maior recompensa. 

Considere o envio de recursos para a API de Classificação do Personalizador que sigam estas recomendações:

* Há recursos suficientes para orientar a personalização. Quanto mais rigorosamente direcionado o conteúdo precisar ser, mais recursos serão necessários.

* Há recursos suficientes de *densidades* diversificadas. Um recurso é *denso* se muitos itens são agrupados em alguns buckets. Por exemplo, milhares de vídeos podem ser classificados como "Longos" (mais de 5 minutos de duração) e "Curtos" (menos em 5 minutos de duração). Esse é um recurso *muito denso*. Por outro lado, os mesmos milhares de itens podem ter um atributo chamado "Title", que quase nunca terá o mesmo valor de um item para outro. Esse é um recurso não muito denso ou *esparso*.  

Ter recursos de alta densidade ajuda o Personalizador a extrapolar o aprendizado de um item para outro. Porém, se houver apenas alguns recursos e eles forem muito densos, o Personalizador tentará direcionar precisamente o conteúdo com apenas alguns buckets à sua escolha.

### <a name="improve-feature-sets"></a>Melhorar os conjuntos de recursos 

Analise o comportamento do usuário fazendo uma avaliação offline. Isso permite que você examine os dados passados para ver quais recursos estão contribuindo intensamente para recompensas positivas versus aqueles que estão contribuindo menos. É possível ver quais recursos estão ajudando, e caberá a você e seu aplicativo encontrar recursos melhores para enviar ao Personalizador a fim de melhorar ainda mais os resultados.

Estas seções a seguir são práticas comuns para melhorar os recursos enviados ao Personalizador.

#### <a name="make-features-more-dense"></a>Tornar os recursos mais densos

É possível melhorar os conjuntos de recursos editando-os, a fim de torná-los maiores e mais ou menos densos.

Por exemplo, um carimbo de data/hora que inclui os segundos é um recurso muito esparso. Ele pode se tornar mais denso (efetivo) com a classificação das horas em "manhã", "meio-dia", "tarde" etc.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expandir conjuntos de recursos com informações extrapoladas

Você também pode obter mais recursos pensando em atributos inexplorados que podem ser derivados das informações que você já tem. Por exemplo, em uma personalização de lista de filmes fictícias, é possível que um fim de semana em vez de um dia final dos usuários seja diferente do comportamento? A hora pode ser expandida para ter um atributo "fim de semana" ou "dia útil". Os feriados culturais nacionais atraem a atenção para determinados tipos de filme? Por exemplo, um atributo "Dia das Bruxas" é útil em locais em que ele é relevante. É possível que o clima chuvoso tenha impacto significativo na escolha de um filme para muitas pessoas? Com a hora e o local, um serviço de meteorologia pode fornecer essas informações, e você pode adicioná-lo como um recurso extra. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expandir conjuntos de recursos com inteligência artificial e serviços cognitivos

A Inteligência Artificial e os Serviços Cognitivos prontos para execução podem ser uma adição muito eficiente ao Personalizador. 

Ao fazer o pré-processamento dos itens usando os serviços de inteligência artificial, você pode extrair de forma automática informações que provavelmente serão relevantes para a personalização.

Por exemplo:

* Você pode executar um arquivo de filme por meio do [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) para extrair elementos de cena, texto, sentimento e muitos outros atributos. Esses atributos podem então se tornar mais densos para refletir características que os metadados do item original não tinham. 
* As imagens podem ser executadas por meio da detecção de objeto, os rostos, por meio de sentimento etc.
* As informações no texto podem ser aumentadas com a extração de entidades e sentimento, expansão de entidades com o grafo de conhecimento do Bing etc.

Você pode usar vários outros [Serviços Cognitivos do Azure](https://www.microsoft.com/cognitive-services), como

* [Vinculação de entidade](../entitylinking/home.md)
* [Análise de Texto](../text-analytics/overview.md)
* [Detecção de Emoções](../emotion/home.md)
* [Pesquisa Visual Computacional](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>As ações representam uma lista de opções

Cada ação:

* Tem uma ID.
* Tem uma lista de recursos.
* A lista de recursos pode ser grande (centenas), mas recomendamos avaliar a eficácia do recurso para remover recursos que não estejam contribuindo para a obtenção de recompensas. 
* Os recursos nas **ações** podem ou não ter alguma correlação com os recursos no **contexto** usados pelo Personalizador.
* Os recursos para ações podem estar presentes em algumas ações e não em outras. 
* Os recursos para determinada ID de ação podem estar disponíveis um dia, mas posteriormente podem ficar indisponíveis. 

Os algoritmos de aprendizado de máquina do Personalizador terão melhor desempenho quando houver conjuntos de recursos estáveis, mas as Chamadas de classificação não falharão se o conjunto de recursos for alterado ao longo do tempo.

Não envie mais de 50 ações ao classificar ações. Elas podem ser as mesmas 50 ações sempre ou podem variar. Por exemplo, se você tiver um catálogo de produtos de 10 mil itens para um aplicativo de comércio eletrônico, poderá usar um mecanismo de recomendação ou de filtragem para determinar os 40 principais itens que possam ser do interesse de um cliente e usar o Personalizador para encontrar aquele que gerará a maior recompensa (por exemplo, o usuário o adicionará à cesta de compras) no contexto atual.


### <a name="examples-of-actions"></a>Exemplos de ações

As ações enviadas para a API de Classificação dependerão do que você está tentando personalizar.

Estes são alguns exemplos:

|Finalidade|Action|
|--|--|
|Personalizar qual artigo é realçado em um site de notícias.|Cada ação é um artigo de notícias potencial.|
|Otimizar o posicionamento de anúncios em um site.|Cada ação será um layout ou regras para criar um layout para os anúncios (por exemplo, na parte superior, à direita, imagens pequenas, imagens grandes).|
|Exibir uma classificação personalizada de itens recomendados em um site de compras.|Cada ação é um produto específico.|
|Sugerir a aplicação de elementos de interface do usuário, como filtros, a uma foto específica.|Cada ação pode ser um filtro diferente.|
|Escolher uma resposta do chatbot para esclarecer a intenção do usuário ou sugerir uma ação.|Cada ação é uma opção de como interpretar a resposta.|
|Escolher o que mostrar no início de uma lista de resultados da pesquisa|Cada ação é um dos poucos resultados da pesquisa principais.|


### <a name="examples-of-features-for-actions"></a>Exemplos de recursos para ações

Veja a seguir bons exemplos de recursos para ações. Eles dependem muito de cada aplicativo.

* Recursos com características das ações. Por exemplo, ele é um filme ou uma série de TV?
* Recursos sobre como os usuários podem ter interagido com esta ação no passado. Por exemplo, esse filme é visto principalmente por pessoas com perfil demográfico A ou B e normalmente não é reproduzido mais de uma vez.
* Recursos sobre as características de como o usuário *vê* as ações. Por exemplo, o cartaz do filme mostrado na miniatura inclui rostos, carros ou paisagens?

### <a name="load-actions-from-the-client-application"></a>Carregar ações do aplicativo cliente

Normalmente, os recursos de ações podem ser provenientes de sistemas de gerenciamento de conteúdo, catálogos e sistemas de recomendação. Seu aplicativo é responsável por carregar as informações sobre as ações dos bancos de dados e dos sistemas relevantes que você tem. Se as ações não são alteradas ou se o carregamento delas sempre tem um impacto desnecessário no desempenho, adicione uma lógica a seu aplicativo para armazenar essas informações em cache.

### <a name="prevent-actions-from-being-ranked"></a>Impedir a classificação de ações

Em alguns casos, há ações que você não deseja exibir aos usuários. A melhor maneira de impedir a classificação de uma ação como mais alta é não a incluir na lista de ações da API de Classificação em primeiro lugar.

Em alguns casos, só pode ser determinado posteriormente na lógica de negócios se uma _ação_ resultante de uma chamada à API de Classificação deve ser mostrada para um usuário. Nesses casos, você deve usar _Eventos Inativos_.

## <a name="json-format-for-actions"></a>Formato JSON para as ações

Ao chamar a Classificação, você enviará várias ações para sua escolha:

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só poderá ser incluída se os itens de matriz forem números. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Exemplos de informações de contexto

As informações para o _contexto_ dependem de cada aplicativo e caso de uso, mas normalmente podem incluir informações como:

* Informações demográficas e de perfil sobre o usuário.
* Informações extraídas de cabeçalhos HTTP, como o agente do usuário, ou derivadas de informações HTTP, como pesquisas geográficas inversas baseadas em endereços IP.
* Informações sobre a hora atual, como dia da semana, fim de semana ou não, manhã ou tarde, festas de fim de ano ou não etc.
* Informações extraídas de aplicativos móveis, como localização, movimentação ou nível da bateria.
* Agregações históricas do comportamento dos usuários – como quais são os gêneros de filmes mais exibidos por este usuário.

Seu aplicativo é responsável por carregar as informações sobre o contexto dos bancos de dados, dos sensores e dos sistemas relevantes que você possa ter. Se as informações de contexto não são alteradas, é possível adicionar uma lógica a seu aplicativo para armazenar essas informações em cache, antes de enviá-las para a API de Classificação.

## <a name="json-format-for-context"></a>Formato JSON para o contexto 

O contexto é expresso como um objeto JSON que é enviado para a API de Classificação:

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só poderá ser incluída se os itens de matriz forem números. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

[Aprendizado de reforço](concepts-reinforcement-learning.md) 
