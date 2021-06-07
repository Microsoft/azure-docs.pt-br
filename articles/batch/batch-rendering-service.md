---
title: Visão geral da renderização
description: Introdução ao uso do Azure para renderização e uma visão geral dos recursos de renderização do Lote do Azure
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98232107"
---
# <a name="rendering-using-azure"></a>Renderizar usando o Azure

A renderização é o processo de obter modelos 3D e convertê-los em imagens 2D. Os arquivos de cenas 3D são criados em aplicativos como Autodesk 3ds Max, Autodesk Maya e Blender.  Aplicativos de renderização como Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles produzem imagens 2D.  Às vezes, imagens individuais são criadas a partir dos arquivos de cena. No entanto, é comum modelar e renderizar várias imagens e, em seguida, combiná-las em uma animação.

A carga de trabalho de renderização é muito usada para efeitos especiais (VFX) na indústria de Mídia e Entretenimento. A renderização também é usada em muitos outros setores como publicidade, varejo, petróleo e gás e manufatura.

O processo de renderização é computacionalmente intensivo, isso significa que pode haver muitos quadros/imagens a serem produzidos e cada imagem poderá demorar muitas horas para ser renderizada.  A renderização é, portanto, uma carga de trabalho de processamento em lote perfeita que pode aproveitar o Azure para executar muitos renderizações em paralelo e utilizar uma ampla gama de hardware, incluindo GPUs.

## <a name="why-use-azure-for-rendering"></a>Por que usar o Azure para renderização?

Por muitos motivos, a renderização é uma carga de trabalho perfeitamente adequada para o Azure:

* As tarefas de renderização podem ser divididas em várias partes que podem ser executadas em paralelo usando várias VMs:
  * As animações consistem em muitos quadros e cada quadro pode ser renderizado em paralelo.  Quanto mais VMs estiverem disponíveis para processar cada quadro, mais rapidamente todos os quadros e a animação poderão ser produzidos.
  * Alguns softwares de renderização permitem que quadros únicos sejam divididos em várias partes, como blocos ou fatias.  Cada peça poderá ser renderizada separadamente e, em seguida, combinada na imagem final quando todas as peças tiverem sido concluídas.  Quanto mais VMs estiverem disponíveis, mais rápido um quadro poderá ser renderizado.
* Projetos de renderização podem exigir grande escala:
  * Quadros individuais podem ser complexos e exigem muitas horas para serem renderizados, mesmo em hardwares de alta tecnologia, pois animações podem consistir em centenas de milhares de quadros.  Uma quantidade enorme de computação é necessária para renderizar animações de alta qualidade em um período de tempo razoável.  Em alguns casos, mais de 100.000 núcleos foram usados para renderizar milhares de quadros em paralelo.
* Os projetos de renderização são baseados em projetos e exigem quantidades variáveis de computação:
  * Aloque a capacidade de armazenamento computação e quando necessário, escale-a ou reduza verticalmente de acordo com a carga durante um projeto e remova-a quando o projeto for concluído.
  * Pague pela capacidade quando alocada, mas não pague quando não houver carga, como entre projetos.
  * Atenda a intermitências devido a alterações inesperadas, isto é, escala maior se houver alterações inesperadas no final de um projeto e essas alterações precisarem ser processadas em um cronograma rigoroso.
* Escolha entre uma ampla seleção de hardware de acordo com aplicativo, carga de trabalho e período de tempo:
  * Há uma ampla seleção de hardware disponível no Azure que pode ser alocado e gerenciado com o Lote.
  * Dependendo do projeto, o requisito pode ser o melhor preço/desempenho ou o melhor desempenho geral.  Cenas diferentes e/ou aplicativos de renderização terão requisitos de memória diferentes.  Alguns aplicativos de renderização podem aproveitar GPUs para obter o melhor desempenho ou determinados recursos. 
* As VMs de baixa prioridade ou [pontuações](https://azure.microsoft.com/pricing/spot/) reduzem o custo:
  * As VMs de baixa prioridade e pontos estão disponíveis para um grande desconto em comparação com as VMs padrão e são adequadas para alguns tipos de trabalho.
  
## <a name="existing-on-premises-rendering-environment"></a>Ambiente de renderização local existente

O caso mais comum é para que haja um farm de renderização local existente gerenciado por um aplicativo de gerenciamento de renderização, como PipelineFX Qube, Royal render, Thinkbox prazo ou um aplicativo personalizado.  O requisito é estender a capacidade do farm de renderização local usando VMs do Azure.

A infraestrutura e os serviços do Azure são usados para criar um ambiente híbrido em que o Azure é usado para complementar a capacidade local. Por exemplo:

* Use uma [rede virtual](../virtual-network/virtual-networks-overview.md) para colocar os recursos do Azure na mesma rede que o farm de renderização local.
* Use [avere vFXT para Azure](../avere-vfxt/avere-vfxt-overview.md) ou [cache HPC do Azure](../hpc-cache/hpc-cache-overview.md) para armazenar em cache arquivos de origem no Azure para reduzir o uso e a latência da largura de banda, maximizando o desempenho.
* Verifique se o servidor de licença existente está na rede virtual e compre as licenças adicionais necessárias para atender à capacidade extra baseada no Azure.

## <a name="no-existing-render-farm"></a>Nenhum farm de renderização existente

As estações de trabalho do cliente podem estar executando a renderização, mas a carga de renderização está aumentando e está demorando muito para usar apenas a capacidade da estação de trabalho.

Há duas opções principais disponíveis:

* Implante um Gerenciador de renderização local, como Royal render, e configure um ambiente híbrido para usar o Azure quando for necessário mais capacidade ou desempenho. Um Gerenciador de renderização é especificamente desenvolvido para renderizar cargas de trabalho e incluirá plug-ins para os aplicativos cliente populares, permitindo o envio fácil de trabalhos de renderização.

* Uma solução personalizada usando o lote do Azure para alocar e gerenciar a capacidade de computação, bem como fornecer o agendamento de trabalho para executar os trabalhos de renderização.

## <a name="next-steps"></a>Próximas etapas

 Saiba como [usar a infraestrutura e os serviços do Azure para estender um farm de renderização local existente](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

Saiba mais sobre os [recursos de renderização do lote do Azure](batch-rendering-functionality.md).
