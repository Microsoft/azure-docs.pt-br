---
title: "Transformação Expandir JSON usando o Azure Machine Learning Workbench"
description: "O documento de referência para a transformação 'Expandir JSON'"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 57f461f9a39e8069654985d86bfe3ae23dd0855a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="expand-json-transformation"></a>Transformação Expandir JSON
A transformação **Expandir JSON** permite que os usuários expandam uma coluna existente que contém o texto JSON válido em várias colunas.

## <a name="how-to-perform-this-transformation"></a>Como executar essa transformação

Siga essas etapas para realizar essa transformação:
1. Selecione a coluna de origem que contém texto JSON.
2. Selecione **Expandir JSON** no menu **Transformações**. Ou, clique com o botão direito do mouse no cabeçalho da coluna de origem e selecione **Expandir JSON** no menu de contexto. 
3. Clique em **OK**. 
 
Novas colunas são adicionadas ao lado da coluna de origem. Essas colunas contêm propriedades do próximo nível da hierarquia no texto JSON. A Chave de Propriedade, se presente, é usada para criar o nome da coluna correspondente. As propriedades aninhadas são preservadas como texto JSON que o usuário pode expandir iterativamente ou descartar conforme necessário.

## <a name="examples"></a>Exemplos

A coluna de origem *Customer* é expandida em duas colunas *Customer.Name* e *Customer.Phone*.

| Cliente                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

