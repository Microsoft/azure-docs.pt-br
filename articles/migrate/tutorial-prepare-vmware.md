---
title: Preparar VMs do VMware para avaliação e migração para o Azure usando as Migrações para Azure | Microsoft Docs
description: Descreve como preparar a avaliação e a migração de VMs locais do VMware para o Azure usando as Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840337"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs do VMware para avaliação e migração para o Azure

Este artigo descreve como preparar a avaliação e a migração de VMs locais do VMware para o Azure usando as [Migrações para Azure](migrate-services-overview.md).

As [Migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do VMware. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prepare o Azure. Configurar permissões para sua conta do Azure e recursos para trabalhar com as Migrações para Azure.
> * Preparar as VMs e os servidores locais do VMware para avaliação da VM.
> * Preparar as VMs e os servidores locais do VMware para migração da VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine as instruções para avaliação e migração do VMware.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Você precisa destas permissões do Azure:

- Sua conta do Azure precisa de permissões para criar um projeto de Migrações para Azure para avaliação e migração. 
- Para avaliação e migração sem agente de VMs do VMware, as Migrações para Azure executam um dispositivo leve que descobre VMs e envia os metadados da VM e dados de desempenho para as Migrações para Azure. No Azure, você precisa de permissões para registrar o dispositivo de Migrações para Azure.
- Para migrar VMs VMware usando a Migração de Servidor de Migrações para Azure, as Migrações para Azure criam um cofre de chaves no grupo de recursos para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Se estiver implantando o dispositivo das Migrações para Azure para avaliar ou executar uma migração sem agente das VMs, você precisará registrá-lo.

- Durante o registro do dispositivo, as Migrações para Azure criam dois aplicativos Azure AD (Azure Active Directory) que identificam o dispositivo de forma exclusiva
    - O primeiro aplicativo comunica-se com os pontos de extremidade de serviço das Migrações para Azure.
    - O segundo aplicativo acessa um Azure Key Vault criado durante o registro para armazenar informações de aplicativo do Azure AD e definições de configuração do dispositivo.
- Você pode atribuir permissões para as Migrações para Azure para criar esses aplicativos do Azure AD usando um dos seguintes métodos:
    - Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
    - Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado. 


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

O locatário/administrador global pode conceder permissões da seguinte maneira

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos 

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Atribuir permissões de atribuição de função

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.

    - Para executar a avaliação do servidor, permissões de **Colaborador** são suficientes.
    - Para executar a migração de servidor sem agente, você precisa ter permissões de **Proprietário** (ou **Colaborador** e **Administrador de Acesso do Usuário**).

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação da VM do VMware

Para se preparar para a avaliação da VM do VMware, você precisa verificar as configurações do host e da VM do Hyper-V e verifique as configurações de implantação do dispositivo.

### <a name="verify-vmware-settings"></a>Verificar as configurações de VMware

1. [Verifique](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements) os requisitos do servidor do VMware para avaliação da VM.
2. [Verifique](migrate-support-matrix-vmware.md#assessment-port-requirements) se as portas necessárias estão abertas nos servidores do vCenter.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta para avaliação

As Migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente. Somente para a avaliação, você precisa de uma conta somente leitura para o vCenter Server.

Se estiver usando um firewall.proxy baseado em URL, permita o acesso às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements) necessárias.

Certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.


### <a name="verify-appliance-settings-for-assessment"></a>Verifique as configurações do dispositivo para avaliação

Antes de configurar o dispositivo das Migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-support-matrix-vmware.md#assessment-appliance-requirements) os requisitos para configurar o dispositivo das Migrações para Azure no VMware.
2. [Examine](migrate-support-matrix-vmware.md#assessment-url-access-requirements) as URLs do Azure que o dispositivo precisará acessar.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#assessment-port-requirements) os requisitos de acesso da porta para o dispositivo.
5. Você implanta o dispositivo das Migrações para Azure como uma VM do VMware usando um arquivo OVA. No vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para migração do VMware sem agente

Examine os requisitos para migração sem agente de VMs do VMware.

1. [Examine](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) os requisitos do servidor do VMware para migração sem agente.
2. Configure uma conta para acessar o vCenter Server com as [permissões necessárias](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) para migração sem agente.
3. [Observe](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) os requisitos para as VMs do VMware que você deseja migrar para o Azure usando a migração sem agente.
4. [Examine](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) os requisitos do dispositivo para migração sem agente.]
5. Observe os requisitos de [acesso à URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) e [acesso à porta](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) do dispositivo para migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para migração do VMware baseada em agente

Examine os requisitos para migração de VMs do VMware baseada em agente.

1. [Examine](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor do VMware para migração sem agente. 
2. Configure uma conta para acessar o vCenter Server com as [permissões necessárias](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) para migração sem agente.
3. [Observe](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) os requisitos para as VMs do VMware que você deseja migrar para o Azure usando a migração baseada em agente, incluindo a instalação do serviço de mobilidade em cada VM que deseja migrar.
4. Observe o [acesso à URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Examine os requisitos de [acesso à porta](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) para o serviço de mobilidade em execução em cada VM e para o servidor de configuração das Migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você irá:
 
> [!div class="checklist"] 
> * Configurou permissões do Azure.
> * Preparou o VMware para avaliação e migração.


Prossiga para o segundo tutorial para configurar um projeto das Migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"] 
> [Avaliar VMs do VMware](./tutorial-migrate-vmware.md) 

