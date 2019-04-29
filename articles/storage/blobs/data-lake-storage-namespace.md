---
title: Namespace hierárquico do Azure Data Lake Storage Gen2
description: Descreve o conceito de namespace hierárquico para o Azure Data Lake Storage Gen2
services: storage
author: WenJason
ms.service: storage
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 02/25/2019
ms.author: v-jay
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b423d40884dd9132312e79ba0cbff00b8771b207
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747533"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Namespace hierárquico do Azure Data Lake Storage Gen2

Um mecanismo essencial que permite que o Azure Data Lake Storage Gen2 ofereça o desempenho do sistema de arquivos na escala e pelo preço do armazenamento de objetos é a adição de um **namespace hierárquico**. Isso permite que a coleta de objetos / arquivos em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados, da mesma forma que o sistema de arquivos do computador é organizado. Com o namespace hierárquico habilitado, uma conta de armazenamento se torna capaz de fornecer a escalabilidade e a relação custo-benefício do armazenamento de objetos, com semântica do sistema de arquivos que são familiares aos mecanismos e estruturas de análise.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Os benefícios do namespace hierárquico

Os benefícios a seguir estão associados a sistemas de arquivos que implementam um namespace hierárquico em dados de blob:

- **Manipulação de Diretório Atômico:** os armazenamentos de objetos aproximam uma hierarquia de diretórios ao adotar a convenção incorporar barras (/) ao nome do objeto para denotar segmentos de linha. Embora essa convenção funcione para organizar objetos, a convenção não fornece assistência para ações como mover, renomear ou excluir diretórios. Sem diretórios reais, os aplicativos devem processar potencialmente milhões de blobs individuais para obter tarefas em nível de diretório. Por outro lado, o espaço de nomes hierárquico processa essas tarefas atualizando uma única entrada (o diretório pai).

    Essa otimização dramática é especialmente significativa para muitas estruturas de análise de big data. Ferramentas como Hive, Spark, etc. geralmente gravam a saída em locais temporários e depois renomeiam a localização na conclusão da tarefa. Sem o namespace hierárquico, essa renomeação pode demorar mais do que o próprio processo de análise. Uma menor latência de trabalho é igual ao custo total de propriedade (TCO) mais baixo para cargas de trabalho de análise.

- **Estilo de interface familiar:** os sistemas de arquivos são bem compreendidos pelos desenvolvedores e usuários. Não há necessidade de aprender um novo paradigma de armazenamento quando você migra para a nuvem, pois a interface do sistema de arquivos exposta pelo Data Lake Storage Gen2 é o mesmo paradigma usado pelos computadores, grandes e pequenos.

Um dos motivos pelos quais os repositórios de objetos nunca deram suporte a namespace hierárquico é que um namespace hierárquico limita a escala. No entanto, o namespace hierárquico do Data Lake Storage Gen2 é dimensionado linearmente e não prejudica a capacidade ou o desempenho dos dados.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Quando habilitar o namespace hierárquico

Recomendamos que você habilite o namespace hierárquico para cargas de trabalho de armazenamento projetadas para sistemas de arquivos que manipulam diretórios. Isso inclui todas as cargas de trabalho que são principalmente para processamento de análise. Conjuntos de dados que exigem um alto grau de organização também serão beneficiados ao habilitar o namespace hierárquico.

As razões para habilitar o namespace hierárquico são determinadas por uma análise de TCO. De um modo geral, melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos de computação por menos tempo. A latência de muitas cargas de trabalho pode ser aprimorada devido à manipulação de diretório atômico que é ativada pelo namespace hierárquico. Em muitas cargas de trabalho, o recurso de computação representa> 85% do custo total e, portanto, até mesmo uma redução modesta na latência da carga de trabalho equivale a uma quantidade significativa de economia de TCO. Mesmo nos casos em que a habilitação do namespace hierárquico aumenta os custos de armazenamento, o TCO ainda é reduzido devido a custos de computação reduzidos.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Quando desabilitar o namespace hierárquico

Algumas cargas de trabalho de repositório de objetos podem não obter nenhum benefício ao habilitar o namespace hierárquico. Alguns exemplos dessas cargas de trabalho incluem backups, armazenamento de imagens e outros aplicativos em que a organização de objetos é armazenada separadamente dos próprios objetos (por exemplo, em um banco de dados separado).

## <a name="next-steps"></a>Próximas etapas

- [Crie uma conta de armazenamento](./data-lake-storage-quickstart-create-account.md)
