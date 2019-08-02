---
title: Versões do Kubernetes com suporte no Serviço de Kubernetes do Azure
description: Entender a política de suporte de versão do Kubernetes e o ciclo de vida dos clusters no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a4726444e2103228cd26be3d3a6d516b31e315ee
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304321"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões do Kubernetes com suporte no AKS (Serviço de Kubernetes do Azure)

A comunidade Kubernetes libera versões secundárias aproximadamente a cada três meses. Essas versões incluem novos recursos e melhorias. As versões de patch são mais frequentes (às vezes, semanais) e são destinadas apenas a correções de bugs críticas em uma versão secundária. Essas versões de patch incluem correções de vulnerabilidades de segurança ou de bugs importantes que afetam um grande número de clientes e produtos em execução na produção com base no Kubernetes.

O AKS tem o objetivo de certificar e lançar novas versões do kubernetes dentro de 30 dias após uma versão de upstream, sujeito à estabilidade da versão.

## <a name="kubernetes-versions"></a>Versões do kubernetes

Kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão. Isso significa que cada versão do kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* As versões principais são alteradas quando as alterações de API incompatíveis ou a compatibilidade com versões anteriores podem ser interrompidas.
* As versões secundárias são alteradas quando são feitas alterações de funcionalidade que são compatíveis com versões anteriores a outros lançamentos secundários.
* As versões de patch são alteradas quando são feitas correções de bugs compatíveis com versões anteriores.

Em geral, os usuários devem executar a versão mais recente do patch da versão secundária em execução, por exemplo, se o cluster de produção estiver em *1.13.6* e *1.13.7* for a versão de patch mais recente disponível para a série *1,13* , Você deve atualizar para o *1.13.7* assim que for possível garantir que o cluster seja totalmente corrigido e tenha suporte.

## <a name="kubernetes-version-support-policy"></a>Política de suporte de versão do Kubernetes

O AKS dá suporte a quatro versões secundárias do Kubernetes:

* A versão secundária atual lançada em AKS (N)
* Três versões secundárias anteriores. Cada versão secundária com suporte também dá suporte a dois patches estáveis.

Isso é conhecido como "N-3"-(N (versão mais recente)-3 (versões secundárias)).

Por exemplo, se AKS apresentar *1.13. x* hoje, o suporte será fornecido para as seguintes versões:

Nova versão secundária    |    Lista de versões com suporte
-----------------    |    ----------------------
1.13. x               |    1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Onde "x" e ". a" e ". b" são versões representativas de patch.

Para obter detalhes sobre as comunicações relacionadas a alterações de versão e expectativas, consulte "comunicações" abaixo.

Quando uma nova versão secundária é introduzida, a versão secundária mais antiga e as versões de patch com suporte são preteridas e removidas. Por exemplo, se a lista de versões com suporte atual for:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

E as versões AKS 1.13. x, isso significa que as versões 1.9. x (todas as versões 1,9) serão removidas e não terão suporte.

> [!NOTE]
> Observe que, se os clientes estiverem executando uma versão sem suporte do kubernetes, eles serão solicitados a atualizar ao solicitar suporte para o cluster. Os clusters que executam versões kubernetes sem suporte não são cobertos pelas [políticas de suporte do AKS](https://docs.microsoft.com/azure/aks/support-policies).


Além das versões secundárias acima, o AKS dá suporte às duas versões mais recentes do *patch** de uma determinada versão secundária. Por exemplo, dadas as seguintes versões com suporte:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Se upstream kubernetes lançaram 1.12.3 e 1.11.6 e AKS lançar essas versões de patch, as versões mais antigas do patch serão preteridas e removidas, e a lista de versões com suporte se tornará:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Os clientes não devem fixar a criação do cluster, CI ou outros trabalhos automatizados para versões de patches específicas. 

### <a name="communications"></a>Comunicações

* Para novas  versões secundárias do kubernetes
  * Todos os usuários são notificados publicamente sobre a nova versão e qual versão será removida.
  * Quando uma nova versão de patch é lançada, a versão mais antiga do patch é removida ao mesmo tempo.
  * Os clientes têm **60 dias** a partir da data de notificação pública para atualizar para uma versão de versão secundária com suporte.
* Para novas versões de **patch** do kubernetes
  * Todos os usuários são notificados sobre a versão do novo patch que está sendo lançada e para atualizar para a versão mais recente do patch.
  * Os usuários têm **30 dias** para atualizar para uma versão de patch mais recente e com suporte. Os usuários têm **30 dias** para atualizar para uma versão de patch com suporte antes que o mais antigo seja removido.

AKS define "lançado" como disponibilidade geral, habilitado em todas as medições de SLO/qualidade de serviço e disponível em todas as regiões.

> [!NOTE]
> Os clientes são notificados sobre as versões e as preterições do kubernetes, quando uma versão secundária é preterida/os usuários removidos recebem 60 dias para atualizar para uma versão com suporte. No caso de versões de patch, os clientes recebem 30 dias para atualizar para uma versão com suporte.

As notificações são enviadas por meio de:

* [Notas de versão do AKS](https://aka.ms/aks/releasenotes)
* Notificações de portal do Azure
* [Canal de atualização do Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Exceções de política

AKS reserva o direito de adicionar ou remover versões novas/existentes que foram identificadas para ter uma ou mais correções críticas que afetam bugs ou problemas de segurança sem aviso prévio.

Versões de patch específicas podem ser ignoradas ou a distribuição acelerada dependendo da severidade do bug ou do problema de segurança.

### <a name="azure-portal-and-cli-default-versions"></a>portal do Azure e versões padrão da CLI

Quando você implanta um cluster AKS no portal ou com o CLI do Azure, o cluster é sempre definido como a versão secundária N-1 e o patch mais recente. Por exemplo, se AKs dá suporte a *1.13. x*, *1.12. a* + *1.12. b*, *1.11. a* + *1.11*. b, *1,10. a* + *1,10 b*, a versão padrão para novos clusters é *1.12. b* .

O padrão AKS é N-1 (Minor. latestPatch, ex 1.12. b) para fornecer aos clientes uma versão conhecida, estável e corrigida por padrão.

## <a name="list-currently-supported-versions"></a>Lista de versões compatíveis no momento

Para descobrir quais versões estão disponíveis no momento para sua assinatura e região, use o comando [AZ AKs Get-Versions][az-aks-get-versions] . O exemplo a seguir lista as versões disponíveis do Kubernetes para a região *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

A saída é semelhante ao exemplo a seguir, que mostra que a versão do kubernetes *1.13.5* é a versão mais recente disponível:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>Perguntas Frequentes

**O que acontece quando um cliente atualiza um cluster do Kubernetes com uma versão secundária sem suporte?**

Se você estiver na versão *n-4* , você estará fora do suporte e será solicitado a fazer a atualização. Se a atualização da versão n-4 para o n-3 tiver sucesso, agora você estará dentro de nossas políticas de suporte. Por exemplo:

- Se as versões do AKS com suporte forem *1.13. x*, *1.12. a* + *1.12. b*, *1.11. c* + *1.11 d*e *1,10. e* + *1,10 f* e você está em *1.9. g* ou *1.9. h*, você está fora do suporte.
- Se a atualização de *1.9. g* ou *1.9. h* para *1,10. e* ou *1,10. f* tiver sucesso, você voltará para dentro de nossas políticas de suporte.

Não há suporte para atualizações para versões anteriores à *n-4*. Nesses casos, é recomendado que os clientes criem clusters do AKS e reimplantem suas cargas de trabalho.

**O que significa "fora do suporte"**

' Fora do suporte ' significa que a versão que você está executando está fora da lista de versões com suporte e você será solicitado a atualizar o cluster para uma versão com suporte ao solicitar suporte. Além disso, o AKS não torna nenhum tempo de execução ou outras garantias para clusters fora da lista de versões com suporte.

**O que acontece quando um cliente dimensiona um cluster do Kubernetes com uma versão secundária sem suporte?**

Para as versões secundárias sem suporte no AKS, a escala ou a redução horizontal continua a funcionar sem problemas.

**Um cliente pode permanecer em uma versão do Kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver em uma das versões com suporte do AKS, o cluster estará fora das políticas de suporte do AKS. O Azure não atualiza nem exclui o cluster automaticamente.

**A qual versão a mestre dá suporte quando o cluster do agente não está em uma das versões com suporte do AKS?**

O mestre é atualizado automaticamente para a versão mais recente com suporte.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como atualizar o cluster, consulte [atualizar um cluster do AKS (serviço kubernetes do Azure)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
