---
title: Namespace hierárquico do Azure Data Lake Storage Gen2
description: Descreve o conceito de um namespace hierárquico para Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 26062d73ae4c61af77b15dd2cac0541f2a988d11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912987"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Namespace hierárquico do Azure Data Lake Storage Gen2

Um mecanismo essencial que permite que o Azure Data Lake Storage Gen2 ofereça o desempenho do sistema de arquivos na escala e pelo preço do armazenamento de objetos é a adição de um **namespace hierárquico**. Isso permite que a coleta de objetos / arquivos em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados, da mesma forma que o sistema de arquivos do computador é organizado. Com um namespace hierárquico habilitado, uma conta de armazenamento torna-se capaz de fornecer a escalabilidade e a economia de custo do armazenamento de objetos, com a semântica do sistema de arquivos familiar aos mecanismos e estruturas de análise.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Os benefícios de um namespace hierárquico

Os benefícios a seguir estão associados a sistemas de arquivos que implementam um namespace hierárquico em dados de blob:

- **Manipulação de Diretório Atômico:** os armazenamentos de objetos aproximam uma hierarquia de diretórios ao adotar uma convenção de barras de incorporação (/) no nome do objeto para denotar segmentos de caminho. Embora essa convenção funcione para organizar objetos, a convenção não fornece assistência para ações como mover, renomear ou excluir diretórios. Sem diretórios reais, os aplicativos devem processar potencialmente milhões de blobs individuais para obter tarefas em nível de diretório. Por outro lado, um namespace hierárquico processa essas tarefas atualizando uma única entrada (o diretório pai).

    Essa otimização dramática é especialmente significativa para muitas estruturas de análise de big data. Ferramentas como Hive, Spark, etc. geralmente gravam a saída em locais temporários e depois renomeiam a localização na conclusão da tarefa. Sem um namespace hierárquico, essa renomeação geralmente pode levar mais tempo do que o próprio processo de análise. Uma menor latência de trabalho é igual ao custo total de propriedade (TCO) mais baixo para cargas de trabalho de análise.

- **Estilo familiar de interface:** Os sistemas de arquivos são bem compreendidos pelos desenvolvedores e usuários. Não há necessidade de aprender um novo paradigma de armazenamento quando você migra para a nuvem, pois a interface do sistema de arquivos exposta pelo Data Lake Storage Gen2 é o mesmo paradigma usado pelos computadores, grandes e pequenos.

Um dos motivos pelos quais os repositórios de objetos nunca deram suporte a namespace hierárquico é que um namespace hierárquico limita a escala. No entanto, o namespace hierárquico do Data Lake Storage Gen2 é dimensionado linearmente e não prejudica a capacidade ou o desempenho dos dados.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decidindo se um namespace hierárquico deve ser habilitado

Depois de habilitar um namespace hierárquico em sua conta, você não poderá revertê-lo de volta para um namespace simples. Portanto, considere se faz sentido habilitar um namespace hierárquico com base na natureza de suas cargas de trabalho de armazenamento de objetos.

Algumas cargas de trabalho podem não obter nenhum benefício ao habilitar um namespace hierárquico. Alguns exemplos dessas cargas de trabalho incluem backups, armazenamento de imagens e outros aplicativos em que a organização de objetos é armazenada separadamente dos próprios objetos (por exemplo, em um banco de dados separado). 

Além disso, embora o suporte para recursos de armazenamento de BLOBs e o ecossistema de serviço do Azure continue crescendo, ainda há alguns recursos e serviços do Azure que ainda não têm suporte em contas que têm um namespace hierárquico. Consulte [problemas conhecidos](data-lake-storage-known-issues.md). 

Em geral, recomendamos que você ative um namespace hierárquico para cargas de trabalho de armazenamento projetadas para sistemas de arquivos que manipulam diretórios. Isso inclui todas as cargas de trabalho que são principalmente para processamento de análise. Os conjuntos de dado que exigem um alto grau de organização também se beneficiarão ao habilitar um namespace hierárquico.

Os motivos para habilitar um namespace hierárquico são determinados por uma análise de TCO. De um modo geral, melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos de computação por menos tempo. A latência de várias cargas de trabalho pode ser aprimorada devido à manipulação de diretório atômica habilitada por um namespace hierárquico. Em muitas cargas de trabalho, o recurso de computação representa> 85% do custo total e, portanto, até mesmo uma redução modesta na latência da carga de trabalho equivale a uma quantidade significativa de economia de TCO. Mesmo nos casos em que a habilitação de um namespace hierárquico aumenta os custos de armazenamento, o TCO ainda é reduzido devido a custos de computação reduzidos.

Para analisar as diferenças em preços de armazenamento de dados, preços de transação e preços de reserva de capacidade de armazenamento entre contas que têm um namespace hierárquico plano versus um namespace hierárquico, consulte [preços de Azure data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](../common/storage-account-create.md)