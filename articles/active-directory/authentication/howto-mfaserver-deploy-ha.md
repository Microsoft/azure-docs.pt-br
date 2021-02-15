---
title: Alta disponibilidade para o Servidor da MFA do Azure – Azure Active Directory
description: Implante várias instâncias do Servidor de Autenticação Multifator do Azure em configurações que fornecem alta disponibilidade.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584469"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurar o Servidor de Autenticação Multifator do Azure para alta disponibilidade

Para obter alta disponibilidade com a implantação do Servidor do Azure MFA, você precisa implantar vários servidores do MFA. Esta seção fornece informações sobre um design com balanceamento de carga para atingir suas metas de alta disponibilidade na implantação do Servidor do Azure MFS.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="mfa-server-overview"></a>Visão geral do Servidor do MFA

A arquitetura de serviço do Servidor do MFA do Azure consiste em vários componentes, conforme mostrado no seguinte diagrama:

 ![Componentes da Arquitetura do Servidor da MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Um Servidor do MFA é um Windows Server que tem o software de Autenticação Multifator do Azure instalado. A instância do Servidor do MFA deve ser ativada pelo Serviço do MFA no Azure para que ela funcione. Mais de um Servidor do MFA pode ser instalado localmente.

O primeiro servidor MFA instalado é o servidor MFA primário na ativação pelo serviço Azure MFA por padrão. O servidor MFA primário tem uma cópia gravável do banco de dados PhoneFactor. pfdata. As instalações posteriores de instâncias do Servidor do MFA são conhecidas como subordinados. Os subordinados do MFA têm uma cópia replicada somente leitura do banco de dados PhoneFactor.pfdata. Os servidores do MFA replicam informações usando a RPC (Chamada de Procedimento Remoto). Todos os Servidores do MFA devem ser ingressados no domínio em conjunto ou ser autônomos para replicar as informações.

Os servidores MFA primário e subordinado MFA se comunicam com o serviço MFA quando a autenticação de dois fatores é necessária. Por exemplo, quando um usuário tenta obter acesso a um aplicativo que exige a autenticação de dois fatores, o usuário primeiro será autenticado por um provedor de identidade, como o AD (Active Directory).

Após a autenticação bem-sucedida no AD, o Servidor do MFA se comunicará com o Serviço do MFA. O Servidor do MFA aguarda a notificação do Serviço do MFA para permitir ou negar o acesso do usuário ao aplicativo.

Se o servidor primário MFA ficar offline, as autenticações ainda poderão ser processadas, mas as operações que exigirem alterações no banco de dados MFA não poderão ser processadas. (Os exemplos incluem: a adição de usuários, alterações do PIN de autoatendimento, a alteração das informações do usuário ou o acesso ao portal do usuário)

## <a name="deployment"></a>Implantação

Considere os pontos importantes a seguir para o balanceamento de carga do Servidor MFA do Azure e de seus componentes relacionados.

* **Usando o padrão RADIUS para obter alta disponibilidade**. Se estiver usando Servidores do MFA do Azure como servidores RADIUS, poderá configurar potencialmente um Servidor do MFA como um destino de autenticação primária RADIUS e outros Servidores do MFA do Azure como destinos de autenticação secundária. No entanto, esse método para obter alta disponibilidade pode não ser prático porque é necessário aguardar até que ocorra um período de tempo limite quando a autenticação falha no destino de autenticação primária antes de poder ser autenticado no destino de autenticação secundária. É mais eficiente balancear a carga do tráfego do RADIUS entre o cliente RADIUS e os Servidores RADIUS (nesse caso, os Servidores do MFA do Azure que atuam como servidores RADIUS), de modo que você possa configurar os clientes RADIUS com uma única URL para a qual eles podem apontar.
* **É necessário promover os servidores subordinados do MFA manualmente**. Se o servidor do Azure MFA primário ficar offline, os servidores secundários do Azure MFA continuarão processando as solicitações de MFA. No entanto, até que um servidor MFA primário esteja disponível, os administradores não podem adicionar usuários nem modificar as configurações de MFA, e os usuários não podem fazer alterações usando o portal do usuário. Promover um MFA subordinado à função primária é sempre um processo manual.
* **Separabilidade de componentes**. O Servidor do MFA do Azure consiste em vários componentes que podem ser instalados na mesma instância do Windows Server ou em instâncias diferentes. Esses componentes incluem o Portal do Usuário, o Serviço Web do Aplicativo Móvel e o adaptador do AD FS (agente). Essa separabilidade possibilita o uso do Proxy de Aplicativo Web para publicar o Portal do Usuário e o Servidor Web do Aplicativo Móvel por meio da rede de perímetro. Uma configuração desse tipo contribui para a segurança geral do design, conforme mostrado no diagrama a seguir. O Portal do Usuário do MFA e o Servidor Web do Aplicativo Móvel também podem ser implantados em configurações com balanceamento de carga de HA.

   ![Servidor do MFA com uma rede de perímetro](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **A OTP (senha de uso único) sobre o SMS (também conhecida como SMS unidirecional) requer o uso de sessões adesivas se o tráfego tiver balanceamento de carga**. O SMS unidirecional é uma opção de autenticação que faz com que o Servidor do MFA envie aos usuários uma mensagem de texto que contém um OTP. O usuário insere o OTP em uma janela de prompt para concluir o desafio de MFA. Se você balancear a carga dos Servidores do MFA do Azure, o mesmo servidor que atendeu à solicitação de autenticação inicial deverá ser o servidor que recebe a mensagem OTP do usuário; se outro Servidor do MFA receber a resposta OTP, o desafio de autenticação falhará. Para obter mais informações, consulte [Senha avulsa por SMS adicionada ao Servidor MFA do Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **As implantações com balanceamento de carga do Portal do Usuário e do Serviço Web do Aplicativo Móvel exigem sessões temporárias**. Se você estiver balanceando a carga do Portal do Usuário do MFA e do Serviço Web do Aplicativo Móvel, cada sessão precisará permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implantação de alta disponibilidade

O diagrama a seguir mostra uma implementação completa com balanceamento de carga de HA do MFA do Azure e seus componentes, juntamente com o AD FS para referência.

 ![Implementação de HA do Servidor do MFA do Azure](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Observe os itens a seguir em relação à área numerada correspondente do diagrama anterior.

1. Os dois Servidores do MFA do Azure (MFA1 e MFA2) têm balanceamento de carga (mfaapp.contoso.com) e são configurados para usar uma porta estática (4443) para replicar o banco de dados PhoneFactor.pfdata. O SDK do Serviço Web é instalado em cada Servidor do MFA para habilitar a comunicação pela porta TCP 443 com os servidores do AD FS. Os servidores do MFA são implantados em uma configuração com balanceamento de carga sem monitoração de estado. No entanto, se desejar usar o OTP por SMS, use o balanceamento de carga com estado.
   ![Servidor MFA do Azure – HA do servidor de aplicativos](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Como a RPC usa portas dinâmicas, não é recomendável abrir firewalls até o intervalo de portas dinâmicas que podem ser potencialmente usadas pela RPC. Se você tiver um firewall **entre** os servidores de aplicativos MFA, deverá configurar o servidor MFA para se comunicar em uma porta estática para o tráfego de replicação entre servidores subordinados e primários e abrir essa porta no firewall. Force a porta estática criando um valor do Registro DWORD em ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` chamado ```Pfsvc_ncan_ip_tcp_port``` e configurando o valor com uma porta estática disponível. As conexões são sempre iniciadas pelos servidores do MFA subordinados para o primário, a porta estática só é necessária no primário, mas como você pode promover um subordinado para ser o primário a qualquer momento, você deve definir a porta estática em todos os servidores MFA.

2. Os dois servidores do Portal do Usuário/Aplicativo Móvel do MFA (MFA-UP-MAS1 e MFA-UP-MAS2) têm balanceamento de carga em uma configuração **com estado** (mfa.contoso.com). Lembre-se de que as sessões temporárias são um requisito para o balanceamento de carga do Portal do Usuário do MFA e do Serviço de Aplicativo Móvel.
   ![Servidor MFA do Azure – HA do Portal do Usuário e do Serviço de Aplicativo Móvel](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. O farm de Servidores do AD FS tem balanceamento de carga e é publicado na Internet por meio de proxies do AD FS com balanceamento de carga na rede de perímetro. Cada Servidor do AD FS usa o agente do AD FS para se comunicar com os Servidores MFA do Azure usando uma única URL com balanceamento de carga (mfaapp.contoso.com) pela porta TCP 443.

## <a name="next-steps"></a>Próximas etapas

* [Instalar e configurar o Servidor do MFA do Azure](howto-mfaserver-deploy.md)
