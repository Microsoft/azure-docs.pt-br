---
title: Isolamento da rede
description: Os usuários podem restringir o acesso público a recursos de QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c2fad19bd84418d41aca1b2e0770eaa3cde488b0
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043352"
---
# <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

Você deve seguir as etapas abaixo para restringir o acesso público a recursos de QnA Maker. Proteja um recurso de serviços cognitivas do acesso público [Configurando a rede virtual](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restringir o acesso ao serviço de aplicativo (tempo de execução QnA)

Você pode adicionar IPs à lista de permissões do serviço de aplicativo para restringir o acesso ou configurar Ambiente do Serviço de Aplicativo para hospedar QnA Maker serviço de aplicativo.

#### <a name="add-ips-to-app-service-allow-list"></a>Adicionar IPs à lista de permissões do serviço de aplicativo

1. Permitir tráfego somente de IPs de serviços cognitivas. Eles já estão incluídos na marca de serviço `CognitiveServicesManagement` . Isso é necessário para criar APIs (criar/atualizar KB) para invocar o serviço de aplicativo e atualizar o serviço de Azure Search de acordo. Confira [mais informações sobre marcas de serviço.](../../../virtual-network/service-tags-overview.md)
2. Certifique-se de também permitir outros pontos de entrada, como o serviço de bot do Azure, QnA Maker portal, etc. para acesso à API de previsão "GenerateAnswer".
3. Siga estas etapas para adicionar os intervalos de endereços IP a uma lista de permissões:

   1. Baixe [intervalos de IP para todas as marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Selecione os IPs de "CognitiveServicesManagement".
   3. Navegue até a seção rede do recurso do serviço de aplicativo e clique na opção "configurar restrição de acesso" para adicionar os IPs a uma lista de permissões.

Também temos um script automatizado para fazer o mesmo para seu serviço de aplicativo. Você pode encontrar o [script do PowerShell para configurar uma lista de permissões](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) no github. Você precisa inserir a ID da assinatura, o grupo de recursos e o nome real do serviço de aplicativo como parâmetros de script. A execução do script adicionará automaticamente os IPs à lista de permissões do serviço de aplicativo.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configurar Ambiente do Serviço de Aplicativo para hospedar QnA Maker serviço de aplicativo
    
O Ambiente do Serviço de Aplicativo (ASE) pode ser usado para hospedar QnA Maker serviço de aplicativo. Siga as etapas abaixo:

1. Crie um Ambiente do Serviço de Aplicativo e marque-o como "externo". Siga o [tutorial](../../../app-service/environment/create-external-ase.md) para obter instruções.
2.  Crie um serviço de aplicativo dentro do Ambiente do Serviço de Aplicativo.
    1. Verifique a configuração do serviço de aplicativo e adicione ' PrimaryEndpointKey ' como uma configuração de aplicativo. O valor de ' PrimaryEndpointKey ' deve ser definido como " \<app-name\> -PrimaryEndpointKey". O nome do aplicativo é definido na URL do serviço de aplicativo. Por exemplo, se a URL do serviço de aplicativo for "mywebsite.myase.p.azurewebsite.net", o nome do aplicativo será "mysite". Nesse caso, o valor de ' PrimaryEndpointKey ' deve ser definido como "mysite-PrimaryEndpointKey".
    2. Crie um serviço de Azure Search.
    3. Verifique se Azure Search e as configurações do aplicativo estão configuradas adequadamente. 
          Siga este [tutorial](../reference-app-service.md?tabs=v1#app-service).
3.  Atualizar o grupo de segurança de rede associado ao Ambiente do Serviço de Aplicativo
    1. Atualize as regras de segurança de entrada criadas previamente de acordo com seus requisitos.
    2. Adicione uma nova regra de segurança de entrada com a fonte como ' marca de serviço ' e a marca de serviço de origem como ' CognitiveServicesManagement '.
       
    ![exceções de porta de entrada](../media/inbound-ports.png)

4.  Crie um QnA Maker instância de serviço cognitiva (Microsoft. Cognitivaservices/accounts) usando Azure Resource Manager, em que QnA Maker ponto de extremidade deve ser definido para o ponto de extremidade do serviço de aplicativo criado acima (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Restringir o acesso ao recurso Pesquisa Cognitiva

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Pesquisa Cognitiva instância pode ser isolada por meio de um ponto de extremidade privado após a criação dos recursos de QnA Maker. As conexões de ponto de extremidade particulares exigem uma VNet por meio da qual a instância de Serviço de Pesquisa pode ser acessada. 

Se o serviço de aplicativo QnA Maker for restrito usando um Ambiente do Serviço de Aplicativo, use a mesma VNet para criar uma conexão de ponto de extremidade privada para a instância de Pesquisa Cognitiva. Crie uma nova entrada DNS na VNet para mapear o ponto de extremidade Pesquisa Cognitiva para o endereço IP do ponto de extremidade particular Pesquisa Cognitiva. 

Se um Ambiente do Serviço de Aplicativo não for usado para o serviço de aplicativo QnAMaker, crie primeiro um novo recurso de VNet e, em seguida, crie a conexão de ponto de extremidade privada para a instância de Pesquisa Cognitiva. Nesse caso, o serviço de aplicativo QnA Maker precisa [ser integrado à VNet](../../../app-service/web-sites-integrate-with-vnet.md) para se conectar à instância de pesquisa cognitiva. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

[Crie pontos de extremidade privados](../reference-private-endpoint.md) para o recurso de Azure Search.

---