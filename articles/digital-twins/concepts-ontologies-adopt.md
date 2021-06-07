---
title: Adoção de ontologias padrão do setor
titleSuffix: Azure Digital Twins
description: Saiba mais sobre os ontologies do setor existentes que podem ser adotados para o gêmeos digital do Azure
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124220"
---
# <a name="adopting-an-industry-ontology"></a>Adotando uma ontologia do setor

Como pode ser mais fácil começar com uma DTDL ontologia de software livre do que a partir de uma página em branco, a Microsoft está fazendo parcerias com especialistas em domínio para publicar ontologies, que representam convenções de setor amplamente aceitas e que dão suporte a uma variedade de casos de uso de clientes. 

O resultado é um conjunto de ontologies com base em DTDL de software livre, que aprende com, aproveitam, aprendem ou usam diretamente os padrões do setor. Os ontologies são projetados para atender às necessidades dos desenvolvedores de downstream, com o potencial de serem amplamente adotados e/ou estendidos pelo setor.

Neste momento, a Microsoft trabalhou com parceiros para desenvolver um ontologia para [prédios inteligentes](#realestatecore-smart-building-ontology) e um ontologia para as [cidades inteligentes](#smart-cities-ontology), que fornecem um aterramento comum para modelar com base em padrões nesses setores para evitar a reinvenção. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologia de construção inteligente RealEstateCore

*Encontre o ontologia aqui: [**gêmeos RealEstateCore ontologia com base em linguagem para prédios inteligentes**](https://github.com/Azure/opendigitaltwins-building)*.

A Microsoft estabeleceu uma parceria com a [RealEstateCore](https://www.realestatecore.io/), um consórcio Sueco de proprietários de imóveis, fornecedores de software e instituições de pesquisa, a fim de fornecer esse DTDL ontologia de código-fonte aberto para o setor de imóveis.

Este ontologia de prédios inteligentes fornece um solo comum para a modelagem de prédios inteligentes, usando padrões do setor (como [esquema de Brick](https://brickschema.org/ontology/) ou ontologia de [topologia de construção W3C](https://w3c-lbd-cg.github.io/bot/index.html)) para evitar a reinvenção. O ontologia também vem com as práticas recomendadas de como consumir e estendê-lo corretamente. 

Para saber mais sobre a estrutura e as convenções de modelagem do ontologia, como usá-la, como estendê-la e como contribuir, visite o repositório do ontologia no GitHub: [Azure/opendigitaltwins-Building](https://github.com/Azure/opendigitaltwins-building). 

Você também pode ler mais sobre a parceria com RealEstateCore e metas desta iniciativa nesta postagem de blog e vídeo que acompanha: [RealEstateCore, um ontologia de construção inteligente para o digital gêmeos, agora está disponível](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologia de cidades inteligentes

*Encontre o ontologia aqui: [**DTDL (digital gêmeos Definition Language) ontologia para as cidades inteligentes**](https://github.com/Azure/opendigitaltwins-smartcities)*.

A Microsoft tem colaborado com o [OASC (Agile Smart cidades)](https://oascities.org/) e o [Sirus](https://sirus.be/) para fornecer um ontologia baseado em DTDL para cidades inteligentes, começando com o [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Além do ETSI NGSI-LD, também avaliamos Saref4City, CityGML, ISO e outros.

A versão atual do ontologia está concentrada em um conjunto inicial de modelos. Os autores do ontologia lhe apreciam contribuir para estender o conjunto inicial de casos de uso, bem como melhorar os modelos existentes. 

Para saber mais sobre o ontologia, como usá-lo e como contribuir, visite o repositório do ontologia no GitHub: [Azure/opendigitaltwins-SmartCITIES](https://github.com/Azure/opendigitaltwins-smartcities). 

Você também pode ler mais sobre as parcerias e a abordagem para as cidades inteligentes nesta postagem no blog e o vídeo que acompanha: as [cidades inteligentes ontologia para gêmeos digital](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como estender ontologies padrão do setor para atender às suas especificações: [*conceitos: estendendo ontologies do setor*](concepts-ontologies-extend.md).

* Ou, continue no caminho para desenvolver modelos com base em ontologies: [*usando estratégias de ontologia em um caminho de desenvolvimento de modelo*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).