---
title: Redefinir as credenciais do vSphere para a solução do Azure VMware
description: Saiba como redefinir as credenciais do vSphere para sua nuvem privada da solução Azure VMware e garantir que o conector do HCX tenha as credenciais mais recentes do vSphere.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 1376b6322250da506d32b8ced0a62ddbf60ba9f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587620"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Redefinir as credenciais do vSphere para a solução do Azure VMware

Neste artigo, percorreremos as etapas para redefinir as credenciais do vCenter Server e do NSX-T Manager para sua nuvem privada da solução Azure VMware. Isso permitirá que você verifique se o conector do HCX tem as credenciais de vCenter Server mais recentes.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Redefinir suas credenciais de solução do Azure VMware

 Primeiro, vamos redefinir suas credenciais de componentes da solução VMare do Azure. Suas credenciais de administrador do vCenter Server CloudAdmin e NSX-T não expiram; no entanto, você pode seguir estas etapas para gerar novas senhas para essas contas.

> [!NOTE]
> Se você usar suas credenciais do CloudAdmin para serviços conectados como HCX, vRealize Orchestrator, vRealizae Operations Manager ou VMware Horizonte, suas conexões deixarão de funcionar quando você atualizar sua senha.  Esses serviços devem ser interrompidos antes de iniciar a rotação de senha.  Não fazer isso pode resultar em bloqueios temporários em suas contas de administrador do vCenter CloudAdmin e do NSX-T, pois esses serviços irão chamar continuamente usando suas credenciais antigas.  Para obter mais informações sobre como configurar contas separadas para serviços conectados, consulte [conceitos de acesso e identidade](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. No portal do Azure, abra uma sessão de Azure Cloud Shell.

2. Execute o comando a seguir para atualizar sua senha do vCenter CloudAdmin.  Será necessário substituir {SubscriptionId}, {resourcegroup} e {PrivateCloudName} pelos valores reais da nuvem privada à qual a conta do CloudAdmin pertence.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Execute o comando a seguir para atualizar sua senha de administrador do NSX-T. Será necessário substituir {SubscriptionId}, {resourcegroup} e {PrivateCloudName} pelos valores reais da nuvem privada à qual a conta de administrador do NSX-T pertence.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Verifique se o conector do HCX tem suas credenciais de vCenter Server mais recentes

Agora que você redefiniu suas credenciais, siga estas etapas para garantir que o conector do HCX tenha suas credenciais atualizadas.

1. Depois que sua senha for alterada, vá para a interface da Web do conector HCX local usando https://{IP do dispositivo do conector do HCX}: 443. Certifique-se de usar a porta 443. Faça logon usando suas novas credenciais.

2. No painel do VMware HCX, selecione **emparelhamento de site**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Captura de tela do painel do VMware HCX com emparelhamento de site realçado.":::
 
3. Selecione a conexão correta com a solução do Azure VMware (se houver mais de uma) e selecione **Editar conexão**.
 
4. Forneça o novo vCenter Server credenciais de usuário do CloudAdmin e selecione **Editar**, que salva as credenciais. Salvar deve mostrar com êxito.

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou a redefinição das credenciais do vCenter Server e do NSX-T Manager para a solução do Azure VMware, talvez queira saber mais sobre:

- [Configurando os componentes de rede do NSX na solução VMware do Azure](configure-nsx-network-components-azure-portal.md).
- [Gerenciamento do ciclo de vida de VMs de solução do Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Implantando a recuperação de desastre de máquinas virtuais usando a solução VMware do Azure](disaster-recovery-for-virtual-machines.md).
