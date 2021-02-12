---
title: Perguntas frequentes sobre várias sessões do Windows 10 Enterprise – Azure
description: Perguntas frequentes e práticas recomendadas para usar o Windows 10 Enterprise Multi-Session para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24d27859aad7642392a5702ab7ddbb727b538d51
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025003"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Perguntas frequentes sobre o Windows 10 Enterprise de várias sessões

Este artigo responde a perguntas frequentes e explica as práticas recomendadas para o Windows 10 Enterprise Multi-Session.

## <a name="what-is-windows-10-enterprise-multi-session"></a>O que é o Windows 10 Enterprise Multi-Session?

O Windows 10 Enterprise Multi-Session, anteriormente conhecido como Windows 10 Enterprise para desktops virtuais (EVD), é um novo Host da Sessão da Área de Trabalho Remota que permite várias sessões interativas simultâneas. Anteriormente, apenas o Windows Server poderia fazer isso. Esse recurso oferece aos usuários uma experiência familiar do Windows 10, enquanto pode se beneficiar das vantagens de custo de várias sessões e usar o licenciamento do Windows por usuário existente em vez de CALs (licenças de acesso para cliente) do RDS. Para obter mais informações sobre licenças e preços, consulte [preços da área de trabalho virtual do Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quantos usuários podem simultaneamente ter uma sessão interativa no Windows 10 Enterprise Multi-Session?

Quantas sessões interativas podem estar ativas ao mesmo tempo dependem dos recursos de hardware do seu sistema (vCPU, memória, disco e vGPU), como os usuários usam seus aplicativos enquanto estiverem conectados a uma sessão e a sua carga de trabalho do sistema. Sugerimos que você valide o desempenho do sistema para entender quantos usuários você pode ter no Windows 10 Enterprise Multi-Session. Para saber mais, confira [preços da área de trabalho virtual do Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Por que meu aplicativo relata o Windows 10 Enterprise Multi-Session como um sistema operacional de servidor?

O Windows 10 Enterprise Multi-Session é uma edição virtual do Windows 10 Enterprise. Uma das diferenças é que esse sistema operacional (SO) relata o [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) como tendo um valor de 3, o mesmo valor que o Windows Server. Essa propriedade mantém o sistema operacional compatível com as ferramentas de gerenciamento de RDSH existentes, aplicativos de regestão de várias sessões de RDSH e, principalmente, otimizações de desempenho do sistema de nível inferior para ambientes de RDSH. Alguns instaladores de aplicativo podem bloquear a instalação em várias sessões do Windows 10, dependendo se detectarem o ProductType está definido como cliente. Se seu aplicativo não for instalado, entre em contato com o fornecedor do aplicativo para obter uma versão atualizada.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Posso executar o Windows 10 Enterprise com várias sessões no local?

O Windows 10 Enterprise Multi-Session não pode ser executado em ambientes de produção locais porque ele é otimizado para o serviço de área de trabalho virtual do Windows para Azure. Ele está relacionado ao contrato de licenciamento para executar o Windows 10 Enterprise Multi-Session fora do Azure para fins de produção. O Windows 10 Enterprise Multi-Session não será ativado nos serviços de gerenciamento de chaves (KMS) locais.

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Como fazer personalizar a imagem de várias sessões do Windows 10 Enterprise para minha organização?

Você pode iniciar uma VM (máquina virtual) no Azure com Windows 10 Windows 10 Enterprise Multi-Session e personalizá-la Instalando aplicativos LOB, Sysprep/generalizar e, em seguida, criar uma imagem usando o portal do Azure.

Para começar, crie uma VM no Azure com o Windows 10 Enterprise Multi-Session. Em vez de iniciar a VM no Azure, você pode baixar o VHD diretamente. Depois disso, você poderá usar o VHD que baixou para criar uma nova VM de geração 1 em um PC com Windows 10 com o Hyper-V habilitado.

Personalize a imagem para suas necessidades Instalando aplicativos LOB e Sysprep a imagem. Quando você terminar de personalizar, carregue a imagem no Azure com o VHD dentro. Depois disso, obtenha a área de trabalho virtual do Windows no Azure Marketplace e use-a para implantar um novo pool de hosts com a imagem personalizada.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Como fazer gerenciar o Windows 10 Enterprise Multi-Session após a implantação?

Você pode usar qualquer ferramenta de configuração com suporte, mas é recomendável Configuration Manager a versão 1906 porque ela dá suporte a Windows 10 Enterprise Multi-Session. No momento, estamos trabalhando no suporte Microsoft Intune.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>O ingresso no Windows 10 Enterprise Multi-Session Azure Active Directory (AD) pode ser ingressado?

O Windows 10 Enterprise Multi-Session tem suporte no momento para ser ingressado no Azure AD híbrido. Depois que o Windows 10 Enterprise Multi-Session estiver ingressado no domínio, use o objeto Política de Grupo existente para habilitar o registro do Azure AD. Para obter mais informações, consulte [planejar sua implementação de junção de Azure Active Directory híbrida](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Onde posso encontrar a imagem de várias sessões do Windows 10 Enterprise?

O Windows 10 Enterprise Multi-Session está na galeria do Azure. Para encontrá-lo, navegue até a portal do Azure e procure a versão Windows 10 Enterprise para áreas de trabalho virtuais. Para uma imagem integrada com Microsoft 365 aplicativos para empresas, vá para a portal do Azure e procure por **aplicativos do Microsoft Windows 10 + Microsoft 365 para empresas**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Qual imagem de várias sessões do Windows 10 Enterprise devo usar?

A galeria do Azure tem várias versões, incluindo Windows 10 Enterprise Multi-Session, versão 1809 e Windows 10 Enterprise Multi-Session, versão 1903. É recomendável usar a versão mais recente para melhorar o desempenho e a confiabilidade.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quais versões de várias sessões do Windows 10 Enterprise têm suporte?

O Windows 10 Enterprise Multi-Session, versões 1809 e posteriores têm suporte e estão disponíveis na galeria do Azure. Essas versões seguem a mesma política de ciclo de vida de suporte que o Windows 10 Enterprise, o que significa que a versão de Março tem suporte por 18 meses e pela versão de setembro por 30 meses.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Qual solução de gerenciamento de perfil devo usar para o Windows 10 Enterprise Multi-Session?

Recomendamos que você use contêineres de perfil FSLogix ao configurar o Windows 10 Enterprise em ambientes não persistentes ou outros cenários que precisam de um perfil armazenado centralmente. O FSLogix garante que o perfil do usuário esteja disponível e atualizado para cada sessão de usuário. Também recomendamos que você use seu contêiner de perfil do FSLogix para armazenar um perfil de usuário em qualquer compartilhamento SMB com as permissões apropriadas, mas você pode armazenar perfis de usuário no armazenamento de blobs de páginas do Azure, se necessário. Os usuários da área de trabalho virtual do Windows podem usar o FSLogix sem custo adicional.

Para obter mais informações sobre como configurar um contêiner de perfil do FSLogix, consulte [Configurar o contêiner do perfil FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Qual licença preciso para acessar várias sessões do Windows 10 Enterprise?

Para obter uma lista completa das licenças aplicáveis, consulte [preços da área de trabalho virtual do Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Por que meus aplicativos desaparecem depois que eu sair?

Isso acontece porque você está usando o Windows 10 Enterprise Multi-Session com uma solução de gerenciamento de perfil como FSLogix. Sua solução de administrador ou perfil configurou seu sistema para excluir perfis de usuário quando os usuários se desconectarem. Essa configuração significa que, quando o sistema exclui seu perfil de usuário depois de sair, ele também remove todos os aplicativos que você instalou durante a sessão. Se você quiser manter os aplicativos instalados, precisará pedir ao administrador para provisionar esses aplicativos para todos os usuários em seu ambiente de área de trabalho virtual do Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Como fazer garantir que os aplicativos não desapareçam quando os usuários se desconectarem?

A maioria dos ambientes virtualizados é configurada por padrão para impedir que os usuários instalem aplicativos adicionais em seus perfis. Se você quiser garantir que um aplicativo não desapareça quando o usuário sair da área de trabalho virtual do Windows, será necessário provisionar esse aplicativo para todos os perfis de usuário em seu ambiente. Para obter mais informações sobre como provisionar aplicativos, confira estes recursos:

- [Publicar aplicativos internos na área de trabalho virtual do Windows](publish-apps.md)
- [Opções de linha de comando de serviço do pacote de aplicativo do DISM](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Como fazer garantir que os usuários não baixem e instalem aplicativos do Microsoft Store?

Você pode desabilitar o aplicativo Microsoft Store para garantir que os usuários não baixem aplicativos extras além dos aplicativos que você já provisionou para eles.

Para desabilitar o aplicativo da loja:

1. Crie um novo Política de Grupo.
2. Selecione **configuração do computador**  >  **modelos administrativos**  >  **componentes do Windows**.
3. Selecione **Repositório**.
4. Selecione **armazenar aplicativo**.
5. Selecione **desabilitado** e selecione **OK**.
6. Escolha **Aplicar**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a área de trabalho virtual do Windows e a várias sessões do Windows 10 Enterprise:

- Leia nossa [documentação da área de trabalho virtual do Windows](overview.md)
- Visite nossa [área de trabalho virtual do Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configurar sua implantação de área de trabalho virtual do Windows com os [tutoriais de área de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)