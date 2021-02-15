---
title: O que mudar para a cota de volume rígido significa para seu serviço de Azure NetApp Files | Microsoft Docs
description: Descreve a alteração para usar a cota de volume rígido, como planejar a alteração e como monitorar e gerenciar capacidades.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: b173342c1c384213e88f216334b5e03cd8b7bea7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374483"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>O que mudar para a cota de volume rígido significa para seu serviço de Azure NetApp Files

Desde o início do serviço, Azure NetApp Files tem usado um provisionamento de pool de capacidade e um mecanismo de aumento automático. Os volumes de Azure NetApp Files são provisionados de thin em um pool de capacidade provisionada, de uma camada e tamanho selecionados. Os tamanhos de volume (cotas) são usados para fornecer desempenho e capacidade, e as cotas podem ser ajustadas imediatamente a qualquer momento. Esse comportamento significa que, atualmente, a cota de volume é uma alavanca de desempenho usada para controlar a largura de banda para o volume. Atualmente, a subposição de pools de capacidade aumenta automaticamente quando a capacidade é preenchida.   

> [!IMPORTANT] 
> O comportamento de Azure NetApp Files de provisionamento de pool de volume e capacidade será alterado para um mecanismo *controlável* e *manual* . **A partir de 1º de abril de 2021, os tamanhos de volume (cota) gerenciarão o desempenho da largura de banda, bem como a capacidade provisionada, e os pools de capacidade subjacentes não serão mais ampliados automaticamente.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Motivos para a alteração da cota de volume rígido

Muitos clientes indicaram três desafios principais com o comportamento *inicial* :
* Os clientes da VM verão a capacidade do provisionamento dinâmico (100 TiB) de qualquer volume determinado ao usarem o espaço do sistema operacional ou as ferramentas de monitoramento de capacidade, fornecendo visibilidade de capacidade do lado do cliente ou do aplicativo imprecisa.
* Os proprietários de aplicativos não teriam nenhum controle sobre o espaço do pool de capacidade provisionado (e o custo associado), devido ao comportamento de aumento automático do pool de capacidade. Essa situação é complicada em ambientes em que "processos de execução" podem preencher rapidamente e aumentar a capacidade e o custo provisionados.
* Os clientes desejam ver e manter uma correlação direta entre o tamanho do volume (cota) e o desempenho. Com o comportamento atual de (implícito) sobrecarregar um volume (capacidade-inteligente) e um aumento automático de pool, os clientes não têm uma correlação direta, até que a cota de volume tenha sido configurada ou redefinida ativamente. 

Muitos clientes solicitaram o controle direto sobre a capacidade provisionada. Eles querem controlar e balancear a capacidade e a utilização do armazenamento. Eles também desejam controlar o custo, juntamente com a visibilidade do lado do cliente e do aplicativo de capacidade disponível, usada e provisionada e o desempenho de seus volumes de aplicativos. 

## <a name="what-is-the-volume-hard-quota-change"></a>Qual é a alteração da cota de volume rígido   

Com a alteração da cota de volume rígido, os volumes de Azure NetApp Files não serão mais provisionados em (o máximo) 100 TiB. Os volumes serão provisionados no tamanho real configurado (cota). Além disso, os pools de capacidade de subposição não aumentarão mais automaticamente após atingir o consumo de capacidade total. Essa alteração refletirá o comportamento como o Azure Managed disks, que também são provisionados no estado em que se encontram, sem aumento de capacidade automática.

Por exemplo, considere um volume Azure NetApp Files configurado em um tamanho de 1 TiB (cota) em um pool de capacidade de nível de serviço de 4 TiB de ultradesempenho. Um aplicativo está gravando dados continuamente no volume.

O comportamento *inicial* :  
* Largura de banda esperada: 128 MiB/s
* Capacidade total utilizável (e visível do cliente): 100 TiB   
    Você não poderá gravar mais dados no volume além desse tamanho.
* Pool de capacidade: aumenta automaticamente com 1 incrementos de TiB quando ele está cheio.
* Alteração de cota de volume: somente altera o desempenho (largura de banda) do volume. Ele não altera a capacidade utilizável ou visível do cliente.

O comportamento *alterado* :  
* Largura de banda esperada: 128 MiB/s
* Capacidade total utilizável (e visível do cliente): 1 TiB você não poderá gravar mais dados no volume além desse tamanho.
* Pool de capacidade: permanece 4 TiB de tamanho e não aumenta automaticamente. 
* Alteração de cota de volume: altera o desempenho (largura de banda) e o cliente visíveis ou capacidade utilizável do volume.

Você precisa monitorar proativamente a utilização de Azure NetApp Files volumes e pools de capacidade. Você precisa alterar de forma intencional o volume e a utilização do pool para consumo de fechamento para completo. Azure NetApp Files continuará a permitir [operações de redimensionamento de volume e capacidade em funcionamento](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Como colocar em operação a alteração da cota de volume rígido

Esta seção fornece orientação sobre como colocar em operação a alteração da cota de volume rígido de uma transição suave. Ele também fornece informações para lidar com os volumes e pools de capacidade provisionados no momento, monitoramento contínuo e opções de gerenciamento de capacidade e alertas.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Volumes e pools de capacidade provisionados no momento

Devido à alteração da cota de volume rígido, você deve alterar seu modelo operacional. Os volumes provisionados e os pools de capacidade exigirão o gerenciamento de capacidade contínuo.  Como o comportamento alterado ocorrerá instantaneamente, a equipe de Azure NetApp Files recomenda uma série de medidas corretivas de uso único para volumes e pools de capacidade existentes anteriormente provisionados, conforme descrito nesta seção.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Recomendações de medidas corretivas ou preventivas de uso único  

A alteração da cota de volume rígido resultará em alterações na capacidade provisionada e disponível para volumes e pools provisionados anteriormente. Como resultado, podem ocorrer alguns desafios de alocação de capacidade. Para evitar situações de curto prazo de indisponibilidade de espaço para os clientes, a equipe de Azure NetApp Files recomenda as seguintes medidas corretivas/preventivas a seguir: 

* **Tamanhos de volume provisionados**:   
    Redimensione cada volume provisionado para ter o buffer apropriado com base na taxa de alteração e no alerta ou no tempo de retorno de redimensionamento (por exemplo, 20% com base nas considerações de carga de trabalho típicas), com um máximo de 100 TiB (que é o [limite de tamanho do volume](azure-netapp-files-resource-limits.md#resource-limits)). Esse novo tamanho de volume, incluindo a capacidade de buffer, deve ser baseado nos seguintes fatores:
    * Capacidade de volume **provisionada** , caso a capacidade usada seja menor do que a cota de volume provisionada.
    * Capacidade de volume **usada** , caso a capacidade usada seja maior que a cota de volume provisionada.  
    Não haverá nenhum custo adicional para aumentar a capacidade de nível de volume se o pool de capacidade de subposição não precisar ser aumentado. Como efeito dessa alteração, você pode observar um *aumento* de limite de largura de banda para o volume (caso o [tipo de pool de capacidade de QoS automático](azure-netapp-files-understand-storage-hierarchy.md#qos_types) seja usado).

* **Tamanhos de pool de capacidade provisionado**:   
    Depois que os tamanhos de volume forem ajustados, se a soma dos tamanhos dos volumes se tornar maior do que o tamanho do pool de capacidade de hospedagem, o pool de capacidade terá que ser aumentado para um tamanho igual ou maior que a soma dos volumes, com um máximo de 500 TiB (que é o [limite de tamanho do pool de capacidade](azure-netapp-files-resource-limits.md#resource-limits)). A capacidade adicional do pool de capacidade estará sujeita à cobrança de ACR normalmente.

Você deve trabalhar com seus especialistas em Azure NetApp Files para validar seu ambiente, se precisar de ajuda para configurar o monitoramento ou alertas, conforme descrito nas seções abaixo.

### <a name="ongoing-capacity-management"></a>Gerenciamento de capacidade contínua  

Depois de executar as medidas corretivas de uso único, você deve reunir processos contínuos para monitorar e gerenciar a capacidade. As seções a seguir fornecem sugestões e alternativas sobre o monitoramento e gerenciamento de capacidade.

### <a name="monitor-capacity-utilization"></a>Monitorar a utilização da capacidade

Você pode monitorar a utilização da capacidade em vários níveis. 

#### <a name="vm-level-monitoring"></a>Monitoramento em nível de VM 

O nível mais alto de monitoramento (mais próximo ao aplicativo) é de dentro da máquina virtual do aplicativo. Você observará uma alteração no comportamento do relatório de capacidade de dentro do sistema operacional do cliente da VM.

Nos dois cenários a seguir, considere um volume Azure NetApp Files configurado com um tamanho de 1 TiB (cota) em um pool de capacidade de nível de serviço de 4 TiB. 

##### <a name="windows"></a>Windows

Os clientes do Windows podem verificar a capacidade usada e disponível de um volume usando as propriedades da unidade mapeada da rede. Você pode usar a   ->  opção de propriedades da **unidade** do Explorer  ->   .  

Os exemplos a seguir mostram os relatórios de capacidade de volume no Windows *antes* do comportamento alterado:

![Capturas de tela que mostram a capacidade de armazenamento de exemplo de um volume antes da alteração do comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Você também pode usar o `dir` comando no prompt de comando, conforme mostrado abaixo:

![Captura de tela que mostra o uso de um comando para exibir a capacidade de armazenamento de um volume antes da alteração do comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Os exemplos a seguir mostram os relatórios de capacidade de volume no Windows *após* o comportamento alterado:

![Capturas de tela que mostram a capacidade de armazenamento de exemplo de um volume após a alteração do comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

O exemplo a seguir mostra a `dir` saída do comando:  

![Captura de tela que mostra o uso de um comando para exibir a capacidade de armazenamento de um volume após a alteração do comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Os clientes Linux podem verificar a capacidade usada e disponível de um volume usando o [ `df` comando](https://linux.die.net/man/1/df). A `-h` opção mostrará o tamanho, o espaço usado e o espaço disponível no formato legível por humanos, usando os tamanhos de unidade M, G e T.

O exemplo a seguir mostra o relatório de capacidade de volume no Linux *antes* do comportamento alterado:  

![Captura de tela que mostra o uso do Linux para exibir a capacidade de armazenamento de um volume antes da alteração do comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

O exemplo a seguir mostra o relatório de capacidade de volume no Linux *após* o comportamento alterado:  

![Captura de tela que mostra o uso do Linux para exibir a capacidade de armazenamento de um volume após a alteração do comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Configurar alertas usando o ANFCapacityManager

Você pode usar a ferramenta ANFCapacityManager de aplicativos lógicos com suporte da Comunidade para monitorar Azure NetApp Files capacidade e receber alertas personalizados. A ferramenta ANFCapacityManager está disponível na [página do GitHub do ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager).

ANFCapacityManager é um aplicativo lógico do Azure que gerencia as regras de alerta baseadas em capacidade. Ele aumenta automaticamente os tamanhos de volume para impedir que os volumes de Azure NetApp Files sejam executados sem espaço. É fácil implantar e fornece os seguintes recursos de Gerenciamento de Alertas:

* Quando um pool de capacidade Azure NetApp Files ou um volume é criado, o ANFCapacityManager cria uma regra de alerta de métrica com base no limite de porcentagem consumido especificado.
* Quando um pool de capacidade Azure NetApp Files ou volume é redimensionado, o ANFCapacityManager modifica a regra de alerta de métrica com base no limite especificado de porcentagem de capacidade consumida. Se a regra de alerta não existir, ela será criada.
* Quando um pool de capacidade Azure NetApp Files ou volume é excluído, a regra de alerta de métrica correspondente será excluída.

Você pode definir as seguintes configurações de alerta de chave:  

* **Limite total do pool de capacidade** -essa configuração determina o limite consumido que dispara um alerta para os pools de capacidade. Um valor de 90 faria com que um alerta fosse disparado quando o pool de capacidade atingir 90% consumido.
* **% Limite completo do volume** -essa configuração determina o limite consumido que dispara um alerta para volumes. Um valor de 80 faria com que um alerta fosse disparado quando o volume atingir 80% consumido.
* **Grupo de ações existente para notificações de capacidade** – essa configuração é o grupo de ações que será disparado para alertas baseados em capacidade. Essa configuração deve ser criada previamente por você. O grupo de ações pode enviar email, SMS ou outros formatos.

A ilustração a seguir mostra a configuração de alerta:  

![Ilustração que mostra a configuração de alerta usando ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Depois de instalar o ANFCapacityManager, você pode esperar o seguinte comportamento: quando um pool de capacidade Azure NetApp Files ou volume é criado, modificado ou excluído, o aplicativo lógico criará, modificará ou excluirá automaticamente uma regra de alerta de métrica baseada em capacidade com o nome `ANF_Pool_poolname` ou `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Gerenciar a capacidade

Além de monitoramento e alertas, você também deve incorporar uma prática de gerenciamento de capacidade de aplicativo para gerenciar o consumo de capacidade de Azure NetApp Files (aumentado). Quando um volume de Azure NetApp Files ou um pool de capacidade é preenchido, a [capacidade extra pode ser fornecida imediatamente sem interrupção do aplicativo](azure-netapp-files-resize-capacity-pools-or-volumes.md). Esta seção descreve várias maneiras manuais e automatizadas de aumentar o espaço provisionado de volume e capacidade, conforme necessário.
 
#### <a name="manual"></a>Manual 

Você pode usar o portal ou a CLI para aumentar manualmente os tamanhos do volume ou do pool de capacidade. 

##### <a name="portal"></a>Portal 

Você pode [alterar o tamanho de um volume](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) conforme necessário. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume.

1. Na folha Gerenciar Conta do NetApp, clique em **Volumes**.  
2. Clique com o botão direito do mouse no nome do volume que você deseja redimensionar ou clique no `…` ícone no final da linha do volume para exibir o menu de contexto. 
3. Use as opções de menu de contexto para redimensionar ou excluir o volume.   

   ![Captura de tela que mostra as opções do menu de contexto para um volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Captura de tela que mostra a janela atualizar cota do volume.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Em alguns casos, o pool de capacidade de hospedagem não tem capacidade suficiente para redimensionar os volumes. No entanto, você pode [alterar o tamanho do pool de capacidade](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) em incrementos de 1 Tib ou decrementas. O tamanho do pool de capacidade não pode ser inferior a 4 TiB. *Redimensionar o pool de capacidade altera a capacidade adquirida do Azure NetApp Files.*

1. Na folha Gerenciar Conta do NetApp, clique no pool de capacidade que você deseja redimensionar.
2. Clique com o botão direito do mouse no nome do pool de capacidade ou clique no `…` ícone no final da linha do pool de capacidade para exibir o menu de contexto.
3. Use as opções de menu de contexto para redimensionar ou excluir o pool de capacidade.    

   ![Captura de tela que mostra as opções do menu de contexto para um pool de capacidade.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Captura de tela que mostra a janela redimensionar pool.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI ou PowerShell

Você pode usar as [ferramentas da CLI do Azure NetApp files](azure-netapp-files-sdk-cli.md#cli-tools), incluindo o CLI do Azure e Azure PowerShell, para alterar manualmente o tamanho do pool ou do volume de capacidade.  Os dois comandos a seguir podem ser usados para gerenciar Azure NetApp Files recursos de volume e pool:  

* [`az netappfiles pool`](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest&preserve-view=true)
* [`az netappfiles volume`](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true)

Para gerenciar Azure NetApp Files recursos usando CLI do Azure, você pode abrir o portal do Azure e selecionar o link de **Cloud Shell** do Azure na parte superior da barra de menus: 

[![Captura de tela que mostra como acessar Cloud Shell link. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Essa ação abrirá o Azure Cloud Shell:

[![Captura de tela que mostra Cloud Shell janela. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Os exemplos a seguir usam os comandos para [Mostrar](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show&preserve-view=true) e [Atualizar](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-update&preserve-view=true) o tamanho de um volume:
 
[![Captura de tela que mostra o uso do PowerShell para mostrar o tamanho do volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Captura de tela que mostra o uso do PowerShell para atualizar o tamanho do volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Os exemplos a seguir usam os comandos para [Mostrar](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-show&preserve-view=true) e [Atualizar](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-update&preserve-view=true) o tamanho de um pool de capacidade:

[![Captura de tela que mostra o uso do PowerShell para mostrar o tamanho do pool de capacidade. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Captura de tela que mostra o uso do PowerShell para atualizar o tamanho do pool de capacidade. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizado  

Você pode criar um processo automatizado para gerenciar o comportamento alterado.

##### <a name="rest-api"></a>API REST   

A API REST para o serviço Azure NetApp Files define operações HTTP em relação a recursos como a conta do NetApp, o pool de capacidade, os volumes e os instantâneos. A especificação da API REST para Azure NetApp Files é publicada por meio da [página GitHub do Resource Manager do Azure NetApp files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. Você pode encontrar [código de exemplo para uso com APIs REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) no github.

Consulte [desenvolver para Azure NetApp files com a API REST](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>API REST usando o PowerShell  

A API REST para o serviço Azure NetApp Files define operações HTTP em relação a recursos como a conta do NetApp, o pool de capacidade, os volumes e os instantâneos. A [especificação da API REST para Azure NetApp files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) é publicada por meio do github.

Consulte [desenvolver para Azure NetApp files com a API REST usando o PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Gerenciamento de capacidade usando o ANFCapacityManager

ANFCapacityManager é um aplicativo lógico do Azure que gerencia as regras de alerta baseadas em capacidade. Ele aumenta automaticamente os tamanhos de volume para impedir que os volumes de Azure NetApp Files sejam executados sem espaço. Além de enviar alertas, ele pode habilitar o aumento automático dos tamanhos de pool de volume e capacidade para impedir que os volumes de Azure NetApp Files sejam executados sem espaço: 

* Opcionalmente, quando um volume de Azure NetApp Files atinge o limite de porcentagem consumido especificado, a cota de volume (tamanho) será aumentada pelo percentual especificado entre 10-100.  
* Se o aumento do tamanho do volume exceder a capacidade do pool de capacidade que o contém, o tamanho do pool de capacidade também será aumentado para acomodar o novo tamanho do volume.

Você pode definir a seguinte configuração de gerenciamento de capacidade de chave:  

* **Crescimento** automático de aumento de porcentagem do tamanho do volume existente para aumentar automaticamente um volume se ele atingir o **limite de% total** especificado. Um valor de 0 (zero) desativará o recurso de crescimento automático. É recomendável um valor entre 10 e 100.

    ![Captura de tela que mostra a janela definir porcentagem de aumento automático do volume.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Perguntas frequentes 

Esta seção responde a algumas perguntas sobre a alteração da cota de volume rígido. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>O espaço de instantâneo é contabilizado em direção à capacidade utilizável ou provisionada de um volume?

Sim, a capacidade de instantâneo consumida conta em direção ao espaço provisionado no volume. Caso o volume seja executado de forma completa, considere duas opções de correção:

* Redimensione o volume conforme descrito neste artigo.
* Remova os instantâneos mais antigos para liberar espaço no volume de hospedagem.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Essa alteração significa que o comportamento de crescimento automático do volume desaparecerá da Azure NetApp Files?

Um equívoco comum é que Azure NetApp Files *volumes* aumentarão automaticamente após o preenchimento. Os volumes foram provisionados de forma estreita com um tamanho de 100 TiB, independentemente da cota do conjunto real, enquanto o *pool de capacidade* subjacente aumenta automaticamente com incrementos de 1 Tib. Essa alteração irá endereçar o tamanho do *volume* (visível e utilizável) para a cota definida, e os *pools de capacidade* não aumentarão mais automaticamente. Essa alteração resulta em comum espaço preciso do cliente e relatórios de capacidade. Ele evita o consumo de capacidade de "fuga".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Essa alteração tem algum efeito sobre volumes replicados com replicação entre regiões (versão prévia)? 

A cota de volume de hardware não é imposta nos volumes de destino de replicação.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Essa alteração tem algum efeito sobre as métricas disponíveis atualmente no Azure Monitor?

As métricas do portal e as estatísticas de Azure Monitor refletirão com precisão o novo modelo de alocação e utilização.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Essa alteração tem algum efeito sobre os limites de recursos para Azure NetApp Files?

Não há nenhuma alteração nos limites de recursos para Azure NetApp Files além das alterações de cota descritas neste artigo.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Há um exemplo de fluxo de trabalho de ANFCapacityManager?  

Sim. Consulte a [página GitHub de exemplo de fluxo de trabalho de crescimento automático de volume](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Há suporte para a ANFCapacityManager da Microsoft?  

[O aplicativo lógico ANFCapacityManager é fornecido como está e não tem suporte da NetApp ou da Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Você será incentivado a modificar para se ajustar ao seu ambiente ou requisitos específicos. Você deve testar a funcionalidade antes de implantá-la em qualquer ambiente comercialmente crítico ou de produção.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Como posso relatar um bug ou enviar uma solicitação de recurso para ANFCapacityManger?
Você pode enviar bugs e solicitações de recursos clicando em **novo problema** na [página do GitHub ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Próximas etapas
* [Redimensionar um volume ou um pool de capacidade](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Métricas do Azure NetApp Files](azure-netapp-files-metrics.md) 
