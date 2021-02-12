---
title: Entender os recursos do glossário de negócios no Azure alcance (versão prévia)
description: Este artigo explica o que é o Glossário de negócios no Azure alcance.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551816"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Entender os recursos do glossário de negócios no Azure alcance

Este artigo fornece uma visão geral do recurso de Glossário de negócios no Azure alcance. 

## <a name="business-glossary"></a>Glossário de negócios

Um glossário fornece vocabulário para usuários empresariais.  Ele consiste em termos de negócios que podem estar relacionados entre si e permite que eles sejam categorizados para que possam ser compreendidos em contextos diferentes. Esses termos podem ser mapeados para ativos como um banco de dados, tabelas, colunas etc. Isso ajuda a abstrair o jargão técnico associado aos repositórios de dados e permite que o usuário comercial descubra e trabalhe com dados no vocabulário que é mais familiar para eles.


Um glossário de negócios é uma coleção de termos. Cada termo representa um objeto em uma organização e é muito provável que haja vários termos que representem o mesmo objeto. Um cliente também pode ser chamado de cliente, comprador ou comprador. Esses vários termos têm uma relação entre si. A relação entre esses termos pode ser uma das seguintes:

- Sinônimos – termos diferentes com a mesma definição
- relacionado-nome diferente com definição semelhante

O mesmo termo também pode implicar vários objetos comerciais. É importante que cada termo seja bem definido e claramente compreendido na organização.

## <a name="custom-attributes"></a>Atributos personalizados

O Azure alcance dá suporte a oito atributos prontos para uso para qualquer termo do glossário de negócios:
- Nome
- Definição
- Administradores de dados
- Especialistas em dados
- Acrônimo
- Sinônimos
- Termos relacionados
- Recursos

Esses atributos não podem ser editados ou excluídos. No entanto, esses atributos não são suficientes para definir completamente um termo em uma organização. Para resolver esse problema, o alcance fornece um recurso em que você pode definir atributos personalizados para seu Glossário.

## <a name="term-templates"></a>Modelos de termo

Modelos de termo fornece atributos personalizados de glossário para serem agrupados logicamente no catálogo. O recurso permite agrupar todos os atributos personalizados relevantes em um modelo e, em seguida, aplicar o modelo ao criar o termo do glossário. Por exemplo, todos os atributos personalizados relacionados a finanças, como centro de custo, centro de lucro, código contábil, podem ser agrupados em um modelo de termo de finanças e o modelo de Finanças pode ser usado para criar termos de Glossário financeiro.

Todos os atributos padrão são agrupados em um modelo padrão do sistema. Qualquer modelo de termo que você criar conterá esses atributos juntamente com quaisquer atributos personalizados adicionais criados como parte do processo de criação de modelo.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Glossário vs classificação vs. rótulos de sensibilidade

Enquanto os termos, classificações e rótulos do glossário são anotações em um ativo de dados, cada um deles tem um significado diferente no contexto do catálogo. 

### <a name="glossary"></a>Glossário

Conforme mencionado acima, o termo do glossário de negócios define o vocabulário de negócios para uma organização e ajuda a unir a lacuna entre vários departamentos em sua empresa.

### <a name="classifications"></a>Classificações

As classificações são anotações que podem ser atribuídas a entidades. A flexibilidade das classificações permite usá-las em vários cenários, como:

- Compreendendo a natureza dos dados armazenados nos ativos de dados
- definindo políticas de controle de acesso

Alcance tem mais de 100 classificadores de sistema hoje e você pode definir seus próprios classificadores no catálogo. Como parte do processo de verificação, detectamos automaticamente essas classificações e as aplicamos a ativos de dados e esquemas. No entanto, você pode substituí-los a qualquer momento. As substituições humanas nunca são substituídas por verificações automatizadas.

### <a name="sensitivity-labels"></a>Rótulos de confidencialidade

Rótulos de sensibilidade são um tipo de anotação que permite classificar e proteger os dados da sua organização, sem atrapalhar a produtividade e a colaboração. Os rótulos de sensibilidade são usados para identificar as categorias de tipos de classificação dentro de seus dados organizacionais e agrupar as políticas que você deseja aplicar a cada categoria. O alcance usa os mesmos tipos de informações confidenciais que Microsoft 365, o que permite que você amplie suas políticas de segurança existentes e a proteção em todo o conteúdo e o estado dos dados. Os mesmos rótulos podem ser compartilhados entre Microsoft Office produtos e ativos de dados no alcance.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar modelos de termo](how-to-manage-term-templates.md)
- [Procurar o catálogo de dados no Azure alcance](how-to-browse-catalog.md)
