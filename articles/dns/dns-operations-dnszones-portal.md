---
title: Gerenciar as zonas DNS no DNS do Azure – Portal do Azure | Microsoft Docs
description: Você pode gerenciar zonas DNS usando o Portal do Azure. Este artigo descreve como atualizar, excluir e criar zonas DNS no DNS do Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: d0a20de8738e8c7b2719a9de85d5fd16aa5778cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926272"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Como gerenciar zonas DNS usando o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássica do Azure](dns-operations-dnszones-cli-nodejs.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra como gerenciar suas zonas DNS usando o Portal do Azure. Você também pode gerenciar as zonas DNS usando a plataforma cruzada [CLI do Azure](dns-operations-dnszones-cli.md) ou o Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Entre no Portal do Azure
2. No menu Hub, navegue até **Criar um recurso > Rede > zona DNS** para abrir a folha **Criar zona DNS**.

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. Na folha **Criar zona DNS**, insira os seguintes valores e clique em **Criar**:


   | **Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Assinatura**|[Sua assinatura]|Selecione uma assinatura para criar a zona DNS.|
   |**Grupo de recursos**|**Criar um novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localidade**|Oeste dos EUA||

> [!NOTE]
> O grupo de recursos se refere ao local do grupo de recursos e não tem impacto sobre o local da zona DNS. O local da zona DNS sempre é "global" e não é exibido.

## <a name="list-dns-zones"></a>Listar as zonas DNS

No Portal do Azure, navegue até **Mais serviços** > **Rede** > **Zonas DNS**. Cada zona DNS é seu próprio recurso e as informações como número de conjuntos de registros e servidores de nomes são visíveis a partir dessa exibição. A coluna **SERVIDORES DE NOMES** não está na exibição padrão. Para adicioná-la, clique em **Colunas**, selecione **Servidores de nomes** e, em seguida, clique em **Concluído**.

![listagem de zonas DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

Navegue até uma zona DNS no portal. Na folha **Zona DNS**, clique em **Excluir zona**. Em seguida, você deverá confirmar que deseja excluir a zona DNS. Excluir uma zona DNS também exclui todos os registros que estão contidos na zona.

## <a name="next-steps"></a>Próximas etapas

Aprenda a trabalhar com sua zona DNS e registros visitando [Introdução ao DNS do Azure usando o Portal do Azure](dns-getstarted-portal.md).