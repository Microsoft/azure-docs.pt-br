---
title: Hotpatch para Windows Server Azure Edition (versão prévia)
description: Saiba como o hotpatch para Windows Server Azure Edition funciona e como habilitá-lo
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 710e6902be6ebe28caaf40fb446e4ee7cd2bf4dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687559"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch para novas máquinas virtuais (visualização)

HotPatching é uma nova maneira de instalar atualizações em novas VMs (máquinas virtuais) do Windows Server Azure Edition que não exigem uma reinicialização após a instalação. Este artigo aborda informações sobre o hotpatch para VMs do Windows Server Azure Edition, que tem os seguintes benefícios:
* Menor impacto na carga de trabalho com menos reinicializações
* Implantação mais rápida de atualizações à medida que os pacotes são menores, são instalados mais rapidamente e facilitam a orquestração de patch com o Gerenciador de atualização do Azure
* Melhor proteção, pois os pacotes de atualização do hotpatch têm como escopo as atualizações de segurança do Windows que são instaladas mais rapidamente sem reinicialização

## <a name="how-hotpatch-works"></a>Como o hotpatch funciona

O hotpatch funciona primeiro estabelecendo uma linha de base com uma Windows Update atualização cumulativa mais recente. Os Hotpatches são lançados periodicamente (por exemplo, na segunda terça-feira do mês) que se baseiam nessa linha de base. Hotpatches conterá atualizações que não exigem uma reinicialização. Periodicamente (começando a cada três meses), a linha de base é atualizada com uma nova atualização cumulativa mais recente.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch exemplo de agendamento.":::

Há dois tipos de linhas de base: linhas de base **planejadas** e linhas de base não **planejadas**.
*  As **linhas de base planejadas** são lançadas em uma cadência regular, com versões hotpatchs entre elas.  As linhas de base planejadas incluem todas as atualizações em uma _atualização cumulativa mais recente_ comparável para esse mês e exigem uma reinicialização.
    * O exemplo de agendamento acima ilustra quatro lançamentos de linha de base planejados em um ano civil (cinco totais no diagrama) e oito versões hotpatch.
* As **linhas de base não planejadas** são lançadas quando uma atualização importante (como uma correção de dia zero) é liberada e essa atualização específica não pode ser lançada como um hotpatch.  Quando as linhas de base não planejadas são liberadas, uma versão hotpatch será substituída por uma linha de base não planejada nesse mês.  Linhas de base não planejadas também incluem todas as atualizações em uma _atualização cumulativa mais recente_ comparável para esse mês e também exigem uma reinicialização.
    * O exemplo de agendamento acima ilustra duas linhas de base não planejadas que substituem as versões hotpatch para esses meses (o número real de linhas de base não planejadas em um ano não é conhecido com antecedência).

## <a name="regional-availability"></a>Disponibilidade regional
O hotpatch está disponível em todas as regiões globais do Azure na visualização. As regiões do Azure governamental não têm suporte na versão prévia.

## <a name="how-to-get-started"></a>Como começar

> [!NOTE]
> Durante a fase de visualização, você só pode começar a usar o portal do Azure usando [este link](https://aka.ms/AzureAutomanageHotPatch).

Para começar a usar o hotpatch em uma nova VM, siga estas etapas:
1.  Habilitar acesso de visualização
    * É necessária uma habilitação de acesso de visualização única por assinatura.
    * O acesso de visualização pode ser habilitado por meio da API, do PowerShell ou da CLI, conforme descrito na seção a seguir.
1.  Criar uma VM a partir do portal do Azure
    * Durante a versão prévia, você precisará começar a usar [este link](https://aka.ms/AzureAutomanageHotPatch).
1.  Detalhes da VM de fornecimento
    * Verifique se o _Windows Server 2019 datacenter: edição do Azure_ está selecionado na lista suspensa imagem)
    * Na etapa da guia gerenciamento, role para baixo até a seção "atualizações do sistema operacional convidado". Você verá HotPatching definido como ativado e a instalação de patch padrão para aplicação de patch orquestrada do Azure.
    * As práticas recomendadas de autogerenciamento de VM serão habilitadas por padrão
1. Criar sua nova VM

## <a name="enabling-preview-access"></a>Habilitando o acesso de visualização

### <a name="rest-api"></a>API REST

O exemplo a seguir descreve como habilitar a visualização para sua assinatura:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Use o ```Register-AzProviderFeature``` cmdlet para habilitar a visualização para sua assinatura.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>CLI do Azure

Use ```az feature register``` para habilitar a visualização para sua assinatura.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Instalação de patch

Durante a versão prévia, a [aplicação automática de patches de convidado de VM](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) é habilitada automaticamente para todas as VMs criadas com o _Windows Server 2019 datacenter: edição do Azure_. Com a aplicação automática de patches de convidado de VM habilitada:
* Os patches classificados como críticos ou de segurança são baixados e aplicados automaticamente na VM.
* Os patches são aplicados fora do horário de pico no fuso horário da VM.
* A orquestração de patch é gerenciada pelo Azure e os patches são aplicados após os [princípios de disponibilidade-primeiro](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching).
* A integridade da máquina virtual, conforme determinado por meio de sinais de integridade da plataforma, é monitorada para detectar falhas de patch.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Como funciona a aplicação automática de patches de convidado de VM?

Quando a [aplicação automática de patches de convidado de VM](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) está habilitada em uma VM, os patches críticos e de segurança disponíveis são baixados e aplicados automaticamente. Esse processo é ativado automaticamente todos os meses quando novos patches são lançados. A avaliação e a instalação do patch são automáticas e o processo inclui a reinicialização da VM, conforme necessário.

Com o hotpatch habilitado no _Windows Server 2019 datacenter: VMs de edição do Azure_ , as atualizações de segurança mais mensais são entregues como hotpatches que não exigem reinicializações. As últimas atualizações cumulativas enviadas em meses de linha de base planejados ou não planejados exigirão reinicializações de VM. Patches de segurança ou críticos adicionais também podem estar disponíveis periodicamente, o que pode exigir reinicializações de VM.

A VM é avaliada automaticamente a cada poucos dias e várias vezes em qualquer período de 30 dias para determinar os patches aplicáveis para essa VM. Essa avaliação automática garante que todos os patches ausentes sejam descobertos na primeira oportunidade possível.

Os patches são instalados dentro de 30 dias após as versões mensais dos patches, após os [princípios de disponibilidade-princípio](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching). Os patches são instalados somente fora do horário de pico para a VM, dependendo do fuso horário da VM. A VM deve estar em execução durante os horários de pico para que os patches sejam instalados automaticamente. Se uma VM for desligada durante uma avaliação periódica, a VM será avaliada e os patches aplicáveis serão instalados automaticamente durante a próxima avaliação periódica quando a VM estiver ligada. A próxima avaliação periódica geralmente ocorre em alguns dias.

Atualizações de definições e outros patches não classificados como críticos ou segurança não serão instalados por meio de aplicação automática de patches de convidado de VM.

## <a name="understanding-the-patch-status-for-your-vm"></a>Noções básicas sobre o status do patch para sua VM

Para exibir o status do patch para sua VM, navegue até a seção **convidado + atualizações de host** para sua vm no portal do Azure. Na seção **atualizações do sistema operacional convidado** , clique em ' ir para hotpatch (visualização) ' para exibir o status do patch mais recente para sua VM.

Nessa tela, você verá o status de hotpatch para sua VM. Você também pode examinar se há patches disponíveis para sua VM que não foram instaladas. Conforme descrito na seção "instalação de patch" acima, todas as atualizações de segurança e críticas serão instaladas automaticamente em sua VM usando [aplicação automática de patches de convidado de VM](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) e nenhuma ação extra será necessária. Patches com outras classificações de atualização não são instalados automaticamente. Em vez disso, eles são visíveis na lista de patches disponíveis na guia "conformidade da atualização". Você também pode exibir o histórico de implantações de atualização em sua VM por meio do ' histórico de atualização '. O histórico de atualizações dos últimos 30 dias é exibido, juntamente com os detalhes da instalação do patch.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Gerenciamento de hotpatch.":::

Com a aplicação automática de patches de convidado de VM, sua VM é avaliada periodicamente e automaticamente quanto a atualizações disponíveis. Essas avaliações periódicas garantem que os patches disponíveis sejam detectados. Você pode exibir os resultados da avaliação na tela atualizações acima, incluindo a hora da última avaliação. Você também pode optar por disparar uma avaliação de patch sob demanda para sua VM a qualquer momento usando a opção ' avaliar agora ' e examinar os resultados após a conclusão da avaliação.

Semelhante à avaliação sob demanda, você também pode instalar patches sob demanda para sua VM usando a opção ' instalar atualizações agora '. Aqui você pode optar por instalar todas as atualizações em classificações de patch específicas. Você também pode especificar atualizações para incluir ou excluir fornecendo uma lista de artigos individuais da base de dados de conhecimento. Os patches instalados sob demanda não são instalados usando princípios de disponibilidade-primeiro e podem exigir mais reinicializações e tempo de inatividade da VM para a instalação da atualização.

## <a name="supported-updates"></a>Atualizações com suporte

O hotpatch aborda as atualizações de segurança do Windows e mantém a paridade com o conteúdo das atualizações de segurança emitidas para o no canal normal (não hotpatch) do Windows Update.

Há algumas considerações importantes para executar uma VM de edição do Windows Server Azure com o hotpatch habilitado. As reinicializações ainda são necessárias para instalar atualizações que não estão incluídas no programa hotpatch. Reinicializações também são necessárias periodicamente após a instalação de uma nova linha de base. Essas reinicializações mantêm a VM em sincronia com patches que não são de segurança incluídos na atualização cumulativa mais recente.
* Os patches que não estão atualmente incluídos no programa hotpatch incluem atualizações não relacionadas à segurança lançadas para Windows e atualizações não Windows (como patches do .NET).  Esses tipos de patches precisam ser instalados durante um mês de linha de base e exigirão uma reinicialização.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-is-hotpatching"></a>O que é HotPatching?

* HotPatching é uma nova maneira de instalar atualizações em um datacenter do Windows Server 2019: VM do Azure Edition no Azure que não exige uma reinicialização após a instalação. Ele funciona corrigindo o código na memória de processos em execução sem a necessidade de reiniciar o processo.

### <a name="how-does-hotpatching-work"></a>Como funciona o HotPatching?

* HotPatching funciona estabelecendo uma linha de base com uma Windows Update atualização cumulativa mais recente e, em seguida, se baseia nessa linha de base com atualizações que não exigem uma reinicialização para entrar em vigor.  A linha de base é atualizada periodicamente com uma nova atualização cumulativa. A atualização cumulativa inclui todas as atualizações de segurança e qualidade e requer uma reinicialização.

### <a name="why-should-i-use-hotpatch"></a>Por que devo usar o hotpatch?

* Quando você usa o hotpatch no Windows Server 2019 datacenter: edição do Azure, sua VM terá maior disponibilidade (menos reinicializações) e atualizações mais rápidas (pacotes menores que são instalados mais rapidamente sem a necessidade de reiniciar os processos). Esse processo resulta em uma VM que está sempre atualizada e segura.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Quais tipos de atualizações são cobertos pelo hotpatch?

* O hotpatch atualmente aborda as atualizações de segurança do Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Quando receberei a primeira atualização de hotpatch?

* As atualizações do hotpatch normalmente são lançadas na segunda terça-feira de cada mês. Para obter mais informações, veja abaixo.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Como será a aparência do hotpatch?

* HotPatching funciona estabelecendo uma linha de base com uma Windows Update atualização cumulativa mais recente e, em seguida, baseia-se nessa linha de base com atualizações de hotpatch liberadas mensalmente.  Durante a visualização, as linhas de base serão lançadas a partir de três meses. Consulte a imagem abaixo para obter um exemplo de uma agenda anual de três meses (incluindo exemplos de linhas de base não planejadas devido a correções de dia zero).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch exemplo de agendamento.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>As reinicializações ainda são necessárias para uma VM inscrita no hotpatch?

* As reinicializações ainda são necessárias para instalar as atualizações não incluídas no programa hotpatch e são necessárias periodicamente após a instalação de uma linha de base (Windows Update última atualização cumulativa). Essa reinicialização manterá sua VM em sincronia com todos os patches incluídos na atualização cumulativa. As linhas de base (que exigem uma reinicialização) começarão em uma cadência de três meses e aumentarão ao longo do tempo.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Meus aplicativos são afetados quando uma atualização do hotpatch é instalada?

* Como o hotpatch corrige o código na memória de processos em execução sem a necessidade de reiniciar o processo, seus aplicativos não serão afetados pelo processo de aplicação de patch. Observe que isso é separado das possíveis implicações de desempenho e funcionalidade do próprio patch.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Posso desativar o hotpatch em minha VM?

* Você pode desativar o hotpatch em uma VM por meio do portal do Azure.  A desativação do hotpatch cancelará o registro da VM de hotpatch, que reverte a VM para o comportamento de atualização típico do Windows Server.  Depois de cancelar o registro do hotpatch em uma VM, você poderá registrar novamente essa VM quando a próxima linha de base de hotpatch for liberada.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Posso atualizar do meu sistema operacional Windows Server existente?

* Atualmente, não há suporte para a atualização de versões existentes do Windows Server (ou seja, Windows Server 2016 ou 2019 edições não Azure). Haverá suporte para a atualização para versões futuras da edição do Windows Server Azure.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Posso usar hotpatch para cargas de trabalho de produção durante a visualização?

* As visualizações são destinadas apenas para fins de teste e não para uso em ambientes de produção.

### <a name="will-i-be-charged-during-the-preview"></a>Serei cobrado durante a versão prévia?

* A licença do Windows Server Azure Edition é gratuita durante a versão prévia. No entanto, o custo de qualquer infraestrutura subjacente configurada para sua VM (armazenamento, computação, rede, etc.) ainda será cobrado em sua assinatura.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Como posso obter suporte para a solução de problemas de HotPatching?

* Você pode arquivar um [tíquete de caso de suporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Para a opção de serviço, procure e selecione **máquina virtual executando o Windows** em computação. Selecione **recursos do Azure** para o tipo de problema e **aplicação automática de patches de convidado de VM** para o subtipo de problema.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o Azure Gerenciamento de Atualizações [aqui](https://docs.microsoft.com/azure/automation/update-management/overview).
* Saiba mais sobre aplicação automática de patches de convidado de VM [aqui](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching)