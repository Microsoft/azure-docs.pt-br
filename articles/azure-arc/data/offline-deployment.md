---
title: Implantação offline
description: A implantação offline permite que você extraia imagens de contêiner de um registro de contêiner privado em vez de extrair do registro de contêiner da Microsoft.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955358"
---
# <a name="offline-deployment-overview"></a>Visão geral da implantação offline

Normalmente, as imagens de contêiner usadas na criação do controlador de dados Arc do Azure, instâncias gerenciadas do SQL e grupos de servidores de hiperescala PostgreSQL são extraídas diretamente do MCR (registro de contêiner da Microsoft). Em alguns casos, o ambiente no qual você está implantando não terá conectividade com o registro de contêiner da Microsoft.  Para situações como essa, você pode extrair as imagens de contêiner usando um computador, _que tem acesso_ ao registro de contêiner da Microsoft e, em seguida, marcá-las e enviá-las por push para um registro de contêiner privado que possa _ser_ conectado do ambiente no qual você deseja implantar os serviços de dados habilitados para Arc do Azure.

Como as atualizações mensais são fornecidas para os serviços de dados habilitados para o Azure Arc e há um grande número de imagens de contêiner, é melhor executar esse processo de extração, marcação e envio por push das imagens de contêiner para um registro de contêiner privado usando um script.  O script pode ser automatizado ou executado manualmente.

Um [script de exemplo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) pode ser encontrado no repositório GitHub do arco do Azure.

> [!NOTE]
> Esse script requer a instalação do Python e a [CLI do Docker](https://docs.docker.com/install/).

O script irá solicitar interativamente as informações a seguir.  Como alternativa, se você quiser que o script seja executado sem prompts interativos, poderá definir as variáveis de ambiente correspondentes antes de executar o script.

|Prompt|Variável de ambiente|Observações|
|---|---|---|
|Fornecer registro de contêiner de origem-pressione ENTER para usar `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Normalmente, você extrairia as imagens do registro de contêiner da Microsoft, mas se estiver participando de uma visualização privada com um registro diferente, poderá usar as informações fornecidas como parte do programa de visualização.|
|Forneça o repositório do registro de contêiner de origem-pressione ENTER para usar `arcdata` :|SOURCE_DOCKER_REPOSITORY|Se você estiver extraindo do registro de contêiner da Microsoft, o repositório será `arcdata` .|
|Forneça o nome de usuário para o registro de contêiner de origem-pressione ENTER para usar nenhum:|SOURCE_DOCKER_USERNAME|Forneça um valor apenas se você estiver extraindo imagens de contêiner de uma fonte que requer logon.  O registro de contêiner da Microsoft não requer um logon.|
|Forneça a senha para o registro de contêiner de origem-pressione ENTER para usar nenhum:|SOURCE_DOCKER_PASSWORD|Forneça um valor apenas se você estiver extraindo imagens de contêiner de uma fonte que requer logon.  O registro de contêiner da Microsoft não requer um logon. Este é um prompt de senha mascarado.  Você não verá a senha se digitar ou colá-la no.|
|Forneça a marca de imagem de contêiner para as imagens na fonte-pressione ENTER para usar ' `<current monthly release tag>` ':|SOURCE_DOCKER_TAG|O nome da marca padrão será atualizado mensalmente para refletir o mês e o ano da versão atual no registro de contêiner da Microsoft.|
|Forneça o nome DNS ou o endereço IP do registro de contêiner de destino:|TARGET_DOCKER_REGISTRY|O nome DNS ou endereço IP do registro de destino.  Esse é o registro _para_ o qual as imagens serão enviadas.|
|Forneça o repositório do registro de contêiner de destino:|TARGET_DOCKER_REPOSITORY|O repositório no registro de destino para o qual enviar as imagens.|
|Forneça o nome de usuário para o registro de contêiner de destino-pressione ENTER para usar nenhum:|TARGET_DOCKER_USERNAME|O nome de usuário, se houver, que é usado para fazer logon no registro de contêiner de destino.|
|Forneça a senha para o registro de contêiner de destino-pressione ENTER para usar nenhum:|TARGET_DOCKER_PASSWORD|A senha, se houver, usada para fazer logon no registro de contêiner de destino. Este é um prompt de senha mascarado.  Você não verá a senha se digitar ou colá-la no.|
|Forneça a marca de imagem de contêiner para as imagens no destino:|TARGET_DOCKER_TAG|Normalmente, você usaria a mesma marca que a origem para evitar confusão.|