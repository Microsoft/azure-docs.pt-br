---
title: 'Conectar um computador a uma rede virtual usando P2S: autenticação de certificado: portal do Azure clássico'
titleSuffix: Azure VPN Gateway
description: Crie uma conexão de gateway de VPN ponto a site clássica usando o portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94657067"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configurar uma conexão ponto a site usando a autenticação de certificado (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra como criar uma VNet com uma conexão ponto a site. Você cria essa VNet com o modelo de implantação clássico usando o portal do Azure. Essa configuração usa certificados para autenticar o cliente de conexão, autoassinado ou emitido por uma Autoridade de Certificação. Você também pode criar essa configuração com um modelo ou ferramenta de implantação diferente usando as opções descritas nos artigos a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Use um gateway VPN P2S (conexão ponto a site) para criar uma conexão segura para sua rede virtual com base em um computador cliente individual. As conexões VPN ponto a site são úteis quando você deseja se conectar à rede virtual de uma localização remota. Quando você tiver apenas alguns clientes que precisam se conectar a uma VNet, uma VPN P2S é uma solução útil para ser usada em vez da VPN site a site. Uma conexão VPN P2S é estabelecida iniciando-a do computador cliente.

> [!IMPORTANT]
> O modelo de implantação clássico dá suporte somente a clientes VPN do Windows e usa o protocolo SSTP (Secure Socket Tunneling), um protocolo de VPN baseada em SSL. Para a compatibilidade com clientes VPN não Windows, você deve criar sua VNet com o modelo de implantação do Resource Manager. O modelo de implantação do Resource Manager oferece suporte à VPN IKEv2, além de SSTP. Para saber mais, veja [Sobre conexões P2S](point-to-site-about.md).
>
>

![Diagrama ponto a site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Configurações e requisitos

### <a name="requirements"></a>Requisitos

As conexões de autenticação de certificado ponto a site exigem os seguintes itens. Há etapas neste artigo que o ajudarão a criá-las.

* Um gateway de VPN dinâmico.
* A chave pública (arquivo .cer) para um certificado raiz, que é carregado no Azure. Essa chave é considerada um certificado confiável e é usada para autenticação.
* O certificado de cliente é gerado a partir do certificado raiz e instalado em cada computador cliente que irá se conectar. Esse certificado é usado para autenticação do cliente.
* Um pacote de configuração de cliente VPN deve ser gerado e instalado em cada computador cliente que se conecta. O pacote de configuração do cliente configura o cliente VPN nativo que já está no sistema operacional com as informações necessárias para se conectar à VNet.

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público local. A conexão VPN é criada no SSTP (Secure Socket Tunneling Protocol). No lado do servidor, há suporte para as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide qual versão usar. Por padrão, para Windows 8.1 e posterior, o SSTP usa 1.2.

Para obter mais informações, consulte [sobre conexões ponto a site](point-to-site-about.md) e as [perguntas frequentes](#faq).

### <a name="example-settings"></a>Configurações de exemplo

Use os seguintes valores para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo:

* **Grupo de recursos:** TestRG
* **Nome da VNet:** VNet1
* **Espaço de endereço:** 192.168.0.0/16 <br>Neste exemplo, usamos apenas um espaço de endereço. Você pode ter mais de um espaço de endereço para sua rede virtual.
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Região:** (EUA) Leste dos EUA
* **Espaço de endereço do cliente:** 172.16.201.0/24 <br> Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool especificado.
* **Tipo de conexão**: selecione **ponto a site**.
* **Intervalo de endereços GatewaySubnet (bloco CIDR):** 192.168.200.0/24

Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

Se você já tiver uma rede virtual, verifique se as configurações são compatíveis com seu design de gateway de VPN. Preste atenção especial em todas as sub-redes que possam se sobrepor a outras redes.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Criar um gateway de VPN

1. Navegue até a VNet que você criou.
1. Na página VNet, em configurações, selecione **Gateway**. Na página **Gateway** , você pode exibir o gateway para sua rede virtual. Essa rede virtual ainda não tem um gateway. Clique na observação que diz **clique aqui para adicionar uma conexão e um gateway**.
1. Na página **Configurar uma conexão VPN e um gateway** , selecione as seguintes configurações:

   * Tipo de conexão: ponto a site
   * Espaço de endereço de cliente: Adicione o intervalo de endereços IP do qual os clientes VPN recebem um endereço IP ao se conectar. Use um intervalo de endereços IP privado que não coincida com a localização local da qual você se conecta ou com a rede virtual à qual você se conecta.
1. Deixe a caixa de seleção para não **configurar um gateway no momento** desmarcada. Criaremos um gateway.
1. Na parte inferior da página, selecione **Avançar: Gateway >**.
1. Na guia **Gateway** , selecione os seguintes valores:

   * **Tamanho:** O tamanho é o SKU de gateway para seu gateway de rede virtual. No portal do Azure, a SKU padrão é **Padrão**. Para obter mais informações sobre as SKUs de gateway, consulte [sobre as configurações de gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Tipo de roteamento:** Você deve selecionar **dinâmico** para uma configuração ponto a site. O roteamento estático não funcionará.
   * **Sub-rede de gateway:** Este campo já está preenchido com preenchimento automática. Você não pode alterar o nome. Se você tentar alterar o nome usando o PowerShell ou qualquer outro meio, o gateway não funcionará corretamente.
   * **Intervalo de endereços (bloco CIDR):** Embora seja possível criar uma sub-rede de gateway tão pequena quanto/29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos/28 ou/27. Fazer isso permitirá endereços suficientes a fim de acomodar as possíveis configurações adicionais que você possa querer no futuro. Ao trabalhar com sub-redes de gateway, evite a associação de um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN não funcione conforme o esperado.
1. Selecione **Examinar + criar** para validar suas configurações.
1. Depois que a validação for aprovada, selecione **criar**. Um gateway de VPN pode levar até 45 minutos para ser concluído, dependendo do SKU de gateway que você selecionar.

## <a name="create-certificates"></a><a name="generatecerts"></a>Criar certificados

O Azure usa certificados para autenticar clientes VPN para VPNs ponto a site. Você pode carregar as informações da chave públicas do certificado raiz no Azure. A chave pública é considerada *confiável*. Os certificados de cliente devem ser gerados do certificado raiz confiável e, em seguida, em cada computador cliente no repositório de certificados Certificates-Current User\Personal\Certificates. O certificado é usado para autenticar o cliente quando ele se conecta à VNet. 

Se você usa certificados autoassinados, eles devem ser criados usando parâmetros específicos. Você pode criar um certificado autoassinado usando as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que você siga as etapas nestas instruções ao usar os certificados raiz autoassinados e gerar certificados de cliente do certificado raiz autoassinado. Caso contrário, os certificados criados não serão compatíveis com conexões P2S e você receberá um erro de conexão.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Adquirir a chave pública (.cer) do certificado raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Gerar um certificado do cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Carregar o arquivo .cer do certificado raiz

Após o gateway ser criado, você pode carregar o arquivo. cer (que contém as informações de chave pública) para um certificado raiz confiável do Azure. Não carregue a chave privada do certificado raiz. Depois de carregar o certificado, o Azure o utiliza para autenticar clientes com um certificado de cliente instalado gerado de um certificado raiz confiável. Você pode carregar arquivos de certificado raiz confiável adicionais posteriormente (até um total de 20) se necessário.

1. Navegue até a rede virtual que você criou.
1. Em **configurações**, selecione **conexões ponto a site**.
1. Selecione **gerenciar certificado**.
1. Escolha **Carregar**.
1. No painel **carregar um certificado** , selecione o ícone de pasta e navegue até o certificado que você deseja carregar.
1. Escolha **Carregar**.
1. Depois que o certificado for carregado com êxito, você poderá exibi-lo na página Gerenciar certificado. Talvez seja necessário selecionar **Atualizar** para exibir o certificado que você acabou de carregar.

## <a name="configure-the-client"></a>Configurar o cliente

Para se conectar a uma rede virtual usando uma VPN ponto a site, cada cliente deve instalar um pacote para configurar o cliente VPN do Windows nativo. O pacote de configuração configura o cliente VPN do Windows nativo com as configurações necessárias para se conectar à rede virtual.

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais cliente com suporte, consulte [sobre conexões ponto a site](point-to-site-about.md) e as [perguntas frequentes](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Gerar e instalar um pacote de configuração de cliente VPN

1. Navegue até as configurações de **conexões ponto a site** para sua VNet.
1. Na parte superior da página, selecione o pacote de download que corresponde ao sistema operacional do cliente em que ele será instalado:

   * Para clientes de 64 bits, selecione **cliente VPN (64 bits)**.
   * Para clientes de 32 bits, selecione **cliente VPN (32 bits)**.

1. O Azure gera um pacote com as configurações específicas que o cliente requer. Sempre que fizer alterações na VNet ou no gateway, você precisará baixar um novo pacote de configuração do cliente e instalá-los nos computadores cliente.
1. Depois que o pacote for gerado, selecione **baixar**.
1. Instale o pacote de configuração do cliente no computador cliente. Ao instalar o, se você vir um pop-up do SmartScreen dizendo ao Windows protegido seu PC, selecione **mais informações** e, em seguida, selecione **executar mesmo assim**. Você também pode salvar o pacote de instalação em outros computadores clientes.

### <a name="install-a-client-certificate"></a>Instalar um certificado de cliente

Para este exercício, quando você gerou o certificado do cliente, ele foi instalado automaticamente no computador. Para criar uma conexão P2S de um computador cliente diferente daquele usado para gerar os certificados de cliente, você deve instalar o certificado de cliente gerado nesse computador.

Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente. Normalmente, você pode instalar o certificado simplesmente clicando duas vezes nele. Para obter mais informações, consulte [instalar um certificado de cliente exportado](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect-to-your-vnet"></a>Conectar-se à sua VNet

>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do qual você está se conectando.
>

1. No computador cliente, vá para configurações de VPN.
1. Selecione a VPN que você criou. Se você usou as configurações de exemplo, a conexão será rotulada como **grupo TestRG VNet1**.
1. Selecione **Conectar**.
1. Na caixa rede virtual do Windows Azure, selecione **conectar**. Se uma mensagem pop-up sobre o certificado for exibida, selecione **continuar** a usar privilégios elevados e **Sim** para aceitar alterações de configuração.
1. Quando a conexão for concluída com sucesso, você verá uma notificação **conectada** .

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificar a conexão VPN

1. Verifique se a conexão VPN está ativa. Abra um prompt de comandos com privilégios elevados no computador cliente e execute **ipconfig/all**.
1. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do intervalo de endereços de conectividade ponto a site que você especificou quando criou a sua VNet. Os resultados devem ser semelhantes a este exemplo:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Para conectar-se a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Para adicionar ou remover certificados raiz confiáveis

Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado raiz, os clientes que têm um certificado gerado dessa raiz não poderão mais fazer a autenticação e se conectar. Para esses clientes fazerem a autenticação e se conectem novamente, você deve instalar um novo certificado do cliente gerado de um certificado confiável pelo Azure.

### <a name="add-a-trusted-root-certificate"></a>Adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos. cer de certificado raiz confiável no Azure usando o mesmo processo que você usou para adicionar o primeiro certificado raiz confiável.

### <a name="remove-a-trusted-root-certificate"></a>Remover um certificado raiz confiável

1. Na seção **conexões ponto a site** da página de sua VNet, selecione **gerenciar certificado**.
1. Selecione as reticências ao lado do certificado que você deseja remover e, em seguida, selecione **excluir**.

## <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Se necessário, você pode revogar um certificado de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Este método é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticar a conexão ponto a site.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

Você pode revogar um certificado de cliente adicionando a impressão digital à lista de revogação.

1. Recupere a impressão digital do certificado de cliente. Para saber mais, confira [Como recuperar a impressão digital de um certificado](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Copie as informações para um editor de texto e remova os espaços para que seja uma cadeia de caracteres contínua.
1. Navegue até **conexão VPN ponto a site** e selecione **gerenciar certificado**.
1. Selecione **Lista de revogação** para abrir a página **Lista de revogação**.
1. Na **Impressão digital**, cole a impressão digital do certificado como uma linha contínua de texto, sem espaços.
1. Selecione **+ Adicionar à lista** para adicionar a impressão digital à CRL (lista de certificados revogados).

Após a conclusão da atualização, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado recebem uma mensagem informando que o certificado não é mais válido.

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Após sua conexão ser concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](../index.yml).

* Para saber mais sobre redes e máquinas virtuais do Linux, confira [Visão geral de rede de VMs do Linux e do Azure](../virtual-machines/network-overview.md).

* Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).