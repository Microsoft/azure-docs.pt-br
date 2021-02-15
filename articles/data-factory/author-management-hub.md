---
title: Hub de gerenciamento
description: Gerenciar suas conexões, configuração de controle do código-fonte e propriedades de criação global no Hub de gerenciamento de Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 02/01/2021
ms.openlocfilehash: b4b9ecef84f8ffcc82107299ad6603466380d1c0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371491"
---
# <a name="management-hub-in-azure-data-factory"></a>Hub de gerenciamento no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Hub de gerenciamento, acessado pela guia *gerenciar* no Azure data Factory UX, é um portal que hospeda ações de gerenciamento global para seu data Factory. Aqui, você pode gerenciar suas conexões com armazenamentos de dados e computações externas, configuração de controle do código-fonte e configurações de gatilho.

## <a name="manage-connections"></a>Gerenciar conexões

### <a name="linked-services"></a>Serviços vinculados

Os serviços vinculados definem as informações de conexão para Azure Data Factory se conectar a armazenamentos de dados externos e ambientes de computação. Para obter mais informações, consulte [conceitos de serviços vinculados](concepts-linked-services.md). A criação, edição e exclusão do serviço vinculado são feitas no Hub de gerenciamento.

![Gerenciar serviços vinculados](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Runtimes de integração

Um tempo de execução de integração é uma infraestrutura de computação usada pelo Azure Data Factory para fornecer recursos de integração de dados em diferentes ambientes de rede. Para obter mais informações, saiba mais sobre os [conceitos do Integration Runtime](concepts-integration-runtime.md). No Hub de gerenciamento, você pode criar, excluir e monitorar seus tempos de execução de integração.

![Gerenciar runtimes de integração](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Gerenciar controle do código-fonte

### <a name="git-configuration"></a>Configuração do git

Você pode Exibir/editar todas as informações relacionadas ao git nas definições de configuração do git no Hub de gerenciamento. 

As últimas informações de confirmação publicadas também estão listadas e podem ajudar a entender a confirmação exata, que foi publicada/implantada pela última vez entre ambientes. Ele também pode ser útil ao fazer hot fixes em produção.

Para obter mais informações, saiba mais sobre o [controle do código-fonte no Azure data Factory](source-control.md).

![Gerenciar repositório git](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Modelo de parametrização

Para substituir os parâmetros de modelo do Resource Manager gerados ao publicar a partir da ramificação de colaboração, você pode gerar ou editar um arquivo de parâmetros personalizado. Para obter mais informações, saiba como [usar parâmetros personalizados no modelo do Resource Manager](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template). O modelo de parametrização só está disponível ao trabalhar em um repositório git. Se o *arm-template-parameters-definition.jsno* arquivo não existir no Branch de trabalho, a edição do modelo padrão irá gerá-lo.

![Gerenciar parâmetros personalizados](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Gerenciar a criação

### <a name="triggers"></a>Gatilhos

Os gatilhos determinam quando uma execução de pipeline deve ser inicializada. Atualmente, os gatilhos podem estar em uma agenda de relógio de parede, operar em um intervalo periódico ou depender de um evento. Para obter mais informações, saiba mais sobre a [execução do gatilho](concepts-pipeline-execution-triggers.md#trigger-execution). No Hub de gerenciamento, você pode criar, editar, excluir ou exibir o estado atual de um gatilho.

![Captura de tela que mostra onde criar, editar, excluir ou exibir o estado atual de um gatilho.](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Parâmetros globais

Os parâmetros globais são constantes em um data factory que podem ser consumidos por um pipeline em qualquer expressão. Para obter mais informações, saiba mais sobre [parâmetros globais](author-global-parameters.md).

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar um repositório git](source-control.md) para o ADF


