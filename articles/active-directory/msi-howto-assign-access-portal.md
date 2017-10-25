---
title: Como atribuir um acesso MSI a um recurso do Azure usando o portal do Azure
description: "As instruções passo a passo sobre como atribuir uma MSI em um acesse de recurso a outro recurso usando o portal do Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 335f360c833f1c803c8ccc2d74a87efff23b3a7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de Identidade de Serviço Gerenciado a um recurso usando o portal do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com uma MSI (Identidade do Serviço Gerenciado), você pode conceder acesso à MSI a outro recurso, assim como qualquer entidade de segurança. Este artigo mostra como conceder o acesso de MSI de uma máquina virtual do Azure a uma conta de Armazenamento do Azure usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usar o RBAC para atribuir o acesso de MSI a outro recurso

Depois de habilitar o MSI em um recurso do Azure, [como uma VM do Azure](msi-qs-configure-portal-windows-vm.md):

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure sob a qual você configurou a MSI.

2. Navegue até o recurso desejado no qual você deseja modificar o controle de acesso. Neste exemplo, nós estamos fornecendo um acesso da máquina virtual do Azure para uma conta de armazenamento e, portanto, navegamos até a conta de armazenamento.

3. Selecione a página **Controle de Acesso (IAM)** do recurso e selecione **+ Adicionar**. Então especifique a **Função**, **Atribuir acesso à Máquina Virtual** e especifique a **Assinatura** e o **Grupo de Recursos** correspondentes no qual o recurso reside. Na área de critérios de pesquisa, você verá o recurso. Selecione o recurso e selecione **Salvar**. 

   ![Captura de tela de controle de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Você será levado de volta à página principal de **Controle de Acesso (IAM)**, na qual você verá uma nova entrada para a MSI do recurso. Neste exemplo, a VM "SimpleWinVM" do Grupo de Recursos de Demonstração recebeu o acesso **Colaborador** à conta de armazenamento.

   ![Captura de tela de controle de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Solucionar problemas

Se o MSI para o recurso não aparecer na lista de identidades disponíveis, verifique se ele foi habilitado corretamente. Em nosso caso, podemos voltar à VM do Azure e verificar o seguinte:

- Examine a página **Configuração** e verifique se o valor para **MSI habilitada** é **Sim**.
- Examine a página **Extensões** e verifique se a extensão da MSI foi implantada com êxito.

Se um deles estiver incorreto, será preciso reimplantar a MSI em seu recurso ou solucionar a falha de implantação.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para habilitar o MSI em uma VM do Azure, consulte [Configurar um MSI (identidade do serviço gerenciada) usando o portal do Azure](msi-qs-configure-portal-windows-vm.md).


