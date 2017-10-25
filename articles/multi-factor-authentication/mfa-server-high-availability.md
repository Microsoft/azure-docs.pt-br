---
title: Configurar o Servidor do MFA do Azure para alta disponibilidade | Microsoft Docs
description: "Implante várias instâncias do Servidor de Autenticação Multifator do Azure em configurações que fornecem alta disponibilidade."
services: multi-factor-authentication
keywords: MFA do Azure,
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: 9f03e61e05383c309fb66b67e0641b17df5ab00f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurar o Servidor de Autenticação Multifator do Azure para alta disponibilidade

Para obter alta disponibilidade com a implantação do Servidor do Azure MFA, você precisa implantar vários servidores do MFA. Esta seção fornece informações sobre um design com balanceamento de carga para atingir suas metas de alta disponibilidade na implantação do Servidor do Azure MFS.

## <a name="mfa-server-overview"></a>Visão geral do Servidor do MFA

A arquitetura de serviço do Servidor do MFA do Azure consiste em vários componentes, conforme mostrado no seguinte diagrama:

 ![Arquitetura do Servidor do MFA](./media/mfa-server-high-availability/mfa-ha-architecture.png)

Um Servidor do MFA é um Windows Server que tem o software de Autenticação Multifator do Azure instalado. A instância do Servidor do MFA deve ser ativada pelo Serviço do MFA no Azure para que ela funcione. Mais de um Servidor do MFA pode ser instalado localmente.

O primeiro Servidor do MFA instalado é o Servidor mestre do MFA após a ativação pelo Serviço do MFA do Azure, por padrão. O servidor mestre do MFA tem uma cópia gravável do banco de dados PhoneFactor.pfdata. As instalações posteriores de instâncias do Servidor do MFA são conhecidas como servidores subordinados. Os servidores subordinados do MFA têm uma cópia replicada somente leitura do banco de dados PhoneFactor.pfdata. Os servidores do MFA replicam informações usando a RPC (Chamada de Procedimento Remoto). Todos os Servidores do MFA devem ser ingressados no domínio em conjunto ou ser autônomos para replicar as informações.

Os Servidores mestre e subordinado do MFA se comunicam com o Serviço do MFA quando a autenticação de dois fatores é obrigatória. Por exemplo, quando um usuário tenta obter acesso a um aplicativo que exige a autenticação de dois fatores, o usuário primeiro será autenticado por um provedor de identidade, como o AD (Active Directory).

Após a autenticação bem-sucedida no AD, o Servidor do MFA se comunicará com o Serviço do MFA. O Servidor do MFA aguarda a notificação do Serviço do MFA para permitir ou negar o acesso do usuário ao aplicativo.

Se o servidor mestre do MFA ficar offline, as autenticações ainda poderão ser processadas, mas as operações que exigem alterações no banco de dados do MFA não poderão ser processadas. (Os exemplos incluem: a adição de usuários, alterações do PIN de autoatendimento e a alteração das informações do usuário)

## <a name="deployment"></a>Implantação

Considere os pontos importantes a seguir para o balanceamento de carga do Servidor MFA do Azure e de seus componentes relacionados.

* **Usando o padrão RADIUS para obter alta disponibilidade**. Se estiver usando Servidores do MFA do Azure como servidores RADIUS, poderá configurar potencialmente um Servidor do MFA como um destino de autenticação primária RADIUS e outros Servidores do MFA do Azure como destinos de autenticação secundária. No entanto, esse método para obter alta disponibilidade pode não ser prático porque é necessário aguardar até que ocorra um período de tempo limite quando a autenticação falha no destino de autenticação primária antes de poder ser autenticado no destino de autenticação secundária. É mais eficiente balancear a carga do tráfego do RADIUS entre o cliente RADIUS e os Servidores RADIUS (nesse caso, os Servidores do MFA do Azure que atuam como servidores RADIUS), de modo que você possa configurar os clientes RADIUS com uma única URL para a qual eles podem apontar.
* **É necessário promover os servidores subordinados do MFA manualmente**. Se o servidor mestre do MFA do Azure ficar offline, os Servidores secundários do MFA do Azure continuarão processando as solicitações do MFA. No entanto, até que um servidor mestre do MFA esteja disponível, os administradores não poderão adicionar usuários nem modificar as configurações do MFA e os usuários não poderão fazer alterações usando o portal do usuário. A promoção de um servidor subordinado do MFA para a função de mestre é sempre um processo manual.
* **Separabilidade de componentes**. O Servidor do MFA do Azure consiste em vários componentes que podem ser instalados na mesma instância do Windows Server ou em instâncias diferentes. Esses componentes incluem o Portal do Usuário, o Serviço Web do Aplicativo Móvel e o adaptador do AD FS (agente). Essa separabilidade possibilita o uso do Proxy de Aplicativo Web para publicar o Portal do Usuário e o Servidor Web do Aplicativo Móvel por meio da rede de perímetro. Uma configuração desse tipo contribui para a segurança geral do design, conforme mostrado no diagrama a seguir. O Portal do Usuário do MFA e o Servidor Web do Aplicativo Móvel também podem ser implantados em configurações com balanceamento de carga de HA.

   ![Servidor do MFA com uma rede de perímetro](./media/mfa-server-high-availability/mfasecurity.png)

* **A senha avulsa (OTP) por SMS (também conhecida como SMS unidirecional) exige o uso de sessões temporárias se o tráfego apresentar balanceamento de carga**. O SMS unidirecional é uma opção de autenticação que faz com que o Servidor do MFA envie aos usuários uma mensagem de texto que contém um OTP. O usuário insere o OTP em uma janela de prompt para concluir o desafio de MFA. Se você balancear a carga dos Servidores do MFA do Azure, o mesmo servidor que atendeu à solicitação de autenticação inicial deverá ser o servidor que recebe a mensagem OTP do usuário; se outro Servidor do MFA receber a resposta OTP, o desafio de autenticação falhará. Para obter mais informações, consulte [Senha avulsa por SMS adicionada ao Servidor do MFA do Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **As implantações com balanceamento de carga do Portal do Usuário e do Serviço Web do Aplicativo Móvel exigem sessões temporárias**. Se você estiver balanceando a carga do Portal do Usuário do MFA e do Serviço Web do Aplicativo Móvel, cada sessão precisará permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implantação de alta disponibilidade

O diagrama a seguir mostra uma implementação completa com balanceamento de carga de HA do MFA do Azure e seus componentes, juntamente com o AD FS para referência.

 ![Implementação de HA do Servidor do MFA do Azure](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Observe os itens a seguir em relação à área numerada correspondente do diagrama anterior.

1. Os dois Servidores do MFA do Azure (MFA1 e MFA2) têm balanceamento de carga (mfaapp.contoso.com) e são configurados para usar uma porta estática (4443) para replicar o banco de dados PhoneFactor.pfdata. O SDK do Serviço Web é instalado em cada Servidor do MFA para habilitar a comunicação pela porta TCP 443 com os servidores do AD FS. Os servidores do MFA são implantados em uma configuração com balanceamento de carga sem monitoração de estado. No entanto, se desejar usar o OTP por SMS, use o balanceamento de carga com estado.
   ![Servidor MFA do Azure – HA do servidor de aplicativos](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Como a RPC usa portas dinâmicas, não é recomendável abrir firewalls até o intervalo de portas dinâmicas que podem ser potencialmente usadas pela RPC. Se você tiver um firewall **entre** os servidores de aplicativos do MFA, deverá configurar o Servidor do MFA para se comunicar em uma porta estática do tráfego de replicação entre os servidores mestre e subordinado e abrir essa porta no firewall. Force a porta estática criando um valor do Registro DWORD em ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` chamado ```Pfsvc_ncan_ip_tcp_port``` e configurando o valor com uma porta estática disponível. As conexões são sempre iniciadas pelos Servidores subordinados do MFA para o mestre e a porta estática é necessária somente no mestre, mas como você pode promover um subordinado para ser o mestre a qualquer momento, você deve definir a porta estática em todos os Servidores do MFA.

2. Os dois servidores do Portal do Usuário/Aplicativo Móvel do MFA (MFA-UP-MAS1 e MFA-UP-MAS2) têm balanceamento de carga em uma configuração **com estado** (mfa.contoso.com). Lembre-se de que as sessões temporárias são um requisito para o balanceamento de carga do Portal do Usuário do MFA e do Serviço de Aplicativo Móvel.
   ![Servidor MFA do Azure – HA do Portal do Usuário e do Serviço de Aplicativo Móvel](./media/mfa-server-high-availability/mfaportal.png)
3. O farm de Servidores do AD FS tem balanceamento de carga e é publicado na Internet por meio de proxies do AD FS com balanceamento de carga na rede de perímetro. Cada Servidor do AD FS usa o agente do AD FS para se comunicar com os Servidores MFA do Azure usando uma única URL com balanceamento de carga (mfaapp.contoso.com) pela porta TCP 443.

## <a name="next-steps"></a>Próximas etapas

* [Instalar e configurar o Servidor MFA do Azure](multi-factor-authentication-get-started-server.md)
