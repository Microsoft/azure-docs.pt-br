---
title: "Implantação de alta disponibilidade entre fronteiras geográficas do AD FS no Azure com o Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Neste documento, você aprenderá a implantar o AD FS no Azure para obter alta disponibilidade."
keywords: "AD fs com Gerenciador de Tráfego do Azure, adfs com Gerenciador de Tráfego do Azure, fronteiras geográficas, vários datacenters, datacenters em fronteiras geográficas, datacenters em várias fronteiras geográficas, implantar o AD FS no azure, implantar adfs no azure, adfs do azure, azure ad fs, implantar ad fs, implantar o ad fs, adfs no azure, implantar o ad fsno azure, implantar ad fs no azure, adfs do azure, introdução ao AD FS, Azure, AD FS no Azure, iaas, ADFS, mover o adfs para o azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: 077710049894d2690299ce0fcb0ead9911aa4bb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Implantação do AD FS de alta disponibilidade entre fronteiras geográficas no Azure com o Gerenciador de Tráfego do Azure
[implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) fornece orientação passo a passo sobre como você pode implantar uma infraestrutura do AD FS simples para sua organização no Azure. Este artigo fornece as etapas a seguir para criar uma implantação entre fronteiras geográficas do AD FS no Azure usando o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). O Gerenciador de Tráfego do Azure ajuda a criar uma infraestrutura do AD FS de alto desempenho e alta disponibilidade entre fronteiras geográficas para sua organização, fazendo uso da gama de métodos de roteamento disponíveis para atender às diferentes necessidades da infraestrutura.

Uma infraestrutura altamente disponível do AD FS entre fronteiras geográficas habilita o seguinte:

* **Eliminação de pontos únicos de falha:** com recursos de failover do Gerenciador de Tráfego do Azure, você pode obter uma infraestrutura altamente disponível do AD FS mesmo quando um dos datacenters em alguma parte do mundo fica inativo
* **Desempenho aprimorado:** você pode usar a implantação sugerida neste artigo para fornecer uma infraestrutura do AD FS de alto desempenho que pode ajudar os usuários a realizar a autenticação mais rapidamente. 

## <a name="design-principles"></a>Princípios de design
![Design geral](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Os princípios básicos de design serão os mesmos, conforme listado nos princípios de Design no artigo de implantação do AD FS no Azure. O diagrama acima mostra uma extensão simples da implantação básica para outra região geográfica. Abaixo estão alguns pontos a considerar ao estender a implantação para a nova região geográfica

* **Rede virtual:** você deve criar uma nova rede virtual na região geográfica em que deseja implantar a infraestrutura do AD FS adicional. No diagrama acima, você vê Geo1 VNET e Geo2 VNET como as duas redes virtuais em cada região geográfica.
* **Controladores de domínio e servidores do AD FS na nova rede virtual geográfica:** é recomendável implantar controladores de domínio na nova região geográfica para que os servidores do AD FS na nova região não precisem contatar um controlador de domínio em outra rede longe para concluir uma autenticação e, assim, melhorar o desempenho.
* **Contas de armazenamento:** as contas de armazenamento estão associadas a uma região. Como implantará computadores em uma nova região geográfica, você precisará criar novas contas de armazenamento a serem usadas na região.  
* **Grupos de Segurança de Rede:** assim como contas de armazenamento, Grupos de Segurança de Rede criados em uma região não podem ser usados em outra região geográfica. Portanto, você precisará criar novos grupos de segurança de rede semelhantes aos da primeira região geográfica para as sub-redes INT e DMZ na nova região geográfica.
* **Rótulos de DNS para endereços IP públicos:** o Gerenciador de Tráfego do Azure pode referir-se aos pontos de extremidade somente por meio de rótulos DNS. Portanto, você precisa criar rótulos de DNS para os endereços IP públicos dos balanceadores de carga externos.
* **Gerenciador de Tráfego do Azure:** o Gerenciador de Tráfego do Microsoft Azure permite que você controle a distribuição do tráfego do usuário para seus pontos de extremidade do serviço em execução em diferentes data centers em todo o mundo. O Gerenciador de Tráfego do Azure funciona no nível de DNS. Ele usa respostas de DNS para direcionar o tráfego do usuário final para pontos de extremidade globalmente distribuídos. Assim, os clientes se conectam a esses pontos de extremidade diretamente. Com diferentes opções de roteamento (Desempenho, Ponderado e Prioridade), você poderá facilmente escolher a opção de roteamento mais adequada às necessidades de sua organização. 
* **Conectividade de V-net para V-net entre duas regiões:** não é necessário ter conectividade entre as redes virtuais em si. Já que cada rede virtual tem acesso aos controladores de domínio e tem o servidor do AD FS e WAP em si, elas podem funcionar sem nenhuma conectividade entre as redes virtuais em diferentes regiões. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Etapas para integrar o Gerenciador de Tráfego do Azure
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Implantar o AD FS na nova região geográfica
Siga as etapas e diretrizes em [Implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) para implantar a mesma topologia na nova região geográfica.

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Rótulos de DNS para endereços IP públicos dos balanceadores de carga (públicos) para a Internet
Conforme mencionado acima, o Gerenciador de Tráfego do Azure só pode se referir aos rótulos DNS como pontos de extremidade. Portanto, é importante criar rótulos de DNS para os endereços IP públicos dos balanceadores de carga externos. A captura de tela abaixo mostra como você pode definir o rótulo de DNS para o endereço IP público. 

![Rótulo de DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Implantação do Gerenciador de Tráfego do Azure
Siga as etapas abaixo para criar um perfil do gerenciador de tráfego. Para obter mais informações, você também pode conferir [Gerenciar um perfil do Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Criar um perfil do Gerenciador de Tráfego:** dê um nome exclusivo a seu perfil do gerenciador de tráfego. Esse nome do perfil é parte do nome DNS e atua como um prefixo para o rótulo de nome de domínio do Gerenciador de Tráfego. O nome/prefixo é adicionado a .trafficmanager.net para criar um rótulo de DNS para o Gerenciador de Tráfego. A captura de tela abaixo mostra o prefixo de DNS do gerenciador de tráfego que está sendo definido como mysts. O rótulo de DNS resultante será mysts.trafficmanager.net. 
   
    ![Criação de perfil do Gerenciador de Tráfego](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Método de roteamento de tráfego:** há três opções de roteamento disponíveis no gerenciador de tráfego:
   
   * Prioridade 
   * Desempenho
   * Ponderado
     
     **Desempenho** é a opção recomendada para obter uma infraestrutura do AD FS altamente responsiva. No entanto, você pode escolher qualquer método de roteamento mais adequado para suas necessidades de implantação. A funcionalidade do AD FS não é afetada pela opção de roteamento selecionada. Veja [Métodos de roteamento de tráfego do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md) para obter mais informações. Na captura de tela de exemplo acima, você pode ver o método **Desempenho** selecionado.
3. **Configurar pontos de extremidade:** na página do gerenciador de tráfego, clique em pontos de extremidade e selecione Adicionar. Isso abrirá uma página Adicionar ponto de extremidade semelhante à captura de tela abaixo
   
   ![Configurar pontos de extremidade](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   Para as diferentes entradas, siga a diretriz abaixo:
   
   **Tipo:** selecione ponto de extremidade do Azure, pois apontaremos para um endereço IP público do Azure.
   
   **Nome:** crie um nome que você queira associar ao ponto de extremidade. Esse não é o nome DNS e não afeta os registros de DNS.
   
   **Tipo de recurso de destino:** selecione Endereço IP público como o valor para essa propriedade. 
   
   **Recurso de destino:** isso lhe dará a opção de escolher dentre os diferentes rótulos de DNS disponíveis em sua assinatura. Escolha o rótulo DNS correspondente ao ponto de extremidade que você está configurando.
   
   Adicione o ponto de extremidade para cada região geográfica para a qual você deseja que o Gerenciador de Tráfego do Azure encaminhe o tráfego.
   Para obter mais informações e etapas detalhadas sobre como adicionar/configurar pontos de extremidade no gerenciador de tráfego, veja [Adicionar, desabilitar, habilitar ou excluir pontos de extremidade](../traffic-manager/traffic-manager-endpoints.md)
4. **Configurar investigação:** na página do gerenciador de tráfego, clique em Configuração. Na página de configuração, você precisa alterar as configurações de monitor para investigação em HTTP na porta 80 e caminho relativo /adfs/probe
   
    ![Configurar investigação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Verifique se o status dos pontos de extremidade é ONLINE quando a configuração for concluída**. Se todos os pontos de extremidade estiverem no estado 'degradado', o Gerenciador de Tráfego do Azure tentará encaminhar o tráfego, supondo que o diagnóstico esteja incorreto e todos os pontos de extremidade estejam acessíveis.
   > 
   > 
5. **Modificar registros de DNS para o Gerenciador de Tráfego do Azure:** seu serviço de federação deve ser um CNAME para o nome DNS do Gerenciador de Tráfego do Azure. Crie um CNAME nos registros de DNS público para que quem quer que tente acessar o serviço de federação acesse o Gerenciador de Tráfego do Azure.
   
    Por exemplo, para apontar o fs.fabidentity.com do serviço de federação para o Gerenciador de Tráfego, você precisaria atualizar o registro de recurso de DNS para o seguinte:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Testar o roteamento e a entrada no AD FS
### <a name="routing-test"></a>Teste de roteamento
Um teste muito básico para o roteamento seria tentar executar o ping do nome do DNS do serviço de federação de um computador em cada região geográfica. Dependendo do método de roteamento escolhido, o ponto de extremidade que realmente executa o ping será refletido na exibição do ping. Por exemplo, se você tiver selecionado o roteamento de desempenho, o ponto de extremidade mais próximo à região do cliente será acessado. Abaixo está o instantâneo de dois pings de dois computadores de clientes de regiões diferentes, um na região Leste da Ásia e outra no Oeste dos EUA. 

![Teste de roteamento](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>Teste de entrada no AD FS
A maneira mais fácil de testar o AD FS é usar a página IdpInitiatedSignon.aspx. Para fazer isso, é necessário habilitar IdpInitiatedSignOn nas propriedades do AD FS. Siga as etapas abaixo para verificar a instalação do AD FS

1. Execute o cmdlet abaixo no servidor do AD FS, usando o PowerShell, para defini-lo como habilitado. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. De qualquer computador externo, acesse https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Você deve ver a página do AD FS como indicado abaixo:
   
    ![Teste de ADFS - desafio de autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    e quando você entrar com êxito, ele lhe fornecerá uma mensagem de êxito, conforme mostrado abaixo:
   
    ![Teste de ADFS - êxito da autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Links relacionados
* [Implantação básica do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Métodos de roteamento de tráfego do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar um perfil de Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Adicionar, desabilitar, habilitar ou excluir pontos de extremidade](../traffic-manager/traffic-manager-endpoints.md) 

