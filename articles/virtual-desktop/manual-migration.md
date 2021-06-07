---
title: Migrar manualmente da área de trabalho virtual do Windows (clássico)-Azure
description: Como migrar manualmente da área de trabalho virtual do Windows (clássica) para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516164"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Migrar manualmente da área de trabalho virtual do Windows (clássico)

A área de trabalho virtual do Windows (clássica) cria seu ambiente de serviço com cmdlets do PowerShell, APIs REST e objetos de serviço. Um "objeto" em um ambiente de serviço de área de trabalho virtual do Windows é uma coisa que a área de trabalho virtual do Windows cria. Os objetos de serviço incluem locatários, pools de hosts, grupos de aplicativos e hosts de sessão.

No entanto, a área de trabalho virtual do Windows (clássica) não está integrada ao Azure. Sem a integração do Azure, todos os objetos que você criar não serão gerenciados automaticamente pelo portal do Azure porque não estão conectados à sua assinatura do Azure.

A recente atualização importante da área de trabalho virtual do Windows marca uma mudança no serviço em direção à integração completa do Azure. Os objetos criados na área de trabalho virtual do Windows são gerenciados automaticamente pelo portal do Azure.

Neste artigo, explicaremos por que você deve considerar a migração para a versão mais recente da área de trabalho virtual do Windows. Depois disso, vamos lhe dizer como migrar manualmente da área de trabalho virtual do Windows (clássica) para a atualização mais recente da área de trabalho virtual do Windows.

## <a name="why-migrate"></a>Por que migrar?

As principais atualizações podem ser inconvenientes, especialmente aquelas que você precisa fazer manualmente. No entanto, há algumas razões pelas quais você não pode migrar automaticamente:

- Os objetos de serviço existentes feitos com a versão clássica não têm nenhuma representação no Azure. Seu escopo não se estende além do serviço de área de trabalho virtual do Windows.
- Com a atualização mais recente, a ID do aplicativo do serviço foi alterada para remover o consentimento de aplicativos como fazia para a área de trabalho virtual do Windows (clássica). Você não poderá criar novos objetos do Azure com a área de trabalho virtual do Windows, a menos que eles sejam autenticados com a nova ID do aplicativo.

Apesar da complicação, a migração para fora da versão clássica ainda é importante. Veja o que você pode fazer depois de migrar:

- Gerencie a área de trabalho virtual do Windows por meio do portal do Azure.
- Atribuir grupos de usuários Azure Active Directory (AD) a grupos de aplicativos.
- Use o recurso de Log Analytics aprimorado para solucionar problemas de implantação.
- Use o Azure RBAC (controle de acesso baseado em função) nativo do Azure para gerenciar o acesso administrativo.

## <a name="when-should-i-migrate"></a>Quando devo migrar?

Ao se perguntar se você deve migrar, você também deve levar em conta a situação atual e futura da sua implantação.

Há alguns cenários em particular em que recomendamos que você migre manualmente:

- Você tem uma instalação de pool de hosts de teste com um pequeno número de usuários.
- Você tem uma configuração de pool de hosts de produção com um pequeno número de usuários, mas planeja eventualmente aumentar para centenas de usuários.
- Você tem uma configuração simples que pode ser facilmente replicada. Por exemplo, se suas VMs usarem uma imagem da galeria.

> [!IMPORTANT]
> Se você estiver usando uma configuração avançada que demorou muito tempo para estabilizar ou ter muitos usuários, não recomendamos a migração manual.

## <a name="prepare-for-migration"></a>Preparar para a migração

Antes de começar, você precisará certificar-se de que seu ambiente está pronto para migrar.

Veja o que você precisa para iniciar o processo de migração:

- Uma assinatura do Azure em que você criará novos objetos de serviço do Azure.
- Certifique-se de que você está atribuído às seguintes funções:
    
    - Colaborador
    - Administrador de Acesso do Usuário
    
    A função colaborador permite criar objetos do Azure em sua assinatura e a função Administrador de acesso do usuário permite atribuir usuários a grupos de aplicativos.

## <a name="how-to-migrate-manually"></a>Como migrar manualmente

Agora que você se preparou com o processo de migração, é hora de realmente migrar.

Para migrar manualmente da área de trabalho virtual do Windows (clássico) para a área de trabalho virtual do Windows:

1. Siga as instruções em [criar um pool de hosts com o portal do Azure](create-host-pools-azure-marketplace.md) para criar todos os objetos de alto nível com o portal do Azure.
2. Se você quiser trazer as máquinas virtuais que já está usando, siga as instruções em [registrar as máquinas virtuais no pool de hosts da área de trabalho virtual do Windows](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) para registrá-las manualmente no novo pool de hosts criado na etapa 1.
3. Crie novos grupos de aplicativos do RemoteApp.
4. Publicar usuários ou grupos de usuários nos novos grupos de aplicativos de área de trabalho e RemoteApp.
5. Atualize sua política de acesso condicional para permitir os novos objetos seguindo as instruções em [Configurar a autenticação multifator](set-up-mfa.md).

Para evitar o tempo de inatividade, primeiro você deve registrar os hosts de sessão existentes nos pools de hosts Azure Resource Manager integrados em pequenos grupos de cada vez. Depois disso, traga seus usuários lentamente para os novos grupos de aplicativos integrados ao Azure Resource Manager.

## <a name="next-steps"></a>Próximas etapas

Depois de migrar, saiba como a área de trabalho virtual do Windows funciona conferindo [nossos tutoriais](create-host-pools-azure-marketplace.md). Saiba mais sobre os recursos de gerenciamento avançado em [expandir um pool de hosts existente](expand-existing-host-pool.md) e [Personalizar as propriedades de RDP](customize-rdp-properties.md).

Para saber mais sobre os objetos de serviço, confira [ambiente de área de trabalho virtual do Windows](environment-setup.md).
