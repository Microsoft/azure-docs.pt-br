---
title: 'Gateway de VPN: solucionar problemas de cliente VPN-autenticação do Azure AD'
description: Solucionar problemas do gateway de VPN P2S clientes de autenticação do Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84988106"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Solucionar problemas de um cliente VPN de autenticação do Azure AD

Este artigo ajuda a solucionar problemas de um cliente VPN para se conectar a uma rede virtual usando VPN ponto a site e autenticação Azure Active Directory.

## <a name="view-status-log"></a><a name="status"></a>Exibir log de status

Exibir o log de status para mensagens de erro.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. Clique no ícone de setas no canto inferior direito da janela do cliente para mostrar os **logs de status**.
2. Verifique os logs em busca de erros que possam indicar o problema.
3. As mensagens de erro são exibidas em vermelho.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Limpar informações de entrada

Limpe as informações de entrada.

![entrar](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecione o... ao lado do perfil que você deseja solucionar problemas. Selecione **Configurar-> limpar conta salva**.
2. Selecione **Salvar**.
3. Tente se conectar.
4. Se a conexão ainda falhar, prossiga para a próxima seção.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Executar diagnóstico

Execute o diagnóstico no cliente VPN.

![diagnóstico](./media/troubleshoot-ad-vpn-client/3.png)

1. Clique em **...** ao lado do perfil no qual você deseja executar o diagnóstico. Selecione **diagnosticar-> executar diagnóstico**.
2. O cliente executará uma série de testes e exibirá o resultado do teste

   * Acesso à Internet – verifica se o cliente tem conectividade com a Internet
   * Credenciais do cliente – Verifique se o ponto de extremidade de autenticação do Azure Active Directory está acessível
   * Servidor resolvível – entra em contato com o servidor DNS para resolver o endereço IP do servidor VPN configurado
   * Servidor alcançável – verifica se o servidor VPN está respondendo ou não
3. Se algum dos testes falhar, contate o administrador da rede para resolver o problema.
4. A próxima seção mostra como coletar os logs, se necessário.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Coletar arquivos de log do cliente

Colete os arquivos de log do cliente VPN. Os arquivos de log podem ser enviados para suporte/administrador por meio de um método de sua escolha. Por exemplo, email.

1. Clique no botão "..." ao lado do perfil no qual você deseja executar o diagnóstico. Selecione **diagnosticar-> mostrar diretório de logs**.

   ![Mostrar logs](./media/troubleshoot-ad-vpn-client/4.png)
2. O Windows Explorer é aberto na pasta que contém os arquivos de log.

   ![Exibir arquivo](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [criar um locatário de Azure Active Directory para conexões VPN abertas do P2S que usam a autenticação do Azure ad](openvpn-azure-ad-tenant.md).