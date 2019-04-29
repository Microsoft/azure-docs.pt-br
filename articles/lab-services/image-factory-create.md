---
title: Criar uma fábrica de imagem no Azure DevTest Labs | Microsoft Docs
description: Saiba como criar uma fábrica de imagem personalizada no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622607"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagem personalizada no Azure DevTest Labs
Este artigo mostra como definir uma fábrica de imagem personalizada usando scripts de exemplo disponíveis na [repositório do Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>O que é uma fábrica de imagem?
Uma fábrica de imagem é uma solução de configuração como código que compila e distribui imagens automaticamente em uma base regular com todas as configurações desejadas. As imagens na fábrica de imagem estejam sempre atualizadas e a manutenção contínua é quase zero depois que todo o processo é automatizado. E, como as configurações necessárias já estão na imagem, salva a hora de configurar manualmente o sistema depois que uma VM foi criada com o sistema operacional base.

O acelerador significativo para obter uma área de trabalho do desenvolvedor para um estado pronto no DevTest Labs está usando imagens personalizadas. A desvantagem de imagens personalizadas é que há algo extras para manter no laboratório. Por exemplo, expiração de versões de avaliação de produtos ao longo do tempo (ou) atualizações de segurança lançadas recentemente não são aplicadas, o que nos forçaria a atualizar a imagem personalizada periodicamente. Com uma fábrica de imagem, você tem uma definição da imagem do check-in no controle do código-fonte e ter um processo automatizado para produzir imagens personalizadas com base na definição.

A solução permite que a velocidade da criação de máquinas virtuais de imagens personalizadas enquanto elimina os custos de manutenção em andamento adicionais. Com essa solução, você pode automaticamente criar imagens personalizadas, distribuí-los a outros laboratórios de desenvolvimento/teste e desativar as imagens antigas. O vídeo a seguir, você aprenderá sobre a fábrica de imagem e como ele é implementado com o DevTest Labs.  Todos os scripts do Powershell do Azure estão disponíveis gratuitamente e está localizado aqui: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Exibição de alto nível da solução
A solução permite que a velocidade da criação de máquinas virtuais de imagens personalizadas enquanto elimina os custos de manutenção em andamento adicionais. Com essa solução, você pode criar imagens personalizadas e distribuí-los a outros laboratórios de desenvolvimento/teste automaticamente. Você usa o DevOps do Azure (anteriormente conhecido como Visual Studio Team Services) como o mecanismo de orquestração para automatizar todas as operações no DevTest Labs.

![Exibição de alto nível da solução](./media/create-image-factory/high-level-view-of-solution.png)

Há um [extensão do VSTS para laboratórios de desenvolvimento/teste](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite que você execute essas etapas individuais:

- Criar imagem personalizada
- Criar VM
- Excluir VM
- Criar ambiente
- Excluir ambiente
- Preencher o ambiente

Usando a extensão do DevTest Labs é uma maneira fácil de começar a criar automaticamente as imagens personalizadas no DevTest Labs.

Há uma implementação alternativa usando o script do PowerShell para um cenário mais complexo. Usando o PowerShell, você pode automatizar totalmente uma fábrica de imagem com base no DevTest Labs que podem ser usados em sua integração contínua e a cadeia de ferramentas de entrega contínua (CI/CD). São os princípios seguidos nesta solução alternativa:

- Atualizações comuns não devem exigir nenhuma alteração para a fábrica de imagem. (por exemplo, adicionando um novo tipo de imagem personalizada, desativação automática de imagens antigas, adicionando um novo 'ponto de extremidade' DevTest Labs para receber imagens personalizadas e assim por diante).
- Alterações comuns têm o suporte de controle do código fonte (infraestrutura como código)
- Receber imagens personalizadas do DevTest Labs não estejam na mesma assinatura do Azure (labs abrangem assinaturas)
- Scripts do PowerShell devem ser reutilizáveis, portanto, podemos pode criar fábricas adicionais conforme necessário

## <a name="next-steps"></a>Próximas etapas
Passar para o próximo artigo nesta seção: [Executar uma fábrica de imagem de DevOps do Azure](image-factory-set-up-devops-lab.md)
