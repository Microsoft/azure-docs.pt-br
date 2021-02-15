---
title: O que é o Azure Resource Mover?
description: Saiba mais sobre o Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653248"
---
# <a name="what-is-azure-resource-mover"></a>O que é o Azure Resource Mover?

Este artigo fornece uma visão geral do serviço Azure Resource Mover. O Resource Mover ajuda você a mover os recursos do Azure entre regiões do Azure.

Você pode mover recursos para diferentes regiões do Azure para:

- **Alinhar-se a um lançamento na região**: mover recursos para uma região do Azure recém-introduzida que não estava disponível anteriormente.
- **Fazer o alinhamento de serviços/recursos**: mover recursos para aproveitar os serviços ou recursos que estão disponíveis em uma região específica.
- **Dar uma resposta aos desenvolvimentos de negócios**: mover recursos para uma região em resposta a alterações comerciais, como fusões ou aquisições.
- **Fazer o alinhamento para proximidade**: mover recursos para uma região local para sua empresa.
- **Cumprir os requisitos de dados**: mover recursos para se alinhar aos requisitos de residência de dados ou às necessidades de classificação de dados.
- **Dar uma resposta aos requisitos de implantação**: mover os recursos que foram implantados com erro ou migre em resposta às necessidades de capacidade.
- **Dar uma resposta ao encerramento**: mover recursos porque uma região foi encerrada.

> [!IMPORTANT]
> O Azure Resource Mover está em versão prévia pública no momento.

## <a name="why-use-resource-mover"></a>Por que usar o Resource Mover?

O Resource Mover fornece:

- Um único hub para mover recursos entre regiões.
- Tempo e complexidade de movimentação reduzidos. Tudo o que você precisa está em um único local.
- Uma experiência simples e consistente para mover os diferentes tipos de recursos do Azure.
- Uma maneira fácil de identificar dependências entre os recursos que você deseja mover. Isso ajuda a mover os recursos relacionados em conjunto, para que tudo funcione conforme o esperado na região de destino após a movimentação.
- Se você quiser excluí-los após a movimentação, realize a limpeza automática de recursos na região de origem.
- Testes. Você pode experimentar uma movimentação e depois descartá-la caso não queira fazer uma movimentação completa.

## <a name="move-across-regions"></a>Mover entre regiões

Para mover recursos entre regiões, selecione os recursos que deseja mover. O Resource Mover valida esses recursos e resolve as dependências que eles têm de outros recursos. Se houver dependências, você terá algumas opções:
- Mova os recursos dependentes para a região de destino.
- Não mova os recursos dependentes, mas use recursos equivalentes na região de destino.

Depois que todas as dependências forem resolvidas, o Resource Mover guiará você em um processo simples de movimentação.

1. Você começa uma movimentação inicial.
2. Após a movimentação inicial, você poderá decidir se deseja confirmar e concluir a movimentação ou descartá-la.
3. Após a conclusão da movimentação, você poderá decidir se deseja excluir os recursos no local de origem.

Você pode mover recursos entre regiões no hub do Resource Mover ou de um grupo de recursos. [Saiba mais](select-move-tool.md) 

## <a name="what-resources-can-i-move-across-regions"></a>Quais recursos posso mover entre regiões?

Usando o Resource Mover, no momento, você pode mover os seguintes recursos entre regiões:

- VMs do Azure e discos associados
- NICs
- Conjuntos de disponibilidade 
- Redes virtuais do Azure 
- Endereços IP públicos
- Grupos de segurança de rede (NSGs):
- Balanceadores de carga internos e públicos 
- Bancos de dados SQL do Azure e pools elásticos


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e sobre o processo de movimentação.
