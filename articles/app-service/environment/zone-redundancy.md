---
title: Suporte à zona de disponibilidade para ambientes do serviço de aplicativo
description: Saiba como implantar seus ambientes de serviço de aplicativo para que seus aplicativos tenham redundância de zona.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624718"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Suporte à zona de disponibilidade para ambientes do serviço de aplicativo

Os ambientes do serviço de aplicativo (ASE) podem ser implantados em Zonas de Disponibilidade (AZ).  Os clientes podem implantar um ILB (balanceador de carga interno) ASEs em um AZ específico dentro de uma região do Azure. Se você fixar o ASE ILB em um AZ específico, os recursos usados por um ASE ILB serão fixados no AZ especificado ou implantados de maneira redundante de zona.  

Um ASE ILB que é implantado explicitamente em um AZ é considerado um recurso de zona porque o ASE ILB está fixado em uma zona específica. As seguintes dependências do ASE ILB serão fixadas na zona especificada:

- o endereço IP do balanceador de carga interno do ASE
- os recursos de computação usados pelo ASE para gerenciar e executar aplicativos Web

O armazenamento de arquivos remoto para aplicativos Web implantados em um ASE ILB de zona usa o ZRS (armazenamento com redundância de zonas).

A menos que as etapas descritas neste artigo sejam seguidas, ILB ASEs não serão implantadas automaticamente de maneira zonal. Não é possível fixar um ASE externo com um endereço IP público em uma zona de disponibilidade específica. 

ILB zona ASEs pode ser criada em qualquer uma das seguintes regiões:

- Leste da Austrália
- Canadá Central
- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Leste dos EUA 2 (EUAP)
- França Central 
- Leste do Japão
- Norte da Europa
- Europa Ocidental
- Sudeste Asiático
- Sul do Reino Unido
- Oeste dos EUA 2

Os aplicativos implantados em um ASE ILB de zona continuarão a ser executados e atenderem ao tráfego nesse ASE, mesmo se outras zonas na mesma região sofrerem uma interrupção.  É possível que comportamentos que não sejam de tempo de execução, incluindo; o dimensionamento do plano do serviço de aplicativo, a criação de aplicativos, a configuração de aplicativos e a publicação de aplicativos ainda podem ser afetados de uma interrupção em outras zonas de disponibilidade. A implantação fixada por zona de um ASE de ILB zonal garante apenas o tempo de atividade contínuo para aplicativos já implantados.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Como implantar um Ambiente do Serviço de Aplicativo em uma zona de disponibilidade ##

Zonal ILB ASEs deve ser criada usando modelos ARM. Depois que um ASE ILB de zona é criado por meio de um modelo de ARM, ele pode ser exibido e interagir por meio do portal do Azure e da CLI.  Um modelo de ARM só é necessário para a criação inicial de um ASE ILB de zona.

A única alteração necessária em um modelo ARM para especificar um ASE ILB de zona é a nova propriedade ***Zones** _. A propriedade _*_Zones_*_ deve ser definida com um valor de "1", "2" ou "3" dependendo da zona de disponibilidade lógica em que o ase ILB deve ser fixado.

O trecho de modelo do ARM de exemplo abaixo mostra a nova propriedade de _*_zonas_*_ especificando que o ase ILB deve ser fixado à zona 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Para tornar a zona de aplicativos redundante, você precisa implantar duas ILB zonais de ASEs. Os dois ASEs de ILB zonais devem estar em zonas de disponibilidade separadas. Em seguida, você precisa implantar seus aplicativos em cada ASEs ILB. Depois que seus aplicativos forem criados, você precisará configurar uma solução de balanceamento de carga. A solução recomendada é implantar um [Gateway de aplicativo com redundância de zona](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) upstream do ases de ILB zonal. 

## <a name="in-region-data-residency"></a>Residência de dados na região ##

ILB ASEs implantado em uma zona de disponibilidade armazenará somente os dados do cliente na região em que o ASE de ILB zonal foi implantado. O conteúdo do arquivo do site, bem como as configurações fornecidas pelo cliente e os segredos armazenados no serviço de aplicativo permanecem dentro da região em que o ASE de ILB de zona é implantado.

Os clientes garantem a residência de dados de região única seguindo as etapas descritas anteriormente na seção "como implantar um Ambiente do Serviço de Aplicativo em uma zona de disponibilidade". Ao configurar um Ambiente do Serviço de Aplicativo de acordo com essas etapas, um Ambiente do Serviço de Aplicativo implantado em uma zona de disponibilidade atende aos requisitos de residência de dados de região, incluindo aqueles especificados no [central de confiabilidade do Azure](https://azuredatacentermap.azurewebsites.net/).

Os clientes podem validar se um Ambiente do Serviço de Aplicativo está configurado corretamente para armazenar dados em uma única região seguindo estas etapas: 

1. Usando o [Gerenciador de recursos](https://resources.azure.com), navegue até o recurso ARM para o ambiente do serviço de aplicativo.  ASEs são listados em _providers/Microsoft.Web/hostingEnvironments *.
2. Se uma propriedade de *zonas* existir na exibição da sintaxe de JSON de ARM e contiver uma matriz JSON de valor único com um valor de "1", "2" ou "3", o ase será zonally implantado e os dados do cliente permanecerão na mesma região.
2. Se uma propriedade de *zonas* não existir ou se a propriedade não tiver um valor de zona válido, conforme especificado anteriormente, o ase não será implantado zonally e os dados do cliente não serão armazenados exclusivamente na mesma região.
