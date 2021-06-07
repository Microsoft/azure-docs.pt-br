---
title: Proteger o acesso remoto à VM no Azure AD Domain Services | Microsoft Docs
description: Saiba como proteger o acesso remoto a VMs usando o NPS (servidor de diretivas de rede) e a autenticação multifator do Azure AD com uma implantação de Serviços de Área de Trabalho Remota em um domínio Azure Active Directory Domain Services gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618919"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Proteger o acesso remoto a máquinas virtuais no Azure Active Directory Domain Services

Para proteger o acesso remoto a VMs (máquinas virtuais) executadas em um domínio gerenciado Azure Active Directory Domain Services (AD DS do Azure), você pode usar o Serviços de Área de Trabalho Remota (RDS) e o servidor de políticas de rede (NPS). O Azure AD DS autentica os usuários à medida que eles solicitam acesso por meio do ambiente RDS. Para aumentar a segurança, você pode integrar a autenticação multifator do Azure AD para fornecer um prompt de autenticação adicional durante eventos de entrada. A autenticação multifator do Azure AD usa uma extensão para que o NPS forneça esse recurso.

> [!IMPORTANT]
> A maneira recomendada para se conectar com segurança às suas VMs em um domínio gerenciado AD DS do Azure está usando a bastiões do Azure, um serviço de PaaS totalmente gerenciado por plataforma que você provisiona dentro de sua rede virtual. Um host de bastiões fornece conectividade de protocolo RDP (RDP) segura e direta para suas VMs diretamente no portal do Azure sobre SSL. Quando você se conecta por meio de um host de bastiões, suas VMs não precisam de um endereço IP público e você não precisa usar grupos de segurança de rede para expor o acesso ao RDP na porta TCP 3389.
>
> É altamente recomendável que você use a bastiões do Azure em todas as regiões em que há suporte. Em regiões sem a disponibilidade de bastiões do Azure, siga as etapas detalhadas neste artigo até que a bastiões do Azure esteja disponível. Tome cuidado com a atribuição de endereços IP públicos a VMs Unidas ao Azure AD DS em que todo o tráfego de entrada RDP é permitido.
>
> Para obter mais informações, consulte [o que é a bastiões do Azure?][bastion-overview].

Este artigo mostra como configurar o RDS no Azure AD DS e, opcionalmente, usar a extensão NPS da autenticação multifator do Azure AD.

![Visão geral do Serviços de Área de Trabalho Remota (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma sub-rede de *cargas de trabalho* criada em seu Azure Active Directory Domain Services rede virtual.
    * Se necessário, [Configure a rede virtual para um Azure Active Directory Domain Services domínio gerenciado][configure-azureadds-vnet].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Implantar e configurar o ambiente de Área de Trabalho Remota

Para começar, crie no mínimo duas VMs do Azure que executam o Windows Server 2016 ou o Windows Server 2019. Para redundância e alta disponibilidade do seu ambiente de Área de Trabalho Remota (RD), você pode adicionar e balancear a carga de hosts adicionais mais tarde.

Uma implantação de RDS sugerida inclui as duas VMs a seguir:

* *RDGVM01* -executa o servidor do agente de conexão de área de trabalho remota, o servidor de acesso via Web RD e o servidor Gateway RD.
* *RDSHVM01* -executa o servidor de host da Sessão RD.

Verifique se as VMs estão implantadas em uma sub-rede de *cargas de trabalho* de sua rede virtual do Azure AD DS e, em seguida, ingresse as VMs no domínio gerenciado. Para obter mais informações, consulte como [criar e ingressar uma VM do Windows Server em um domínio gerenciado][tutorial-create-join-vm].

A implantação do ambiente RD contém várias etapas. O guia de implantação de RD existente pode ser usado sem nenhuma alteração específica para usar em um domínio gerenciado:

1. Entre em VMs criadas para o ambiente de RD com uma conta que faça parte do grupo de *Administradores de DC do Azure ad* , como *contosoadmin*.
1. Para criar e configurar o RDS, use o [Guia de implantação de ambiente de área de trabalho remota][deploy-remote-desktop]existente. Distribua os componentes do servidor RD entre suas VMs do Azure, conforme desejado.
    * Específico do Azure AD DS-quando você configura o Licenciamento RD, defina-o para o modo **por dispositivo** , não **por usuário** , conforme observado no guia de implantação.
1. Se você quiser fornecer acesso usando um navegador da Web, [Configure o cliente web área de trabalho remota para seus usuários][rd-web-client].

Com o RD implantado no domínio gerenciado, você pode gerenciar e usar o serviço como faria com um domínio de AD DS local.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>Implantar e configurar o NPS e a extensão NPS do Azure AD MFA

Se você quiser aumentar a segurança da experiência de entrada do usuário, você pode opcionalmente integrar o ambiente de RD com a autenticação multifator do Azure AD. Com essa configuração, os usuários recebem um prompt adicional durante a entrada para confirmar sua identidade.

Para fornecer esse recurso, um servidor de diretivas de rede (NPS) adicional é instalado em seu ambiente junto com a extensão NPS da autenticação multifator do Azure AD. Essa extensão se integra ao Azure AD para solicitar e retornar o status de prompts de autenticação multifator.

Os usuários devem ser [registrados para usar a autenticação multifator do Azure ad][user-mfa-registration], que pode exigir licenças adicionais do Azure AD.

Para integrar a autenticação multifator do Azure AD ao seu ambiente do Azure AD DS Área de Trabalho Remota, crie um servidor NPS e instale a extensão:

1. Crie uma VM Windows Server 2016 ou 2019 adicional, como *NPSVM01*, que está conectada a uma sub-rede de *cargas de trabalho* em sua rede virtual do Azure AD DS. Ingresse a VM no domínio gerenciado.
1. Entre na VM do NPS como uma conta que faça parte do grupo de *Administradores de DC do Azure ad* , como *contosoadmin*.
1. Em **Gerenciador do servidor**, selecione **adicionar funções e recursos** e, em seguida, instale a função de *serviços de acesso e política de rede* .
1. Use o artigo de instruções existentes para [instalar e configurar a extensão NPS do Azure ad MFA][nps-extension].

Com o servidor NPS e a extensão NPS da autenticação multifator do Azure AD instaladas, conclua a próxima seção para configurá-lo para uso com o ambiente de RD.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Integrar o gateway de Área de Trabalho Remota e a autenticação multifator do Azure AD

Para integrar a extensão do NPS da autenticação multifator do Azure AD, use o artigo de instruções existentes para [integrar sua infraestrutura de área de trabalho remota gateway usando a extensão do servidor de políticas de rede (NPS) e o Azure ad][azure-mfa-nps-integration].

As opções de configuração adicionais a seguir são necessárias para integrar um domínio gerenciado:

1. Não [Registre o servidor NPS no Active Directory][register-nps-ad]. Esta etapa falha em um domínio gerenciado.
1. Na [etapa 4 para configurar a política de rede][create-nps-policy], marque também a caixa para **ignorar as propriedades de discagem da conta de usuário**.
1. Se você usar o Windows Server 2019 para o servidor NPS e a extensão NPS da autenticação multifator do Azure AD, execute o seguinte comando para atualizar o canal seguro para permitir que o servidor NPS se comunique corretamente:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Agora, os usuários são solicitados a fornecer um fator de autenticação adicional ao entrarem, como uma mensagem de texto ou um prompt no aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como melhorar a resiliência de sua implantação, consulte [serviços de área de trabalho remota-alta disponibilidade][rds-high-availability].

Para obter mais informações sobre como proteger a entrada do usuário, consulte [como ele funciona: autenticação multifator do Azure ad][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability