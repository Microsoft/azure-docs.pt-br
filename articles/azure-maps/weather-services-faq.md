---
title: Perguntas frequentes sobre os serviços meteorológicos do Microsoft Azure (versão prévia)
description: Encontre respostas para perguntas comuns sobre os recursos e os dados dos serviços meteorológicos do Azure (versão prévia).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678122"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Perguntas frequentes sobre os serviços meteorológicos do Azure Maps (versão prévia)

> [!IMPORTANT]
> O serviço Clima do Azure Mapas está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo responde a perguntas comuns sobre dados e recursos [dos serviços meteorológicos](/rest/api/maps/weather) do Azure Maps. Os seguintes tópicos são abordados:

* Fontes de dados e modelos de dados
* Disponibilidade e cobertura dos serviços meteorológicos
* Frequência de atualização de dados
* Desenvolvendo com SDKs do Azure Maps
* Opções para visualizar dados meteorológicos, incluindo a integração do Microsoft Power BI

## <a name="data-sources-and-data-models"></a>Fontes de dados e modelos de dados

**Como o Azure mapeia dados meteorológicos de origem?**

O mapas do Azure é criado com a colaboração de parceiros de tecnologia de localização e mobilidade de classe mundial, incluindo AccuWeather, que fornece os dados de clima subjacentes. Para ler o comunicado da colaboração do Azure Maps com o AccuWeather, confira [Rain ou brilhar: os serviços meteorológicos do Azure Maps vão aprofundar-se em sua empresa](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather tem informações de clima e ambientais em tempo real disponíveis em qualquer lugar do mundo, em grande parte devido à sua parceria com várias agências de clima governamentais nacionais e outras disposições proprietárias. Uma lista dessas informações básicas é fornecida abaixo.

* Observações de superfície global disponíveis publicamente de agências governamentais
* Conjuntos de linhas de observação de superfície proprietária de governos e empresas privadas
* Dados de radar de alta resolução para mais de 40 países/regiões
* Os melhores dados do Lightning global em tempo real do setor
* Avisos meteorológicos emitidos pelo governo para mais de 60 países/regiões e territórios
* Dados de satélite de satélites de clima geoestático que cobrem todo o mundo
* Mais de 150 modelos de previsão numérica, incluindo modelagem interna e proprietária, modelos governamentais, como o GFS (sistema de previsão global) dos EUA e modelos de downscaled exclusivos fornecidos por empresas privadas
* Observações de qualidade do ar
* Observações de departamentos de transporte

Dezenas de milhares de observações de superfície, juntamente com outros dados, são incorporadas para criar e influenciar as condições atuais disponibilizadas para os usuários. Isso inclui não apenas conjuntos de informações padrão livremente disponíveis, mas também observações exclusivas obtidas de serviços aeródromo nacionais em muitos países/regiões, incluindo Índia, Brasil e Canadá e outras entradas proprietárias. Esses conjuntos de dados exclusivos aumentam a resolução espacial e temporal de data de condição atual para nossos usuários. 

Esses conjuntos de dados são revisados em tempo real quanto à precisão do sistema de previsão digital, que utiliza os algoritmos de inteligência artificial proprietários do AccuWeather para modificar continuamente as previsões, garantindo que sempre incorpore os dados mais recentes e, assim, maximizando sua precisão contínua.

**Quais modelos criam dados de previsão do clima?**

Vários sistemas de diretrizes de previsão meteorológico são utilizados para formular previsões globais. Mais de 150 modelos de previsão numérica são usados todos os dias, tanto os conjuntos de valores externos quanto os internos. Isso inclui modelos governamentais, como o Centro Europeu ECMWF e o GFS (sistema de previsão global) dos EUA. Além disso, o AccuWeather incorpora modelos de alta resolução proprietários que diminuir previsões para locais específicos e domínios regionais estratégicos para prever o clima com mais precisão. Os algoritmos de mesclagem e de peso exclusivos do AccuWeather foram desenvolvidos nas últimas várias décadas. Esses algoritmos utilizam de forma ideal as várias entradas de previsão para fornecer previsões altamente precisas.

## <a name="weather-services-preview-coverage-and-availability"></a>Disponibilidade e cobertura de serviços meteorológicos (versão prévia)

**Que tipo de cobertura posso esperar para diferentes países/regiões?**

A cobertura do serviço meteorológico varia por país/região. Todos os recursos não estão disponíveis em todos os países/regiões. Para obter mais informações, consulte a [documentação de cobertura](./weather-coverage.md).

## <a name="data-update-frequency"></a>Frequência de atualização de dados

**Com que frequência os dados das condições atuais são atualizados?**

Os dados das condições atuais são aproximadamente atualizados pelo menos uma vez por hora, mas podem ser atualizados com mais frequência com condições de alteração rápida – como alterações de temperatura grandes, alterações nas condições do céu, alterações precipitação e assim por diante. A maioria das estações de observação em todo o mundo relata muitas vezes por hora conforme as condições mudam. No entanto, algumas áreas ainda serão atualizadas apenas uma vez, duas vezes ou quatro horas em intervalos agendados.  

O Azure Maps armazena em cache os dados de condições atuais por até 10 minutos para ajudar a capturar a frequência de atualização quase em tempo real dos dados conforme eles ocorrem. Para ver quando a resposta armazenada em cache expira e evitar a exibição de dados desatualizados, você pode aproveitar as informações de cabeçalho Expires no cabeçalho HTTP da resposta da API do Azure Maps.

**Com que frequência os dados de previsão diária e por hora são atualizados?**

Os dados de previsão diária e por hora são atualizados várias vezes por dia, conforme as observações atualizadas são recebidas.  Por exemplo, se uma temperatura alta/baixa prevista for ultrapassada, nossos dados de previsão serão ajustados no próximo ciclo de atualização. Isso pode ocorrer em intervalos diferentes, mas normalmente ocorre em uma hora. Muitas condições de clima repentinas podem causar uma alteração nos dados de previsão. Por exemplo, em uma tarde de verão quente, um Thunderstorm isolado pode surgir repentinamente, trazendo cobertura de nuvem pesada e chuva. O Storm isolado pode efetivamente descartar a temperatura em até 10 graus. Esse novo valor de temperatura afetará as previsões horárias e diárias do restante do dia e, dessa forma, serão atualizados em nossos conjuntos de data.

As APIs de previsão do Azure Maps são armazenadas em cache por até 30 minutos. Para ver quando a resposta armazenada em cache expira e evitar a exibição de dados desatualizados, você pode aproveitar as informações de cabeçalho Expires no cabeçalho HTTP da resposta da API do Azure Maps. É recomendável atualizar, conforme necessário, com base em um caso de uso específico do produto e na interface do usuário.

## <a name="developing-with-azure-maps-sdks"></a>Desenvolvendo com SDKs do Azure Maps

**O SDK da Web do Azure Maps oferece suporte nativo à integração de serviços meteorológicos (versão prévia)?**

O SDK da Web do Azure Maps fornece um módulo de serviços. O módulo serviços é uma biblioteca auxiliar que torna mais fácil usar os serviços REST do Azure Maps em aplicativos Web ou Node.js. usando JavaScript ou TypeScript. Para começar, consulte nossa [documentação](./how-to-use-services-module.md).

**O Azure Maps SDK do Android dá suporte nativo à integração de serviços meteorológicos (versão prévia)?**

Os SDKs do Android do Azure Maps dão suporte a camadas de bloco Mercator, que podem ter notação x/y/zoom, notação de tecla quádrupla ou notação de caixa delimitadora de EPSG 3857.

Planejamos criar um módulo de serviços para Java/Android semelhante ao módulo SDK da Web. O módulo de serviços Android facilitará o acesso a todos os serviços do Azure Maps em um aplicativo Java ou Android.  

## <a name="data-visualizations"></a>Visualizações de dados  

**O Azure mapeia Power BI suporte ao Visual do Azure mapeia os blocos meteorológicos?**

Sim. Para saber como migrar blocos satélite de radar e infravermelho para o Microsoft Power BI Visual, consulte [Adicionar uma camada de bloco ao Power bi Visual](./power-bi-visual-add-tile-layer.md). 

**Como fazer interpretar as cores usadas para os blocos de radar e satélite?**

O [artigo conceito do clima](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) do Azure Maps inclui um guia para ajudar a interpretar as cores usadas para os blocos de radar e satélite. O artigo aborda exemplos de cores e códigos de cores hexadecimais.
 
**Posso criar animações de radar e de bloco satélite?**

Sim. Além dos blocos de radar e satélite em tempo real, os clientes do Azure Maps podem solicitar blocos passados e futuros para aprimorar visualizações de dados com sobreposições de mapa. Isso pode ser feito por meio da chamada direta da [API Get do mapa v2](/rest/api/maps/renderv2/getmaptilepreview) ou pela solicitação de blocos por meio do SDK da Web do Azure Maps. Os blocos de radar são fornecidos por até 1,5 horas no passado e por até 2 horas no futuro. Os blocos e estão disponíveis em intervalos de 5 minutos. Os blocos de infravermelho são fornecidos por até três horas no passado e estão disponíveis em intervalos de 10 minutos. Para obter mais informações, consulte o [exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)de animação de bloco do clima de código-fonte aberto.  

**Você oferece ícones para diferentes condições de clima?**

Sim. Você pode encontrar ícones e seus respectivos códigos [aqui](./weather-services-concepts.md#weather-icons). Observe que apenas algumas das APIs de serviço meteorológico (versão prévia), como  [obter a API de condições atuais](/rest/api/maps/weather/getcurrentconditionspreview), retornam o *iconCode* na resposta. Para obter mais informações, consulte o [exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)-fonte aberto WeatherConditions atual.

## <a name="next-steps"></a>Próximas etapas

Se essas perguntas frequentes não responderem à sua pergunta, você poderá entrar em contato conosco por meio dos seguintes canais (em ordem de escalonamento):

* A seção de comentários deste artigo.
* [MSFT Q&uma página para mapas do Azure](/answers/topics/azure-maps.html).
* O Suporte da Microsoft. Para criar uma nova solicitação de suporte, na [portal do Azure](https://portal.azure.com/), na guia ajuda, selecione o botão **ajuda +** suporte e, em seguida, selecione **nova solicitação de suporte**.
* O [Azure mapeia UserVoice](https://feedback.azure.com/forums/909172-azure-maps) para enviar solicitações de recursos.

Saiba como solicitar dados meteorológicos em tempo real e previsão usando os serviços meteorológicos do Azure Maps (versão prévia):
> [!div class="nextstepaction"]
> [Solicitar dados meteorológicos em tempo real ](how-to-request-weather-data.md)

Artigo sobre os conceitos dos serviços meteorológicos do Azure (versão prévia):
> [!div class="nextstepaction"]
> [Conceitos de serviços meteorológicos](weather-coverage.md)

Explore a documentação da API dos serviços meteorológicos do Azure Maps (versão prévia):

> [!div class="nextstepaction"]
> [Serviços Clima do Azure Mapas](/rest/api/maps/weather)