---
title: Tabela de referência para todas as recomendações da Central de Segurança do Azure
description: Este artigo listará as recomendações de segurança da Central de Segurança do Azure que ajudarão a fortalecer e proteger seus recursos.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 02/09/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 9120f9fed706bd5c2b6dc1273006b995b000cec6
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007581"
---
# <a name="security-recommendations---a-reference-guide"></a>Recomendações de segurança – um guia de referência

Este artigo lista as recomendações que você pode ver na Central de Segurança do Azure. As recomendações mostradas em seu ambiente dependem dos recursos que você está protegendo e a configuração personalizada.

As recomendações da Central de Segurança baseiam-se no [Azure Security Benchmark](../security/benchmarks/introduction.md). O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

Para saber mais sobre como responder a essas recomendações, confira [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

A classificação de segurança é baseada na quantidade de recomendações da Central de Segurança que você executou. Examine a gravidade e o possível impacto de cada recomendação em sua classificação de segurança para decidir qual delas usar primeiro.

> [!TIP]
> Se a descrição de uma recomendação disser "Nenhuma política relacionada", isso geralmente ocorrerá porque essa recomendação depende de uma recomendação diferente e _sua_ política. Por exemplo, a recomendação "As falhas de integridade de Endpoint protection devem ser corrigidas...", depende da recomendação que verifica se uma solução de proteção de ponto de extremidade ainda está _instalada_ ("A solução de Endpoint protection deve estar instalada..."). A recomendação subjacente _tem_ uma política.
> Limitar as políticas a apenas a recomendação básica simplifica o gerenciamento de políticas.

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Recomendações de computação

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Recomendações de dados

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Recomendações de IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Recomendações de rede

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Recomendações preteridas

|Recomendação|Descrição e política relacionada|Severity|Correção rápida habilitada? ([Saiba mais](security-center-remediate-recommendations.md#quick-fix-remediation))|Tipo de recurso|
|----|----|----|----|----|
|**O acesso aos Serviços de Aplicativos deve ser restrito**|Restrinja o acesso aos Serviços de Aplicativos alterando a configuração de rede, para negar o tráfego de entrada de intervalos muito amplos.<br>(Política relacionada: [versão prévia]: O acesso aos Serviços de Aplicativos deve ser restrito)|Alta|N|serviço de aplicativo|
|**As regras para aplicativos Web nos NSGs da IaaS devem ser fortalecidas**|Proteger o NSG (grupo de segurança de rede) de suas máquinas virtuais que estão executando aplicativos Web, com regras do NSG que são excessivamente permissivas com relação às portas do aplicativo Web.<br>(Política relacionada: as regras de NSGs para aplicativos Web em IaaS devem ser fortalecidas)|Alta|N|Máquina virtual|
|**As políticas de segurança de pods devem ser definidas para reduzir o vetor de ataque removendo privilégios de aplicativo desnecessários (versão prévia)**|Defina políticas de segurança de pods para reduzir o vetor de ataque removendo privilégios de aplicativo desnecessários. Recomendamos configurar políticas de segurança de pods para que apenas os pods possam acessar os recursos aos quais eles têm permissões de acesso.<br>(Política relacionada: [versão prévia]: as políticas de segurança de pods devem ser definidas nos Serviços de Kubernetes)|Médio|N|Recursos de computação (contêineres)|
|**Instalar o módulo de segurança da Central de Segurança do Azure para IoT para obter mais visibilidade dos seus dispositivos IoT**|Instale o módulo de segurança da Central de Segurança do Azure para IoT para obter mais visibilidade dos seus dispositivos IoT.|Baixo|N|Dispositivo IoT|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações, confira o seguinte:

- [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
