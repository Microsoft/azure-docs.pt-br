---
title: Criar uma fábrica de imagens no Azure DevTest Labs | Microsoft Docs
description: Este artigo mostra como configurar uma fábrica de imagens Personalizada usando scripts de exemplo disponíveis no repositório git (Azure DevTest Labs).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482064"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagens Personalizada no Azure DevTest Labs
Este artigo mostra como configurar uma fábrica de imagens Personalizada usando scripts de exemplo disponíveis no [repositório git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>O que é uma fábrica de imagens?
Uma fábrica de imagens é uma solução de configuração como código que cria e distribui imagens automaticamente de forma regular com todas as configurações desejadas. As imagens na fábrica de imagens estão sempre atualizadas e a manutenção contínua é quase zero quando o processo inteiro é automatizado. E, como todas as configurações necessárias já estão na imagem, ela poupa a hora de configurar manualmente o sistema após a criação de uma VM com o sistema operacional base.

O acelerador significativo para obter uma área de trabalho do desenvolvedor para um estado pronto no DevTest Labs está usando imagens personalizadas. A desvantagem das imagens personalizadas é que há algo extra a ser mantido no laboratório. Por exemplo, versões de avaliação de produtos expiram ao longo do tempo (ou) atualizações de segurança liberadas recentemente não são aplicadas, o que nos obriga a atualizar a imagem personalizada periodicamente. Com uma fábrica de imagens, você tem uma definição da imagem verificada no controle do código-fonte e tem um processo automatizado para produzir imagens personalizadas com base na definição.

A solução permite a velocidade de criação de máquinas virtuais a partir de imagens personalizadas, ao mesmo tempo que elimina custos de manutenção contínuas adicionais. Com essa solução, você pode criar automaticamente imagens personalizadas, distribuí-las a outros laboratórios do DevTest e desativar as imagens antigas. No vídeo a seguir, você aprende sobre a fábrica de imagens e como ela é implementada com o DevTest Labs.  Todos os scripts do Azure PowerShell estão disponíveis gratuitamente e estão localizados aqui:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Exibição de alto nível da solução
A solução permite a velocidade de criação de máquinas virtuais a partir de imagens personalizadas, ao mesmo tempo que elimina custos de manutenção contínuas adicionais. Com essa solução, você pode criar imagens personalizadas automaticamente e distribuí-las a outros laboratórios do DevTest. Você usa o Azure DevOps (anteriormente Visual Studio Team Services) como o mecanismo de orquestração para automatizar todas as operações no DevTest Labs.

![Exibição de alto nível da solução](./media/create-image-factory/high-level-view-of-solution.png)

Há uma [extensão do VSTS para DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite executar essas etapas individuais:

- Criar imagem personalizada
- Criar VM
- Excluir VM
- Criar ambiente
- Excluir ambiente
- Popular ambiente

Usar a extensão do DevTest Labs é uma maneira fácil de começar a criar automaticamente imagens personalizadas no DevTest Labs.

Há uma implementação alternativa usando o script do PowerShell para um cenário mais complexo. Usando o PowerShell, você pode automatizar totalmente uma fábrica de imagens com base no DevTest Labs que pode ser usado em sua ferramentas de CI/CD (integração contínua e entrega contínua). Os princípios seguidos nesta solução alternativa são:

- Atualizações comuns não devem exigir nenhuma alteração na fábrica de imagens. (por exemplo, adicionar um novo tipo de imagem personalizada, desativar automaticamente imagens antigas, adicionar um novo ' Endpoint ' DevTest Labs para receber imagens personalizadas e assim por diante.)
- As alterações comuns são apoiadas pelo controle do código-fonte (infraestrutura como código)
- Os laboratórios de DevTest que recebem imagens personalizadas podem não estar na mesma assinatura do Azure (laboratórios abrangem assinaturas)
- Os scripts do PowerShell devem ser reutilizáveis para que possamos acelerar fábricas adicionais conforme necessário

## <a name="next-steps"></a>Próximas etapas
Vá para o próximo artigo nesta seção: [executar uma fábrica de imagens do Azure DevOps](image-factory-set-up-devops-lab.md)
