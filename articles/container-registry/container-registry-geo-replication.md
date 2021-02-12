---
title: Replicação geográfica de um registro
description: Comece a criar e gerenciar um registro de contêiner do Azure com replicação geográfica, que permite que o registro atenda a várias regiões com réplicas regionais de vários mestres. A replicação geográfica é um recurso da camada de serviço Premium.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: e5f0fe76b599874afe8d64c293f3d914da5dd243
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705159"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replicação geográfica no Registro de Contêiner do Azure

As empresas que desejam ter uma presença local ou um backup dinâmico optam por executar os serviços de várias regiões do Azure. Como prática recomendada, colocar um Registro de contêiner em cada região em que as imagens são executadas permite operações perto da rede, possibilitando transferências de camada de imagem rápidas e confiáveis. A replicação geográfica permite que um Registro de contêiner do Azure funcione como um único Registro, atendendo a várias regiões com Registros regionais multimestre. 

Um Registro com replicação geográfica oferece os seguintes benefícios:

* Nomes únicos de registro, imagem e marca podem ser usados em várias regiões
* Melhorar o desempenho e a confiabilidade de implantações regionais com acesso ao registro de fechamento de rede
* Reduza os custos de transferência de dados ao extrair as camadas de imagem de um registro local replicado na mesma região ou em um lado próximo do host do contêiner
* Gerenciamento único de um Registro entre várias regiões

> [!NOTE]
> Se você precisar manter cópias de imagens de contêiner em mais de um registro de contêiner do Azure, o Registro de Contêiner do Azure também dará suporte à [importação de imagens](container-registry-import-images.md). Por exemplo, em um fluxo de trabalho de DevOps, você pode importar uma imagem de um registro de desenvolvimento para um registro de produção, sem a necessidade de usar comandos do Docker.
>

## <a name="example-use-case"></a>Caso de uso de exemplo
A Contoso executa um site com presença pública localizado nos EUA, Canadá e Europa. Para atender a esses mercados com conteúdo local e perto da rede, a Contoso executa os clusters do [Serviço de Kubernetes do Azure](../aks/index.yml) (AKS) no Oeste dos EUA, Leste dos EUA, Canadá Central e Europa Ocidental. O aplicativo do site, implantado como uma imagem do Docker, utiliza os mesmos código e imagem em todas as regiões. O conteúdo, local para essa região, é recuperado de um banco de dados, provisionado exclusivamente em cada região. Cada implantação regional tem sua configuração exclusiva para recursos como o banco de dados local.

A equipe de desenvolvimento está localizada em Seattle WA, utilizando o data center do Oeste dos EUA.

![Efetuando push para vários Registros](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Efetuando push para vários Registros*

Antes de usar os recursos de replicação geográfica, a Contoso tinha um Registro no Oeste dos EUA, com um Registro adicional na Europa Ocidental. Para atender a essas regiões diferentes, a equipe de desenvolvimento precisava efetuar push de imagens para dois registros diferentes.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Efetuando pull de vários Registros](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Efetuando pull de vários Registros*

Os desafios comuns de vários Registros incluem:

* Todos os clusters do Leste dos EUA, Oeste dos EUA e Canadá Central efetuam push do Registro do Oeste dos EUA, incorrendo taxas de saída à medida que esses hosts do contêiner remoto efetuam pull de imagens de data centers do Oeste dos EUA.
* A equipe de desenvolvimento deve efetuar push de imagens para Registros do Oeste dos EUA e da Europa Ocidental.
* A equipe de desenvolvimento deve configurar e manter cada implantação regional com nomes de imagem que referenciam o Registro local.
* O acesso ao Registro deve ser configurado para cada região.

## <a name="benefits-of-geo-replication"></a>Benefícios da replicação geográfica

![Efetuando pull de um Registro com replicação geográfica](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Usando o recurso de replicação geográfica do Registro de Contêiner do Azure, estes benefícios são realizados:

* Gerenciar um único Registro em todas as regiões:`contoso.azurecr.io`
* Gerencie uma única configuração de implantações de imagem, pois todas as regiões usam a mesma URL de imagem: `contoso.azurecr.io/public/products/web:1.2`
* Enviar por push para um único registro, enquanto o ACR gerencia a replicação geográfica. O ACR só Replica camadas exclusivas, reduzindo a transferência de dados entre regiões. 
* Configure [WebHooks](container-registry-webhook.md) regionais para notificá-lo de eventos em réplicas específicas.

O registro de contêiner do Azure também dá suporte a [zonas de disponibilidade](zone-redundancy.md) para criar um registro de contêiner do Azure resiliente e de alta disponibilidade em uma região do Azure. A combinação de zonas de disponibilidade para redundância em uma região e replicação geográfica em várias regiões aumenta a confiabilidade e o desempenho de um registro.

## <a name="configure-geo-replication"></a>Configurar a replicação geográfica

Configurar a replicação geográfica é tão fácil quanto clicar em regiões em um mapa. Você também pode gerenciar a replicação geográfica usando ferramentas que incluem os comandos [az acr replication](/cli/azure/acr/replication) na CLI do Azure ou implantar um registro habilitado para replicação geográfica com um [modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

A replicação geográfica é um recurso de [Registros Premium](container-registry-skus.md). Se seu Registro ainda não é Premium, é possível alterar de Básico e Standard para Premium no [Portal do Azure](https://portal.azure.com):

![Como alternar camadas de serviço no portal do Azure](media/container-registry-skus/update-registry-sku.png)

Para configurar a replicação geográfica para o registro do Premium, faça logon no Portal do Azure em https://portal.azure.com.

Navegue até o Registro de Contêiner do Azure e selecione **Replicações**:

![Replicações na interface do usuário de Registro de contêiner do Portal do Azure](media/container-registry-geo-replication/registry-services.png)

Um mapa é exibido mostrando todas as regiões atuais do Azure:

 ![Mapa de região no Portal do Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Hexágonos azuis representam réplicas atuais
* Hexágonos verdes representam regiões de réplica possíveis
* Hexágonos cinza representam regiões do Azure ainda não disponíveis para replicação

Para configurar uma réplica, selecione um hexágono verde e, em seguida, **Criar**:

 ![Criar a interface do usuário de replicação no Portal do Azure](media/container-registry-geo-replication/create-replication.png)

Para configurar réplicas adicionais, selecione os hexágonos verdes para outras regiões e, em seguida, clique em **Criar**.

O ACR começa a sincronizar imagens em réplicas configurados. Depois de concluído, o portal reflete *Pronto*. O status da réplica no portal não é atualizado automaticamente. Use o botão Atualizar para ver o status atualizado.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Considerações sobre o uso de um registro com replicação geográfica

* Cada região em um registro com replicação geográfica é independente após a configuração. Os SLAs de Registro de Contêiner do Azure se aplicam a cada região geográfica replicada.
* Quando você envia imagens por push ou pull de um registro com replicação geográfica, o Gerenciador de Tráfego do Azure em segundo plano envia a solicitação para o registro localizado na região mais próxima de você em termos de latência de rede.
* Depois que você envia uma atualização de imagem ou marca por push para a região mais próxima, demora algum tempo até o Registro de Contêiner do Azure replicar as camadas e manifestos para as demais regiões que você aceitou. As imagens maiores demoram mais tempo para replicar do que as menores. As imagens e marcas são sincronizadas em todas as regiões de replicação com um modelo de consistência eventual.
* Para gerenciar fluxos de trabalho que dependem de atualizações por push para um registro com replicação geográfica, recomendamos que você configure [webhooks](container-registry-webhook.md) para responder a eventos por push. Você pode configurar webhooks regionais dentro de um registro com replicação geográfica para acompanhar eventos por push, conforme eles são concluídos em todas as regiões com replicação geográfica.
* Para atender a BLOBs que representam camadas de conteúdo, o registro de contêiner do Azure usa pontos de extremidade de dados. Você pode habilitar [pontos de extremidade de dados dedicados](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) para seu registro em cada uma das regiões replicadas geograficamente do registro. Esses pontos de extremidade permitem a configuração de regras de acesso a firewall com escopo bem delimitado. Para fins de solução de problemas, opcionalmente, você pode [desabilitar o roteamento para uma replicação](#temporarily-disable-routing-to-replication) , mantendo os dados replicados.
* Se você configurar um [link privado](container-registry-private-link.md) para o registro usando pontos de extremidade privados em uma rede virtual, os pontos de extremidade de dados dedicados em cada uma das regiões com replicação geográfica serão habilitados por padrão. 

## <a name="delete-a-replica"></a>Excluir uma réplica

Depois de configurar uma réplica para o registro, você poderá excluí-la a qualquer momento se ela não for mais necessária. Exclua uma réplica usando o portal do Azure ou outras ferramentas, como o comando [az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete) na CLI do Azure.

Para excluir uma réplica no portal do Azure:

1. Navegue até o Registro de Contêiner do Azure e selecione **Replicações**.
1. Selecione o nome de uma réplica e selecione **Excluir**. Confirme que você realmente deseja excluir a réplica.

Para usar a CLI do Azure para excluir uma réplica de *myregistry* na região leste dos EUA:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Preços da replicação geográfica

A replicação geográfica é um recurso da [camada de serviço Premium](container-registry-skus.md) do Registro de Contêiner do Azure. Quando você replica um Registro para as regiões desejadas, incorre em taxas de Registro do Premium para cada região.

No exemplo anterior, a Contoso consolidou dois registros inoperantes em um, adicionado réplicas ao Oeste dos EUA, Canadá Central e Europa Ocidental. A Contoso pagaria o Premium quatro vezes por mês, sem nenhuma configuração ou gerenciamento adicional. Agora cada região efetua pull de suas imagens localmente, melhorando o desempenho e a confiabilidade sem taxas de saída de rede do Oeste dos EUA para o Canadá e o Leste dos EUA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Solução de problemas de operações de push com registros com replicação geográfica
 
Um cliente do Docker que efetua push de uma imagem para um registro com replicação geográfica pode não efetuar push de todas as camadas de imagem e seu manifesto para uma única região replicada. Isso pode ocorrer porque o Gerenciador de Tráfego do Azure roteia as solicitações de registro para o registro replicado mais próximo da rede. Se o registro tiver duas regiões de replicação *próximas*, as camadas de imagem e o manifesto poderão ser distribuídos para os dois sites, e a operação de push falhará quando o manifesto for validado. Esse problema ocorre devido à maneira como o nome DNS do registro é resolvido em alguns hosts Linux. Esse problema não ocorre no Windows, que fornece um cache DNS do lado do cliente.
 
Se esse problema ocorrer, uma solução será aplicar um cache DNS do lado do cliente, como `dnsmasq`, no host Linux. Isso ajuda a garantir que o nome do registro seja resolvido de forma consistente. Caso esteja usando uma VM do Linux no Azure para efetuar push para um registro, confira as opções em [Opções de resolução de nomes DNS para máquinas virtuais do Linux no Azure](../virtual-machines/linux/azure-dns.md).

Para otimizar a resolução DNS para a réplica mais próxima ao efetuar push de imagens, configure um registro com replicação geográfica nas mesmas regiões do Azure da origem das operações de push ou a região mais próxima ao trabalhar fora do Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Desabilitar temporariamente o roteamento para replicação

Para solucionar problemas de operações com um registro replicado geograficamente, talvez você queira desabilitar temporariamente o roteamento do Gerenciador de tráfego para uma ou mais replicações. A partir do CLI do Azure versão 2,8, você pode configurar uma `--region-endpoint-enabled` opção (versão prévia) ao criar ou atualizar uma região replicada. Quando você define a opção de replicação `--region-endpoint-enabled` como `false` , o Gerenciador de tráfego não roteia mais as solicitações Push ou pull do Docker para essa região. Por padrão, o roteamento para todas as replicações é habilitado e a sincronização de dados entre todas as replicações ocorrerá se o roteamento estiver habilitado ou desabilitado.

Para desabilitar o roteamento para uma replicação existente, primeiro execute [AZ ACR Replication List][az-acr-replication-list] para listar as replicações no registro. Em seguida, execute [AZ ACR Replication Update][az-acr-replication-update] e defina `--region-endpoint-enabled false` para uma replicação específica. Por exemplo, para definir a configuração para a replicação *westus* no *myregistry*:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Para restaurar o roteamento para uma replicação:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Próximas etapas

Confira a série de tutoriais em três partes, [Replicação geográfica no Registro de Contêiner do Azure](container-registry-tutorial-prepare-registry.md). Percorra a criação de um Registro com replicação geográfica, criando um contêiner e, em seguida, implantando-o com um único comando `docker push` em várias instâncias regionais dos Aplicativos Web para Contêineres.

> [!div class="nextstepaction"]
> [Replicação geográfica no Registro de Contêiner do Azure](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
