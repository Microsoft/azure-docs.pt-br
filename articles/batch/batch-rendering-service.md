---
title: Visão geral da renderização - Lote do Azure
description: Introdução ao uso do Azure para renderização e uma visão geral dos recursos de renderização do Lote do Azure
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774022"
---
# <a name="rendering-using-azure"></a>Renderizar usando o Azure

A renderização é o processo de obter modelos 3D e convertê-los em imagens 2D. Os arquivos de cenas 3D são criados em aplicativos como Autodesk 3ds Max, Autodesk Maya e Blender.  Aplicativos de renderização como Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles produzem imagens 2D.  Às vezes, imagens individuais são criadas a partir dos arquivos de cena. No entanto, é comum modelar e renderizar várias imagens e, em seguida, combiná-las em uma animação.

A carga de trabalho de renderização é muito usada para efeitos especiais (VFX) na indústria de Mídia e Entretenimento. Renderização também é usada em muitos outros setores, como anúncios, varejo, fabricação, petróleo e gás.

O processo de renderização é computacionalmente intensivo, isso significa que pode haver muitos quadros/imagens a serem produzidos e cada imagem poderá demorar muitas horas para ser renderizada.  A renderização é, portanto, uma carga de trabalho de processamento em lotes perfeita que pode aproveitar o Azure e o Lote do Azure para executar muitas renderizações em paralelo.

## <a name="why-use-azure-for-rendering"></a>Por que usar o Azure para renderização?

Por vários motivos, a renderização é uma carga de trabalho perfeitamente adequada para o Azure e Lote do Azure:

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
* VMs de baixa prioridade reduzem custos:
  * VMs de baixa prioridade estão disponíveis para um grande desconto em comparação com as VMs regulares sob demanda e são adequadas para alguns tipos de trabalho.
  * VMs de baixa prioridade podem ser alocadas pelo Lote do Azure, com o Lote fornecendo flexibilidade sobre como são usadas para atender a um amplo conjunto de requisitos.  Os pools do Lote podem consistir de VMs dedicadas e de baixa prioridade, sendo possível alterar a combinação de tipos de VM a qualquer momento.

## <a name="options-for-rendering-on-azure"></a>Opções para renderizar no Azure

Há um intervalo de recursos do Azure que pode ser usado para renderizar cargas de trabalho.  Quais recursos usar dependem de qualquer ambiente e requisitos existentes.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Ambiente de renderização local existente usando um aplicativo de gerenciamento de renderização

O caso mais comum é que haja um farm de renderização local gerenciado por um aplicativo de gerenciamento de renderização, como PipelineFX Qube, Royal Render ou Thinkbox Deadline.  O requisito é estender a capacidade do farm de renderização local usando VMs do Azure.

O software de gerenciamento de renderização tem suporte integrado ao Azure ou disponibilizamos plug-ins que adicionam suporte ao Azure. Para obter mais informações sobre os gerenciadores de renderização com suporte e a funcionalidade habilitada, consulte o artigo sobre [usar gerenciadores de renderização](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Fluxo de trabalho de renderização personalizada

O requisito é que as VMs estendam um farm de renderização existente.  Os pools do Lote do Azure podem alocar um grande número de VMs, permitir que VMs de baixa prioridade sejam usadas e dinamicamente dimensionadas automaticamente com VMs com preços completos e oferecer licenciamento de pagamento por uso para aplicativos de renderização populares.

### <a name="no-existing-render-farm"></a>Nenhum farm de renderização existente

As estações de trabalho do cliente podem estar executando a renderização, mas a carga de trabalho de renderização está aumentando e demorando muito para usar somente a capacidade da estação de trabalho.  O Lote do Azure pode ser usado para alocar a computação de farm de renderização sob demanda, bem como agendar trabalhos de renderização para o farm de renderização do Azure.

## <a name="azure-batch-rendering-capabilities"></a>Recursos de renderização de Lote do Azure

O Lote do Azure permite que cargas de trabalho paralelas sejam executadas no Azure.  Ele permite a criação e o gerenciamento de um grande número de VMs nas quais os aplicativos são instalados e executados.  Ele também fornece recursos abrangentes de agendamento de trabalhos para executar instâncias desses aplicativos, fornecendo a atribuição de tarefas a VMs, filas, monitoramento de aplicativos, e assim por diante.

O Lote do Azure é usado para muitas cargas de trabalho, mas os recursos a seguir estão disponíveis para facilitar e agilizar especificamente a execução de cargas de trabalho de renderização.

* Imagens de VM com gráficos pré-instalados e aplicativos de renderização:
  * As imagens de VM do Azure Marketplace estão disponíveis e contêm aplicativos gráficos e de renderização populares, evitando a necessidade de instalar os aplicativos por conta própria ou criar suas próprias imagens personalizadas com os aplicativos instalados. 
* Licenciamento de pagamento por uso para renderizar aplicativos:
  * É possível optar para pagar os aplicativos por minuto, além de pagar pelas VMs de computação, o que evita a necessidade de comprar licenças e, potencialmente, configurar um servidor de licenças para os aplicativos.  Pagar pelo uso também significa que é possível atender a uma carga variável e inesperada, pois não há um número fixo de licenças.
  * Também é possível usar os aplicativos pré-instalados com suas próprias licenças e não usar o licenciamento de pagamento por uso. Para fazer isso, normalmente você instala um local ou baseado no servidor de licença baseado no Azure e usa uma rede virtual do Azure para conectar o pool de renderização ao servidor de licença.
* Plug-ins para aplicativos de modelagem e design do cliente:
  * Os plug-ins permitem que os usuários finais utilizem o Lote do Azure diretamente do aplicativo cliente como Autodesk Maya, permitindo que criem pools, enviem trabalhos e usem mais capacidade de computação para executar renderizações mais rápidas.
* Integração do gerenciador de renderização:
  * O Lote do Azure é integrado aos aplicativos de gerenciamento de renderização ou os plug-ins são disponibilizados para fornecer a integração do Lote do Azure.

Há várias maneiras de usar o Lote do Azure, que também aplicam-se à renderização do Lote do Azure.

* APIs:
  * Grave código usando [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch) ou outras APIs com suporte.  Desenvolvedores podem integrar os recursos do Lote do Azure aos fluxos de trabalho ou aplicativos existentes, sejam baseados em nuvem ou locais.  Por exemplo, o [plug-in do Autodesk Maya](https://github.com/Azure/azure-batch-maya) utiliza a API do Python do Lote para invocar o Lote, criar e gerenciar pools, enviar trabalhos e tarefas e monitorar status.
* Ferramentas de linha de comando:
  * A [linha de comando do Azure](https://docs.microsoft.com/cli/azure/) ou o [PowerShell](https://docs.microsoft.com/powershell/azure/overview) pode ser usado para script de uso do Lote.
  * Em particular, o suporte de modelo da CLI do Lote torna muito mais fácil criar pools e enviar trabalhos.
* Interfaces do usuário:
  * O [Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta cliente de plataforma cruzada que também permite que as contas do Lote sejam gerenciadas e monitoradas, mas fornece alguns recursos mais avançados em comparação com a interface do usuário do portal do Azure.  Um conjunto de modelos de trabalho e pool é fornecido, adaptado para cada aplicativo com suporte e pode ser usado para criar pools e enviar trabalhos facilmente.
  * O portal do Azure pode ser usado para gerenciar e monitorar o Lote do Azure.
* Plug-ins de aplicativo cliente:
  * Estão disponíveis plug-ins que permitem que a renderização de Lote seja usada diretamente nos aplicativos de modelagem e design de cliente. Os plug-ins invocam principalmente o aplicativo Batch Explorer com informações contextuais sobre o modelo 3D atual.
  * Os plug-ins a seguir estão disponíveis:
    * [Lote do Azure para Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Introdução ao processamento do Lote do Azure

Consulte os tutoriais introdutórios a seguir para experimentar a renderização de Lote do Azure:

* [Usar o Batch Explorer para renderizar uma cena do Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Usar a CLI do Lote para renderizar uma cena do Autodesk 3ds Max](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Próximas etapas

Determine a lista de aplicativos e versões de renderização incluídos nas imagens da VM do Azure Marketplace [neste artigo](https://docs.microsoft.com/azure/batch/batch-rendering-applications).