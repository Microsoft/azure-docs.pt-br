---
title: Solucionar problemas de dimensionamento automático com conjunto de dimensionamento de máquinas virtuais | Microsoft Docs
description: Solução de problemas de dimensionamento automático com conjuntos de escala de máquina virtual. Compreenda os problemas típicos encontrados e como resolvê-los.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 3308b22606e87853aad7e3d3a3995aab8d1b5401
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803563"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Solução de problemas do dimensionamento automático com conjuntos de escala de máquina virtual
**Problema**: você criou uma infraestrutura de dimensionamento automático no Azure Resource Manager usando conjuntos de dimensionamento de máquinas virtuais, por exemplo, implantando um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – suas regras de escala estão definidas e ele funciona muito bem, exceto que, independentemente da carga colocada nas VMs, ele não usa dimensionamento automático.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
Alguns aspectos a serem considerados incluem:

* Quantas vCPUs cada VM tem e você está carregando cada vCPU?
  O modelo de Início Rápido do Azure de exemplo acima tem um script do_work.php, que carrega uma única vCPU. Se você estiver usando uma VM de tamanho maior do que o de uma VM de vCPU única, como Standard_A1 ou D1, será necessário executar essa carga várias vezes. Verifique quantas vCPUs suas VMs têm conferindo [Tamanhos para máquinas virtuais do Windows no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Há quantas VMs no conjunto de dimensionamento de máquinas virtuais, e você está trabalhando em cada uma delas?
  
    Um evento de expansão só ocorre quando a CPU média em **todas** as VMs em um conjunto de escala exceder o valor de limite além do tempo interno definido nas regras de dimensionamento automático.
* Você perdeu algum evento de escala?
  
    Verifique os logs de auditoria no Portal do Azure sobre os eventos de escala. Pode ter ocorrido uma escala ou redução vertical que foi perdida. Você pode filtrar por "Escala".
  
    ![Logs de Auditoria][audit]
* Os limites de escala e redução são diferentes o suficiente?
  
    Suponha que você definiu uma regra para escalar horizontalmente quando a CPU média for maior que 50% durante 50 minutos e reduzir horizontalmente quando a CPU média for menor que 50%. Essa configuração causaria um problema de "oscilação" quando uso da CPU estiver perto desse limite, com ações de escala constantemente aumentando e diminuindo o tamanho do conjunto. Devido a essa configuração, o serviço de dimensionamento automático tenta impedir "oscilações", que podem se manifestar como o impedimento do dimensionamento. Portanto, garanta que seus limites de expansão e redução sejam diferentes o suficiente para que exista algum espaço entre os dimensionamentos.
* Você criou seu próprio modelo JSON?
  
    É fácil cometer erros, portanto, comece com um modelo como o mostrado acima, com funcionamento comprovado, e faça pequenas alterações incrementais. 
* Você consegue aumentar ou reduzir horizontalmente?
  
    Tente reimplantar o recurso do conjunto de dimensionamento de máquinas virtuais com uma configuração de "capacidade" diferente para alterar o número de VMs manualmente. Um modelo de exemplo está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – você pode precisar editar o modelo para certificar-se de que ele tem o mesmo tamanho que a máquina que usa o conjunto de dimensionamento. Se for possível alterar o número de VMs manualmente, você saberá que o problema é isolado ao dimensionamento automático.
* Verifique seus recursos Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights no [Gerenciador de Recursos do Azure](https://resources.azure.com/)
  
    O Azure Resource Explorer é uma ferramenta indispensável de solução de problemas que mostra o estado dos seus recursos do Azure Resource Manager. Clique na sua assinatura e examine o Grupo de Recursos em que você está solucionando problemas. No Provedor de recursos de computação, examine o conjunto de escala de máquina virtual criado e verifique a Exibição de Instância, que mostra o estado de uma implantação. Verifique também a exibição de instância de VMs no conjunto de dimensionamento de máquinas virtuais. Em seguida, vá ao provedor de recursos Microsoft.Insights e verifique se as regras de dimensionamento automático parecem corretas.
* A extensão de Diagnóstico está funcionando e emitindo os dados de desempenho?
  
    **Update:** Dimensionamento automático do Azure foi aprimorado para usar um pipeline de métricas baseadas em host, que não requer mais uma extensão de diagnóstico a serem instalados. Os poucos parágrafos seguintes já não se aplicarão se você criar um aplicativo de dimensionamento automático usando o novo pipeline. Um exemplo de modelos do Azure que foram convertidos para usar o pipeline de host está disponível aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    A utilização de métricas baseadas em host para o dimensionamento automático é melhor pelos seguintes motivos:
  
  * Menos partes móveis, já que não é necessário instalação de nenhuma extensão de diagnóstico.
  * Modelos mais simples. Basta adicionar informações de regras de dimensionamento automático em um modelo de conjunto de dimensionamento existente.
  * Relatórios mais confiáveis e inicialização mais rápida de novas VMs.
    
    Os únicos motivos pelos quais você pode querer continuar usando uma extensão de diagnóstico é a necessidade de fazer relatório ou dimensionamento de diagnóstico de memória. As métricas baseadas em host não fornecem relatório de memória.
    
    Com isso em mente, só será necessário seguir na leitura deste artigo se você ainda estiver usando extensões de diagnóstico para o dimensionamento automático.
    
    O dimensionamento automático no Azure Resource Manager pode trabalhar (mas não precisa mais) por meio de uma extensão de VM chamada a Extensão de Diagnóstico. Emite dados de desempenho para uma conta de armazenamento definida no modelo. Esses dados são então agregados pelo serviço do Azure Monitor.
    
    Se o serviço do Insights não conseguir ler dados das máquinas virtuais, ele deverá enviar-lhe um email. Por exemplo, você receberá um email se as VMs estiverem inativas. Certifique-se de verificar seu email, no endereço de email que você especificou quando criou sua conta do Azure.
    
    Você mesmo também pode examinar os dados. Examine a conta de armazenamento do Azure usando um gerenciador de nuvem. Por exemplo, usando o [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), faça logon e escolha a assinatura do Azure que você está usando. Em seguida, observe o nome de conta de armazenamento de Diagnóstico mencionado na definição de extensão de Diagnóstico em seu modelo de implantação.
    
    ![Gerenciador de Nuvem][explorer]
    
    Você verá um monte de tabelas em que os dados de cada VM estão sendo armazenados. Usando o Linux e a métrica de CPU como exemplo, procure linhas mais recentes. O Visual Studio Cloud Explorer oferece suporte a uma linguagem de consulta, de modo que você possa executar uma consulta. Por exemplo, você pode executar uma consulta para "Carimbo de data/hora gt Data/Hora’2016-02-02T21:20:00Z’" para garantir que obtém os eventos mais recentes. O fuso horário corresponde ao UTC. Os dados exibidos correspondem às regras de escala que você configurou? No exemplo seguinte, a CPU do computador 20 começou a aumentar para 100% nos últimos 5 minutos.
    
    ![Tabelas de Armazenamento][tables]
    
    Se os dados não estiverem lá, isso significará que o problema está na extensão de diagnóstico em execução nas VMs. Se os dados estiverem lá, isso significará que há um problema com as suas regras de escala ou com o serviço do Insights. Verifique o [Status do Azure](https://azure.microsoft.com/status/).
    
    Depois de passar por essas etapas, se você ainda tiver problemas de dimensionamento automático, tente os seguintes recursos: 
    * Leia os fóruns no [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Faça uma chamada de suporte. Prepare-se para compartilhar o modelo e uma exibição dos dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
