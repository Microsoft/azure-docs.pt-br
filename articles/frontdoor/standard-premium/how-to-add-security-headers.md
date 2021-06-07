---
title: Configurar cabeçalhos de segurança com o conjunto de regras Standard/Premium (visualização) do Azure front door
description: Este artigo fornece orientação sobre como usar o conjunto de regras para configurar cabeçalhos de segurança.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098405"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Configurar cabeçalhos de segurança com o conjunto de regras Standard/Premium (visualização) do Azure front door

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

Este artigo mostra como implementar cabeçalhos de segurança para evitar vulnerabilidades baseadas em navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Os atributos baseados em segurança também podem ser definidos com cookies.

O exemplo a seguir mostra como adicionar um cabeçalho Content-Security-Policy a todas as solicitações de entrada que correspondem ao caminho na rota. Aqui, só permitiremos a execução de scripts do nosso site confiável, **https://apiphany.portal.azure-api.net** , no aplicativo.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Antes de configurar os cabeçalhos de segurança de configuração, você deve primeiro criar uma porta frontal. Para saber mais, confira [Início Rápido: Criar um Front Door](create-front-door-portal.md).
* Examine como [configurar um conjunto de regras](how-to-configure-rule-set.md) se você ainda não usou o recurso conjunto de regras antes.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adicionar um cabeçalho Content-Security-Policy no portal do Azure

1. Vá para o perfil padrão/Premium da porta de front-end do Azure e selecione **conjunto de regras** em **configurações.**

1. Selecione **Adicionar** para adicionar um novo conjunto de regras. Dê ao conjunto de regras um **nome** e forneça um **nome** para a regra. Selecione **Adicionar uma ação** e, em seguida, selecione **cabeçalho de resposta**.

1. Defina o operador como **acrescentar** para adicionar esse cabeçalho como uma resposta a todas as solicitações de entrada para essa rota.

1. Adicione o nome do cabeçalho: **Content-Security-Policy** e defina os valores que esse cabeçalho deverá aceitar. Nesse cenário, escolhemos *"script-src ' self ' https://apiphany.portal.azure-api.net "*.

1. Depois de adicionar todas as regras que você deseja para sua configuração, não se esqueça de associar o conjunto de regras a uma rota. Esta etapa é *necessária* para permitir que o conjunto de regras execute uma ação. 

> [!NOTE]
> Neste cenário, não adicionamos [condições de correspondência](concept-rule-set-match-conditions.md) à regra. Todas as solicitações de entrada que correspondem ao caminho definido na rota associada terão essa regra aplicada. Caso deseje que ela só se aplique a um subconjunto dessas solicitações, lembre-se de adicionar as **condições de correspondência** específicas a essa regra.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="deleting-a-rule"></a>Excluindo uma regra

Nas etapas anteriores, você configurou o cabeçalho de política de segurança de conteúdo com o conjunto de regras. Se você não quiser mais uma regra, poderá selecionar o nome do conjunto de regras e, em seguida, selecionar excluir regra. 

### <a name="deleting-a-rule-set"></a>Excluindo um conjunto de regras

Se você quiser excluir um conjunto de regras, certifique-se de desassociá-lo de todas as rotas antes de excluir. Para obter orientações detalhadas sobre como excluir um conjunto de regras, consulte [Configurar o conjunto de regras](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar um firewall do aplicativo Web para sua porta frontal, consulte [Firewall do aplicativo Web e porta frontal](../../web-application-firewall/afds/afds-overview.md).
