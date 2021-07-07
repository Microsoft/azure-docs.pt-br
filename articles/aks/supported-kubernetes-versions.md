---
title: Versões do Kubernetes com suporte no Serviço de Kubernetes do Azure
description: Entender a política de suporte de versão do Kubernetes e o ciclo de vida dos clusters no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 09/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: c7d06172abd696e386337e563fa29fdbd2d3cdae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493655"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões do Kubernetes com suporte no AKS (Serviço de Kubernetes do Azure)

A comunidade Kubernetes libera versões secundárias aproximadamente a cada três meses. Recentemente, a Comunidade kubernetes [aumentou a janela de suporte para cada versão, de 9 meses a 12 meses](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), a partir da versão 1,19. Essas versões incluem novos recursos e melhorias. As versões de patch são mais frequentes (às vezes semanais) e destinam-se a correções de bugs críticos em uma versão secundária. Essas versões de patch incluem correções para vulnerabilidades de segurança ou principais bugs.

## <a name="kubernetes-versions"></a>Versões do Kubernetes

O kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão, o que significa que cada versão do kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* As versões principais são alteradas quando as alterações de API incompatíveis ou a compatibilidade com versões anteriores podem ser interrompidas.
* As versões secundárias são alteradas quando são feitas alterações de funcionalidade que são compatíveis com versões anteriores a outros lançamentos secundários.
* As versões de patch são alteradas quando são feitas correções de bugs compatíveis com versões anteriores.

Os usuários devem visar executar a versão mais recente do patch da versão secundária em execução, por exemplo, se o cluster de produção estiver ativado **`1.17.7`** e **`1.17.8`** for a versão de patch mais recente disponível para a série *1,17* , você deverá atualizar para o assim que for **`1.17.8`** possível, para garantir que o cluster seja totalmente corrigido e tenha suporte.

## <a name="kubernetes-version-support-policy"></a>Política de suporte de versão do Kubernetes

AKS define uma versão geralmente disponível, como uma versão habilitada em todas as medições de SLO ou SLA e quando disponível em todas as regiões. O AKS dá suporte a três versões secundárias de GA do kubernetes:

* A versão secundária mais recente do GA lançada no AKS (que faremos referência a N).
* Duas versões secundárias anteriores.
* Cada versão secundária com suporte também dá suporte a um máximo de dois (2) patches estáveis.
* O AKS também pode oferecer suporte a versões de visualização, que são rotuladas explicitamente e sujeitas a [termos e condições de visualização][preview-terms].

> [!NOTE]
> O AKS usa práticas de implantação segura que envolvem a implantação gradual de região. Isso significa que pode levar até 10 dias úteis para um novo lançamento ou uma nova versão estar disponível em todas as regiões.

A janela com suporte das versões do kubernetes no AKS é conhecida como "N-2": (N (versão mais recente)-2 (versões secundárias)).

Por exemplo, se AKS apresenta *1.17. a* hoje, o suporte é fornecido para as seguintes versões:

Nova versão secundária    |    Lista de versões com suporte
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Onde ". letter" é representativo das versões de patch.

Quando uma nova versão secundária é introduzida, a versão secundária mais antiga e as versões de patch com suporte são preteridas e removidas. Por exemplo, se a lista de versões com suporte atual for:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

E o AKS libera 1,18. \* , isso significa que todas as 1,15. as \* versões serão removidas e ficarão sem suporte em 30 dias.

> [!NOTE]
> Observe que, se os clientes estiverem executando uma versão sem suporte do kubernetes, eles serão solicitados a atualizar ao solicitar suporte para o cluster. Os clusters que executam versões kubernetes sem suporte não são cobertos pelas [políticas de suporte do AKS](./support-policies.md).

Além do acima, o AKS dá suporte a um máximo de duas versões de **patch** de uma determinada versão secundária. Portanto, dadas as seguintes versões com suporte:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Se AKS versões `1.17.9` e `1.16.11` as versões mais antigas do patch forem preteridas e removidas, e a lista de versões com suporte se tornar:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Versões com suporte `kubectl`

Você pode usar uma versão secundária mais antiga ou mais nova de `kubectl` em relação à sua versão do *Kube-apiserver* , que é consistente com a [política de suporte do kubernetes para kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Por exemplo, se seu *Kube-apiserver* estiver em *1,17*, você poderá usar as versões *1,16* a *1,18* de `kubectl` com esse *Kube-apiserver*.

Para instalar ou atualizar sua versão do `kubectl` , execute `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Processo de liberação e de reprovação

Você pode fazer referência a versões e substituições futuras da versão no calendário de versões do [AKs kubernetes](#aks-kubernetes-release-calendar).

Para novas versões **secundárias** do kubernetes
1. O AKS publica um pré-registro com a data planejada de um novo lançamento de versão e a respectiva substituição de versão antiga nas [notas](https://aka.ms/aks/releasenotes) de versão do AKs, pelo menos 30 dias antes da remoção.
2. O AKS publica uma [notificação de integridade do serviço](../service-health/service-health-overview.md) disponível para todos os usuários com acesso ao portal e do AKs e envia um email para os administradores de assinatura com as datas de remoção da versão planejada.
````
To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
````
3. Os usuários têm **30 dias** desde a remoção da versão até a atualização para uma versão secundária com suporte para continuar recebendo suporte.

Para novas versões de **patch** do kubernetes
  * Devido à natureza urgente das versões de patch, elas podem ser introduzidas no serviço à medida que se tornam disponíveis.
  * Em geral, o AKS não faz comunicações amplas para o lançamento das novas versões de patch. No entanto, o AKS monitora e valida constantemente os patches de CVE disponíveis para dar suporte a eles no AKS em tempo hábil. Se um patch crítico for encontrado ou a ação do usuário for necessária, o AKS notificará os usuários a atualizar para o patch recentemente disponível.
  * Os usuários têm **30 dias** a partir do momento em que uma versão de patch é removida do AKS para atualizar para um patch com suporte e continuar recebendo suporte.

### <a name="supported-versions-policy-exceptions"></a>Exceções de política de versões com suporte

AKS reserva o direito de adicionar ou remover versões novas/existentes que foram identificadas para ter uma ou mais correções críticas que afetam bugs ou problemas de segurança sem aviso prévio.

Versões de patch específicas podem ser ignoradas ou a distribuição acelerada dependendo da severidade do bug ou do problema de segurança.

## <a name="azure-portal-and-cli-versions"></a>Versões portal do Azure e CLI

Quando você implanta um cluster AKS no portal ou com o CLI do Azure, o cluster é padronizado para a versão secundária N-1 e o patch mais recente. Por exemplo, se AKS dá suporte a *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e* e *1.15. f*, a versão padrão selecionada é *1.16. c*.

Para descobrir quais versões estão disponíveis atualmente para sua assinatura e região, use o comando [az aks get-versions][az-aks-get-versions]. O exemplo a seguir lista as versões disponíveis do Kubernetes para a região *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Calendário de versão do AKS kubernetes

Para obter o histórico de lançamentos anteriores, consulte [kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Versão do K8s | Versão de upstream  | Visualização do AKS  | GA AKS  | Fim da vida útil |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Dec-09-19  | Jan 2019   | Julho de 2020  | 1,20 GA | 
| 1,18  | Março de 23-20  | Maio de 2020   | 2020 de agosto  | 1,21 GA | 
| 1,19  | Agosto de 04-20  | Setembro de 2020   | 2020 de novembro  | 1,22 GA | 
| 1,20  | Dec-08-20  | Jan 2021   | Mar de 2021  | 1,23 GA |
| 1.21  | Abr-08-21 * | Maio de 2021   | Jun 2021  | 1,24 GA |

\* A versão do upstream do kubernetes 1,21 está sujeita a alterações como o calendário upstream ainda para ser finalizado.


## <a name="faq"></a>Perguntas frequentes

**Com que frequência devo esperar para atualizar as versões do kubernetes para manter o suporte?**

A partir do kubernetes 1,19, a [comunidade de código-fonte aberto expandiu o suporte para 1 ano](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS confirmações para habilitar patches e suporte correspondentes aos compromissos upstream, no mínimo. Isso significa que, a partir de clusters AKS em 1,19, você poderá atualizar no mínimo uma vez por ano para permanecer em uma versão com suporte. Para versões em 1,18 ou abaixo, a janela de suporte permanece a 9 meses, o que requer uma atualização uma vez a cada 9 meses para permanecer em uma versão com suporte. É altamente recomendável testar regularmente as novas versões e estar preparado para atualizar para versões mais recentes para capturar os aprimoramentos estáveis mais recentes no kubernetes.

**O que acontece quando um usuário atualiza um cluster kubernetes com uma versão secundária que não tem suporte?**

Se você estiver na versão *n-3* ou mais antiga, isso significa que você está fora do suporte e será solicitado a fazer a atualização. Quando a atualização da versão n-3 para o n-2 tiver sucesso, você voltará para nossas políticas de suporte. Por exemplo:

- Se a versão mais antiga do AKS com suporte for *1.15. a* e você estiver no *1.14. b* ou mais antigo, você estará fora do suporte.
- Quando a atualização de *1.14. b* para *1.15. a* ou superior for realizada com sucesso, você voltará para nossas políticas de suporte.

Não há suporte para downgrades.

**O que significa ' fora do suporte '**

' Fora do suporte ' significa que a versão que você está executando está fora da lista de versões com suporte e será solicitado que você atualize o cluster para uma versão com suporte ao solicitar suporte, a menos que você esteja dentro do período de carência de 30 dias após a reprovação da versão. Além disso, o AKS não torna nenhum tempo de execução nem outras garantias para clusters fora da lista de versões com suporte.

**O que acontece quando um usuário dimensiona um cluster kubernetes com uma versão secundária que não tem suporte?**

Para versões secundárias sem suporte do AKS, o dimensionamento ou saída deve continuar funcionando, mas não há nenhuma garantia de qualidade de serviço, portanto, é altamente recomendável atualizar para colocar o cluster de volta no suporte.

**Um usuário pode permanecer em uma versão do kubernetes para sempre?**

Se um cluster estiver sem suporte para mais de três (3) versões secundárias e tiver sido considerado para transportar riscos de segurança, o Azure o contatará para atualizar o cluster de forma proativa. Se você não executar outras ações, o Azure reservará o direito de atualizar automaticamente o cluster em seu nome.

**A qual versão o plano de controle dá suporte se o pool de nós não estiver em uma das versões do AKS com suporte?**

O plano de controle deve estar dentro de uma janela de versões de todos os pools de nós. Para obter detalhes sobre como atualizar o plano de controle ou pools de nós, visite a documentação sobre como [Atualizar pools de nós](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Posso ignorar várias versões do AKS durante a atualização do cluster?**

Quando você atualiza um cluster AKS com suporte, as versões secundárias do kubernetes não podem ser ignoradas. Por exemplo, as atualizações entre *1.12. x*  ->  *1.13. x* ou *1.13. x*  ->  *1.14.* x são permitidas, no entanto *1.12. x*  ->  *1.14. x* não é.

Para atualizar, de *1.12. x*  ->  *1.14.* x, primeiro atualize de *1.12. x*  ->  *1.13. x* e, em seguida, atualize de *1.13. x*  ->  *1.14. x*.

Ignorar várias versões só pode ser feito ao atualizar de uma versão sem suporte de volta para uma versão com suporte. Por exemplo, atualizar de um *1,10. x* sem suporte > um *1.15. x* com suporte pode ser concluído.

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como atualizar seu cluster, confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
