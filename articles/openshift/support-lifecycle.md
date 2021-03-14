---
title: Ciclo de vida do suporte do Azure Red Hat OpenShift
description: Entender o ciclo de vida do suporte e as versões com suporte para o Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: fca01c77a1ff47cbeee167eb408ed9f29a1307bd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634321"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Dar suporte ao ciclo de vida para Red Hat OpenShift no Azure 4

O Red Hat lança versões secundárias da OpenShift (plataforma de contêiner do Red Hat) aproximadamente a cada três meses. Essas versões incluem novos recursos e melhorias. As versões de patch são mais frequentes (geralmente semanalmente) e são destinadas apenas a correções de bugs críticos em uma versão secundária. Essas versões de patch podem incluir correções para vulnerabilidades de segurança ou principais bugs.

O Azure Red Hat OpenShift é criado a partir de versões específicas do OCP. Este artigo aborda as versões de OCP que têm suporte para o Azure Red Hat OpenShift e detalhes sobre atualizações, substituições e política de suporte.

## <a name="red-hat-openshift-versions"></a>Versões do Red Hat OpenShift

A plataforma de contêiner do Red Hat OpenShift usa o controle de versão semântico. O controle de versão semântico usa diferentes níveis de números de versão para especificar diferentes níveis de controle de versão. A tabela a seguir ilustra as diferentes partes de um número de versão semântica, neste caso, usando o número de versão de exemplo 4.4.11.

|Versão principal (x)|Versão secundária (y)|Patch (z)|
|-|-|-|
|4|4|11|

Cada número na versão indica compatibilidade geral com a versão anterior:

* **Versão principal**: nenhuma versão principal das versões está planejada neste momento. As versões principais são alteradas quando as alterações de API incompatíveis ou a compatibilidade com versões anteriores podem ser interrompidas.
* **Versão secundária**: lançada aproximadamente a cada três meses. Atualizações de versão secundárias podem incluir adições de recursos, aprimoramentos, substituições, remoções, correções de bugs, aprimoramentos de segurança e outros aprimoramentos.
* **Patches**: normalmente lançados a cada semana ou conforme necessário. Atualizações de versão de patch podem incluir correções de bugs, aprimoramentos de segurança e outras melhorias.

Os clientes devem visar a executar a versão secundária mais recente da versão principal que estão em execução. Por exemplo, se o seu cluster de produção estiver em 4,4 e 4,5 for a versão secundária mais recente disponível para a série 4, você deverá atualizar para o 4,5 assim que puder.

### <a name="upgrade-channels"></a>Atualizar canais

Os canais de atualização estão vinculados a uma versão secundária da OCP (Red Hat OpenShift container Platform). Por exemplo, os canais de atualização OCP 4,4 nunca incluirão uma atualização para uma versão 4,5. Os canais de atualização controlam apenas a seleção de versão e não afetam a versão do cluster.

O Azure Red Hat OpenShift 4 dá suporte apenas a canais estáveis. Por exemplo: estável-4,4.

Você pode usar o canal estável-4,5 para atualizar de uma versão secundária anterior do Azure Red Hat OpenShift. Os clusters atualizados usando canais rápidos, de pré-lançamento e candidatos não terão suporte.

Se você alterar para um canal que não inclui sua versão atual, um alerta será exibido e nenhuma atualização poderá ser recomendada, mas você poderá alterar de volta para o canal original a qualquer momento.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Política de suporte da versão da plataforma de contêiner do Red Hat OpenShift

O Azure Red Hat OpenShift dá suporte a duas versões secundárias de GA (disponibilidade geral) da plataforma de contêiner do Red Hat OpenShift:
* A versão secundária mais recente do GA lançada no Azure Red Hat OpenShift (que iremos nos referir como N)
* Uma versão secundária anterior (N-1)

Se disponível em um canal de atualização estável, versões secundárias mais recentes (N + 1, N + 2) disponíveis em upstream OCP também podem ter suporte.

Atualizações de patch críticas são aplicadas a clusters automaticamente por SRE (engenheiros de confiabilidade do site do Red Hat OpenShift) do Azure. Os clientes que desejam instalar atualizações de patch com antecedência são livres para fazer isso.

Por exemplo, se o Azure Red Hat OpenShift introduz 4.5. z hoje, o suporte é fornecido para as seguintes versões:

|Nova versão secundária|Lista de versões com suporte|
|-|-|
|4,5. z|4,5. z, 4.4. z|

". z" é representativo das versões de patch. Se disponível em um canal de atualização estável, os clientes também podem atualizar para 4.6. z.

Quando uma nova versão secundária é introduzida, a versão secundária mais antiga é preterida e removida. Por exemplo, digamos que a lista de versões com suporte atual seja 4.5. z e 4.4. z. Quando o Azure Red Hat OpenShift lança o 4.6. z, a versão 4.4. z será removida e não terá suporte dentro de 30 dias.

> [!NOTE]
> Observe que, se os clientes estiverem executando uma versão do Red Hat OpenShift sem suporte, eles poderão ser solicitados a atualizar ao solicitarem suporte para o cluster. Os clusters que executam versões do Red Hat OpenShift sem suporte não são cobertos pelo SLA do Azure Red Hat OpenShift.

## <a name="release-and-deprecation-process"></a>Processo de liberação e de reprovação

Você pode fazer referência a versões e substituições futuras da versão no calendário de lançamento do Azure Red Hat OpenShift.

Para novas versões secundárias da plataforma de contêiner do Red Hat OpenShift:
* A equipe do Azure Red Hat OpenShift SRE publica um pré-registro com a data planejada de um novo lançamento de versão e a respectiva reprovação de versão antiga nas [notas de versão do Azure Red Hat OpenShift](https://github.com/Azure/OpenShift/releases) , pelo menos 30 dias antes da remoção.
* A equipe do Azure Red Hat OpenShift SRE publica uma notificação de integridade do serviço disponível para todos os clientes com o Azure Red Hat OpenShift e o acesso ao portal e envia um email para os administradores de assinatura com as datas de remoção de versão planejada.
* Os clientes têm 30 dias desde a remoção da versão até a atualização para uma versão secundária com suporte para continuar recebendo suporte.

Para novas versões de patch da plataforma de contêiner do Red Hat OpenShift:
* Devido à natureza urgente das versões de patch, elas podem ser introduzidas no serviço pela equipe do Azure Red Hat OpenShift SRE à medida que elas se tornam disponíveis.
* Em geral, a equipe do Azure Red Hat OpenShift SRE não executa comunicações amplas para a instalação de novas versões de patch. No entanto, a equipe monitora e valida constantemente os patches de CVE disponíveis para dar suporte a eles em tempo hábil. Se a ação do cliente for necessária, a equipe notificará os clientes sobre a atualização.

## <a name="supported-versions-policy-exceptions"></a>Exceções de política de versões com suporte

A equipe do Azure Red Hat OpenShift SRE se reserva o direito de adicionar ou remover versões novas/existentes ou atrasar versões secundárias futuras, que foram identificadas para ter uma ou mais correções críticas que afetam bugs ou problemas de segurança sem aviso prévio.

Versões de patch específicas podem ser ignoradas ou a distribuição pode ser acelerada dependendo da severidade do bug ou do problema de segurança.

## <a name="azure-portal-and-cli-versions"></a>Versões portal do Azure e CLI

Quando você implanta um cluster do Red Hat OpenShift do Azure no portal ou com o CLI do Azure, o cluster é padronizado para a versão secundária mais recente (N) e o patch crítico mais recente. Por exemplo, se o Azure Red Hat OpenShift der suporte a 4,5. z e 4.4. z, a versão padrão para novas instalações será 4.5. z. Os clientes que desejam usar a versão secundária do upstream OCP mais recente (N + 1, N + 2) podem atualizar seu cluster a qualquer momento para qualquer versão disponível nos canais de atualização estáveis.

## <a name="azure-red-hat-openshift-release-calendar"></a>Calendário de lançamento do Azure Red Hat OpenShift

Consulte o seguinte guia para o [histórico de lançamento da plataforma de contêiner do Red Hat OpenShift (upstream)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|Versão OCP|Versão de upstream|Disponibilidade geral do Azure Red Hat OpenShift|Fim de vida|
|-|-|-|-|
|4.3|Janeiro de 2020|Abril de 2020| Agosto de 2020|
|4.4|Maio de 2020|Julho de 2020|4.6 GA|
|4.5|Julho de 2020| Novembro de 2020|4,7 GA
|4.6|Outubro de 2020| Fevereiro de 2021|4,8 GA|

## <a name="faq"></a>Perguntas frequentes

**O que acontece quando um usuário atualiza um cluster OpenShift com uma versão secundária que não tem suporte?**

Se você estiver na versão N-2 ou mais antiga, isso significa que você está fora do suporte e será solicitado a fazer a atualização. Quando a atualização da versão N-2 para o N-1 tiver sucesso, você voltará para nossas políticas de suporte. Por exemplo:
* Se a versão mais antiga do Azure Red Hat OpenShift com suporte for 4.4. z e você estiver no 4.3. z ou mais antigo, você estará fora do suporte.
* Quando a atualização de 4.3. z a 4.4. z ou superior for realizada com sucesso, você voltará para nossas políticas de suporte.

Não há suporte para reverter o cluster para uma versão anterior ou uma reversão. Há suporte apenas para a atualização para uma versão mais recente.

**O que significa "fora do suporte"?**

"Fora do suporte" significa que a versão que você está executando está fora da lista de versões com suporte, e você pode ser solicitado a atualizar o cluster para uma versão com suporte ao solicitar suporte, a menos que você esteja dentro do período de carência de 30 dias após a reprovação da versão. Além disso, o Azure Red Hat OpenShift não faz nenhuma garantia de tempo de execução ou SLA para clusters fora da lista de versões com suporte no final do período de carência de 30 dias.
