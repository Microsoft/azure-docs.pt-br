---
title: Atualizar um cluster do Azure Service Fabric
description: Saiba mais sobre como atualizar a versão ou a configuração de um cluster de Service Fabric do Azure — definindo o modo de atualização de cluster, atualizando certificados, adicionando portas de aplicativo, realizando patches do sistema operacional e o que você pode esperar quando as atualizações são executadas.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 028c91f85a6e318f7ea686c1bcd50262eb7c6bf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571021"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Atualizar um cluster do Azure Service Fabric

Para qualquer sistema moderno, oferecer a possibilidade de atualização é fundamental para o sucesso duradouro de seu produto. Um cluster do Azure Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que você pode configurar por conta própria.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de malha que é executada em seu cluster

Verifique se o cluster está sempre executando uma [versão de malha com suporte](service-fabric-versions.md). Cada vez que a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir dessa data. Novas versões são anunciadas no [blog da equipe do Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dias antes da expiração da versão do cluster em execução, um evento de integridade é gerado, colocando seu cluster em um estado de integridade de aviso. O cluster permanecerá em um estado de aviso até você atualize para uma versão do Fabric com suporte.

Você pode definir o cluster para receber atualizações automáticas do Fabric, assim que elas forem liberadas pela Microsoft ou pode optar por selecionar uma versão com suporte do Fabric na qual deseja que o cluster se encontre.  Para saber mais, leia [atualizar a versão do Service Fabric do cluster](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Comportamento de atualização do Fabric durante as atualizações automáticas

A Microsoft mantém o código de fábrica e a configuração executada em um cluster do Azure. Executamos atualizações automáticas monitoradas no software de acordo com a necessidade. Essas atualizações podem ser feitas no código, na configuração ou em ambos. Para garantir que seu aplicativo sofra nenhum impacto ou impacto mínimo devido a essas atualizações, as atualizações são executadas nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: uma atualização é executada usando todas as políticas de integridade do cluster

Durante esta fase, as atualizações realizam um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (para integridade do nó e integridade do aplicativo) são seguidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida e um email será enviado ao proprietário da assinatura. O email contém as seguintes informações:

* Uma notificação de que precisamos reverter uma atualização de cluster.
* Sugestões de ações corretivas, se houver alguma.
* O número de dias (*n*) até a execução da fase 2.

Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Após os *n* dias a partir da data em que o email foi enviado, continuamos para a fase 2.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida. Isso é para evitar enviar muitos emails; o recebimento de um email deve ser visto como uma exceção ao normal. Esperamos que a maioria das atualizações do cluster tenha êxito sem afetar a disponibilidade de seu aplicativo.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: uma atualização é executada usando apenas as políticas de integridade padrão

As políticas de integridade desta fase são definidas de forma que o número de aplicativos íntegros no início da atualização permaneça o mesmo durante o processo de atualização. Assim como na Fase 1, na Fase 2 as atualizações ocorrem em um domínio de atualização por vez, e os aplicativos em execução no cluster continuam em execução sem qualquer tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster em vigor não forem atendidas, a atualização será revertida. Em seguida, um email é enviado ao proprietário da assinatura. O email contém as seguintes informações:

* Uma notificação de que precisamos reverter uma atualização de cluster.
* Sugestões de ações corretivas, se houver alguma.
* O número de dias (n) até a execução da Fase 3.

Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Um lembrete será enviado por email alguns dias antes do término dos n dias. Após os n dias a partir da data de envio do email, prosseguiremos para a Fase 3. Os emails que enviamos na Fase 2 devem ser levados a sério e as ações corretivas devem ser realizadas.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: uma atualização é executada usando políticas de integridade agressivas

Essas políticas de integridade desta fase são destinadas à conclusão da atualização, em vez da integridade dos aplicativos. Pouquíssimas atualizações de cluster chegam a esta fase. Caso seu cluster chegue a esta fase, há uma boa chance de seu aplicativo deixar de ser íntegro e/ou de perder a disponibilidade.

Assim como nas duas outras fases, as atualizações da Fase 3 realizam um domínio de atualização por vez.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Tentamos executar a mesma atualização algumas vezes mais, caso alguma atualização falhe por motivos de infraestrutura. Depois disso, o cluster será marcado para que não receba mais suporte e/ou atualizações.

Um email com essas informações é enviado ao proprietário da assinatura, juntamente com as ações corretivas. Não esperamos que qualquer cluster entre em um estado no qual a Fase 3 falhou.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada bem-sucedida e marcada como concluída. Isso poderá acontecer durante a atualização inicial ou durante qualquer nova execução das atualizações desta fase. Nenhum email de confirmação será enviado após uma execução bem-sucedida.

## <a name="manage-certificates"></a>Gerenciar certificados

O Service Fabric usa [certificados de servidor X.509](service-fabric-cluster-security.md) que você especifica ao criar um cluster para proteger as comunicações entre os nós de cluster e autenticar clientes. Você pode adicionar, atualizar ou excluir certificados de cluster e do cliente no [portal do Azure](https://portal.azure.com) ou usando a CLI do Azure/PowerShell.  Para saber mais, leia [adicionar ou remover certificados](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Abrir portas do aplicativo

Você pode alterar as portas do aplicativo alterando as propriedades do recurso de Balanceador de carga associadas ao tipo de nó. Você pode utilizar o portal do Azure ou pode usar o PowerShell/CLI do Azure. Para obter mais informações, leia [Abrir portas do aplicativo para um cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definir propriedades de nó

Às vezes, você pode querer garantir que determinadas cargas de trabalho sejam executadas apenas em determinados tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, enquanto outras, não. Para cada um dos tipos de nós em um cluster, você pode adicionar propriedades de nós customizados aos nós do cluster. As restrições de posicionamento são as instruções anexadas a serviços individuais que selecionam uma ou mais propriedades do nó. Restrições de posicionamento definem onde os serviços devem ser executados.

Para obter detalhes sobre o uso de restrições de posicionamento, propriedades do nó e como defini-las, leia [propriedades do nó e restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade

Para cada um dos tipos de nó, é possível adicionar métricas de capacidade personalizadas que você deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar carga, confira os documentos do Gerenciador de Recursos do cluster do Service Fabric em [Descrevendo seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [Métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Definir políticas de integridade para atualizações automáticas

Você pode especificar as políticas de integridade personalizadas para atualização do Fabric. Se você tiver configurado o cluster para atualizações automáticas do Fabric, essas políticas serão aplicadas à Fase 1 das atualizações automáticas do Fabric.
Se você tiver configurado o cluster para atualizações manuais do Fabric, essas políticas serão aplicadas cada vez que você selecionar uma nova versão ao disparar o sistema para acionar a atualização do Fabric em seu cluster. Se você não substituir as políticas, os padrões serão usados.

Você pode especificar as políticas de integridade personalizados ou examinar as configurações atuais na folha de "atualização de malha", selecionando as configurações avançadas de atualização. Examine a imagem a seguir com detalhes.

![Gerenciar políticas de integridade personalizadas][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar as configurações do Fabric para seu cluster

Muitas configurações diferentes podem ser personalizadas em um cluster, como o nível de confiabilidade das propriedades de nó e o cluster. Para obter mais informações, leia as [ configurações da malha do cluster do Service Fabric ](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Corrigir o sistema operacional nos nós do cluster

O aplicativo de orquestração de patch (POA) é um aplicativo do Service Fabric que automatiza a correção do sistema operacional em um cluster do Service Fabric sem tempo de inatividade. O [Aplicativo de Orquestração de Patches para Windows](service-fabric-patch-orchestration-application.md) pode ser implantado em seu cluster para instalar os patches de maneira orquestrada, mantendo os serviços disponíveis o tempo todo.

## <a name="next-steps"></a>Próximas etapas

* Saiba como personalizar algumas das [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar o cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md)
* Saiba mais sobre [atualizações de aplicativos](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
