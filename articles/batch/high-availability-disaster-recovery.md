---
title: Alta disponibilidade e recuperação de desastres – Lote do Azure | Microsoft Docs
description: Aprenda a projetar seu aplicativo de Lote para uma interrupção regional
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549745"
---
# <a name="design-your-application-for-high-availability"></a>Projetar aplicativo para a alta disponibilidade

O Lote do Azure é um serviço regional. O Lote está disponível em todas as regiões do Azure, porém, quando uma conta do Lote é criada, ela deve ser associada a uma região. Todas as operações para a conta do Lote então se aplicam a essa região. Por exemplo, pools e VMs (máquinas virtuais) associadas são criadas na mesma região que a conta do Lote.

Ao criar um aplicativo que usa o Lote, você deve considerar a possibilidade de o Lote não está disponível em uma região. É possível encontrar uma rara situação em que há um problema com a região como um todo, todo o Lote de serviço na região ou um problema com sua conta específica do Lote.

Se o aplicativo ou solução que estiver usando o Lote sempre precisar estar disponível, ele deverá ser projetado para failover em outra região ou sempre ter a carga de trabalho dividida entre duas ou mais regiões. As duas abordagens exigem pelo menos duas contas do Lote, estando cada conta localizada em uma região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Várias contas do lote em várias regiões

Usar várias contas do Lote em várias regiões possibilita que o aplicativo continue em execução em caso de não disponibilidade de uma conta do Lote em outra região. Usar várias contas é especialmente importante se o aplicativo precisa estar altamente disponível.

Em alguns casos, um aplicativo pode ser projetado para sempre usar duas ou mais regiões. Por exemplo, quando você precisar de uma quantidade considerável de capacidade, pode ser necessário usar várias regiões para lidar com um aplicativo de larga escala ou atender crescimento futuro.

## <a name="design-considerations-for-providing-failover"></a>Considerações de design para fornecer failover

Um ponto importante a considerar ao fornecer a capacidade de fazer failover para uma região alternativa é que todos os componentes em uma solução precisam ser levados em conta. Não é suficiente simplesmente ter uma segunda conta d Lote. Por exemplo, na maioria dos aplicativos de Lote, uma conta de armazenamento do Azure é necessária, sendo que a conta de armazenamento e a conta do Lote devem estar na mesma região para um desempenho aceitável.

Considere os seguintes pontos ao projetar uma solução que pode fazer failover:

- Crie previamente todas as contas necessárias em cada região, como a conta do Lote e a conta de armazenamento. Muitas vezes, não há nenhuma cobrança para criar contas, somente quando há dados armazenados ou a conta é usada.
- Verifique se as cotas são definidas nas contas com antecedência para que você possa alocar o número necessário de núcleos usando a conta do Lote.
- Use modelos de e/ou scripts para automatizar a implantação do aplicativo em uma região.
- Mantenha dados de referência e binários de aplicativo atualizado em todas as regiões. Permanecer atualizado garantirá que a região possa ser colocada online rapidamente sem necessidade de esperar o upload e a implantação de arquivos. Por exemplo, se um aplicativo personalizado para instalar em nós do pool for armazenado e referenciado usando pacotes de aplicativos de Lote, quando uma nova versão do aplicativo for produzida, ela deverá ser carregada para cada conta do Lote e referenciada pela configuração do pool (ou tornar a nova versão a versão padrão).
- No aplicativo que está chamando Lote, armazenamento e quaisquer outros serviços, alterne facilmente clientes ou carga para a região diferente.
- Uma prática recomendada para garantir o sucesso de um failover é mudar com frequência para uma região alternativa como parte da operação normal. Por exemplo, com duas implantações em regiões separadas, mude para a região alternativa todos os meses.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como criar contas do Lote com o [portal do Azure](batch-account-create-portal.md), a [CLI do Azure](cli-samples.md), o [PowerShell](batch-powershell-cmdlets-get-started.md) ou a [API de gerenciamento do Lote](batch-management-dotnet.md).
- Cotas padrão estão associadas a uma conta do Lote. [Este artigo](batch-quota-limit.md) detalha os valores de cota padrão e descreve como as cotas podem ser aumentadas.
