---
title: Grupos de contêineres das Instâncias de Contêiner do Azure
description: Entender como os grupos de vários contêineres funcionam nas instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b17004e7821bcac61ca98afdbeaf87644da2a441
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326046"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contêineres em Instâncias de Contêiner do Azure

O recurso de nível superior em Instâncias de Contêiner do Azure é um *grupo de contêineres*. Este artigo descreve o que são os grupos de contêineres e os tipos de cenários que eles permitem.

## <a name="how-a-container-group-works"></a>Como um grupo de contêineres funciona

Um grupo de contêineres é uma coleção de contêineres que são agendados no mesmo computador host. Os contêineres em um grupo de contêineres compartilham um ciclo de vida, recursos, rede local e volumes de armazenamento. Ele é semelhante em conceito a um *Pod* em [kubernetes][kubernetes-pod].

O diagrama a seguir mostra um exemplo de um grupo de contêineres que inclui vários contêineres:

![Diagrama de grupos de contêineres][container-groups-example]

Este grupo de contêineres de exemplo:

* Está agendado em um único computador host.
* É atribuído um rótulo de nome DNS.
* Expõe um único endereço IP público, com uma porta exposta.
* Consiste em dois contêineres. Um contêiner escuta na porta 80, enquanto o outro escuta na porta 5000.
* Inclui dois compartilhamentos de arquivos do Azure como montagens de volume e cada contêiner monta um dos compartilhamentos localmente.

> [!NOTE]
> Atualmente, os grupos de vários contêineres dão suporte apenas a contêineres do Linux. Para contêineres do Windows, as instâncias de contêiner do Azure dão suporte apenas à implantação de uma única instância. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers)do serviço.

## <a name="deployment"></a>Implantação

Aqui estão duas maneiras comuns de implantar um grupo de vários contêineres: Use um [modelo][resource-manager template] or a [YAML file][yaml-file]do Resource Manager. Um modelo do Resource Manager é recomendado quando você precisa implantar recursos adicionais de serviço do Azure (por exemplo, um [compartilhamento de arquivos]do Azure, arquivos[do]Azure) ao implantar as instâncias de contêiner. Devido à natureza mais concisa do formato YAML, um arquivo YAML é recomendado quando sua implantação inclui apenas instâncias de contêiner.

Para preservar a configuração de um grupo de contêineres, você pode exportar a configuração para um arquivo YAML usando o comando de CLI do Azure [AZ contêiner Export][az-container-export]. A exportação permite que você armazene as configurações do grupo de contêineres no controle de versão para "configuração como código". Ou então, usar o arquivo exportado como ponto de partida ao desenvolver uma nova configuração em YAML.

## <a name="resource-allocation"></a>Alocação de recurso

As instâncias de contêiner do Azure alocam recursos como CPUs, memória e, opcionalmente, [GPUs][gpus] (preview) to a container group by adding the [resource requests][resource-requests] das instâncias no grupo. Usando recursos de CPU como exemplo, se você criar um grupo de contêineres com duas instâncias, cada uma solicitando 1 CPU, o grupo de contêineres tem 2 CPUs alocadas.

O máximo de recursos disponíveis para um grupo de contêineres depende da [região do Azure][region-availability] usada para a implantação.

### <a name="container-resource-requests-and-limits"></a>Limites e solicitações de recursos de contêiner

* Por padrão, as instâncias de contêiner em um grupo compartilham os recursos solicitados do grupo. Em um grupo com duas instâncias, cada uma solicitando 1 CPU, o grupo como todo tem acesso a duas CPUs. Cada instância pode usar até as duas CPUs e as instâncias podem competir pelo recurso de CPU enquanto estiverem em execução.

* Para limitar o uso de recursos por uma instância em um grupo, opcionalmente, defina um [limite de recurso][resource-limits] para a instância. Em um grupo com duas instâncias solicitando 1 CPU, um de seus contêineres pode exigir mais CPUs para execução do que o outro.

  Nesse cenário, você pode definir um limite de recursos de CPU de 0,5 para uma instância e um limite de 2 CPUs para o segundo. Essa configuração limita o uso de recursos do primeiro contêiner a 0,5 de CPU, permitindo que o segundo contêiner use até as 2 CPUs completas, se disponíveis.

Para obter mais informações, consulte a propriedade [ResourceRequirements][resource-requirements] na API REST dos grupos de contêineres.

### <a name="minimum-and-maximum-allocation"></a>Alocação mínima e máxima

* Aloque um **mínimo** de 1 CPU e 1 GB de memória para um grupo de contêineres. As instâncias de contêiner individuais dentro de um grupo podem ser provisionadas com menos de 1 CPU e 1 GB de memória. 

* Para obter o **máximo** de recursos em um grupo de contêineres, consulte [disponibilidade de recursos][region-availability] para instâncias de contêiner do Azure na região de implantação.

## <a name="networking"></a>Rede

Grupos de contêineres compartilham um endereço IP e um namespace de porta nesse endereço IP. Para permitir que clientes externos alcancem um contêiner dentro do grupo, você deve expor a porta no endereço IP e do contêiner. Como os contêineres no grupo compartilham um namespace de porta, o mapeamento de porta não tem suporte. Os contêineres dentro de um grupo podem alcançar uns aos outros por meio de localhost nas portas que foram expostas, mesmo se essas portas não estiverem expostas externamente no endereço IP do grupo.

Opcionalmente, implante grupos de contêineres em uma [rede virtual do Azure][virtual-network] (versão prévia) para permitir que os contêineres se comuniquem com segurança com outros recursos na rede virtual.

## <a name="storage"></a>Armazenamento

Você pode especificar volumes externos para montar dentro de um grupo de contêineres. Você pode mapear os volumes para caminhos específicos dentro dos contêineres individuais em um grupo.

## <a name="common-scenarios"></a>Cenários comuns

Grupos de vários contêineres são úteis quando você deseja dividir uma única tarefa funcional em um pequeno número de imagens de contêiner. Essas imagens podem, então, ser fornecidas por equipes diferentes e têm requisitos de recursos separados.

Os exemplos de uso podem incluir:

* Um contêiner que atende a um aplicativo Web e um contêiner efetuando pull do conteúdo mais recente do controle do código-fonte.
* Um contêiner de aplicativo e um contêiner de log. O contêiner de log coleta logs e métricas de saída do aplicativo principal e grava-as em armazenamento de longo prazo.
* Um contêiner de aplicativo e um contêiner de monitoramento. O contêiner de monitoramento faz uma solicitação periódica ao aplicativo para garantir que ele esteja em execução e respondendo corretamente e emite um alerta em caso negativo.
* Um contêiner de front-end e um contêiner de back-end. O front-end pode servir a um aplicativo Web, com o back-end executando um serviço para recuperar dados. 

## <a name="next-steps"></a>Próximas etapas

Saiba como implantar um grupo de contêineres com vários contêineres com um modelo do Azure Resource Manager:

> [!div class="nextstepaction"]
> [Implantar um grupo de contêineres][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
