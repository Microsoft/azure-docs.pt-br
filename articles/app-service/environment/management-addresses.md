---
title: Endereços de gerenciamento do Ambiente do Serviço de Aplicativo - Azure
description: Lista os endereços de gerenciamento usados para comandar um Ambiente de Serviço de Aplicativo
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766037"
---
# <a name="app-service-environment-management-addresses"></a>Endereços de gerenciamento de Ambiente de Serviço de Aplicativo

O ambiente de serviço de aplicativo (ASE) é uma implantação de único locatário do serviço de aplicativo do Azure que é executado em sua rede Virtual do Azure (VNet).  Embora o ASE executados em sua rede virtual, ele ainda deverá ser acessível a partir de um número de endereços IP dedicados que são usados pelo serviço de aplicativo do Azure para gerenciar o serviço.  No caso de um ASE, o tráfego de gerenciamento atravessa a rede controlada pelo usuário. Se o tráfego estiver bloqueado ou estiver encaminhado erroneamente, o ASE será suspenso. Para obter detalhes sobre as dependências da rede do ASE, leia [Considerações sobre a rede e o ambiente do Serviço de Aplicativo][networking]. Para obter informações gerais sobre o ASE, você pode iniciar com [Introdução ao Ambiente do Serviço de Aplicativo][intro].

Todos os ASEs têm um VIP público no qual o tráfego de gerenciamento entra. O tráfego de gerenciamento de entrada desses endereços é proveniente das portas 454 e 455 no VIP público de seu ASE. Este documento lista os endereços de origem do Serviço de Aplicativo para o tráfego de gerenciamento para o ASE. Esses endereços também estão na marca de serviço de IP denominada AppServiceManagement.

Os endereços indicados a seguir podem ser configurados em uma tabela de rotas para evitar problemas de roteamento assimétricos com o tráfego de gerenciamento. Rotas agir em relação ao tráfego no nível do IP e não têm reconhecimento de direção do tráfego ou que o tráfego é uma parte de uma mensagem de resposta TCP. Se o endereço de resposta para uma solicitação TCP é diferente do endereço a que ser enviado ao, você terá um problema de roteamento assimétrico. Para evitar problemas de roteamento assimétricos com o tráfego de gerenciamento do ASE, você precisa garantir que as respostas são enviadas de volta do mesmo endereço que foram enviadas para. Para obter detalhes sobre como configurar o ASE para operar em um ambiente em que o tráfego de saída é enviado localmente, leia [Configurar ASE com túnel forçado][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista de endereços de gerenciamento ##

| Região | Endereços |
|--------|-----------|
| Todas as regiões públicas | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Governamental | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Configurando um Grupo de Segurança de Rede

Com grupos de segurança de rede, você não precisa se preocupar com os endereços individuais ou manter sua própria configuração. Há uma marca de serviço IP denominada AppServiceManagement que é mantida atualizada com todos os endereços. Para usar essa marca de IP do serviço em seu NSG, acesse o portal, abra sua interface de usuário de grupos de segurança de rede e selecione as regras de segurança de entrada. Se você tiver uma regra já existente para o tráfego de gerenciamento de entrada, editá-lo. Se esse NSG não foi criado com o ASE ou se ele é novo, selecione **Adicionar**. No menu suspenso Origem, selecione **Marca de Serviço**.  Sob a marca de serviço do código-fonte, selecione **AppServiceManagement**. Defina os intervalos da porta de origem como \*, Destino como **Qualquer**, os intervalos da porta de destino como **454-455**, Protocolo como **TCP** e Ação como **Permitir**. Se você estiver fazendo a regra, você precisará definir a prioridade. 

![criando um NSG com a marca de serviço][1]

## <a name="configuring-a-route-table"></a>Configurando uma tabela de rotas

Os endereços de gerenciamento podem ser colocados em uma tabela de rotas com um próximo salto da Internet para garantir que todo o tráfego de gerenciamento de entrada possa percorrer novamente o mesmo caminho. Essas rotas são necessárias ao configurar o túnel forçado. Para criar a tabela de rotas, use o portal, o PowerShell ou a CLI do Azure.  Os comandos para criar uma tabela de rotas usando a CLI do Azure em um prompt do PowerShell são mostrados abaixo. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

Depois de criar a tabela de rotas, você precisará configurá-la na sub-rede do ASE.  

## <a name="get-your-management-addresses-from-api"></a>Obter seus endereços de gerenciamento de API ##

Você pode listar os endereços de gerenciamento que correspondem ao seu ASE com a seguinte chamada à API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

A API retorna um documento JSON que inclui todos os endereços de entrada do ASE. A lista de endereços inclui os endereços de gerenciamento, o VIP usado pelo seu ASE e o intervalo de endereços de sub-rede do ASE em si.  

Para chamar a API com o [armclient](https://github.com/projectkudu/ARMClient) use os seguintes comandos, mas substitua a ID da assinatura, o grupo de recursos e o nome do ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
