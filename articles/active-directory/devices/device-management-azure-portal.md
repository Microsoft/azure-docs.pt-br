---
title: Como gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a0cc93b4d159a4ba87c1cadc2d0dedc0a28b2d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683803"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerenciar identidades de dispositivo usando o portal do Azure

O Azure AD fornece um local central para gerenciar identidades de dispositivo.

A página **todos os dispositivos** permite que você:

- Identificar dispositivos, incluindo:
   - Dispositivos que foram ingressados ou registrados no Azure AD.
   - Dispositivos implantados usando o [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Impressoras usando a [impressão universal](/universal-print/fundamentals/universal-print-getting-started)
- Execute tarefas de gerenciamento de identidade do dispositivo como habilitar, desabilitar, excluir ou gerenciar.
   - [Impressoras](/universal-print/fundamentals/) e dispositivos [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-autopilot) têm opções de gerenciamento limitadas no Azure AD. Eles devem ser gerenciados de suas respectivas interfaces de administrador.
- Defina as configurações de identidade do dispositivo.
- Habilitar ou desabilitar Enterprise State Roaming.
- Examinar os logs de auditoria relacionados ao dispositivo

[![Exibição de todos os dispositivos na portal do Azure](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Você pode acessar o portal de dispositivos usando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >  **dispositivos**.

## <a name="manage-devices"></a>Gerenciar dispositivos

Há dois locais para gerenciar dispositivos no Azure AD:

- **Portal do Azure**  >  **Azure Active Directory**  >  **Dispositivos** do
- **Portal do Azure**  >  **Azure Active Directory**  >  **Os usuários** > selecionar um usuário > **dispositivos**

Ambas as opções permitem aos administradores a capacidade de:

- Pesquisar dispositivos.
- Consulte os detalhes do dispositivo, incluindo:
    - Nome do dispositivo
    - ID do Dispositivo
    - Sistema operacional e versão
    - Tipo de junção
    - Proprietário
    - Gerenciamento e conformidade de dispositivos móveis
    - Chave de recuperação do BitLocker
- Execute tarefas de gerenciamento de identidade do dispositivo como, habilitar, desabilitar, excluir ou gerenciar.
   - [Impressoras](/universal-print/fundamentals/) e dispositivos [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-autopilot) têm opções de gerenciamento limitadas no Azure AD. Eles devem ser gerenciados de suas respectivas interfaces de administrador.

> [!TIP]
> - Os dispositivos do Windows 10 ingressados no Azure AD híbrido não têm um proprietário. Se você estiver procurando um dispositivo por proprietário e não o encontrar, pesquise pela ID do dispositivo.
>
> - Se você vir um dispositivo que é "ingressado no Azure AD híbrido" com um estado "pendente" na coluna registrado, ele indica que o dispositivo foi sincronizado do Azure AD Connect e está aguardando a conclusão do registro do cliente. Leia mais sobre como [planejar sua implementação de ingresso no Azure ad híbrido](hybrid-azuread-join-plan.md). Informações adicionais podem ser encontradas no artigo, perguntas frequentes sobre [dispositivos](faq.md).
>
> - Para alguns dispositivos iOS, os nomes de dispositivo que contém apóstrofos podem usar caracteres diferentes que se parecem com apóstrofos. Portanto, Pesquisar por tais dispositivos é um pouco complicado – se você não estiver vendo os resultados da pesquisa corretamente, verifique se a cadeia de caracteres de pesquisa contém o caractere de apóstrofo correspondente.

### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, poderá gerenciar dispositivos em que o MDM está marcado **Microsoft Intune**. Se o dispositivo não estiver registrado com Microsoft Intune, a opção "gerenciar" ficará esmaecida.

### <a name="enable-or-disable-an-azure-ad-device"></a>Habilitar ou desabilitar um dispositivo do Azure AD

Para habilitar ou desabilitar dispositivos, você tem duas opções:

- A barra de ferramentas na página **todos os dispositivos** depois de selecionar um ou mais dispositivos.
- A barra de ferramentas depois de fazer drill down em um dispositivo específico.

> [!IMPORTANT]
> - Você deve ser um administrador global ou administrador de dispositivo de nuvem no Azure AD para habilitar ou desabilitar um dispositivo. 
> - A desabilitação de um dispositivo impede que um dispositivo seja autenticado com êxito com o Azure AD, impedindo que o dispositivo acesse seus recursos do Azure AD protegidos pelo acesso condicional baseado em dispositivo ou usando as credenciais do Windows Hello para empresas.
> - A desabilitação de um dispositivo revogará o token de atualização primário (PRT) e os tokens de atualização (RT) no dispositivo.
> - As impressoras não podem ser habilitadas ou desabilitadas no Azure AD.

### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure Active Directory

Para excluir um dispositivo, você tem duas opções:

- A barra de ferramentas na página **todos os dispositivos** depois de selecionar um ou mais dispositivos.
- A barra de ferramentas depois de fazer drill down em um dispositivo específico.

> [!IMPORTANT]
> - Você deve ser atribuído à função Administrador do dispositivo de nuvem, administrador do Intune ou administrador global no Azure AD para excluir um dispositivo.
> - Impressoras e dispositivos do Windows AutoPilot não podem ser excluídos no Azure AD
> - Excluindo um dispositivo:
>    - Impede que um dispositivo acesse seus recursos do Azure Active Directory.
>    - Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  
>    - Representa uma atividade não recuperável e não é recomendável a menos que necessário.

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, Microsoft Intune), verifique se o dispositivo foi apagado/desativado antes de excluir o dispositivo no Azure AD. Examine como [gerenciar dispositivos obsoletos](manage-stale-devices.md) antes de excluir qualquer dispositivo.

### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar as chaves do BitLocker

Você pode exibir e copiar as chaves do BitLocker para permitir que os usuários recuperem unidades criptografadas. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode encontrar essas chaves ao acessar detalhes de um dispositivo selecionando **Mostrar chave de recuperação**. Selecionar **Mostrar chave de recuperação** gerará um log de auditoria, que pode ser encontrado na `KeyManagement` categoria.

![Exibir chaves do BitLocker](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Para exibir ou copiar as chaves do BitLocker, você precisa ser o proprietário do dispositivo ou um usuário que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de Dispositivo de Nuvem
- Administrador global
- Administrador de assistência técnica
- Administrador de serviços do Intune
- Administrador de Segurança
- Leitor de segurança

### <a name="device-list-filtering-preview"></a>Filtragem da lista de dispositivos (visualização)

Anteriormente, você podia filtrar apenas a lista de dispositivos por atividade e o estado habilitado. Essa visualização agora permite que você filtre a lista de dispositivos pelos seguintes atributos em um dispositivo:

- Estado habilitado
- Estado de conformidade
- Tipo de junção (ingressado no Azure AD, ingressado no Azure AD híbrido, Azure AD registrado)
- Carimbo de data/hora da atividade
- Sistema operacional
- Tipo de dispositivo (impressoras, VMs seguras, dispositivos compartilhados, dispositivos registrados)

Para habilitar a funcionalidade de filtragem de visualização na exibição **todos os dispositivos** :

![Habilitar a funcionalidade de visualização de filtragem](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >  **dispositivos**.
1. Selecione a faixa que diz, **Experimente as melhorias de filtragem dos novos dispositivos. Clique para habilitar a visualização.**

Agora você terá a capacidade de **adicionar filtros** à sua exibição **todos os dispositivos** .

## <a name="configure-device-settings"></a>Definir configurações de dispositivo

Para gerenciar identidades de dispositivo usando o portal do AD do Azure, esses dispositivos precisam ser [registrados ou ingressados](overview.md) no Azure AD. Como administrador, você pode controlar o processo de registro e junção de dispositivos definindo as seguintes configurações de dispositivo.

Você deve receber uma das seguintes funções para exibir ou gerenciar as configurações do dispositivo no portal do Azure:

- Administrador global
- Administrador de dispositivo em nuvem
- Leitor global
- Leitor de diretório

![Configurações do dispositivo relacionadas ao Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Os usuários podem ingressar dispositivos no Azure ad** – essa configuração permite que você selecione os usuários que podem registrar seus dispositivos como dispositivos ingressados no Azure AD. O padrão é **All**.

> [!NOTE]
> **Os usuários podem ingressar dispositivos na configuração do Azure ad** é aplicável somente ao ingresso no Azure AD no Windows 10.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Esses usuários são adicionados à função *Administradores de dispositivos* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).
- **Os usuários podem registrar seus dispositivos com o Azure ad** -você precisa definir essa configuração para permitir que dispositivos Windows 10 Personal, Ios, Android e MacOS sejam registrados com o Azure AD. Se você selecionar **nenhum**, os dispositivos não terão permissão para se registrar no Azure AD. O registro com Microsoft Intune ou MDM (gerenciamento de dispositivo móvel) para Microsoft 365 requer o registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.
- Os **dispositivos que devem ser ingressados no Azure ad ou no Azure ad registrados exigem autenticação multifator** – você pode escolher se os usuários devem fornecer um fator de autenticação adicional para ingressar ou registrar seu dispositivo no Azure AD. O padrão é **No**. É recomendável exigir a autenticação multifator ao registrar ou ingressar em um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para obter mais informações sobre os diferentes serviços de autenticação multifator do Azure AD, consulte [introdução à autenticação multifator do Azure ad](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> Os **dispositivos que devem ser ingressados no Azure ad ou no Azure ad registrados exigem a configuração de autenticação multifator** se aplicam a dispositivos que são associados ao Azure AD (com algumas exceções) ou ao AD do Azure registrado. Essa configuração não se aplica a dispositivos adicionados ao Azure AD híbrido, [VMs Unidas do Azure AD no Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) e dispositivos ingressados no Azure ad usando o [modo de Autoimplantação do Windows AutoPilot](/mem/autopilot/self-deploying).

- **Número máximo de dispositivos** – essa configuração permite que você selecione o número máximo de dispositivos registrados no Azure ad ou do AD do Azure que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. O valor padrão é **50**.

> [!NOTE]
> A configuração **de número máximo de dispositivos** se aplica a dispositivos que estão ingressados no Azure ad ou no Azure ad registrados. Essa configuração não se aplica a dispositivos ingressados no Azure AD híbrido.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Logs de auditoria

As atividades de dispositivo estão disponíveis por meio dos logs de atividade. Esses logs incluem atividades disparadas pelo serviço de registro de dispositivo e pelos usuários:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo
- Alterações nas configurações do dispositivo
- Operações de dispositivo como excluir ou atualizar um dispositivo

O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da página **Dispositivos**.

O log de auditoria tem uma exibição de lista padrão que mostra:

- A data e a hora da ocorrência
- Os destinos
- O iniciador/ator (quem) de uma atividade
- A atividade (o quê)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Captura de tela de uma tabela na seção atividade da página dispositivos que lista a data, o destino, o ator e a atividade para quatro logs de auditoria." border="false":::

Você pode personalizar o modo de exibição de lista clicando em **colunas** na barra de ferramentas.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Captura de tela mostrando a barra de ferramentas da página dispositivos. O item colunas está realçado." border="false":::

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Captura de tela dos controles de filtro de dados de auditoria, com os campos categoria, tipo de recurso de atividade, atividade, intervalo de datas, destino e ator e um campo de pesquisa." border="false":::

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)