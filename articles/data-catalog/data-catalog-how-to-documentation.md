---
title: Como documentar fontes de dados no Catálogo de Dados do Azure
description: Artigo de instruções que destaca como documentar ativos de dados no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 21168e403ffa8fb1472a5bfcac1fcd2079a52e2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674913"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Como documentar fontes de dados no Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução
**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de Dados do Azure** ajuda as pessoas a descobrir, *entender* e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes.

Quando uma fonte de dados é registrada no **Catálogo de Dados do Azure**, seus metadados são copiados e indexados pelo serviço, mas a história não para por aí. **Catálogo de Dados do Azure** também permite que os usuários forneçam sua própria documentação completa que pode descrever o uso e cenários comuns para a fonte de dados.

Em [Como anotar fontes de dados](data-catalog-how-to-annotate.md), você aprenderá que especialistas que conhecem a fonte de dados podem fazer anotações nela com marcas e uma descrição. O portal do **Catálogo de Dados do Azure** inclui um editor de texto avançado para que os usuários possam documentar totalmente contêineres e ativos de dados. O editor inclui formatação de parágrafo, como cabeçalhos, formatação de texto, listas com marcadores, listas numeradas e tabelas.

Marcas e descrições são ótimas para anotações simples. No entanto, para ajudar os consumidores de dados a entender melhor o uso de uma fonte de dados e cenários de negócios para uma fonte de dados, um especialista pode fornecer documentação completa e detalhada. É fácil documentar uma fonte de dados. Selecione um ativo de dados ou um contêiner e escolha **Documentação**.

![Guia de documentação em um catálogo de dados](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentar ativos de dados
O benefício da documentação do **Catálogo de Dados do Azure** permite que você use seu Catálogo de Dados como um repositório de conteúdo para criar uma narrativa completa de seus ativos de dados. Você pode explorar o conteúdo detalhado que descreve contêineres e tabelas. Se já tem conteúdo em outro repositório de conteúdo, como o SharePoint ou um compartilhamento de arquivos, você pode adicionar ao ativo links de documentação para fazer referência a esse conteúdo existente. Esse recurso torna os documentos existentes mais detectáveis.

> [!NOTE]
> A documentação não está incluída no índice de pesquisa.
>

![Guia de documentação e hiperlink para link da Web](media/data-catalog-documentation/data-catalog-documentation2.png)

O nível de documentação pode variar desde a descrição das características e do valor de um contêiner de ativos de dados até uma descrição detalhada do esquema de tabela em um contêiner. O nível da documentação fornecido deve ser orientado por suas necessidades comerciais. Porém, em geral, aqui estão alguns prós e contras para a documentação de ativos de dados:

* Documentar apenas um contêiner: todo o conteúdo está em um local, mas talvez não tenha os detalhes necessários para que os usuários a tomem uma decisão embasada.
* Documentar apenas as tabelas: o conteúdo é específico para o objeto, mas os usuários têm vários lugares para documentos.
* Documentar tabelas e contêineres: essa é uma abordagem mais abrangente, mas pode exigir mais manutenção dos documentos.

## <a name="summary"></a>Resumo
A documentação de fontes de dados com o **Catálogo de Dados do Azure** pode criar uma narrativa sobre seus ativos de dados com todos os detalhes necessários.  Usando links, você pode vincular ao conteúdo armazenado em um repositório de conteúdo existente, que reúne os documentos e os ativos de dados existentes. Depois que os usuários descobrirem ativos de dados apropriados, eles poderão ter um conjunto completo de documentação.
