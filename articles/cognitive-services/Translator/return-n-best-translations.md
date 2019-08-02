---
title: Retornar traduções N-Best - API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Retorne as traduções N-Best com a API de Tradução de Texto da Microsoft.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 7aec6bb81f3ca99ead744767668c2e4f1d1d4d53
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594892"
---
# <a name="how-to-return-n-best-translations"></a>Como retornar traduções N-Best

> [!NOTE]
> Este método foi preterido. Ele não está disponível na versão V3.0 da API de Tradução de Texto.

Os métodos GetTranslations() e GetTranslationsArray() da API do Microsoft Translator incluem um sinalizador Booliano "IncludeMultipleMTAlternatives" opcional.
O método retornará até maxTranslations alternativas em que o delta é fornecido na lista N-Best do mecanismo tradutor.

A assinatura é:

**Sintaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de Autorização for usado, deixe o campo appid vazio, caso contrário, especifique uma cadeia de caracteres contendo "token de portador" + " " + token de acesso.|
| texto | **Necessário** Uma cadeia de caracteres que representa o texto a ser traduzido. O tamanho do texto não deve exceder dez mil caracteres.|
| from | **Necessário** Uma cadeia de caracteres que representa o código de idioma do texto a ser traduzido. |
| para | **Necessário** Uma cadeia de caracteres que representa o código de idioma para o qual o texto será traduzido. |
| maxTranslations | **Necessário** Um inteiro que representa o número máximo de traduções para retornar. |
| opções | **Opcional** Um objeto TranslateOptions que contém os valores listados abaixo. Eles são todos opcionais e usam como padrão as configurações mais comuns.

* Categoria: A única opção padrão compatível é "geral".
* ContentType: A única opção compatível é "text/plain", que é a opção padrão.
* Estado: O estado do usuário para ajudar a correlacionar solicitação e resposta. Os mesmos conteúdos serão retornados na resposta.
* IncludeMultipleMTAlternatives: o sinalizador para determinar se é preciso retornar mais de uma alternativa do mecanismo de MT. O padrão é false e inclui somente uma alternativa.

## <a name="ratings"></a>Classificações
As classificações são aplicadas da seguinte maneira: A melhor tradução automática tem uma classificação de 5.
As alternativas de tradução (N-Best) geradas automaticamente têm uma classificação 0 e um grau de correspondência 100.

## <a name="number-of-alternatives"></a>Número de alternativas
O número de alternativas retornado é de até maxTranslations, mas pode ser menor.

## <a name="language-pairs"></a>Pacote de idiomas
Essa funcionalidade não está disponível para traduções entre chinês simplificado e tradicional, em ambas as direções. Ele está disponível para todos os outros pares de idiomas com suporte do Microsoft Translator.
