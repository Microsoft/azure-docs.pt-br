---
title: Otimizar o Visual Studio para o Azure Service Fabric malha
description: Esse artigo mostra como otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric para que sua primeira execução de depuração (F5) seja muito mais rápida.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625745"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Esse artigo mostra como otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric para que sua primeira execução de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar configurações do Visual Studio
 
No Visual Studio, em **ferramentas**  >  **Opções**   >  **Service Fabric malha ferramentas**  >  **gerais**, você pode ajustar as seguintes configurações:

- **Extrair imagens necessárias do Docker na abertura do projeto** torna sua primeira execução de depuração (F5) mais rápida iniciando o processo de download de imagem durante o carregamento do projeto.  
- **Implantar o aplicativo na abertura do projeto** pode tornar sua primeira execução de depuração (F5) mais rápida iniciando o processo de implantação assim que o projeto é aberto.  
- **Remover o aplicativo no fechamento do projeto** recupera recursos (CPU, RAM) alocados para o aplicativo removendo o aplicativo de malha quando o projeto é fechado.  

Quando você vê mensagens na janela de saída das Ferramentas do Service Fabric que o Visual Studio está 'extraindo imagens', 'aquecendo' ou 'removendo o aplicativo', elas se referem às configurações acima. Você pode desativar essas configurações.

## <a name="next-steps"></a>Próximas etapas

Leia o [Tutorial de Depurar um aplicativo de malha](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)