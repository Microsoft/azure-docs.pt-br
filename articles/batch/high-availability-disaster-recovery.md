---
title: Alta disponibilidade e recuperação de desastre
description: Aprenda a projetar seu aplicativo de Lote para uma interrupção regional.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830999"
---
# <a name="design-your-batch-application-for-high-availability"></a>Projete seu aplicativo do lote para alta disponibilidade

O lote do Azure está disponível em todas as regiões do Azure, mas quando uma conta do lote é criada, ela deve ser associada a uma região específica. Todas as operações para a conta do Lote então se aplicam a essa região. Por exemplo, pools e VMs (máquinas virtuais) associadas são criadas na mesma região que a conta do Lote.

Ao criar um aplicativo que usa o Lote, você deve considerar a possibilidade de o Lote não está disponível em uma região. É possível encontrar uma situação rara em que há um problema com a região como um todo, o serviço do lote inteiro na região ou sua conta do lote específica.

Se o aplicativo ou solução que estiver usando o Lote sempre precisar estar disponível, ele deverá ser projetado para failover em outra região ou sempre ter a carga de trabalho dividida entre duas ou mais regiões. As duas abordagens exigem pelo menos duas contas do Lote, estando cada conta localizada em uma região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Várias contas do lote em várias regiões

O uso de várias contas do lote em várias regiões permite que seu aplicativo continue em execução se uma conta do lote em uma região ficar indisponível. Se seu aplicativo precisar ser altamente disponível, ter várias contas é especialmente importante.

Em alguns casos, os aplicativos podem ser projetados para usar intencionalmente duas ou mais regiões. Por exemplo, quando você precisar de uma quantidade considerável de capacidade, pode ser necessário usar várias regiões para lidar com um aplicativo de larga escala ou atender crescimento futuro. Esses aplicativos também precisarão de várias contas do lote (uma por região usada).

## <a name="design-considerations-for-providing-failover"></a>Considerações de design para fornecer failover

Ao fornecer a capacidade de failover para uma região alternativa, todos os componentes em uma solução precisam ser considerados; Não é suficiente simplesmente ter uma segunda conta do lote. Por exemplo, na maioria dos aplicativos de Lote, uma conta de armazenamento do Azure é necessária, sendo que a conta de armazenamento e a conta do Lote devem estar na mesma região para um desempenho aceitável.

Considere os seguintes pontos ao projetar uma solução que pode fazer failover:

- Crie previamente todas as contas necessárias em cada região, como a conta do Lote e a conta de armazenamento. Geralmente, não há nenhuma cobrança por ter contas criadas e as cobranças se acumulam somente quando a conta é usada ou quando os dados são armazenados.
- Verifique se as [cotas](batch-quota-limit.md) apropriadas estão definidas em todas as contas antes do tempo, para que você possa alocar o número necessário de núcleos usando a conta do lote.
- Use modelos de e/ou scripts para automatizar a implantação do aplicativo em uma região.
- Mantenha dados de referência e binários de aplicativo atualizado em todas as regiões. Permanecer atualizado garantirá que a região possa ser colocada online rapidamente sem necessidade de esperar o upload e a implantação de arquivos. Por exemplo, se um aplicativo personalizado para instalar em nós do pool for armazenado e referenciado usando pacotes de aplicativos de Lote, quando uma nova versão do aplicativo for produzida, ela deverá ser carregada para cada conta do Lote e referenciada pela configuração do pool (ou tornar a nova versão a versão padrão).
- No aplicativo que chama o lote, o armazenamento e quaisquer outros serviços, facilite a troca de clientes ou a carga em regiões diferentes.
- Considere a possibilidade de alternar com frequência para uma região alternativa como parte da operação normal. Por exemplo, com duas implantações em regiões separadas, alterne para a região alternativa todos os meses.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como criar contas do Lote com o [portal do Azure](batch-account-create-portal.md), a [CLI do Azure](./scripts/batch-cli-sample-create-account.md), o [PowerShell](batch-powershell-cmdlets-get-started.md) ou a [API de gerenciamento do Lote](batch-management-dotnet.md).
- Saiba mais sobre as [cotas padrão associadas a uma conta do lote](batch-quota-limit.md) e como as cotas podem ser aumentadas.
