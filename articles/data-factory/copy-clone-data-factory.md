---
title: Copiar ou clonar um data factory no Azure Data Factory
description: Saiba como copiar ou clonar um data factory no Azure Data Factory
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1ea16785502ce8a82087b79dd8e7f0014c0059fc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375231"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiar ou clonar um data factory no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como copiar ou clonar um data factory no Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Casos de uso para clonar um data factory

Aqui estão algumas das circunstâncias em que você pode considerar útil copiar ou clonar um data factory:

- **Mover data Factory** para uma nova região. Se você quiser mover seu Data Factory para uma região diferente, a melhor maneira é criar uma cópia na região de destino e excluir a existente.

- **Renomeando data Factory**. O Azure não dá suporte para renomeação de recursos. Se você quiser renomear um data factory, poderá clonar o data factory com um nome diferente e excluir o existente.

- **Depurando alterações** quando os recursos de depuração não forem suficientes. Na maioria dos cenários, você pode usar [debug](iterative-development-debugging.md). Em outros, o teste de alterações em um ambiente de área restrita clonado faz mais sentido. Por exemplo, como os pipelines de ETL com parâmetros se comportariam quando um gatilho é acionado na chegada do arquivo versus na janela de tempo em cascata, pode não ser facilmente testado por meio de depuração sozinha. Nesses casos, talvez você queira clonar um ambiente de área restrita para experimentação. Como Azure Data Factory encargos principalmente pelo número de execuções, uma segunda fábrica não leva a cobranças adicionais.

## <a name="how-to-clone-a-data-factory"></a>Para clonar um data factory

1. Como pré-requisito, primeiro você precisa criar o data factory de destino do portal do Azure.

1. Se você estiver no modo GIT:
    1. Sempre que você publicar por meio do portal, o modelo do Resource Manager da fábrica será salvo no GIT no \_ Branch de publicação do ADF
    1. Conecte a nova fábrica ao _mesmo_ repositório e crie a partir da \_ ramificação de publicação do ADF. Recursos, como pipelines, conjuntos de fontes e gatilhos, serão transferidos

1. Se você estiver no modo dinâmico:
    1. Data Factory interface do usuário permite exportar toda a carga do seu data factory para um arquivo de modelo do Resource Manager e um arquivo de parâmetro. Eles podem ser acessados por meio do **modelo ARM \ exportar o botão modelo do Resource Manager** no Portal.
    1. Você pode fazer as alterações apropriadas no arquivo de parâmetro e alternar os novos valores para a nova fábrica
    1. Em seguida, você pode implantá-lo por meio de métodos padrão de implantação de modelo do Resource Manager.

1. Se você tiver um SelfHosted IntegrationRuntime na fábrica de origem, você precisará precriá-lo com o mesmo nome na fábrica de destino. Se você quiser compartilhar o Integration Runtime SelfHosted entre fábricas diferentes, poderá usar o padrão publicado [aqui](create-shared-self-hosted-integration-runtime-powershell.md) em Sharing SelfHosted ir.

1. Por motivos de segurança, o modelo do Resource Manager gerado não conterá nenhuma informação secreta, por exemplo, senhas para serviços vinculados. Portanto, você precisa fornecer as credenciais como parâmetros de implantação. Se a incolocação de credenciais manualmente não for desejável para suas configurações, considere recuperar as cadeias de conexão e as senhas de Azure Key Vault em vez disso. [Ver mais](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Próximas etapas

Examine as diretrizes para a criação de um data factory no portal do Azure em [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md).
