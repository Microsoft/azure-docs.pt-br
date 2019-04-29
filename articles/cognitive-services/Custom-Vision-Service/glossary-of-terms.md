---
title: Glossário de termos - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Glossário de termos para o Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 3a34af77a2806ceb56e939e2b153f2e68bba61cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605158"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Glossário de termos para o Serviço de Visão Personalizada

Estes são alguns termos comumente usados no serviço de visão personalizada:

## <a name="classifier"></a>Classificador

Um classificador é um modelo que você cria com o Serviço de Visão Personalizada usando algumas imagens de treinamento. Depois de concluir o treinamento de um novo classificador, você receberá um ponto de extremidade de avaliação (HTTPS) que poderá ser adicionado ao aplicativo. Cada classificador criado está localizado no próprio projeto e você pode visualizar todos os projetos após ter feito login.

## <a name="domain"></a>Domínio

Ao criar um projeto, você escolhe um "domínio" para o projeto. O domínio otimiza um classificador para um tipo específico de objeto em suas imagens. Por exemplo, se seu caso for classificar entre imagens de torta de maçã e imagens de bolo de cenoura, escolha o domínio "Comida". Se você não tiver certeza de qual domínio escolher, escolha o domínio "Genérico".

- **O domínio Comida.** Otimizado para pratos que você veria no cardápio de um restaurante. Não foi otimizado para reconhecer frutas ou legumes individuais. Se você quiser classificar fotografias de frutas ou legumes individuais, use o domínio Genérico para essa finalidade.
- **O domínio Ponto de Referência.** Otimizado para pontos de referência reconhecidos, tanto naturais quanto artificiais. Este domínio funciona melhor quando o ponto de referência está claramente visível na fotografia, mesmo se o ponto de referência estiver um pouco obstruído por um grupo de pessoas posando para a foto na frente dele.
- **O domínio Varejo.** Otimizado para classificação de imagens em um catálogo de compra ou um site de compra. Se desejar alta precisão ao classificar vestidos, calças, camisas, etc., use o domínio Varejo.
- **O domínio Geral.** Adequado para uma ampla variedade de tarefas de classificação de imagens.

Os modelos gerados por **domínios compactos** podem ser exportados com a funcionalidade de exportação de iteração. Eles são otimizados para as restrições de classificação em tempo real em dispositivos móveis. Classificadores compilados com um domínio compacto podem ser ligeiramente menos precisos que um domínio padrão com a mesma quantidade de dados de treinamento. A desvantagem é que eles são pequenos o suficiente para serem executados localmente quase em tempo real. 

## <a name="evaluation"></a>Avaliação

Depois de ter treinado seu classificador, você pode enviar qualquer imagem para avaliação usando o ponto de extremidade https que foi gerado automaticamente. O classificador retornará um conjunto de marcas previstas, em ordem de confiança.

## <a name="iteration"></a>Iteração

Sempre que você treinar ou treinar novamente seu classificador, criará uma nova iteração do modelo. Mantemos várias iterações antigas para que você possa comparar seu progresso ao longo do tempo. Você pode excluir as iterações que já não são mais úteis. Lembre-se de que a exclusão de uma iteração é permanente e que também excluirá todas as imagens ou alterações exclusivas dessa iteração. 

## <a name="precision"></a>Precisão

Quando você classifica uma imagem, qual é a probabilidade de seu classificador classificar corretamente a imagem? De todas as imagens usadas para treinar o classificador (cachorros e pôneis), qual percentagem o modelo identificou corretamente? 99 marcas corretas em 100 imagens oferece uma precisão de 99%.

## <a name="predictions"></a>Previsões

Como seu classificador aceita novas imagens para classificar, ele armazena as imagens para você. Você pode usar essas imagens para melhorar a precisão do classificador marcando corretamente as imagens incorretas. Em seguida, você pode usar essas novas imagens para treinar novamente seu classificador.

## <a name="recall"></a>Recuperação

De todas as imagens que deveriam ter sido classificadas corretamente, quantas delas seu classificador identificou corretamente? Uma recolha de 100% significaria que, se houvesse 38 imagens de cachorros nas imagens usadas para treinar o classificador, 38 delas teriam sido localizadas pelo classificador.

## <a name="settings"></a>Configurações

Há dois tipos de configurações, as configurações no nível do projeto e no nível do usuário.

- Configurações no nível do projeto:
  
  As configurações no nível do projeto se aplicam a um projeto ou classificador. Elas incluem:

   - Domínio do projeto
   - Nome do projeto
   - Descrição do projeto
   - Uso:
      - Número de imagens de treinamento
      - Número de marcas criadas
      - Número de iterações criadas

- Configurações no nível do usuário: 
   - Chaves de assinatura: uma para Treinamento, uma para Avaliação/Previsão.
   - Uso:
      - Número de projetos criados
      - Número de chamadas de API de Avaliação/Previsão feitas.

## <a name="tags"></a>Marcas

Use marcas para rotular os objetos em suas imagens de treinamento. Se você estiver criando um classificador para identificar cachorros e pôneis, adicione uma marca "cachorro" às imagens que contenham cachorros, uma marca "pônei" às imagens que contenham pôneis e uma marca "cachorro" e "pônei" às imagens que contenham um cachorro e um pônei.

## <a name="training-image"></a>Imagem de treinamento

Para criar um classificador de alta precisão, o Serviço de Visão Personalizada precisa de várias imagens de treinamento. Uma imagem de treinamento é uma foto da imagem que você deseja que o Serviço de Visão Personalizada classifique. Por exemplo, para classificar laranjas, você precisaria carregar várias imagens de laranjas para o Serviço de Visão Personalizada para permitir que o serviço criasse um classificador que conseguisse reconhecer Laranjas. Recomendamos ter pelo menos 30 imagens por marca.

## <a name="workspace"></a>Workspace

Seu workspace contém todas as suas imagens de treinamento e reflete todas as alterações da iteração mais recente, como imagens removidas ou adicionadas. Ao treinar seu classificador, você cria uma nova iteração do classificador usando as imagens presentes em seu Workspace.