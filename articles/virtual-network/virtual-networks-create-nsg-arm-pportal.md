---
title: "Criar grupos de segurança de rede – Portal do Azure | Microsoft Docs"
description: "Aprenda a criar e implantar grupos de segurança de rede usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 865032f350735d35668bb199ccf1ef3f0fae81de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-network-security-groups-using-the-azure-portal"></a>Criar grupos de segurança de rede usando o Portal do Azure

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [criar NSGs no modelo de implantação clássica](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo de comando PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste ao implantar [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal. As etapas abaixo usam **RG-NSG** como o nome do grupo de recursos no qual o modelo foi implantado.

## <a name="create-the-nsg-frontend-nsg"></a>Criar o NSG NSG-FrontEnd
Para criar o NSG **NSG-FrontEnd** , como mostra o cenário acima, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Procurar >** > **Grupos de segurança de rede**.
   
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Na folha **Grupos de segurança de rede**, clique em **Adicionar**.
   
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Na folha **Criar grupo de segurança de rede**, crie um NSG chamado *NSG-FrontEnd* no grupo de recursos *RG-NSG* e clique em **Criar**.
   
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Criar regras em um NSG existente
Para criar regras em um NSG existente por meio do portal do Azure, siga as etapas abaixo.

1. Clique em **Procurar >** > **Grupos de segurança de rede**.
2. Na lista de NSGs, clique em **NSG-FrontEnd** > **Regras de segurança de entrada**
   
    ![Portal do Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Na lista de **Regras de segurança de entrada**, clique em **Adicionar**.
   
    ![Portal do Azure - Adicionar regra](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. Na folha **Adicionar regra de segurança de entrada**, crie uma regra chamada *web-rule* com prioridade de *200* permitindo o acesso via *TCP* à porta *80* a qualquer VM por meio de qualquer origem e, em seguida, clique em **OK**. Observe como a maioria destas configurações já é o valor padrão.
   
    ![Portal do Azure - Configurações de regra](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Depois de alguns segundos, você encontrará a nova regra no NSG.
   
    ![Portal do Azure - Nova regra](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Repita as etapas até 6 para criar uma regra de entrada chamada *rdp-rule* com uma prioridade de *250* permitindo o acesso via *TCP* à porta *3389* a qualquer VM de qualquer origem.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associar o NSG à sub-rede FrontEnd
1. Clique em **Procurar >** > **Grupos de recursos** > **RG-NSG**.
2. Na folha**RG-NSG**, clique em **...** > **TestVNet**.
   
    ![Portal do Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Na folha **Configurações**, clique em **Sub-redes** > **FrontEnd** > **Grupo de segurança de rede** > **NSG-FrontEnd**.
   
    ![Portal do Azure - Configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Na folha **FrontEnd**, clique em **Salvar**.
   
    ![Portal do Azure - Configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Criar o NSG NSG-BackEnd
Para criar o NSG **NSG-BackEnd** e associá-lo à sub-rede **BackEnd**, siga as etapas abaixo.

1. Repita as etapas descritas em [Criar o NSG NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG) para criar um NSG chamado *NSG-BackEnd*
2. Repita as etapas descritas em [Criar regras em um NSG existente](#Create-rules-in-an-existing-NSG) para criar as regras de **entrada** na tabela abaixo.
   
   | Regra de entrada | Regra de saída |
   | --- | --- |
   | ![Portal do Azure - regra de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portal do Azure - regra de saída](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Repita as etapas descritas em [Associar o NSG à sub-rede FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) para associar o NSG **NSG-Backend** à sub-rede **BackEnd**.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [gerenciar NSGs existentes](virtual-network-manage-nsg-arm-portal.md)
* [Habilite o registro em log](virtual-network-nsg-manage-log.md) para NSGs.

