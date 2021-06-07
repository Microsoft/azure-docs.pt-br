---
title: 'Início Rápido: configurar a alta disponibilidade com o Azure Front Door – CLI do Azure'
description: Este início rápido mostrará como usar o Azure Front Door para criar um aplicativo Web global de alta disponibilidade e de alto desempenho usando a CLI do Azure.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: a64c91910ba65901a6d1374df9633062398a90e4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067642"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Início Rápido: criar um Front Door para um aplicativo Web global altamente disponível usando a CLI do Azure

Comece a usar o Azure Front Door empregando a CLI do Azure para criar um aplicativo Web global de alta disponibilidade e de alto desempenho.

O Front Door direciona o tráfego da Web para recursos específicos em um pool de back-end. Você define o domínio de front-end, adiciona recursos a um pool de back-end e cria uma regra de roteamento. Este artigo usa uma configuração simples de um pool de back-end com dois recursos de aplicativo Web e apenas uma regra de roteamento usando o caminho padrão correspondente a "/*".

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- CLI do Azure instalada localmente ou Azure Cloud Shell
- Verifique se a extensão de front door foi adicionada à sua CLI do Azure

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você prefere instalar e usar a CLI localmente, este guia de início rápido exige a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo.

Para este início rápido, você precisa de dois grupos de recursos. Uma na região *EUA Central* e o segundo na região *Centro-Sul dos EUA*.

Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de um aplicativo Web

Duas instâncias de um aplicativo Web em execução em diferentes regiões do Azure são necessárias para este início rápido. Ambas as instâncias do aplicativo Web são executadas no modo ativo/ativo, portanto, qualquer uma pode atender ao tráfego.

Se você ainda não tiver um aplicativo Web, use o script a seguir para configurar dois aplicativos Web de exemplo.

### <a name="create-app-service-plans"></a>Criar planos do Serviço de Aplicativo

Para criar os aplicativos Web, você precisará de dois planos do Serviço de Aplicativo: um na região *EUA Central* e o segundo na região *Centro-Sul dos EUA*.

Crie planos do Serviço de Aplicativo com o [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
--resource-group myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Criar aplicativos Web

A execução dos comandos a seguir criará um aplicativo Web em cada um dos planos do Serviço de Aplicativo na etapa anterior. Os nomes dos aplicativos Web devem ser globalmente exclusivos.

Criar aplicativo Web com o [az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Anote o nome de host padrão de cada aplicativo Web para que você possa definir os endereços de back-end ao implantar o Front Door na próxima etapa.

## <a name="create-the-front-door"></a>Criar o Front Door

Crie um Front Door básico com as configurações padrão de balanceamento de carga, investigação de integridade e regras de roteamento executando o seguinte:

Crie Front Door com o [az network front-door create](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--resource-group:** Especifique um grupo de recursos no qual você deseja implantar o Front Door.

**--name:** Especifique um nome globalmente exclusivo para o Azure Front Door. 

**--accepted-protocols:** Os valores aceitos são **http** e **https**. Se você quiser usar ambos, especifique ambos separados por um espaço.

**--backend-address:** Defina o nome do host de ambos aplicativos Web aqui, separados por um espaço.

Depois que a implantação for concluída com sucesso, anote o nome do host na seção *frontEndpoints*.

## <a name="test-the-front-door"></a>Testar o Front Door

Abra um navegador da Web e insira o nome do host obtido por meio dos comandos. O Front Door direcionará sua solicitação para um dos recursos de back-end.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Página de testes do Front Door":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dos recursos criados com o Front Door, exclua ambos os grupos de recursos. Ao excluir o grupo de recursos, você exclui também o Front Door e todos os recursos relacionados. 

Para excluir o grupo de recursos, use [az group delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:
* Front Door
* Dois aplicativos Web

Para saber como adicionar um domínio personalizado ao Front Door, avance para os tutoriais do Front Door.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](front-door-custom-domain.md)
