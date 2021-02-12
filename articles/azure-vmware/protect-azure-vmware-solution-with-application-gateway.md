---
title: Use Aplicativo Azure gateway para proteger seus aplicativos Web na solução VMware do Azure
description: Configure Aplicativo Azure gateway para expor com segurança seus aplicativos Web em execução na solução VMware do Azure.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: fdef37bd76b08a8778db8401a1e8a0406c2ed652
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988632"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Use Aplicativo Azure gateway para proteger seus aplicativos Web na solução VMware do Azure

[Aplicativo Azure gateway](https://azure.microsoft.com/services/application-gateway/) é um balanceador de carga de tráfego da Web de camada 7 que permite gerenciar o tráfego para seus aplicativos Web. Ele é oferecido na solução do Azure VMware v 1.0 e v 2.0. Ambas as versões testadas com aplicativos Web em execução na solução VMware do Azure.

As funcionalidade s incluem: 
- Afinidade de sessão baseada em cookie
- Roteamento baseado em URL
- Firewall do aplicativo Web (WAF)

Para obter uma lista completa de recursos, consulte [aplicativo Azure recursos de gateway](../application-gateway/features.md). 

Este artigo mostra como usar o gateway de aplicativo na frente de um farm de servidores Web para proteger um aplicativo Web em execução na solução VMware do Azure. 

## <a name="topology"></a>Topologia
O diagrama mostra como o gateway de aplicativo é usado para proteger VMs (máquinas virtuais) IaaS do Azure, conjuntos de dimensionamento de máquinas virtuais do Azure ou servidores locais. O gateway de aplicativo trata as VMs de solução do Azure VMware como servidores locais. 

![Diagrama mostrando como o gateway de aplicativo protege máquinas virtuais (VMs) IaaS do Azure, conjuntos de dimensionamento de máquinas virtuais do Azure ou servidores locais.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Atualmente, o gateway de Aplicativo Azure é o único método com suporte para expor aplicativos Web em execução em VMs de solução do Azure VMware.

O diagrama mostra o cenário de teste usado para validar o gateway de aplicativo com aplicativos Web da solução VMware do Azure.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagrama que mostra o cenário de teste usado para validar o gateway de aplicativo com aplicativos Web da solução Azure VMware." border="false":::

A instância do gateway de aplicativo é implantada no Hub em uma sub-rede dedicada. Ele tem um endereço IP público do Azure. É recomendável ativar a proteção contra DDoS padrão para a rede virtual. O servidor Web é hospedado em uma nuvem privada da solução Azure VMware por trás dos roteadores do NSX T0 e T1. A solução Azure VMware usa o [ExpressRoute alcance global](../expressroute/expressroute-global-reach.md) para habilitar a comunicação com o Hub e os sistemas locais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa.
- Uma nuvem privada da solução Azure VMware implantada e em execução.

## <a name="deployment-and-configuration"></a>Implantação e configuração

1. No portal do Azure, procure gateway de **aplicativo** e selecione **criar gateway de aplicativo**.

2. Forneça os detalhes básicos como na figura a seguir; em seguida, selecione **Avançar: front-ends>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Captura de tela mostrando a página Criar gateway de aplicativo no portal do Azure.":::

3. Escolha o tipo de endereço IP de front-end. Para público, escolha um endereço IP público existente ou crie um novo. Selecione **Avançar: back-ends>**.

    > [!NOTE]
    > Somente os SKUs Standard e WAF (firewall do aplicativo Web) têm suporte para front-ends privados.

4. Adicione um pool de back-end das VMs que são executadas na infraestrutura da solução VMware do Azure. Forneça os detalhes dos servidores Web que são executados na nuvem privada da solução Azure VMware e selecione **Adicionar**.  Em seguida, selecione **Avançar: configuração>**.

1. Na guia **configuração** , selecione **Adicionar uma regra de roteamento**.

6. Na guia **ouvinte** , forneça os detalhes para o ouvinte. Se a opção HTTPS estiver selecionada, você deverá fornecer um certificado de um arquivo PFX ou de um certificado de Azure Key Vault existente. 

7. Selecione a guia **destinos de back-end** e selecione o pool de back-end criado anteriormente. Para o campo **configurações de http** , selecione **Adicionar novo**.

8. Configure os parâmetros para as configurações de HTTP. Selecione **Adicionar**.

9. Se você quiser configurar regras baseadas em caminho, selecione **adicionar vários destinos para criar uma regra com base no caminho**. 

10. Adicione uma regra com base no caminho e selecione **Adicionar**. Repita para adicionar mais regras com base no caminho. 

11. Quando terminar de adicionar regras com base em caminho, selecione **Adicionar** novamente; em seguida, selecione **Avançar: marcas>**. 

12. Adicione marcas e, em seguida, selecione **Avançar: revisar + criar>**.

13. Uma validação será executada no seu gateway de aplicativo; Se tiver êxito, selecione **criar** para implantar.

## <a name="configuration-examples"></a>Exemplos de configuração

Nesta seção, você aprenderá a configurar o gateway de aplicativo com as VMs de solução do Azure VMware como os pools de back-end para esses casos de uso: 

- [Hospedando vários sites](#hosting-multiple-sites)
- [Roteamento por URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hospedando vários sites

Este procedimento mostra como definir pools de endereços de back-end usando VMs em execução em uma nuvem privada da solução Azure VMware em um gateway de aplicativo existente. 

>[!NOTE]
>Este procedimento pressupõe que você tenha vários domínios, então usaremos exemplos de www.contoso.com e www.fabrikam.com.


1. Em sua nuvem privada, crie dois pools de VMs diferentes. Um representa a Contoso e a segunda fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Captura de tela mostrando o resumo dos detalhes de um servidor Web no cliente VSphere.":::

    Usamos o Windows Server 2016 com a função Serviços de Informações da Internet (IIS) instalada para ilustrar este tutorial. Depois que as VMs forem instaladas, execute os seguintes comandos do PowerShell para configurar o IIS em cada uma das VMs. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Em uma instância existente do gateway de aplicativo, selecione **pools de back-end** no menu à esquerda, selecione  **Adicionar** e insira os detalhes dos novos pools. Selecione **Adicionar** no painel direito.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Captura de tela da página pool de back-end para adicionar pools de back-end" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. Na seção **ouvintes** , crie um novo ouvinte para cada site. Insira os detalhes de cada ouvinte e selecione **Adicionar**.

4. À esquerda, selecione **configurações de http** e selecione **Adicionar** no painel esquerdo. Preencha os detalhes para criar uma nova configuração de HTTP e selecione **salvar**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Captura de tela da página de configurações HTTP para criar uma nova configuração de HTTP." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Crie as regras na seção **regras** do menu à esquerda. Associe cada regra ao ouvinte correspondente. Selecione **Adicionar**.

6. Configure o pool de back-end e as configurações de HTTP correspondentes. Selecione **Adicionar**.

7. Teste a conexão. Abra seu navegador preferido e navegue até os diferentes sites hospedados em seu ambiente de solução do Azure VMware, por exemplo, http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Captura de tela da página do navegador mostrando o teste bem-sucedido da conexão.":::

### <a name="routing-by-url"></a>Roteamento por URL

Este procedimento mostra como definir pools de endereços de back-end usando VMs em execução em uma nuvem privada da solução Azure VMware em um gateway de aplicativo existente. Em seguida, você criará regras de roteamento para certificar-se de que o tráfego da Web chega aos servidores apropriados nos pools.

1. Em sua nuvem privada, crie um pool de máquinas virtuais para representar o web farm. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Captura de tela da página no cliente VMSphere mostrando o resumo de outra VM.":::

    O Windows Server 2016 com a função IIS instalado foi usado para ilustrar este tutorial. Depois que as VMs forem instaladas, execute os seguintes comandos do PowerShell para configurar o IIS para cada tutorial de VM. 

    A primeira máquina virtual, contoso-Web-01, hospedará o site principal.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    A segunda máquina virtual, contoso-Web-02, irá hospedar o site de imagens.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    A terceira máquina virtual, contoso-Web-03, irá hospedar o site de vídeo.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Adicione três novos pools de back-end em uma instância existente do gateway de aplicativo. 

   1. Selecione **Pools de back-end** no menu esquerdo. 
   1. Selecione **Adicionar** e insira os detalhes do primeiro pool, **contoso-Web**. 
   1. Adicione uma VM como o destino. 
   1. Selecione **Adicionar**. 
   1. Repita esse processo para **contoso-images** e **contoso-Video**, adicionando uma VM exclusiva como o destino. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Captura de tela de pools de back-end mostrando a adição de três novos pools de back-end." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. Na seção **ouvintes** , crie um novo ouvinte do tipo básico usando a porta 8080.

4. Na barra de navegação à esquerda, selecione **configurações de http** e selecione **Adicionar** no painel esquerdo. Preencha os detalhes para criar uma nova configuração de HTTP e selecione **salvar**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Captura de tela da página Adicionar configuração de HTTP mostrando a configuração das configurações de HTTP.":::

5. Crie as regras na seção **regras** do menu à esquerda. Associe cada regra ao ouvinte criado anteriormente. Em seguida, configure o pool de back-end principal e as configurações de HTTP. Selecione **Adicionar**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Captura de tela da página Adicionar uma regra de roteamento para configurar as regras de roteamento para um destino de back-end.":::

6. Teste a configuração. Acesse o gateway de aplicativo no portal do Azure e copie o endereço IP público na seção **visão geral** . 

   1. Abra uma nova janela do navegador e insira a URL `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Captura de tela da página do navegador mostrando o teste bem-sucedido da configuração.":::

   1. Altere a URL para `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Captura de tela de outro teste bem-sucedido com a nova URL.":::

   1. Altere a URL novamente para `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Captura de tela de teste bem-sucedido com a URL final.":::

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou o uso do gateway de aplicativo para proteger um aplicativo Web em execução na solução VMware do Azure, talvez queira saber mais sobre:

- [Configuração do gateway de aplicativo Azure para cenários diferentes](../application-gateway/configuration-overview.md).
- [Implantando o Gerenciador de tráfego para balancear as cargas de trabalho da solução Azure VMware](deploy-traffic-manager-balance-workloads.md).
- [Integração do Azure NetApp files com cargas de trabalho baseadas em soluções VMware do Azure](netapp-files-with-azure-vmware-solution.md).
