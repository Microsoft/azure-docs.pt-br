---
title: Conjuntos de dados públicos para análise do Azure | Microsoft Docs
description: Saiba mais sobre os conjuntos de dados públicos que você pode usar para criar um protótipo e testar soluções e serviços de análise do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 10/01/2018
ms.date: 04/08/2019
ms.openlocfilehash: e7d01a6512c2d39c86da9f7020aa3988c9680c6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584447"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Conjuntos de dados públicos para criação de protótipos e testes

Procure nesta lista de conjuntos de dados públicos os dados que você pode usar para soluções e serviços de armazenamento de protótipo e de teste.

## <a name="us-government-and-agency-data"></a>EUA Dados do governo e agência

| Fonte de dados | Sobre os dados | Sobre os arquivos |
|---|---|---|
| [Dados do governo dos EUA](https://www.census.gov/data.html) | Mais de 190.000 conjuntos de dados que abrangem agricultura, clima, consumidor, ecossistemas, educação, energia, finanças, integridade, municipal, fabricação, marítima, oceano, segurança pública e ciência e pesquisa nos EUA. | Arquivos de vários tamanhos em vários formatos, incluindo HTML, XML, CSV, JSON, Excel e muitos outros. Você pode filtrar conjuntos de dados disponíveis pelo formato de arquivo. |
| [Dados de censo dos EUA](https://www.census.gov/data.html) | Dados estatísticos sobre a população dos EUA | Os conjuntos de dados estão em vários formatos. |
| [Dados de ciências da Terra da NASA](https://earthdata.nasa.gov/) | Mais de 32.000 coleções de dados que abrangem agricultura, atmosfera, biosfera, clima, criosfera, dimensões humanas, hidrosfera, superfície da Terra, oceanos, interações entre o sol e a terra e muito mais. | Os conjuntos de dados estão em vários formatos. |
| [Atrasos de voo de companhia aérea e outros dados de transporte](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "O Departamento de Estatísticas de Transporte (BTS) do Departamento de Transporte (DOT) dos EUA rastreia o desempenho em tempo de voos domésticos operados por grandes companhias aéreas. Informações de resumo sobre o número de voos no horário, atrasados, cancelados e desviados aparecem... em tabelas de resumo postadas no site." | Os arquivos estão no formato CSV. |
| [Mortes no trânsito - sistema de relatórios de análise de mortes nos EUA (FARS)](https://www.nhtsa.gov/FARS) | "O FARS é um censo nacional que fornece ao NHTSA, ao Congresso e ao público americano os dados anuais públicos sobre lesões fatais sofridas em colisões de veículos motorizados". | "Crie seus próprios dados sobre mortes executados online usando o Sistema de Consultas do FARS. Ou baixe todos os dados do FARS de 1975 até o presente do site de FTP”. |
| [Dados sobre produtos químicos tóxicos - dados do EPA Toxicity ForeCaster (ToxCast™)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Os dados de toxidade de alta produtividade mais atualizados e publicamente disponíveis do EPA sobre milhares de produtos químicos. Esses dados são gerados por meio do esforço de pesquisa do ToxCast do EPA". | Os conjuntos de dados estão disponíveis em vários formatos, incluindo planilhas, pacotes R e arquivos de banco de dados MySQL. |
| [Dados de produtos químicos tóxicos - Desafio de dados NIH Tox21 de 2014](https://tripod.nih.gov/tox21/challenge/) | "O desafio de dados Tox21 de 2014 foi projetado para ajudar a compreender o potencial de produtos e compostos químicos que estão sendo testados por meio da iniciativa Toxicology in the 21st Century para interromper caminhos biológicos de formas que possam resultar em efeitos tóxicos”. | Os conjuntos de dados estão disponíveis nos formatos SMILES e SDF. Os dados fornecem "dados de atividade de reagentes e estruturas químicas na coleção Tox21 de aproximadamente 10.000 compostos (Tox21 10K)". |
| [Dados de biotecnologia e genoma do NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Vários conjuntos de dados que abrangem genes, genomas e proteínas. | Conjuntos de dados estão em texto, XML, BLAST e outros formatos. Um aplicativo BLAST está disponível. |

## <a name="other-statistical-and-scientific-data"></a>Outros dados estatísticos e científicos

| Fonte de dados | Sobre os dados | Sobre os arquivos |
|---|---|---|
| [Dados de táxi de Nova York](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Os registros de corrida de táxi incluem campos que capturam as datas/horas de partida e chegada, distâncias, tarifas discriminadas, tipos de taxa, tipos de pagamento, e contagens de passageiro relatadas pelo motorista." | Os conjuntos de dados estão em arquivos CSV por mês. |
| [Conjuntos de dados do Microsoft Research - "Ciência de Dados para Pesquisa"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Diversos conjuntos de dados que abrangem a interação homem — máquina, áudio/vídeo, mineração de dados/recuperação de informações, geoespaciais/localização, processamento de linguagem natural e visão robótica/computacional. | Os conjuntos de dados estão em vários formatos, compactados para download. |
| [Dados públicos sobre genoma](https://www.completegenomics.com/public-data/) | "Um conjunto variado de dados de genomas humanos completos estão disponíveis gratuitamente para uso público para aprimorar qualquer estudo sobre genoma..." O provedor, Complete Genomics, é uma corporação privada com fins lucrativos. | Os conjuntos de dados, após a extração, estão no formato de texto UNIX. Também há ferramentas de análise disponíveis. |
| [Dados da Open Science Data Cloud](https://www.opensciencedatacloud.org/) | "A Open Science Data Cloud fornece à comunidade científica recursos para armazenar, compartilhar e analisar terabytes e petabytes de conjuntos de dados científicos".| Os conjuntos de dados estão em vários formatos. |
| [Dados sobre clima global – WorldClim](https://worldclim.org/) | "WorldClim é um conjunto de camadas de clima global (dados de clima matriciais) com uma resolução espacial de cerca de 1 km2. Esses dados podem ser usados para mapeamento e modelagem espacial". | Esses arquivos contêm dados geoespaciais. Para saber mais, veja [Formato de dados](https://worldclim.org/formats1). |
| [Dados sobre a sociedade humana - o projeto GDELT](https://www.gdeltproject.org/data.html) | "O projeto GDELT é o maior e mais abrangente banco de dados aberto da sociedade humana já criado, além de contar com a maior resolução". | Os arquivos de dados brutos estão no formato CSV. |
| [Dados de previsão de cliques em publicidade para machine learning da Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "O maior conjunto de dados ML já lançado publicamente". Para saber mais, veja [Conjunto de dados de previsão de cliques de 1 TB da Criteo](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [Conjunto de dados de mineração de texto ClueWeb09 do projeto Lemur](https://www.lemurproject.org/clueweb09.php/) | "O conjunto de dados ClueWeb09 foi criado para oferecer suporte à pesquisa sobre a recuperação de informações e tecnologias relacionadas ao idioma humano. Ele consiste em um bilhão de páginas da Web em 10 idiomas coletadas entre janeiro e fevereiro de 2009". | Veja [Informações de conjunto de dados](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Dados de serviço online

| Fonte de dados | Sobre os dados | Sobre os arquivos |
|---|---|---|
| [Arquivo morto do GitHub](https://www.githubarchive.org/) | "O Arquivo morto do GitHub é um projeto para gravar a linha do tempo pública do GitHub [de eventos], arquivá-la e torná-la facilmente acessível para análise posterior“. | Baixe arquivos mortos de eventos codificados em JSON no formato .gz (Gzip) de um cliente Web. |
| [Dados de atividade do GitHub do projeto GHTorrent](http://ghtorrent.org/) | "O projeto GHTorrent [é] como uma tentativa de criar um espelho dimensionável, consultável e offline de dados oferecidos pela API REST do GitHub. O GHTorrent monitora a linha do tempo de eventos públicos do GitHub. Para cada evento, ele recupera seu conteúdo e suas dependências, exaustivamente“. | Os despejos de banco de dados MySQL estão no formato CSV. |
| [Despejo de dados do Stack Overflow](https://archive.org/details/stackexchange) | "Este é um despejo anônimo de todo o conteúdo enviados por usuários na rede Stack Exchange [incluindo o Stack Overflow]". | "Cada site [como o Stack Overflow] é formatado como um arquivo separado que consiste em arquivos XML compactados com o 7-zip usando a compactação bzip2. Cada arquivo de site inclui publicações, usuários, votos, comentários, PostHistory e PostLinks“. |
