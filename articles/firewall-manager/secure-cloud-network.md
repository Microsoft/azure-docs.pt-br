---
title: 'Tutorial: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure'
description: Neste tutorial, você aprenderá a proteger seu hub virtual com o Gerenciador de Firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: victorh
ms.openlocfilehash: 9d1e2d257074555e7a2e78930e1f9be6cd4d90fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89535995"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutorial: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure

Com o Gerenciador de Firewall do Azure, é possível criar hubs virtuais seguros para proteger o tráfego de rede na nuvem destinado a endereços IP privados, PaaS do Azure e à Internet. O roteamento de tráfego para o firewall é automatizado, portanto, não é necessário criar UDRs (rotas definidas pelo usuário).

![proteger a rede na nuvem](media/secure-cloud-network/secure-cloud-network.png)

O Gerenciador de Firewall também dá suporte à arquitetura de rede virtual de um hub. Para obter uma comparação dos tipos de arquitetura de rede virtual de hub e hub virtual seguro, confira [quais são as opções de arquitetura do Gerenciador de Firewall do Azure?](vhubs-and-vnets.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar a rede virtual spoke
> * Criar um hub virtual seguro
> * Conectar as redes virtuais hub e spoke
> * Rotear o tráfego para o seu hub
> * Implantar os servidores
> * Criar uma política de firewall e proteger seu hub
> * Testar o firewall

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-hub-and-spoke-architecture"></a>Criar uma arquitetura de hub e spoke

Primeiro, crie uma redes virtuais spoke nas quais você pode colocar seus servidores.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Criar duas redes virtuais spoke e sub-redes

As duas redes virtuais terão um servidor de carga de trabalho nelas e serão protegidas pelo firewall.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
2. Em **Assinatura**, selecione sua assinatura.
1. Para **Grupo de recursos**, selecione **Criar** e digite **fw-manager** para o nome e selecione **OK**.
2. Em **Nome**, digite **Spoke-01**.
3. Em **Região**, selecione **EUA (Leste dos EUA)** .
4. Selecione **Avançar: Endereços IP**.
1. Em **Espaço de endereço**, digite **10.1.0.0/16**.
3. Selecione **Adicionar sub-rede**.
4. Digite **Workload-01-SN**.
5. Para **Intervalo de endereços da sub-rede**, digite **10.1.1.0/24**.
6. Selecione **Adicionar**.
1. Selecione **Examinar + criar**.
2. Selecione **Criar**.

Repita esse procedimento para criar outra rede virtual semelhante:

Nome: **Spoke-02**<br>
Espaço de endereço: **10.2.0.0/16**<br>
Nome da sub-rede: **Workload-02-SN**<br>
Intervalo de endereços da sub-rede: **10.2.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Criar o hub virtual seguro

Crie seu hub virtual seguro usando o Gerenciador de Firewall.

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Na caixa de pesquisa, digite **Gerenciador de Firewall** e selecione **Gerenciador de Firewall**.
3. Na página **Gerenciador de Firewall**, selecione **Exibir hubs virtuais protegidos**.
4. Na página **Gerenciador de Firewall | Hubs virtuais seguros**, selecione **Criar hub virtual seguro**.
5. Para **Grupo de recursos**, selecione **fw-manager**.
7. Em **Região**, selecione **Leste dos EUA**.
1. Para o **Nome do hub virtual seguro**, digite **Hub-01**.
2. Em **Espaço de endereço do hub**, digite **10.0.0.0/16**.
3. Para o novo nome de vWAN, digite **Vwan-01**.
4. Deixe a caixa de seleção **Incluir o gateway de VPN para habilitar Parceiros de Segurança Confiáveis** marcada.
5. Selecione **Avançar: Firewall do Azure**.
6. Aceite a configuração padrão **Firewall do Azure** **Habilitado** e, em seguida, selecione **Próximo: Parceiro de Segurança Confiável**.
7. Aceite a configuração padrão **Parceiro de Segurança Confiável** **Desabilitado** e selecione **Próximo: Revisar + criar**.
8. Selecione **Criar**. A implantação levará cerca de 30 minutos.

Agora você pode obter o endereço IP público do firewall.

1. Após a conclusão da implantação, no portal do Azure, selecione **Todos os serviços**.
1. Digite **gerenciador de firewall** e selecione **Gerenciador de Firewall**.
2. Selecione **Hubs virtuais seguros**.
3. Selecione **hub-01**.
7. Selecione **Configuração de IP público**.
8. Anote o endereço IP público para usar depois.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Conectar as redes virtuais hub e spoke

Agora você pode emparelhar as redes virtuais hub e spoke.

1. Selecione o grupo de recursos **fw-manager** e escolha a WAN Virtual **Vwan-01**.
2. Em **Conectividade**, selecione **Conexões de rede virtual**.
3. Selecione **Adicionar conexão**.
4. Em **Nome da conexão**, digite **hub-spoke-01**.
5. Para **Hubs**, selecione **Hub-01**.
6. Para **Grupo de recursos**, selecione **fw-manager**.
7. Para **Rede virtual**, selecione **Spoke-01**.
8. Selecione **Criar**.

Repita para conectar a rede virtual **Spoke-02**: nome da conexão – **hub-spoke-02**

## <a name="deploy-the-servers"></a>Implantar os servidores

1. No portal do Azure, selecione **Criar um recurso**.
2. Selecione **Windows Server 2016 Datacenter** na lista **Popular**.
3. Insira esses valores para a máquina virtual:

   |Configuração  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**fw-manager**|
   |Nome da máquina virtual     |**Srv-workload-01**|
   |Região     |**(EUA) Leste dos EUA**|
   |Nome de usuário administrador     |digite um nome de usuário|
   |Senha     |digite uma senha|

4. Em **Regras de porta de entrada**, **Portas de entrada públicas**, selecione **Nenhuma**.
6. Aceite os outros padrões e selecione **Próximo: Discos**.
7. Aceite os padrões de disco e selecione **Avançar: Rede**.
8. Selecione **Spoke-01** para a rede virtual e selecione **Workload-01-SN** para a sub-rede.
9. Em **IP Público**, selecione **Nenhum**.
11. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.
12. Selecione **Desligar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione **Examinar + criar**.
13. Examine as configurações na página de resumo e, em seguida, selecione **Criar**.

Use as informações na tabela a seguir para definir outra máquina virtual chamada **Srv-Workload-02**. O restante da configuração é o mesmo da máquina virtual **Srv-workload-01**.

|Configuração  |Valor  |
|---------|---------|
|Rede virtual|**Spoke-02**|
|Sub-rede|**Workload-02-SN**|

Depois que os servidores forem implantados, selecione um recurso de servidor e, em **Rede**, anote o endereço IP privado de cada servidor.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Criar uma política de firewall e proteger seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais hubs virtuais seguros. Você criará uma política de firewall e protegerá seu hub.

1. No Gerenciador de Firewall, selecione **Exibir políticas de Firewall do Azure**.
2. Selecione **Criar uma Política de Firewall do Azure**.
3. Em **Detalhes da política**, para **Nome**, digite **Policy-01** e para **Região** selecione **Leste dos EUA**.
4. Selecione **Avançar: Configurações de DNS (versão prévia)** .
1. Selecione **Avançar: Regras**.
2. Na guia **Regras**, selecione **Adicionar a coleção de regras**.
3. Na página **Adicionar a coleção de regras**, digite **App-RC-01** em **Nome**.
4. Para **Tipo de coleção de regras**, selecione **Aplicativo**.
5. Em **Prioridade**, digite **100**.
6. Verifique se a **Ação de coleção de regras** é **Permitir**.
7. Para o **Nome** da regra, digite **Allow-msft**.
8. Para o **Tipo de origem**, selecione **Endereço IP**.
9. Para **Origem**, digite **\*** .
10. Para **Protocolo**, digite **http,https**.
11. Verifique se o **Tipo de destino** é **FQDN**.
12. Para **Destino**, digite **\*.microsoft.com**.
13. Selecione **Adicionar**.

Adicione uma regra DNAT para que você possa conectar uma Área de Trabalho Remota à máquina virtual **Srv-Workload-01**.

1. Selecione **Adicionar uma coleção de regras**.
2. Em **Nome**, digite **DNAT-rdp**.
3. Em **Tipo de coleção de regras**, selecione **DNAT**.
4. Em **Prioridade**, digite **100**.
5. No **Nome** da regra, digite **Allow-rdp**.
6. Para o **Tipo de origem**, selecione **Endereço IP**.
7. Para **Origem**, digite **\*** .
8. Em **Protocolo**, selecione **TCP**.
9. Em **Portas de Destino**, digite **3389**.
10. Para **Tipo de Destino**, selecione **Endereço IP**.
11. Em **Destino**, digite o endereço IP público do firewall que você anotou anteriormente.
12. Para **Endereço convertido**, digite o endereço IP privado de **Srv-Workload-01** que você anotou anteriormente.
13. Para **Porta traduzida**, digite **3389**.
14. Selecione **Adicionar**.

Adicione uma regra de rede para que você possa conectar uma Área de Trabalho Remota de **Srv-Workload-01** para **Srv-Workload-02**.

1. Selecione **Adicionar uma coleção de regras**.
2. Em **Nome**, digite **vnet-rdp**.
3. Para **Tipo de coleção de regras**, selecione **Rede**.
4. Em **Prioridade**, digite **100**.
5. Em **Nome** da regra, digite **Allow-vnet**.
6. Para o **Tipo de origem**, selecione **Endereço IP**.
7. Para **Origem**, digite **\*** .
8. Em **Protocolo**, selecione **TCP**.
9. Em **Portas de Destino**, digite **3389**.
9. Para **Tipo de Destino**, selecione **Endereço IP**.
10. Em **Destino**, digite o endereço IP privado **Srv-Workload-02** que você anotou anteriormente.
11. Selecione **Adicionar**.
1. Selecione **Avançar: Inteligência contra ameaças**.
2. Selecione **Avançar: Hubs**.
3. Na guia **Hubs**, selecione **Associar hubs virtuais**.
4. Selecione **Hub-01** e, em seguida, selecione **Adicionar**.
5. Selecione **Examinar + criar**.
6. Selecione **Criar**.

Isso pode demorar cerca de cinco minutos ou mais para terminar.

## <a name="route-traffic-to-your-hub"></a>Rotear o tráfego para o seu hub

Agora, você deve verificar se o tráfego de rede é roteado por meio do firewall.

1. No Gerenciador de Firewall, selecione **Hubs virtuais seguros**.
2. Selecione **Hub-01**.
3. Em **Configurações**, escolha **Configuração de segurança**.
4. Em **Tráfego da Internet**, selecione **Firewall do Azure**.
5. Em **Tráfego privado**, selecione **Enviar por meio do Firewall do Azure**.
10. Verifique se a conexão **hub-spoke** mostra **Tráfego da Internet** como **Protegido**.
11. Clique em **Salvar**.


## <a name="test-your-firewall"></a>Testar o firewall

Para testar suas regras de firewall, você conectará uma Área de Trabalho Remota usando o endereço IP público do firewall, cujo NAT é realizado para **Srv-Workload-01**. Lá você usará um navegador para testar a regra de aplicativo e conectar uma Área de Trabalho Remota a **Srv-Workload-02** para testar a regra de rede.

### <a name="test-the-application-rule"></a>Testar a regra de aplicativo

Agora, teste as regras de firewall para confirmar se elas funcionam conforme o esperado.

1. Conecte uma Área de Trabalho Remota ao endereço IP público do firewall e entre.

3. Abra o Internet Explorer e navegue até https://www.microsoft.com.
4. Selecione **OK** > **Fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver a página inicial da Microsoft.

5. Navegue até https://www.google.com.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se a regra de aplicativo de firewall está funcionando:

* Você pode navegar para o FQDN permitido, mas não para os outros.

### <a name="test-the-network-rule"></a>Testar a regra de rede

Agora teste a regra de rede.

- Abra uma Área de Trabalho Remota para o endereço IP privado **Srv-Workload-02**.

   Uma Área de Trabalho Remota deve se conectar a **Srv-Workload-02**.

Agora que você verificou se a regra de rede de firewall está funcionando:
* Você pode conectar uma Área de Trabalho Remota a um servidor localizado em outra rede virtual.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de testar seus recursos de firewall, exclua o grupo de recursos **fw-manager** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre parceiros de segurança confiáveis](trusted-security-partners.md)
