---
title: "Solucionar problemas de dimensionamento automático com conjunto de dimensionamento de máquinas virtuais | Microsoft Docs"
description: "Solução de problemas de dimensionamento automático com conjuntos de escala de máquina virtual. Compreenda os problemas típicos encontrados e como resolvê-los."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: guybo
ms.openlocfilehash: bd45a0fb99a77851aa7b91d23bd4b830b6f5cc7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Solução de problemas do dimensionamento automático com conjuntos de escala de máquina virtual
**Problema**: você criou uma infraestrutura de dimensionamento automático no Azure Resource Manager usando Conjuntos de Dimensionamento de VMs, por exemplo implantando um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – suas regras de escala estão definidas e ele funciona muito bem, exceto que, independentemente da carga colocada nas VMs, ele não usa dimensionamento automático.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
Alguns aspectos a serem considerados incluem:

* Quantos núcleos cada VM tem e você está carregando cada núcleo?
  O modelo de Início Rápido do Azure de exemplo acima tem um script do_work.php, que carrega um único núcleo. Se você estiver usando uma VM de tamanho maior do que o de uma VM de núcleo único, como Standard_A1 ou D1, é necessário executar essa carga várias vezes. Verifique quantos núcleos suas VMs têm conferindo [Tamanhos para máquinas virtuais do Windows no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Quantas VMs há no conjunto de VM escala, e você está balhando em cada uma delas?
  
    Um evento de escala horizontal só ocorrerá quando a CPU média em **todas** as VMs em um conjunto de escala exceder o valor de limite além do tempo interno definido nas regras de dimensionamento automático.
* Você perdeu algum evento de escala?
  
    Verifique os logs de auditoria no Portal do Azure sobre os eventos de escala. Pode ter ocorrido uma escala ou redução vertical que foi perdida. Você pode filtrar por “Escala”.
  
    ![Logs de Auditoria][audit]
* Os limites de escala e redução são diferentes o suficiente?
  
    Suponha que você definiu uma regra para escalar horizontalmente quando a CPU média for maior que 50% durante 5 minutos e reduzir horizontalmente quando a CPU média for menor que 50%. Isso causaria um problema de "oscilação" quando uso da CPU estiver perto desse limite, com ações de escala constantemente aumentando e diminuindo o tamanho do conjunto. Por isso, o serviço de dimensionamento automático tenta impedir “oscilações”, que pode se manifestar como o impedimento do dimensionamento. Portanto, verifique se seus limites de escala e redução são diferentes o suficiente para que exista algum espaço entre os dimensionamentos.
* Você criou seu próprio modelo JSON?
  
    É fácil cometer erros, portanto, comece com um modelo como o mostrado acima, com funcionamento comprovado, e faça pequenas alterações incrementais. 
* Você consegue aumentar ou reduzir horizontalmente?
  
    Tente reimplantar o recurso do conjunto de escala de VM com uma configuração de "capacidade" diferente para alterar o número de VMs manualmente. Um modelo de exemplo para fazer isso está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing - pode ser necessário editar o modelo para garantir que ele possua o mesmo tamanho de máquina que o Conjunto de Dimensionamento usa. Se for possível alterar o número de VMs manualmente, você saberá que o problema é isolado ao dimensionamento automático.
* Verifique seus recursos Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights no [Gerenciador de Recursos do Azure](https://resources.azure.com/)
  
    Essa é uma ferramenta indispensável de solução de problemas que mostra o estado dos seus recursos do Azure Resource Manager. Clique na sua assinatura e examine o Grupo de Recursos em que você está solucionando problemas. No Provedor de recursos de computação, examine o conjunto de escala de VM criado e verifique a Exibição de Instância, que mostra o estado de uma implantação. Verifique também a exibição de instância de VMs no conjunto de escala de VM. Vá para o provedor de recursos Microsoft.Insights e verifique se as regras de dimensionamento automático parecem corretas.
* A extensão de Diagnóstico está funcionando e emitindo os dados de desempenho?
  
    **Atualização:** O dimensionamento automático do Azure foi aperfeiçoado para usar um pipeline de métricas baseado em host que não requer mais a instalação de uma extensão de diagnóstico. Isso significa que os próximos parágrafos já não se aplicam se você criar um aplicativo de dimensionamento automático usando o novo pipeline. Este é um exemplo de modelos do Azure que foram convertidos para usar o pipeline do host: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    A utilização de métricas baseada em host para o dimensionamento automático é melhor pelos seguintes motivos:
  
  * Menos partes móveis, já que não é necessário instalação de nenhuma extensão de diagnóstico.
  * Modelos mais simples. Basta adicionar informações de regras de dimensionamento automático em um modelo de conjunto de dimensionamento existente.
  * Relatórios mais confiáveis e inicialização mais rápida de novas VMs.
    
    Os únicos motivos pelos quais você desejaria continuar usando uma extensão de diagnóstico seria se você precisasse de relatório ou dimensionamento de diagnósticos de memória. Métricas baseadas em host não fornecem relatório de memória.
    
    Com isso em mente, só é necessário seguir na leitura deste artigo se você ainda estiver usando extensões de diagnóstico para o dimensionamento automático.
    
    O dimensionamento automático no Azure Resource Manager pode trabalhar (mas não precisa mais) por meio de uma extensão de VM chamada a Extensão de Diagnóstico. Emite dados de desempenho para uma conta de armazenamento definida no modelo. Esses dados são então agregados pelo serviço do Azure Monitor.
    
    Se o serviço do Insights não puderem ler os dados de VMs, ele deverá enviar um email, se as VMs ficarem inativas, por exemplo, por isso verifique seu email (aquele especificado ao criar a conta do Azure).
    
    Também é possível examinar os dados você mesmo. Examine a conta de armazenamento do Azure usando um gerenciador de nuvem. Usando o [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), por exemplo, faça logon e escolha a assinatura do Azure que você está usando e o nome da conta de armazenamento de Diagnóstico referenciada na definição da extensão de Diagnóstico no seu modelo de implantação.
    
    ![Gerenciador de Nuvem][explorer]
    
    Aqui você verá um monte de tabelas em que os dados de cada VM estão sendo armazenados. Usando o Linux e a métrica de CPU como exemplo, procure linhas mais recentes. O Visual Studio Cloud Explorer dá suporte a uma linguagem de consulta para que você possa executar uma consulta como “Timestamp gt datetime’2016-02-02T21:20:00Z’” para obter com certeza os eventos mais recentes (supondo que o fuso horário é UTC). Os dados exibidos correspondem às regras de escala que você configurou? No exemplo abaixo, a CPU do computador 20 começou a aumentar para 100% nos últimos 5 minutos.
    
    ![Tabelas de Armazenamento][tables]
    
    Se os dados não estiverem lá, isso significará que o problema está na extensão de diagnóstico em execução nas VMs. Se os dados estiverem lá, isso significará que há um problema com as suas regras de escala ou com o serviço do Insights. Verifique o [Status do Azure](https://azure.microsoft.com/status/).
    
    Depois que você passou por essas etapas, se ainda estiver tendo problemas de dimensionamento automático, pode tentar os fóruns no [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp), ou o [Stack Overflow](http://stackoverflow.com/questions/tagged/azure) ou abrir uma chamada de suporte. Prepare-se para compartilhar o modelo e uma exibição dos dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
