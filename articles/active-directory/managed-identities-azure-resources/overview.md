---
title: Identidades gerenciadas dos recursos do Azure
description: Uma visão geral das identidades gerenciadas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 06/19/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c4f670f3bb14610e7f29a9201b357e73dacf09b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293228"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>O que são identidades gerenciadas para recursos do Azure?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais no código para autenticar serviços de nuvem. Proteger as credenciais é uma tarefa importante. O ideal é que as credenciais nunca apareçam em estações de trabalho do desenvolvedor e não sejam verificadas no controle de origem. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais, os segredos e outras chaves, mas seu código precisa se autenticar no Key Vault para recuperá-los. 

As identidades de gerenciado para a funcionalidade de recursos do Azure no Azure AD (Azure Active Directory) resolve esse problema. O recurso oferece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para se autenticar em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

As identidades de gerenciado para a funcionalidade de recursos do Azure é gratuita com o Azure AD para assinaturas do Azure. Não há nenhum custo adicional.

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="terminology"></a>Terminologia

Os seguintes termos são usados em todas as identidades gerenciadas do conjunto de documentação de recursos do Azure:

- **ID do Cliente** – um identificador exclusivo gerado pelo Azure AD vinculado a um aplicativo e uma entidade de serviço durante o provisionamento inicial.
- **ID da Entidade de Segurança** – a ID de objeto do objeto da entidade de serviço para a identidade gerenciada usada para permitir acesso baseado em função a um recurso do Azure.
- **IMDS (Serviço de Metadados de Instância) do Azure** – um ponto de extremidade REST acessível a todas as VMs IaaS criadas por meio do Azure Resource Manager. O ponto de extremidade está disponível em um endereço IP não roteável conhecido (169.254.169.254) que pode ser acessado somente de dentro da VM.

## Como as identidades gerenciadas dos recursos do Azure funcionam?<a name="how-does-it-work"></a>

Há dois tipos de identidades gerenciadas:

- Uma **identidade gerenciada atribuída pelo sistema** é habilitada diretamente em uma instância de serviço do Microsoft Azure. Quando a identidade é habilitada, o Azure cria uma identidade para a instância do locatário do Azure AD confiado pela assinatura da instância. Depois que a identidade é criada, as credenciais são provisionadas para a instância. O ciclo de vida de uma identidade atribuída ao sistema está diretamente relacionado à instância de serviço do Azure na qual ela está habilitada. Se a instância é excluída, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.
- Uma **identidade gerenciada atribuída pelo usuário** é criada como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure. O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída.

Internamente, as identidades gerenciadas são entidades de serviço de um tipo especial, que estão bloqueadas para serem usadas apenas com recursos do Azure. Quando a identidade gerenciada é excluída, a entidade de serviço correspondente é removida automaticamente. 

Seu código pode usar uma identidade gerenciada para solicitar tokens de acesso para os serviços que dão suporte à autenticação do Azure AD. O Azure é responsável por reverter as credenciais que são usadas pela instância de serviço. 

O diagrama abaixo mostra como as identidades de serviço gerenciadas funcionam com VMs (máquinas virtuais):

![Identidades de serviço gerenciadas e VMs do Azure](media/overview/msi-vm-vmextension-imds-example.png)

|  Propriedade    | Identidade gerenciada atribuída pelo sistema | Identidade gerenciada atribuída pelo usuário |
|------|----------------------------------|--------------------------------|
| Criação |  Criada como parte de um recurso do Azure (por exemplo, uma máquina virtual do Azure ou o Serviço de Aplicativo do Azure) | Criada como um recurso independente do Azure |
| Ciclo de vida | Ciclo de vida compartilhado com o recurso do Azure com o qual a identidade gerenciada é criada. <br/> Quando o recurso pai é excluído, a identidade gerenciada também é excluída. | Ciclo de vida independente. <br/> Precisa ser explicitamente excluída. |
| Compartilhamento entre recursos do Azure | Não pode ser compartilhada. <br/> Só pode ser associada a um único recurso do Azure. | Pode ser compartilhada <br/> A mesma identidade gerenciada atribuída pelo usuário pode ser associada a mais de um recurso do Azure. |
| Casos de uso comuns | Cargas de trabalho que estão contidas em um único recurso do Azure <br/> Cargas de trabalho para as quais você precisa de identidades independentes. <br/> Por exemplo, um aplicativo que é executado em uma única máquina virtual | Cargas de trabalho que são executadas em vários recursos e que podem compartilhar uma única identidade. <br/> Cargas de trabalho que precisam de pré-autorização para um recurso seguro como parte de um fluxo de provisionamento. <br/> Cargas de trabalho em que os recursos são reciclados com frequência, mas as permissões devem permanecer consistentes. <br/> Por exemplo, uma carga de trabalho em que várias máquinas virtuais precisam acessar o mesmo recurso | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Como uma identidade gerenciada atribuída pelo sistema funciona com uma VM do Azure

1. O Azure Resource Manager recebe uma solicitação para habilitar a identidade gerenciada atribuída pelo sistema em uma VM.
2. O Azure Resource Manager cria uma entidade de serviço no Azure AD para a identidade da VM. A entidade de serviço é criada no locatário do Azure AD confiado pela assinatura.
3. O Azure Resource Manager configura a identidade na máquina virtual:
    1. Atualiza o ponto de extremidade de identidade do Serviço de Metadados de Instância com o certificado e a ID do cliente da entidade de serviço.
    1. Provisiona a extensão da VM (programada para ser preterida em janeiro de 2019) e adiciona o certificado e a ID de cliente da entidade de serviço. (Esta etapa está programada para reprovação.)
4. Depois que a máquina virtual ganhar uma identidade, use as informações da entidade de serviço para conceder acesso aos recursos do Microsoft Azure à VM. Para chamar o Azure Resource Manager, use o RBAC (controle de acesso baseado em função) no Azure AD para atribuir a função apropriada à entidade de serviço da VM. Para chamar o Key Vault, conceda o acesso de código para o segredo ou chave específica no Key Vault.
5. O código que está em execução na VM pode solicitar um token do ponto de extremidade de serviço de Metadados de Instância do Azure, acessível somente a partir da VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - O parâmetro de recurso especifica o serviço ao qual o token é enviado. Para se autenticar no Azure Resource Manager, use `resource=https://management.azure.com/`.
    - O parâmetro de versão de API Especifica a versão IMDS, use a versão da API = 2018-02-01 ou superior.

> [!NOTE]
> O código também pode solicitar um token do ponto de extremidade da extensão da VM, mas isso está previsto para ser preterido em breve. Para obter mais informações sobre a extensão da VM, consulte [Migrar da extensão da VM para IMDS do Azure para autenticação ](howto-migrate-vm-extension.md).

6. A chamada é feita ao Azure AD para solicitar acesso ao token (conforme especificado na etapa 5) usando a ID do cliente e o certificado configurados na etapa 3. O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Como uma identidade gerenciada atribuída pelo usuário funciona com uma VM do Azure

1. O Azure Resource Manager recebe uma solicitação para criar uma identidade gerenciada atribuída pelo usuário.
2. O Azure Resource Manager cria uma entidade de serviço no Azure AD para a identidade gerenciada atribuída pelo usuário. A entidade de serviço é criada no locatário do Azure AD confiado pela assinatura.
3. O Azure Resource Manager recebe uma solicitação para criar uma identidade gerenciada atribuída pelo usuário em uma VM:
    1. Atualiza o ponto de extremidade de identidade do Serviço de Metadados de Instância do Azure com o certificado e a ID do cliente da entidade de serviço de identidade gerenciada.
    1. Provisiona a extensão da VM e adiciona o certificado e a ID de cliente da entidade de serviço de identidade gerenciada atribuída pelo usuário. (Esta etapa está programada para reprovação.)
4. Depois que a identidade gerenciada atribuída pelo usuário for criada, use as informações da entidade de serviço para conceder o acesso de identidade aos recursos do Azure. Para chamar o Azure Resource Manager, use RBAC no Azure AD para atribuir a função apropriada à entidade de serviço da identidade atribuída pelo usuário. Para chamar o Key Vault, conceda o acesso de código para o segredo ou chave específica no Key Vault.

   > [!Note]
   > Você também pode executar essa etapa antes da etapa 3.

5. O código que está em execução na VM pode solicitar um token do ponto de extremidade de identidade do Serviço de Metadados de Instância do Azure, acessível somente a partir da VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - O parâmetro de recurso especifica o serviço ao qual o token é enviado. Para se autenticar no Azure Resource Manager, use `resource=https://management.azure.com/`.
    - O parâmetro de ID de cliente especifica a identidade para a qual o token é solicitado. Esse valor é necessário para eliminar a ambiguidade quando mais de uma identidade atribuída pelo usuário está em uma mesma VM.
    - O parâmetro de versão da API especifica a versão do Serviço de Metadados de Instância do Azure. Use `api-version=2018-02-01` ou superior.

> [!NOTE]
> O código também pode solicitar um token do ponto de extremidade da extensão da VM, mas isso está previsto para ser preterido em breve. Para obter mais informações sobre a extensão da VM, consulte [Migrar da extensão da VM para IMDS do Azure para autenticação ](howto-migrate-vm-extension.md).

6. A chamada é feita ao Azure AD para solicitar acesso ao token (conforme especificado na etapa 5) usando a ID do cliente e o certificado configurados na etapa 3. O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Como usar identidades gerenciadas para recursos do Azure?

Para saber como usar as identidades gerenciadas para acessar diferentes recursos do Azure, experimente estes tutoriais.

> [!NOTE]
> Confira o curso [Implementando identidades gerenciadas para Recursos do Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) para obter mais informações sobre identidades gerenciadas, incluindo vídeos com passo a passo detalhado de vários cenários com suporte.

Saiba como usar uma identidade gerenciada com uma VM do Windows:

* [Acessar Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Acessar o Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Acessar SQL do Azure](tutorial-windows-vm-access-sql.md)
* [Acessar o Armazenamento do Azure usando uma chave de acesso](tutorial-windows-vm-access-storage.md)
* [Acessar o Armazenamento do Azure usando assinaturas de acesso compartilhado](tutorial-windows-vm-access-storage-sas.md)
* [Acessar um recurso que não seja do Azure AD com o Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Saiba como usar uma identidade gerenciada com uma VM do Linux:

* [Acessar Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Acessar o Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Acessar o Armazenamento do Azure usando uma chave de acesso](tutorial-linux-vm-access-storage.md)
* [Acessar o Armazenamento do Azure usando assinaturas de acesso compartilhado](tutorial-linux-vm-access-storage-sas.md)
* [Acessar um recurso que não seja do Azure AD com o Azure Key Vault](tutorial-linux-vm-access-nonaad.md)
* [Acessar o Registro de Contêiner do Azure](../../container-registry/container-registry-authentication-managed-identity.md)

Saiba como usar uma identidade gerenciada com outros serviços do Azure:

* [Serviço de Aplicativo do Azure](/azure/app-service/overview-managed-identity)
* [Funções do Azure](/azure/app-service/overview-managed-identity)
* [Aplicativos Lógicos do Azure](/azure/logic-apps/create-managed-service-identity)
* [Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Hubs de eventos do Azure](../../event-hubs/event-hubs-managed-service-identity.md)
* [Gerenciamento de API do Azure](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Instâncias de Contêiner do Azure](../../container-instances/container-instances-managed-identity.md)
* [Tarefas do Registro de Contêiner do Azure](../../container-registry/container-registry-tasks-authentication-managed-identity.md)

## O recurso tem suporte de quais serviços do Azure?<a name="which-azure-services-support-managed-identity"></a>

As identidades gerenciadas para recursos do Azure podem ser usadas para autenticar os serviços que são compatíveis com a autenticação do Azure AD. Para obter uma lista de serviços do Azure que dão suporte para identidades gerenciadas para a funcionalidade de recursos do Azure, veja [Serviços que dão suporte a identidades gerenciadas para recursos do Azure](services-support-msi.md).

## <a name="next-steps"></a>Próximas etapas

Comece a usar as identidades gerenciadas para funcionalidade de recursos do Azure com os seguintes inícios rápidos:

* [Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Resource Manager](tutorial-linux-vm-access-arm.md)
