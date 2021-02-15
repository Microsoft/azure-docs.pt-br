---
title: Mover recursos do Azure para outra região
description: Fornece uma visão geral da movimentação de recursos do Azure entre regiões do Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007631"
---
# <a name="moving-azure-resources-across-regions"></a>Movendo recursos do Azure entre regiões

Este artigo fornece informações sobre como mover recursos do Azure entre regiões do Azure.

Regiões geográficas, região e zonas de disponibilidade do Azure formam a base da infraestrutura global do Azure. As regiões [geográficas](https://azure.microsoft.com/global-infrastructure/geographies/) do Azure normalmente contêm duas ou mais [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Uma região é uma área em uma geografia, contendo Zonas de Disponibilidade e vários data centers. 

Depois de implantar recursos em uma região específica do Azure, há vários motivos pelos quais você pode querer mover recursos para uma região diferente.

- **Alinhar a uma região de lançamento**: Mova seus recursos para uma região do Azure introduzida recentemente que não estava disponível anteriormente.
- **Fazer o alinhamento de serviços/recursos**: mover recursos para aproveitar os serviços ou recursos que estão disponíveis em uma região específica.
- **Dar uma resposta aos desenvolvimentos de negócios**: mover recursos para uma região em resposta a alterações comerciais, como fusões ou aquisições.
- **Fazer o alinhamento para proximidade**: mover recursos para uma região local para sua empresa.
- **Atender aos requisitos de dados**: Mova os recursos para se alinhar aos requisitos de residência de dados ou às necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Dar uma resposta aos requisitos de implantação**: mover os recursos que foram implantados com erro ou migre em resposta às necessidades de capacidade. 
- **Responder ao encerramento**: mover recursos devido ao encerramento de regiões.

## <a name="move-resources-with-resource-mover"></a>Mover recursos com o movimentador de recursos

Você pode mover recursos para uma região diferente com o [Azure Resource mover](../../resource-mover/overview.md). O Resource Mover fornece:

- Um único hub para mover recursos entre regiões.
- Tempo e complexidade de movimentação reduzidos. Tudo o que você precisa está em um único local.
- Uma experiência simples e consistente para mover os diferentes tipos de recursos do Azure.
- Uma maneira fácil de identificar dependências entre os recursos que você deseja mover. Isso ajuda a mover os recursos relacionados em conjunto, para que tudo funcione conforme o esperado na região de destino após a movimentação.
- Se você quiser excluí-los após a movimentação, realize a limpeza automática de recursos na região de origem.
- Testes. Você pode experimentar uma movimentação e depois descartá-la caso não queira fazer uma movimentação completa.

Você pode mover recursos para outra região usando dois métodos diferentes:

- **Iniciar a movimentação de recursos de um grupo de recursos**: com esse método, você inicia a mudança de região de dentro de um grupo de recursos. Depois de selecionar os recursos que você deseja mover, o processo continua no Hub do Resource mover, para verificar as dependências de recursos e orquestrar o processo de movimentação. [Saiba mais](../../resource-mover/move-region-within-resource-group.md).
- **Iniciar a movimentação de recursos diretamente do Hub do Resource mover**: com esse método, você inicia o processo de movimentação de região diretamente no Hub. [Saiba mais](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>Suporte para movimentação de região

No momento, você pode usar o movimentador de recursos para mover esses recursos para outra região:

- VMs do Azure e discos associados
- NICs
- Conjuntos de disponibilidade
- Redes virtuais do Azure
- Endereços IP públicos
- Grupos de segurança de rede (NSGs):
- Balanceadores de carga internos e públicos
- Bancos de dados SQL do Azure e pools elásticos

## <a name="region-move-process"></a>Processo de movimentação de região

O processo real para mover recursos entre regiões depende dos recursos que você está movendo. No entanto, há algumas etapas principais comuns:

1. **Verificar pré-requisitos**: os pré-requisitos incluem certificar-se de que os recursos necessários estão disponíveis na região de destino, verificando se você tem cota suficiente e verificando se sua assinatura pode acessar a região de destino.
2. **Analisar dependências**: seus recursos podem ter dependências de outros recursos. Antes de mover, descubra as dependências para que os recursos movidos continuem a funcionar conforme o esperado após a movimentação.
3. **Preparar para mover**: essas são as etapas que você seguirá em sua região primária antes da movimentação. Por exemplo, talvez seja necessário exportar um modelo de Azure Resource Manager ou iniciar a replicação de recursos da origem para o destino.
4. **Mover os recursos**: como você move os recursos depende do que eles são. Talvez seja necessário implantar um modelo na região de destino ou reprovar os recursos para o destino.
5. **Descartar recursos de destino**: depois de mover os recursos, talvez você queira dar uma olhada nos recursos agora na região de destino e decidir se há algo que você não precisa.
6. **Confirme a movimentação**: depois de verificar os recursos na região de destino, alguns recursos podem exigir uma ação de confirmação final. Por exemplo, em uma região de destino que agora é a região primária, talvez seja necessário configurar a recuperação de desastres para uma nova região secundária. 
7. **Limpar a origem**: por fim, depois que tudo estiver em funcionamento na nova região, você poderá limpar e encerrar os recursos criados para a movimentação e os recursos em sua região primária.



## <a name="next-steps"></a>Próximas etapas

[Saiba mais](../../resource-mover/about-move-process.md) sobre o processo de movimentação no movimentador de recursos.
