---
title: Notas de versão – Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Obtenha as informações mais recentes sobre as novas versões da equipe de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97813489"
---
# <a name="custom-vision-service-release-notes"></a>Notas de versão do Serviço de Visão Personalizada

## <a name="may-2-2019-and-may-10-2019"></a>2 de maio de 2019 e 10 de maio de 2019

- Correções de bugs e aprimoramentos de back-end

## <a name="may-23-2019"></a>23 de maio de 2019

- Experiência de UX do portal aprimorada relacionada às assinaturas do Azure, facilitando a seleção dos diretórios do Azure.

## <a name="april-18-2019"></a>18 de abril de 2019 

- Exportação de detecção de objetos adicionada para o kit de desenvolvimento de IA de visão.
- Ajustes de interface do usuário, incluindo pesquisa de projeto.

## <a name="april-3-2019"></a>3 de abril de 2019

- Limite de número de caixas delimitadoras por imagem aumentado para 200. 
- Correções de bugs, incluindo uma atualização substancial de desempenho para modelos exportados para o TensorFlow. 

## <a name="march-26-2019"></a>26 de março de 2019

- O Serviço de Visão Personalizada entrou em disponibilidade geral no Azure!
- Foi adicionado o recurso de treinamento avançado com um novo back-end de machine learning para um desempenho aprimorado, especialmente para conjuntos de dados desafiadores e para classificação refinada. Com o treinamento avançado, você pode especificar um orçamento de tempo de computação para treinar, e a Visão Personalizada identificará de modo experimental as melhores configurações de treinamento e de aumento. Para iterações rápidas, você pode continuar usando o treinamento rápido já existente.
- APIs 3.0 introduzidas. Anunciada a próxima depreciação de APIs anteriores à 3.0 em 1º de outubro de 2019. Consulte o [início rápido](./quickstarts/image-classification.md) da documentação para obter exemplos de como começar.
- "Iterações padrão" substituídas por Publicar/Cancelar publicação nas APIs 3.0.
- Novos destinos de exportação de modelo foram adicionados. A exportação do Dockerfile foi atualizada para dar suporte ao ARM para Raspberry Pi 3. O suporte de exportação foi adicionado ao [Kit de desenvolvimento de IA de visão](https://visionaidevkit.com/).
- Limite de tags por projeto aumentado para 500 para a camada S0. Limite de imagens por projeto aumentado para 100.000 para a camada S0.
- Remoção do domínio adulto. Ao invés disso, o domínio geral é recomendado.
- [Preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para disponibilidade geral anunciado.  

## <a name="february-25-2019"></a>25 de fevereiro de 2019

- Foi anunciado o fim dos projetos de avaliação limitada gratuita (projetos não associados a um recurso do Azure), uma vez que a mudança da Visão Personalizada para a versão prévia pública do Azure está quase concluída. A partir de 25 de março de 2019, o site do CustomVision.ai somente dará suporte à exibição de projetos associados a um recurso do Azure, como o recurso de Visão Personalizada gratuito. Até o dia 1º de outubro de 2019, o acesso aos projetos de avaliação limitada já existentes continuará sendo possível por meio das APIs de Visão Personalizada. Isso dará tempo para a atualização das chaves de API de todos os aplicativos escritos com a Visão Personalizada. Após o dia 1º de outubro de 2019, todos os projetos de avaliação limitada que não foram transferidos para o Azure serão excluídos.

## <a name="january-22-2019"></a>22 janeiro de 2019

- Suporte adicionado para novas regiões do Azure: Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Oeste da Europa, Norte da Europa, Sudeste da Ásia, Leste da Austrália, Índia Central, Sul do Reino Unido, Leste do Japão e Centro-Norte dos EUA. O suporte continua para o Centro-Sul dos EUA.

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
- Adicionado ao site o recurso "Mover para o Azure", a fim de facilitar a atualização de um projeto de Avaliação Limitada para vincular-se a um Azure. projeto de recursos vinculado (F0 ou S0). Pode ser encontrado na página de Configurações do produto.  
- Adicionada a exportação para ONNX 1.2, para dar suporte à versão de atualização de outubro de 2018 da ML do Windows.
Correções de bugs, incluindo para a exportação de ONNX com caracteres especiais.

## <a name="august-14-2018"></a>14 de agosto de 2018

- Adicionado widget "Iniciar" ao site customvision.ai para orientar os usuários através do treinamento do projeto.
- Melhorias adicionais no pipeline de aprendizado de máquina para beneficiar projetos de vários rótulos (nova camada de perda).

## <a name="june-28-2018"></a>28 de junho de 2018

- Correções de bugs e aprimoramentos de back-end.
- Classificação multiclasse habilitada para projetos em que as imagens têm somente um rótulo. Em Previsões para o modo multiclasse, as probabilidades serão somadas a uma (todas as imagens são classificadas entre as Marcas especificadas).

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

- Inclusão da versão prévia paga e integração no portal do Azure. Os projetos agora podem ser anexados aos recursos do Azure com a camada F0 (Gratuita) ou S0 (Standard). Introdução dos projetos da camada S0, que permite até 100 marcas e 25.000 imagens.
- O back-end é alterado para o parâmetro de normalização/pipeline de aprendizado de máquina. Isso dará aos clientes um melhor controle das compensações de precisão e recall ao ajustar o Limite de Probabilidade. Como parte dessas alterações, o Limite de Probabilidade padrão no portal do CustomVision.ai foi definido para ser 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Exportação para Android (TensorFlow) adicionada, além da exportação para iOS (CoreML) lançada anteriormente. Isso permite que a exportação de um modelo compacto treinado seja executada offline em um aplicativo.
- Adicionados os domínios “compactos” Varejo e Ponto de referência para habilitar a exportação de modelo para esses domínios.
- Lançamento das versões [API de Treinamento 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [API de Previsão 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas oferecem suporte à exportação de modelo, uma nova operação de Previsão que não salva as imagens em “Previsões” e introdução das operações em lote para a API de Treinamento.
- Ajustes de experiência do usuário, incluindo a capacidade de ver qual domínio foi usado para treinar uma iteração.
- Atualização do [SDK e exemplo de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).