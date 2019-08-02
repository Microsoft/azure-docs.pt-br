---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229221"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso de instância de contêiner do Azure

1. Vá para o [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) página para instâncias de contêiner.

2. Sobre o **Noções básicas de** , insira os detalhes a seguir:

    |Configuração|Valor|
    |--|--|
    |Assinatura|Selecione sua assinatura.|
    |Grupo de recursos|Selecione o grupo de recursos disponíveis ou crie um novo, como `cognitive-services`.|
    |Nome do contêiner|Insira um nome como `cognitive-container-instance`. O nome deve ser inferior em maiusculas.|
    |Location|Selecione uma região para a implantação.|
    |Tipo de Imagem|`Public`|
    |Nome da imagem|Insira o local do contêiner de serviços Cognitivos. O local pode ser o mesmo usado na `docker pull` de comando, consulte a [contêiner repositórios e imagens](../../cognitive-services-container-support.md#container-repositories-and-images) para os nomes de imagem disponível e seu repositório correspondente.|
    |Tipo do SO|`Linux`|
    |Tamanho|Alterar o tamanho de recomendações sugerido para o contêiner de serviço cognitivo específico:<br>2 núcleos de CPU<br>4 GB

3. Sobre o **rede** , insira os detalhes a seguir:

    |Configuração|Valor|
    |--|--|
    |Portas|Defina a porta TCP como `5000`. Expõe o contêiner na porta 5000.|

4. No **Advanced** , insira o necessário **variáveis de ambiente** para o contêiner de configurações do recurso ACI cobrança:

    | Chave | Valor |
    |--|--|
    |`apikey`|Copiado do **chaves** página do recurso. É uma cadeia de caracteres alfanuméricos 32 caracteres sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado do **visão geral** página do recurso.|
    |`eula`|`accept`|

1. Clique em **examinar e criar**
1. Após a validação é bem-sucedida, clique em **criar** para concluir o processo de criação
1. Quando o recurso é implantado com êxito, ele está pronto