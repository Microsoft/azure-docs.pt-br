---
title: Ambiente do Serviço de Aplicativo rede
description: Detalhes da rede Ambiente do Serviço de Aplicativo
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735026"
---
# <a name="app-service-environment-networking"></a>Ambiente do Serviço de Aplicativo rede

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v3 (versão prévia)
> 

O Ambiente do Serviço de Aplicativo (ASE) é uma implantação de locatário único do serviço de Azure App que hospeda aplicativos Web, aplicativos de API e aplicativos de funções. Ao instalar um ASE, você escolhe a rede virtual do Azure (VNet) na qual deseja que ele seja implantado. Todo o aplicativo de tráfego de entrada e de saída estará dentro da VNet que você especificar.  

O ASEv3 usa duas sub-redes.  Uma sub-rede é usada para o ponto de extremidade privado que lida com o tráfego de entrada. Isso pode ser uma sub-rede já existente ou uma nova.  A sub-rede de entrada pode ser usada para outras finalidades do que o ponto de extremidade privado. A sub-rede de saída só pode ser usada para o tráfego de saída do ASE. Nada mais pode ir para a sub-rede de saída do ASE.

## <a name="addresses"></a>Endereços 
O ASE tem os seguintes endereços na criação:

| Tipo de endereço | descrição |
|--------------|-------------|
| Endereço de entrada | O endereço de entrada é o endereço do ponto de extremidade privado usado pelo seu ASE. |
| Sub-rede de saída | A sub-rede de saída também é a sub-rede do ASE. Durante a visualização, essa sub-rede só é usada para tráfego de saída. |
| Endereço de saída do Windows | Os aplicativos do Windows nesse ASE usarão esse endereço, por padrão, ao fazer chamadas de saída para a Internet. |
| Endereço de saída do Linux | Os aplicativos do Linux neste ASE usarão esse endereço, por padrão, ao fazer chamadas de saída para a Internet. |

O ASEv3 tem detalhes sobre os endereços usados pelo ASE na parte **endereços IP** do portal do ase.

![Interface do usuário de endereços do ASE](./media/networking/networking-ip-addresses.png)

Se você excluir o ponto de extremidade privado usado pelo ASE, não poderá acessar os aplicativos em seu ASE.  

O ASE usa endereços na sub-rede de saída para dar suporte à infraestrutura usada pelo ASE. Ao dimensionar seus planos do serviço de aplicativo em seu ASE, você usará mais endereços. Os aplicativos no ASE não têm endereços dedicados na sub-rede de saída. Os endereços usados por um aplicativo na sub-rede de saída por um aplicativo serão alterados com o passar do tempo.

## <a name="ports"></a>Portas

O ASE recebe o tráfego do aplicativo nas portas 80 e 443.  Não há nenhum requisito de porta de entrada ou saída para o ASE. 

## <a name="extra-configurations"></a>Configurações extras

Ao contrário do ASEv2, com ASEv3, você pode definir grupos de segurança de rede (NSGs) e tabelas de rotas (UDRs), como você verá ajustar sem restrição. Se você quiser forçar o túnel de todo o tráfego de saída de seu ASE para um dispositivo NVA. Você pode colocar dispositivos WAF na frente de todo o tráfego de entrada para seu ASE. 

## <a name="dns"></a>DNS

Os aplicativos em seu ASE usarão o DNS com o qual sua VNet está configurada. Siga as instruções em [usando um ambiente do serviço de aplicativo](./using.md#dns-configuration) para configurar o servidor DNS para apontar para o ase. Se desejar que alguns aplicativos usem um servidor DNS diferente do qual a VNet está configurada, você poderá defini-lo manualmente por aplicativo com as configurações do aplicativo WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER. A configuração do aplicativo WEBSITE_DNS_ALT_SERVER configura o servidor DNS secundário. O servidor DNS secundário é usado somente quando não há resposta do servidor DNS primário. 

## <a name="preview-limitation"></a>Limitação de visualização

Há alguns recursos de rede que não estão disponíveis com o ASEv3.  As coisas que não estão disponíveis no ASEv3 incluem:

• FTP • depuração remota • implantação do balanceador externo de carga • capacidade de acessar um registro de contêiner privado para implantações de contêineres • capacidade de fazer chamadas para Vnets globalmente emparelhadas • capacidade de fazer backup/restauração com um ponto de extremidade de serviço ou armazenamento protegido de ponto de extremidade privado Account • capacidade de ter configurações do aplicativo referências do keyvault no ponto de extremidade do serviço ou nas contas protegidas do ponto de extremidade protegido • capacidade de usar o BYOS para um ponto de extremidade de serviço ou uma conta de armazenamento protegida por ponto de extremidade particular • uso do observador de rede ou do fluxo de NSG no tráfego
    
    