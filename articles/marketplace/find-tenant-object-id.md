---
title: Localizar detalhes de ID de locatário, ID de objeto e Associação de parceiro no Azure Marketplace
description: Como encontrar os detalhes de ID de locatário, ID de objeto e Associação de parceiro de uma ID de assinatura no Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109327"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Localizar detalhes de ID de locatário, ID de objeto e Associação de parceiro

Este artigo descreve como encontrar os detalhes de ID de locatário, ID de objeto e Associação de parceiro, juntamente com suas respectivas IDs de assinatura.

Se você precisar obter capturas de tela desses itens em Azure Cloud Shell para usar para obter assistência de depuração, vá para [localizar a associação de locatário, objeto e ID de parceiro para depuração](#find-ids-for-debugging).

>[!Note]
> Somente o proprietário de uma assinatura tem os privilégios para executar essas etapas.

## <a name="find-tenant-id"></a>Localizar ID de locatário

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="O ícone de Azure Active Directory na portal do Azure.":::

3. Selecione **Visão geral**. Sua ID de locatário deve aparecer sob **informações básicas**.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Selecione grupos em portal do Azure.":::

## <a name="find-subscriptions-and-roles"></a>Localizar assinaturas e funções

1. Vá para a portal do Azure e selecione **Azure Active Directory** conforme observado nas etapas 1 e 2 acima.
2. Selecione **Assinaturas**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="O ícone de assinaturas no portal do Azure.":::

3. Exibir assinaturas e funções.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="A tela de assinaturas em portal do Azure.":::

## <a name="find-partner-id"></a>Localizar ID de parceiro

1. Navegue até a página assinaturas, conforme descrito na seção anterior.
2. Selecione uma assinatura.
3. Em **cobrança**, selecione **informações do parceiro**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Informações do parceiro no menu do nav esquerdo.":::

## <a name="find-user-object-id"></a>Localizar usuário (ID de objeto)

1. Entre no portal de [Administração do Office 365](https://portal.office.com/adminportal/home) com uma conta no locatário desejado com os direitos administrativos apropriados.
2. No menu do lado esquerdo, expanda a seção **centros de administração** na parte inferior e selecione a opção Azure Active Directory para iniciar o console de administração em uma nova janela do navegador.
3. Selecione **Usuários**.
4. Navegue até ou procure o usuário desejado e, em seguida, selecione o nome da conta para exibir as informações de perfil da conta do usuário.
5. A ID de objeto está localizada na seção identidade à direita.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Centro de administração do Azure Active Directory.":::

6. Localize **atribuições de função** selecionando **iam (controle de acesso)** no menu à esquerda e **atribuições de função**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Atribuições de função para recursos do Azure.":::

## <a name="find-ids-for-debugging"></a>Localizar IDs para depuração

Esta seção descreve como localizar o locatário, o objeto e a associação de ID de parceiro para fins de depuração.

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Abra Azure Cloud Shell selecionando o ícone do PowerShell na parte superior direita.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Ícone do PowerShell na parte superior direita da tela.":::

3. Selecione **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Selecione o link do PowerShell.":::

4. Selecione a caixa **assinatura** para escolher aquela à qual o parceiro está vinculado e, em seguida, **crie o armazenamento**. Esta é uma ação única; Se você já tiver configurado o armazenamento, vá para a próxima etapa.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Selecione o botão criar armazenamento.":::

5. A criação (ou já existente) do armazenamento abre a janela Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="A janela Azure Cloud Shell.":::

6. Para obter detalhes de associação de parceiro, instale a extensão com este comando:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell notará se a extensão já está instalada:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="A janela de Azure Cloud Shell mostrando a extensão já está instalada.":::

7. Verifique os detalhes do parceiro usando este comando:<br>`az managementpartner show --partner-id xxxxxx`<br>Exemplo: `az managementpartner show --partner-id 4760962`.<br>Ajuste uma captura de tela dos resultados do comando, que será semelhante a este:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Tela de exemplo mostrando os resultados do comando anterior para exibir a ID do parter.":::

>[!NOTE]
>Se várias assinaturas exigirem uma captura de tela, use este comando para alternar entre assinaturas:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Próximas etapas

- [Países e regiões com suporte](sell-from-countries.md)