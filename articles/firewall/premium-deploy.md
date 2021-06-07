---
title: Implantar e configurar a versão prévia do Firewall do Azure Premium
description: Saiba como implantar e configurar o firewall Premium do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721779"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Implantar e configurar a versão prévia do Firewall do Azure Premium

> [!IMPORTANT]
> O firewall Premium do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 O Firewall do Azure Premium Versão Prévia é um firewall de última geração com funcionalidades necessárias para ambientes altamente sensíveis e regulamentados. Ele contém os seguintes recursos:

- **Inspeção de TLS** – descriptografa o tráfego de saída, processa os dados e, em seguida, criptografa os dados e os envia para o destino.
- **IDPS** -um sistema de detecção e prevenção de intrusão na rede (IDPS) permite que você monitore atividades de rede para atividades mal-intencionadas, registre informações sobre essa atividade, relate-o e, opcionalmente, tente bloqueá-lo.
- **Filtragem de URL** – estende a capacidade de filtragem de FQDN do firewall do Azure para considerar uma URL inteira. Por exemplo, `www.contoso.com/a/c` em vez de `www.contoso.com` .
- **Categorias da Web** -os administradores podem permitir ou negar o acesso do usuário a categorias de sites, como sites de jogos de azar, sites de mídia social e outros.

Para obter mais informações, consulte [recursos do firewall do Azure Premium](premium-features.md).

Você usará um modelo para implantar um ambiente de teste que tenha uma VNet central (10.0.0.0/16) com três sub-redes:
- uma sub-rede de trabalho (10.0.10.0/24)
- uma sub-rede de bastiões do Azure (10.0.20.0/24)
- uma sub-rede de firewall (10.0.100.0/24)

Uma única VNet central é usada nesse ambiente de teste para simplificar. Para fins de produção, uma [topologia de Hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) com VNets emparelhadas é mais comum.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Topologia da VNet central":::

A máquina virtual de trabalho é um cliente que envia solicitações HTTP/S por meio do firewall.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="deploy-the-infrastructure"></a>Implantar a infraestrutura

O modelo implanta um ambiente de teste completo para o firewall Premium do Azure habilitado com IDPS, inspeção de TLS, filtragem de URL e categorias da Web:

- uma nova política de firewall e Premium do firewall do Azure com configurações predefinidas para permitir a validação fácil de seus principais recursos (IDPS, inspeção de TLS, filtragem de URL e categorias da Web)
- implanta todas as dependências, incluindo Key Vault e uma identidade gerenciada. Em um ambiente de produção, esses recursos já podem ter sido criados e não são necessários no mesmo modelo.
- gera uma AC raiz autoassinada e a implanta no Key Vault gerado
-  gera uma AC intermediária derivada e a implanta em uma máquina virtual de teste do Windows (WorkerVM)
- um host de bastiões (BastionHost) também é implantado e pode ser usado para se conectar ao computador de teste do Windows (WorkerVM)



[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Testar o firewall

Agora você pode testar IDPS, inspeção de TLS, filtragem da Web e categorias da Web.

### <a name="add-firewall-diagnostics-settings"></a>Adicionar configurações de diagnóstico de firewall

Para coletar logs de firewall, você precisa adicionar configurações de diagnóstico para coletar logs de firewall.

1. Selecione o **DemoFirewall** e, em **monitoramento**, selecione **configurações de diagnóstico**.
2. Selecione **Adicionar configuração de diagnóstico**.
3. Para **nome da configuração de diagnóstico**, digite *FW-diag*.
4. Em **log**, selecione **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**.
5. Em **detalhes de destino**, selecione **Enviar para log Analytics espaço de trabalho**.
6. Clique em **Salvar**.

### <a name="idps-tests"></a>Testes de IDPS

Para testar o IDPS, você precisará implantar seu próprio servidor Web interno com um certificado de servidor apropriado. Para obter mais informações sobre os requisitos de certificado do Azure firewall Premium Preview, consulte [certificados do Azure firewall Premium Preview](premium-certificates.md).

Você pode usar o `curl` para controlar vários cabeçalhos HTTP e simular o tráfego mal-intencionado.

#### <a name="to-test-idps-for-http-traffic"></a>Para testar o IDPS para o tráfego HTTP:

1. Na máquina virtual WorkerVM, abra uma janela de prompt de comando de administrador.
2. Digite o seguinte comando no prompt de comando:

   `curl -A "BlackSun" <your web server address>`
3. Você verá a resposta do servidor Web.
4. Vá para os logs de regra de rede de firewall no portal do Azure para encontrar um alerta semelhante à seguinte mensagem:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Mensagem de alerta":::

   > [!NOTE]
   > Pode levar algum tempo para que os dados comecem a aparecer nos logs. Forneça pelo menos 20 minutos para permitir que os logs comecem a mostrar os dados.
5. Adicione uma regra de assinatura para a assinatura 2008983:

   1. Selecione o **DemoFirewallPolicy** e em **configurações** , selecione **IDPS (versão prévia)**.
   1. Selecione a guia **regras de assinatura** .
   1. Em **ID da assinatura**, na caixa de texto abrir, digite *2008983*.
   1. Em **modo**, selecione **negar**.
   1. Clique em **Salvar**.
   1. Aguarde a conclusão da implantação antes de continuar.



6. Em WorkerVM, execute o `curl` comando novamente:

   `curl -A "BlackSun" <your web server address>`

   Como a solicitação HTTP agora está bloqueada pelo firewall, você verá a seguinte saída depois que o tempo limite de conexão expirar:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Vá para o monitor logs no portal do Azure e localize a mensagem para a solicitação bloqueada.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Para testar o IDPS para o tráfego HTTPS

Repita esses testes de ondulação usando HTTPS em vez de HTTP. Por exemplo:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Você deve ver os mesmos resultados que tinha com os testes de HTTP.

### <a name="tls-inspection-with-url-filtering"></a>Inspeção de TLS com filtragem de URL

Use as etapas a seguir para testar a inspeção de TLS com filtragem de URL.

1. Edite as regras de aplicativo de política de firewall e adicione uma nova regra chamada `AllowURL` à `AllowWeb` coleção de regras. Configure a URL de destino `www.nytimes.com/section/world` , endereço IP de origem **\* *_, tipo de destino _* URL (versão prévia)**, selecione **inspeção TLS (versão prévia)** e protocolos **http, https**.

3. Quando a implantação for concluída, abra um navegador no WorkerVM e vá para `https://www.nytimes.com/section/world` e valide se a resposta HTML é exibida conforme o esperado no navegador.
4. No portal do Azure, você pode exibir a URL inteira nos logs de monitoramento da regra de aplicativo:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Mensagem de alerta mostrando a URL":::

Algumas páginas HTML podem parecer incompletas porque se referem a outras URLs que são negadas. Para resolver esse problema, a seguinte abordagem pode ser tomada:

- Se a página HTML contiver links para outros domínios, você poderá adicionar esses domínios a uma nova regra de aplicativo com permitir acesso a esses FQDNs.
- Se a página HTML contiver links para sub URLs, você poderá modificar a regra e adicionar um asterisco à URL. Por exemplo: `targetURLs=www.nytimes.com/section/world*`

   Como alternativa, você pode adicionar uma nova URL à regra. Por exemplo: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Testes de categorias da Web

Vamos criar uma regra de aplicativo para permitir o acesso aos sites de esportes.
1. No portal, abra o grupo de recursos e selecione **DemoFirewallPolicy**.
2. Selecione **regras de aplicativo** e, em seguida, **adicione uma coleção de regras**.
3. Para **nome**, digite *GeneralWeb*, **prioridade** *103*, **grupo de coleção de regras** selecione **DefaultApplicationRuleCollectionGroup**.
4. Em **regras** para o tipo de **nome** *AllowSports*, **origem** *\** , **protocolo** *http, https*, selecione **inspeção TLS**, **tipo de destino** selecionar *categorias da Web (versão prévia)*, **destino** selecionar *esportes*.
5. Selecione **Adicionar**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Categoria da Web de esportes":::
6. Quando a implantação for concluída, vá para  **WorkerVM** e abra um navegador da Web e navegue até `https://www.nfl.com` .

   Você deve ver a página da Web do NFL e o log de regras do aplicativo mostra que uma **categoria da Web:** regra de esportes foi correspondida e a solicitação foi permitida.

## <a name="next-steps"></a>Próximas etapas

- [Azure firewall Premium Preview no portal do Azure](premium-portal.md)