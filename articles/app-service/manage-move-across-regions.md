---
title: Mover um aplicativo para outra região
description: Saiba como mover os recursos do serviço de aplicativo de uma região para outra.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86524847"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Mover um recurso do serviço de aplicativo para outra região

Este artigo descreve como mover os recursos do serviço de aplicativo para uma região diferente do Azure. Você pode mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para implantar recursos ou serviços disponíveis somente em regiões específicas, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade.

Os recursos do serviço de aplicativo são específicos da região e não podem ser movidos entre regiões. Você deve criar uma cópia dos recursos do serviço de aplicativo existentes na região de destino e, em seguida, mover o conteúdo para o novo aplicativo. Se o aplicativo de origem usar um domínio personalizado, você poderá [migrá-lo para o novo aplicativo na região de destino](manage-custom-dns-migrate-domain.md) quando tiver terminado.

Para facilitar a cópia do seu aplicativo, você pode [clonar um aplicativo de serviço de aplicativo individual](app-service-web-app-cloning.md) em um plano do serviço de aplicativo em outra região, mas ele tem [limitações](app-service-web-app-cloning.md#current-restrictions), especialmente que ele não dá suporte a aplicativos do Linux.

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o aplicativo do serviço de aplicativo está na região do Azure da qual você deseja mover.
- Verifique se a região de destino dá suporte ao serviço de aplicativo e a qualquer serviço relacionado, cujos recursos você deseja mover.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Preparar

Identifique todos os recursos do serviço de aplicativo que você está usando no momento. Por exemplo:

- Aplicativos do Serviço de Aplicativo
- [Planos do Serviço de Aplicativo](overview-hosting-plans.md)
- [Slots de implantação](deploy-staging-slots.md)
- [Domínios personalizados comprados no Azure](manage-custom-dns-buy-domain.md)
- [Certificados SSL](configure-ssl-certificate.md)
- [Integração de rede virtual do Azure](web-sites-integrate-with-vnet.md)
- [Conexões híbridas](app-service-hybrid-connections.md).
- [identidades gerenciadas](overview-managed-identity.md)
- [Configurações de backup](manage-backup.md)

Determinados recursos, como certificados importados ou conexões híbridas, contêm integração com outros serviços do Azure. Para obter informações sobre como mover esses recursos entre regiões, consulte a documentação para os respectivos serviços.

## <a name="move"></a>Mover

1. [Crie um backup do aplicativo de origem](manage-backup.md).
1. [Crie um aplicativo em um novo plano do serviço de aplicativo, na região de destino](app-service-plan-manage.md#create-an-app-service-plan).
2. [Restaurar o backup no aplicativo de destino](web-sites-restore.md)
2. Se você usar um domínio personalizado, [associe-o preventivamente ao aplicativo de destino](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) com `awverify.` e [habilite o domínio no aplicativo de destino](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Configure tudo o mais em seu aplicativo de destino para ser o mesmo que o aplicativo de origem e verifique sua configuração.
4. Quando você estiver pronto para o domínio personalizado apontar para o aplicativo de destino, [remapeie o nome de domínio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Exclua o aplicativo de origem e o plano do serviço de aplicativo. [Um plano do serviço de aplicativo na camada não livre carrega um encargo, mesmo que nenhum aplicativo esteja sendo executado nele.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Próximas etapas

[Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o Azure PowerShell](app-service-web-app-cloning.md)