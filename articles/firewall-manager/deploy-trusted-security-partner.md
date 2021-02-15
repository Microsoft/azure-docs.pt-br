---
title: Implantar um provedor de parceiro de segurança do Gerenciador de firewall do Azure
description: Saiba como implantar um provedor de parceiro de segurança do Gerenciador de firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 906687e08c9f31890a9ecec9154079e704512832
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485715"
---
# <a name="deploy-a-security-partner-provider"></a>Implantar um provedor de parceiro de segurança

Os *provedores de parceiros de segurança* no Gerenciador de firewall do Azure permitem que você use suas melhores ofertas de SECaaS (segurança como serviço) conhecidas e de terceiros para proteger o acesso à Internet para seus usuários.

Para saber mais sobre cenários com suporte e diretrizes de práticas recomendadas, consulte [o que são provedores de parceiros de segurança?](trusted-security-partners.md)


Parceiros de SECaaS (segurança como serviço) de terceiros integrados agora estão disponíveis: 

- **Zscaler**
- **[Ponto de Verificação](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Implantar um provedor de segurança de terceiros em um novo hub

Ignore esta seção se você estiver implantando um provedor de terceiros em um Hub existente.

1. Entre no Portal do Azure em https://portal.azure.com.
2. Em **pesquisa**, digite **Gerenciador de firewall** e selecione-o em **Serviços**.
3. Navegue até **introdução**. Selecione **Exibir hubs virtuais protegidos**.
4. Selecione **criar novo hub virtual protegido**.
5. Insira sua assinatura e grupo de recursos, selecione uma região com suporte e adicione as informações de Hub e WAN virtual. 
6. Selecione **incluir gateway de VPN para habilitar provedores de parceiros de segurança**.
7. Selecione as **unidades de escala de gateway** apropriadas para seus requisitos.
8. Selecione **Avançar: Firewall do Azure**
   > [!NOTE]
   > Os provedores de parceiros de segurança se conectam ao seu hub usando túneis de gateway de VPN. Se você excluir o gateway de VPN, as conexões com seus provedores de parceiros de segurança serão perdidas.
9. Se você quiser implantar o Firewall do Azure para filtrar o tráfego privado junto com o provedor de serviços de terceiros para filtrar o tráfego de Internet, selecione uma política para o Firewall do Azure. Consulte os [cenários com suporte](trusted-security-partners.md#key-scenarios).
10. Se você quiser implantar apenas um provedor de segurança de terceiros no Hub, selecione **Firewall do Azure: habilitado/desabilitado** para defini-lo como **desabilitado**. 
11. Selecione  **Avançar: provedor de parceiros de segurança**.
12. Defina **provedor de parceiros de segurança** como **habilitado**. 
13. Selecione um parceiro. 
14. Selecione **Avançar: Revisar + criar**. 
15. Examine o conteúdo e, em seguida, selecione **criar**.

A implantação do gateway de VPN pode levar mais de 30 minutos.

Para verificar se o Hub foi criado, navegue até o Gerenciador de firewall do Azure->hubs protegidos. Selecione a página Visão geral do Hub->para mostrar o nome do parceiro e o status como **conexão de segurança pendente**.

Depois que o Hub for criado e o parceiro de segurança estiver configurado, continue para conectar o provedor de segurança ao Hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Implantar um provedor de segurança de terceiros em um Hub existente

Você também pode selecionar um Hub existente em uma WAN virtual e convertê-lo em um *Hub virtual protegido*.

1. Em **introdução**, selecione **Exibir hubs virtuais protegidos**.
2. Selecione **converter hubs existentes**.
3. Selecione uma assinatura e um Hub existente. Siga as etapas restantes para implantar um provedor de terceiros em um novo hub.

Lembre-se de que um gateway de VPN deve ser implantado para converter um Hub existente para um hub protegido com provedores de terceiros.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurar provedores de segurança de terceiros para se conectar a um hub protegido

Para configurar túneis para o gateway de VPN do seu hub virtual, os provedores de terceiros precisam de direitos de acesso ao seu hub. Para fazer isso, associe uma entidade de serviço com sua assinatura ou grupo de recursos e conceda direitos de acesso. Em seguida, você deve fornecer essas credenciais para terceiros usando seu portal.

### <a name="create-and-authorize-a-service-principal"></a>Criar e autorizar uma entidade de serviço

1. Criar entidade de serviço do Azure Active Directory (AD): você pode ignorar a URL de redirecionamento. 

   [Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Adicionar direitos de acesso e escopo para a entidade de serviço.
   [Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Você pode limitar o acesso somente ao seu grupo de recursos para um controle mais granular.

### <a name="visit-partner-portal"></a>Visite o portal do parceiro

1. Siga as instruções fornecidas pelo seu parceiro para concluir a instalação. Isso inclui o envio de informações do AAD para detectar e conectar-se ao Hub, atualizar as políticas de saída e verificar o status e os logs de conectividade.

   - [Zscaler: Configure Microsoft Azure integração de WAN virtual](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Ponto de verificação: Configure Microsoft Azure integração de WAN virtual](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss: Configure Microsoft Azure integração de WAN virtual](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Você pode examinar o status de criação do túnel no portal de WAN virtual do Azure no Azure. Depois que os túneis mostrarem **conectados** no portal do Azure e do parceiro, continue com as próximas etapas para configurar as rotas para selecionar quais branches e VNets devem enviar o tráfego de Internet para o parceiro.

## <a name="configure-route-settings"></a>Definir configurações de rota

1. Navegue até o Gerenciador de firewall do Azure-> os hubs protegidos. 
2. Selecione um Hub. O status do hub agora deve mostrar **provisionado** em vez da **conexão de segurança pendente**.

   Verifique se o provedor de terceiros pode se conectar ao Hub. Os túneis no gateway de VPN devem estar em um estado **conectado** . Esse estado é mais refletido na integridade da conexão entre o Hub e o parceiro de terceiros, em comparação com o status anterior.
3. Selecione o Hub e navegue até **configurações de rota**.

   Quando você implanta um provedor de terceiros no Hub, ele converte o Hub em um *Hub virtual protegido*. Isso garante que o provedor de terceiros esteja anunciando uma rota 0.0.0.0/0 (padrão) para o Hub. No entanto, conexões VNet e sites conectados ao Hub não obtêm essa rota, a menos que você opte pelo qual as conexões devem obter essa rota padrão.
4. Em **tráfego da Internet**, selecione **VNet a Internet** ou **ramificação para a Internet** ou ambas as rotas são configuradas como enviar por meio de terceiros.

   Isso indica apenas o tipo de tráfego que deve ser roteado para o Hub, mas ele não afeta as rotas em VNets ou branches ainda. Essas rotas não são propagadas para todos os VNets/branches anexados ao Hub por padrão.
5. Você deve selecionar **conexões seguras** e selecionar as conexões nas quais essas rotas devem ser definidas. Isso indica quais VNets/branches podem começar a enviar tráfego de Internet para o provedor de terceiros.
6. Em **configurações de rota**, selecione **conexões seguras** em tráfego da Internet e, em seguida, selecione a VNet ou branches (*sites* na WAN virtual) a serem protegidos. Selecione **proteger o tráfego da Internet**.
   ![Proteger o tráfego da Internet](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navegue de volta para a página hubs. O status do **provedor de parceiros de segurança** do hub agora deve ser  **protegido**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Tráfego de Internet de filial ou VNet por meio de serviço de terceiros

Em seguida, você pode verificar se as máquinas virtuais VNet ou o site da filial podem acessar a Internet e validar se o tráfego está fluindo para o serviço de terceiros.

Depois de concluir as etapas de configuração de rota, as máquinas virtuais de VNet, bem como os sites de ramificação, são enviadas um 0/0 para rota de serviço de terceiros. Não é possível RDP ou SSH nessas máquinas virtuais. Para entrar, você pode implantar o serviço de [bastiões do Azure](../bastion/bastion-overview.md) em uma VNet emparelhada.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Proteger sua rede na nuvem com o Gerenciador de Firewall do Azure usando o portal do Azure](secure-cloud-network.md)