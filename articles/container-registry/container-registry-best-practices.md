---
title: Práticas recomendadas do registro
description: Saiba como usar o registro de contêiner do Azure efetivamente seguindo estas melhores práticas.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 01c8c7f547be9dd225022fb3315a4bdecc48c2bf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578128"
---
# <a name="best-practices-for-azure-container-registry"></a>Melhores práticas para o Registro de Contêiner do Azure

Ao seguir essas práticas recomendadas, você pode ajudar a maximizar o desempenho e o uso econômico de seu registro privado no Azure para armazenar e implantar imagens de contêiner e outros artefatos.

Para obter informações sobre os conceitos de registro, consulte [sobre registros, repositórios e imagens](container-registry-concepts.md). Consulte também [Recomendações para marcação e controle de versão de imagens de contêiner](container-registry-image-tag-version.md) para obter estratégias para marcação e as imagens de versão no registro. 

## <a name="network-close-deployment"></a>Implantação de fechamento de rede

Crie o registro de contêiner na mesma região do Azure em que você implantar contêineres. Colocar o registro em uma região de fechamento de rede para seus hosts de contêiner pode ajudar a reduzir a latência e o custo.

A implantação de fechamento de rede é um dos principais motivos para usar um registro de contêiner particular. As imagens do docker têm uma [construção de camadas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) eficiente, que permite implantações incrementais. No entanto, os novos nós precisam efetuar o pull de todas as camadas necessárias para uma determinada imagem. Este `docker pull` inicial pode acumular até vários gigabytes. Ter um registro privado perto de sua implantação minimiza a latência de rede.
Além disso, todas as nuvens públicas, incluindo do Azure, implementam taxas de saída de rede. Extrair imagens de um datacenter para outro adiciona taxas de saída de rede, além da latência.

## <a name="geo-replicate-multi-region-deployments"></a>Replicar geograficamente implantações em várias regiões

Use o recurso de [replicação geográfica](container-registry-geo-replication.md) do Registro de Contêiner do Azure se estiver implantando contêineres em várias regiões. Se você estiver atendendo clientes globais de centros de dados locais ou se sua equipe de desenvolvimento estiver em locais diferentes, é possível simplificar o gerenciamento de registro e minimizar a latência por meio da replicação geográfica do registro. Você também pode configurar [WebHooks](container-registry-webhook.md) regionais para notificá-lo de eventos em réplicas específicas, como quando as imagens são enviadas por push.

A replicação geográfica está disponível com registros [Premium](container-registry-skus.md) . Para saber como usar a replicação geográfica, consulte o tutorial em três partes, [Replicação geográfica no Registro de Contêiner do Azure](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>Maximizar o desempenho de pull

Além de colocar imagens perto de suas implantações, as características das suas próprias imagens podem afetar o desempenho de pull.

* **Tamanho da imagem** -minimize os tamanhos das imagens removendo as [camadas](container-registry-concepts.md#manifest) desnecessárias ou reduzindo o tamanho das camadas. Uma maneira de reduzir o tamanho da imagem é usar a abordagem de [Build do Docker de vários estágios](https://docs.docker.com/develop/develop-images/multistage-build/) para incluir apenas os componentes de tempo de execução necessários. 

  Verifique também se a imagem pode incluir uma imagem do sistema operacional base mais clara. E se você usar um ambiente de implantação, como instâncias de contêiner do Azure, que armazena em cache determinadas imagens base, verifique se é possível trocar uma camada de imagem para uma das imagens armazenadas em cache. 
* **Número de camadas** – equilibre o número de camadas usadas. Se você tiver poucos, não se beneficiará da reutilização de camadas e do cache no host. Muitos, e seu ambiente de implantação passa mais tempo deliberando e descompactando. Cinco a 10 camadas é ideal.

Escolha também uma [camada de serviço](container-registry-skus.md) do registro de contêiner do Azure que atenda às suas necessidades de desempenho. A camada Premium fornece a maior largura de banda e a taxa mais alta de operações simultâneas de leitura e gravação quando você tem implantações de alto volume.

## <a name="repository-namespaces"></a>Namespaces do repositório

Usando namespaces de repositório, você pode permitir o compartilhamento de um único registro em vários grupos dentro de sua organização. Os registros podem ser compartilhados entre equipes e implantações. O Registro de Contêiner do Azure oferece suporte a namespaces aninhados, habilitando o isolamento de grupo. No entanto, o registro gerencia todos os repositórios de forma independente, não como uma hierarquia.

Por exemplo, considere as seguintes marcas de imagem de contêiner. Imagens que são usadas em toda a empresa, como `aspnetcore`, são colocadas no namespace raiz, enquanto as imagens de contêiner que pertencem aos grupos de Produtos e Marketing usam cada um seus próprios namespaces.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Grupo de recursos dedicados

Como os registros de contêiner são recursos que são usados em vários hosts de contêiner, um registro deve residir seu próprio grupo de recursos.

Embora você possa experimentar um tipo de host específico, como [instâncias de contêiner do Azure](../container-instances/container-instances-overview.md), provavelmente desejará excluir a instância de contêiner quando terminar. No entanto, você também poderá manter a coleção de imagens que enviou por push para o Registro de Contêiner do Azure. Ao colocar o registro em seu próprio grupo de recursos, você minimiza o risco de excluir acidentalmente a coleção de imagens no registro quando exclui o grupo de recursos de instância do contêiner.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Ao se autenticar com um registro de contêiner do Azure, há dois cenários principais: autenticação individual e autenticação de serviço (ou "sem periférico"). A tabela a seguir fornece uma visão geral sobre esses cenários e o método de autenticação recomendado para cada.

| Type | Cenário de exemplo | Método recomendado |
|---|---|---|
| Identidade individual | Um desenvolvedor que efetua o pull de imagens de ou para seu computador de desenvolvimento. | [az acr login](/cli/azure/acr#az-acr-login) |
| Sem periférico/serviço identidade | Pipelines de implantação e compilação em que o usuário não está diretamente envolvido. | [Entidade de serviço](container-registry-authentication.md#service-principal) |

Para obter informações detalhadas sobre esses e outros cenários de autenticação de registro de contêiner do Azure, consulte [autenticar com um registro de contêiner do Azure](container-registry-authentication.md).

O registro de contêiner do Azure dá suporte a práticas de segurança em sua organização para distribuir tarefas e privilégios para identidades diferentes. Usando o [controle de acesso baseado em função](container-registry-roles.md), atribua as permissões apropriadas a diferentes usuários, entidades de serviço ou outras identidades que executam diferentes operações de registro. Por exemplo, atribua permissões de push a uma entidade de serviço usada em um pipeline de compilação e atribua permissões de pull a uma identidade diferente usada para implantação. Crie [tokens](container-registry-repository-scoped-permissions.md) para um acesso refinado e limitado por tempo a repositórios específicos.

## <a name="manage-registry-size"></a>Gerenciar o tamanho do registro      

As restrições de armazenamento de [cada camada de serviço do registro de contêiner][container-registry-skus] devem se alinhar com um cenário típico: **básico** para introdução, **Standard** para a maioria dos aplicativos de produção e **Premium** para desempenho de hiperescala e [replicação geográfica][container-registry-geo-replication]. Durante a vida útil do registro, você deve gerenciar o tamanho periodicamente excluindo conteúdo não utilizado.

Use o comando da CLI do Azure [az acr show-usage][az-acr-show-usage] para exibir o tamanho atual do registro:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Você também pode encontrar o armazenamento atual usado na **Visão geral** do seu registro no portal do Azure:

![Informações de uso do registro no portal do Azure][registry-overview-quotas]

### <a name="delete-image-data"></a>Excluir dados de imagem

O Registro de Contêiner do Azure dá suporte a vários métodos para excluir dados de imagem do registro de contêiner. É possível excluir imagens por marca, resumo de manifesto ou excluir um repositório inteiro.

Para obter detalhes sobre como excluir dados de imagem do registro, incluindo imagens não marcadas (às vezes chamadas de "pendentes" ou "órfãs"), consulte [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

## <a name="next-steps"></a>Próximas etapas

O registro de contêiner do Azure está disponível em várias camadas (também chamadas de SKUs) que fornecem recursos diferentes. Para obter detalhes sobre as camadas de serviço disponíveis, consulte [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

Para obter recomendações para melhorar a postura de segurança de seus registros de contêiner, consulte [linha de base de segurança do Azure para registro de contêiner do Azure](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md