---
title: Notas de versão – Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 2b0d8b8a86c3105b1bda7fb0d72cbcb72ed82995
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816375"
---
# <a name="custom-vision-service-release-notes"></a>Notas de versão do Serviço de Visão Personalizada

## <a name="march-26-2019"></a>26 de março de 2019

- Serviço de visão personalizada entrou em disponibilidade geral no Azure!
- Adição de recurso avançado de treinamento com uma novo back-end para melhorar o desempenho, especialmente em um desafio de conjuntos de dados e classificação refinada de aprendizado de máquina. Com o treinamento avançado, você pode especificar que um orçamento de tempo de computação para treinamento e de visão personalizada Experimentalmente identificará as melhores configurações de ampliação e de treinamento. Para iterações rápidas, você pode continuar a usar o treinamento rápido existente.
- Introduzido 3.0 APIs. Anunciamos a reprovação próxima das APIs de pré-3.0 em 1 de outubro de 2019. Consulte os inícios rápidos de documentação para [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [nó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), ou [vá](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) para obter exemplos informações sobre como começar a usar.
- Substituído "Default iterações" com o publicar/cancelar a publicação no 3.0 APIs.
- Foram adicionados novos destinos de exportação de modelo. Exportação de Dockerfile foi atualizada para dar suporte a ARM para Raspberry Pi 3. Foi adicionado suporte de exportação para o [Vision AI Dev Kit.](https://visionaidevkit.com/).
- Limite aumentado de marcas por projeto para 500 para camada S0. Limite aumentado de imagens por projeto a 100.000 para camada S0.
- Somente para adultos domínio foi removido. Domínio geral é recomendável em vez disso.
- Anunciado [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para disponibilidade geral.  

## <a name="february-25-2019"></a>25 de fevereiro de 2019

- A fim de avaliação limitada projetos (não associado a um recurso do Azure), anunciado como visão personalizada se aproximar a conclusão da sua mudança para a visualização pública do Azure. A partir de 25 de março de 2019, o site CustomVision.ai dará suporte somente a projetos de exibição associados a um recurso do Azure, como o recurso de visão personalizada gratuito. Por meio de 1 de outubro de 2019, você ainda poderá acessar seus projetos de avaliação limitados existentes por meio das APIs de visão personalizada. Isso dará a você tempo às chaves de API de atualização para todos os aplicativos que você escreveu com visão personalizada. Após 1 de outubro de 2019, quaisquer projetos avaliação limitados, você ainda não mudou para o Azure serão excluídos.

## <a name="january-22-2019"></a>22 janeiro de 2019

- Suporte adicionado para novas regiões do Azure: Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Europa Ocidental, Norte da Europa, Sudeste Asiático, Leste da Austrália, Índia Central, Sul do Reino Unido, Leste do Japão e Centro-Norte dos EUA. O suporte continua para o Centro-Sul dos EUA.

## <a name="december-12-2018"></a>12 de dezembro de 2018

- Suporte para exportação de modelos de Detecção de Objetos (introduzido o Domínio compacto de detecção de objetos).
- Corrigidos diversos problemas de acessibilidade para aprimorar o suporte para navegação por teclado e leitor de tela.
- Atualizações da experiência do usuário para o visualizador de imagens e melhor experiência de marcação para detecção de objetos para uma marcação mais rápida.  
- Atualização do modelo base para o Domínio de detecção de objetos para uma detecção de objetos de melhor qualidade.
- Correções de bugs.

## <a name="november-6-2018"></a>6 de novembro de 2018

- Adicionado suporte para o domínio do logotipo na Detecção de objetos.

## <a name="october-9-2018"></a>9 de outubro de 2018

- A Detecção de Objetos entra em visualização paga. Agora você pode criar projetos da Detecção de Objetos com um recurso do Azure.
- Adicionado ao site o recurso "Mover para o Azure", a fim de facilitar a atualização de um projeto de Avaliação Limitada para vincular-se a um Azure. projeto de recursos vinculados (F0 ou S0.) Você pode encontrar isso na página de configurações do seu produto.  
- Adicionada a exportação para ONNX 1.2, para dar suporte à versão de atualização de outubro de 2018 da ML do Windows.
Correções de bugs, incluindo para exportação ONNX com caracteres especiais.

## <a name="august-14-2018"></a>14 de agosto de 2018

- Adicionado widget "Iniciar" ao site customvision.ai para orientar os usuários através do treinamento do projeto.
- Melhorias adicionais no pipeline de aprendizado de máquina para beneficiar projetos de vários rótulos (nova camada de perda).

## <a name="june-28-2018"></a>28 de junho de 2018

- Correções de bugs e melhorias de back-end.
- Habilitada a classificação multiclasse, para projetos em que as imagens têm exatamente um rótulo. Previsões para o modo de multiclasse, probabilidades totalizarão para um (todas as imagens são classificadas entre suas marcas especificadas).

## <a name="june-13-2018"></a>13 de junho de 2018

- Atualização UX, focada na facilidade de uso e acessibilidade.
- Melhorias no pipeline de aprendizado de máquina para beneficiar projetos de vários rótulos com um grande número de marcas.
- Corrigido o bug na exportação do TensorFlow. Habilitado o controle de versão do modelo exportado, para que as iterações possam ser exportadas mais de uma vez.

## <a name="may-7-2018"></a>7 de maio de 2018

- Introdução da versão prévia do recurso de Detecção de Objetos para projetos de Avaliação Limitada.
- Atualização para APIs 2.0
- Camada S0 expandida para até 250 marcas e 50.000 imagens.
- Melhorias significativas de back-end para o pipeline de aprendizado de máquina para projetos de classificação de imagem. Projetos treinados depois de 27 de abril de 2018 irão se beneficiar dessas atualizações.
- Adicionada a exportação de modelo para ONNX, para uso com o Windows ML.
- Adicionada a exportação de modelo para o Dockerfile. Isso permite que você baixe os artefatos para criar seus próprios contêineres do Windows ou Linux, incluindo um DockerFile, o modelo do TensorFlow e o código de serviço.
- Para modelos recentemente treinados exportados para TensorFlow nos domínios Geral (Compacto) e Ponto de referência (Compacto), os [Valores Médios agora são (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), para manter a consistência em todos os projetos.

## <a name="march-1-2018"></a>1º de março de 2018

- Inserir a visualização paga e integrado no portal do Azure. Os projetos agora podem ser anexados aos recursos do Azure com a camada F0 (Gratuita) ou S0 (Standard). Introdução dos projetos da camada S0, que permite até 100 marcas e 25.000 imagens.
- O back-end é alterado para o parâmetro de normalização/pipeline de aprendizado de máquina. Isso dará aos clientes um melhor controle das compensações de precisão e recall ao ajustar o Limite de Probabilidade. Como parte dessas alterações, o Limite de Probabilidade padrão no portal do CustomVision.ai foi definido para ser 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Adicionada a exportação para o Android (TensorFlow), além de uma exportação lançada anteriormente para iOS (CoreML). Isso permite a exportação de um modelo compacto treinado para ser executado offline em um aplicativo.
- Adicionados os domínios “compactos” Varejo e Ponto de referência para habilitar a exportação de modelo para esses domínios.
- Lançamento das versões [API de Treinamento 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [API de Previsão 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas oferecem suporte à exportação de modelo, uma nova operação de Previsão que não salva as imagens em “Previsões” e introdução das operações em lote para a API de Treinamento.
- Ajustes de experiência do usuário, incluindo a capacidade de ver qual domínio foi usado para treinar uma iteração.
- Atualização do [SDK e exemplo de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).