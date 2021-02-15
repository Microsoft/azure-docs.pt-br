---
title: Arquivos de contêineres de perfil FSLogix da área de trabalho virtual do Windows – Azure
description: Este artigo descreve os contêineres de perfil FSLogix nos arquivos da área de trabalho virtual do Windows e do Azure.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b977944f3a531365487b9655820e5d8f85e13d0
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225908"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contêineres de perfil FSLogix e arquivos do Azure

O serviço de área de trabalho virtual do Windows recomenda contêineres de perfil FSLogix como uma solução de perfil de usuário. O FSLogix é projetado para usar perfis móveis em ambientes de computação remota, como área de trabalho virtual do Windows. Ele armazena um perfil de usuário completo em um único contêiner. Ao entrar, esse contêiner é dinamicamente anexado ao ambiente de computação usando o VHD (disco rígido virtual) com suporte nativo e o VHDX (disco rígido virtual) do Hyper-V. O perfil do usuário fica imediatamente disponível e aparece no sistema exatamente como um perfil do usuário nativo. Este artigo descreve como os contêineres de perfil FSLogix usados com os arquivos do Azure funcionam na área de trabalho virtual do Windows.

>[!NOTE]
>Se você estiver procurando material de comparação sobre as diferentes opções de armazenamento de contêiner de perfil FSLogix no Azure, consulte [Opções de armazenamento para contêineres de perfil FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Perfis do usuário

Um perfil de usuário contém elementos de dados sobre um indivíduo, incluindo informações de configuração, como configurações de área de trabalho, conexões de rede persistentes e configurações de aplicativo. Por padrão, o Windows cria um perfil de usuário local totalmente integrado ao sistema operacional.

Um perfil de usuário remoto fornece uma partição entre os dados do usuário e o sistema operacional. Ele permite que o sistema operacional seja substituído ou alterado sem afetar os dados do usuário. No Host da Sessão da Área de Trabalho Remota (RDSH) e nas infraestruturas de área de trabalho virtual (VDI), o sistema operacional pode ser substituído pelos seguintes motivos:

- Uma atualização do sistema operacional
- Uma substituição de uma VM (máquina virtual) existente
- Um usuário que faz parte de um ambiente de RDSH ou VDI em pool (não persistente)

Os produtos da Microsoft operam com várias tecnologias para perfis de usuário remotos, incluindo essas tecnologias:
- Perfis de usuário de roaming (RUP)
- Discos de perfil do usuário (UPD)
- ESR (Enterprise State roaming)

O UPD e o RUP são as tecnologias mais usadas para perfis de usuário em Host da Sessão da Área de Trabalho Remota (RDSH) e ambientes de disco rígido virtual (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Desafios com as tecnologias de perfil de usuário anteriores

As soluções da Microsoft existentes e herdadas para perfis de usuário vêm com vários desafios. Nenhuma solução anterior tratou de todas as necessidades de perfil de usuário fornecidas com um ambiente de RDSH ou de VDI. Por exemplo, o UPD não pode lidar com grandes arquivos OST e o RUP não mantém as configurações modernas.

#### <a name="functionality"></a>Funcionalidade

A tabela a seguir mostra os benefícios e as limitações das tecnologias de perfil de usuário anteriores.

| Tecnologia | Configurações modernas | Configurações do Win32 | Configurações do so | Dados do usuário | Com suporte no SKU do servidor | Armazenamento de back-end no Azure | Armazenamento de back-end local | Suporte à versão | Hora de entrada subsequente |Observações|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Discos de perfil do usuário (UPD)** | Sim | Sim | Sim | Sim | Sim | Não | Sim | Win 7 + | Sim | |
| **Perfil de usuário de roaming (RUP), modo de manutenção** | Não | Sim | Sim | Sim | Sim| Não | Sim | Win 7 + | Não | |
| **Enterprise State Roaming (ESR)** | Sim | Não | Sim | Não | Consulte as observações | Sim | Não | Win 10 | Não | Funções no SKU do servidor, mas sem interface do usuário de suporte |
| **User Experience Virtualization (UE-V)** | Sim | Sim | Sim | Não | Sim | Não | Sim | Win 7 + | Não |  |
| **Arquivos de nuvem do OneDrive** | Não | Não | Não | Sim | Consulte as observações | Consulte as observações  | Consulte as observações | Win 10 RS3 | Não | Não testado no SKU do servidor. O armazenamento de back-end no Azure depende do cliente de sincronização. O armazenamento de back-end no local precisa de um cliente de sincronização. |

#### <a name="performance"></a>Desempenho

O UPD requer [espaços de armazenamento diretos (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) para resolver os requisitos de desempenho. O UPD usa o protocolo SMB. Ele copia o perfil para a VM na qual o usuário está sendo registrado.

#### <a name="cost"></a>Cost

Embora os clusters de S2D alcancem o desempenho necessário, o custo é caro para clientes corporativos, mas especialmente caro para clientes de pequenas e médias empresas (SMB). Para essa solução, as empresas pagam os discos de armazenamento, juntamente com o custo das VMs que usam os discos para um compartilhamento.

#### <a name="administrative-overhead"></a>Sobrecarga administrativa

Os clusters S2D exigem um sistema operacional que seja corrigido, atualizado e mantido em um estado seguro. Esses processos e a complexidade de configurar a recuperação de desastres S2D tornam o S2D viável somente para empresas com uma equipe de ti dedicada.

## <a name="fslogix-profile-containers"></a>Contêineres de perfil FSLogix

Em 19 de novembro de 2018, a [Microsoft adquiriu o FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix resolve muitos desafios de contêiner de perfil. A chave entre eles são:

- **Desempenho:** Os [contêineres de perfil do FSLogix](/fslogix/configure-profile-container-tutorial/) são de alto desempenho e resolvem problemas de desempenho que têm o modo de troca armazenado em cache historicamente bloqueado.
- **Onedrive:** Sem os contêineres de perfil do FSLogix, o OneDrive for Business não tem suporte em ambientes de RDSH ou VDI não persistentes. A [página de suporte do VDI do onedrive](/onedrive/sync-vdi-support) informará como eles interagem. Para obter mais informações, consulte [usar o cliente de sincronização em áreas de trabalho virtuais](/deployoffice/rds-onedrive-business-vdi/).
- **Pastas adicionais:** O FSLogix fornece a capacidade de estender perfis de usuário para incluir pastas adicionais.

Desde a aquisição, a Microsoft começou a substituir as soluções de perfil de usuário existentes, como o UPD, por contêineres de perfil FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integração de arquivos do Azure com o serviço de domínio Azure Active Directory

O desempenho e os recursos dos contêineres de perfil do FSLogix aproveitam a nuvem. Em 7 de agosto de 2019, os arquivos de Microsoft Azure anunciaram a disponibilidade geral da [autenticação de arquivos do Azure com o AD DS (serviço de domínio Azure Active Directory)](../storage/files/storage-files-active-directory-overview.md). Ao abordar o custo e a sobrecarga administrativa, os arquivos do Azure com a autenticação de AD DS do Azure são uma solução premium para perfis de usuário no serviço de área de trabalho virtual do Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Práticas recomendadas para área de trabalho virtual do Windows

A área de trabalho virtual do Windows oferece controle total sobre o tamanho, o tipo e a contagem de VMs que estão sendo usadas pelos clientes. Para obter mais informações, consulte [o que é a área de trabalho virtual do Windows?](overview.md).

Para garantir que seu ambiente de área de trabalho virtual do Windows siga as práticas recomendadas:

- A conta de armazenamento de arquivos do Azure deve estar na mesma região que as VMs do host de sessão.
- As permissões dos arquivos do Azure devem corresponder às permissões descritas em [requisitos – contêineres de perfil](/fslogix/fslogix-storage-config-ht).
- Cada VM do pool de hosts deve ser criada do mesmo tipo e de uma VM de tamanho com base na mesma imagem mestra.
- Cada VM do pool de hosts deve estar no mesmo grupo de recursos para ajudar no gerenciamento, no dimensionamento e na atualização.
- Para obter um desempenho ideal, a solução de armazenamento e o contêiner de perfil FSLogix devem estar no mesmo local de data center.
- A conta de armazenamento que contém a imagem mestra deve estar na mesma região e assinatura em que as VMs estão sendo provisionadas.

## <a name="next-steps"></a>Próximas etapas

Use os guias a seguir para configurar um ambiente de área de trabalho virtual do Windows.

- Para começar a criar sua solução de virtualização de desktops, consulte [criar um locatário na área de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- Para criar um pool de hosts dentro de seu locatário de área de trabalho virtual do Windows, confira [criar um pool de hosts com o Azure Marketplace](create-host-pools-azure-marketplace.md).
- Para configurar compartilhamentos de arquivos totalmente gerenciados na nuvem, consulte [Configurar o compartilhamento de arquivos do Azure](/azure/storage/files/storage-files-active-directory-enable/).
- Para configurar contêineres de perfil FSLogix, consulte [criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos](create-host-pools-user-profile.md).
- Para atribuir usuários a um pool de hosts, consulte [gerenciar grupos de aplicativos para a área de trabalho virtual do Windows](manage-app-groups.md).
- Para acessar os recursos da área de trabalho virtual do Windows em um navegador da Web, consulte [conectar-se à área de trabalho virtual do Windows](connect-web.md).
