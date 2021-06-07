---
title: Exemplos de modelo do Azure Resource Manager – Azure Front Door
description: Saiba mais sobre os exemplos do modelo do Resource Manager para o Azure Front Door, incluindo modelos para criar um Front Door básico e configurar a limitação da taxa do Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: duau
ms.openlocfilehash: bac1df020bf2a683fc04a4d05ae73311e149f70c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511766"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modelo de implantação do Azure Resource Manager para o Front Door

A tabela a seguir inclui links para modelos dos modelos de implantação do Azure Resource Manager para o Azure Front Door. 

| Modelo | Descrição |
| ---| ---|
| [Criar um Front Door básico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Cria uma configuração básica de Front Door com um único back-end. |
| [Criar um Front Door com vários back-ends e pools de back-end e roteamento com base em URL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Cria um Front Door com balanceamento de carga configurado para vários back-ends em um pool de back-end e também entre pools de back-end com base no caminho da URL. |
| [Integrar um domínio personalizado com o Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Adicionar um domínio personalizado ao seu Front Door. |
| [Criar um Front Door com filtragem geográfica](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Crie um Front Door que permite/bloqueia o tráfego de determinados países. |
| [Controlar as investigações de integridade para os back-ends no Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Atualize o Front Door para alterar as configurações de investigação de integridade, atualizando o caminho de investigação e também os intervalos nos quais as investigações serão enviadas. |
| [Criar o Front Door com a configuração de back-end Ativa/Em espera](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Cria um Front Door que demonstra o roteamento baseado em prioridade para topologia de aplicativo ativa/em espera, ou seja, enviar por padrão todo o tráfego para o back-end primário (prioridade mais alta) até ele ficar não disponível. |
| [Criar um Front Door com o cache habilitado para determinadas rotas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Cria um Front Door com o cache habilitado para a configuração de roteamento definida, consequentemente armazenando em cache todos os ativos estáticos para sua carga de trabalho. |
| [Configurar a afinidade de sessão para seus nomes de host do Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Atualiza um Front Door para habilitar a afinidade de sessão para o host de front-end, enviando assim o tráfego subsequente da mesma sessão de usuário para o mesmo back-end. |
| [Configurar o Front Door para a lista de permitidos ou a lista de bloqueados de IP do cliente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Configura um Front Door para restringir o tráfego de certos IPs de cliente, usando controle de acesso personalizado com esses IPs. |
| [Configurar o Front Door para realizar uma ação com parâmetros HTTP específicos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Configura um Front Door para permitir ou bloquear determinado tráfego de acordo com os parâmetros HTTP na solicitação de entrada, usando regras de controle de acesso personalizadas com esses parâmetros. |
| [Configurar a limitação de taxa do Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Configura um Front Door para limitar a taxa de tráfego de entrada para um determinado host de front-end. |
| | |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
