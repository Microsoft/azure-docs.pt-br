---
title: "Exemplo de DMZ do Azure ‑ Criar um DMZ simples com NSGs | Microsoft Docs"
description: "Criar uma DMZ com grupos de segurança de rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Exemplo 1 – Criar uma DMZ simples usando NSGs com um modelo do Azure Resource Manager
[Voltar à página Práticas recomendadas de limite de segurança][HOME]

> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clássico - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Este exemplo criará uma DMZ simples com quatro servidores Windows e Grupos de Segurança de Rede. Este exemplo descreve cada uma das seções relevantes do modelo para fornecer uma compreensão mais profunda de cada etapa. Também há uma seção Cenário de Tráfego para fornecer um passo a passo detalhado para ver como o tráfego passa pelas camadas de defesa na DMZ. Por fim, na seção de referências, há o código do modelo e as instruções completas para criar este ambiente para testar e experimentar diversos cenários. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![DMZ de entrada com NSG][1]

## <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, uma assinatura contém os seguintes recursos:

* Um único grupo de recursos
* Uma rede virtual com duas sub-redes: “FrontEnd” e “BackEnd”
* Um grupo de segurança de rede que é aplicado a ambas as sub-redes
* Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
* Dois servidores Windows que representam servidores de back-end de aplicativos (“AppVM01”, “AppVM02”)
* Um servidor Windows que representa um servidor DNS ("DNS01")
* Um endereço IP público associado ao servidor Web do aplicativo

Na seção de referências, há um link para um modelo do Azure Resource Manager que cria o ambiente descrito neste exemplo. A criação das VMs e das Redes Virtuais, embora seja realizada pelo modelo de exemplo, não será descrita em detalhes neste documento. 

**Para criar esse ambiente** (as instruções detalhadas estão na seção de referências deste documento);

1. Implantar o Modelo do Azure Resource Manager em: [Modelos de Início Rápido do Azure][Template]
2. Instalar o aplicativo de exemplo em: [Script do Aplicativo de Exemplo][SampleApp]

>[!NOTE]
>Para usar o RDP em quaisquer servidores de back-end nesta instância, o servidor IIS é usado como uma “jump box”. Primeiro use RDP no servidor IIS e, em seguida, do RDP do Servidor IIS para o servidor de back-end. Como alternativa, um IP público pode ser associado a cada servidor de NIC para realizar RDP mais facilmente.
> 
>

As seções a seguir fornecem uma descrição detalhada do Grupo de Segurança de Rede e como ele funciona para este exemplo, abordando as principais linhas do Modelo do Azure Resource Manager.

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com seis regras. 

>[!TIP]
>Em geral, você deve criar suas regras específicas "Permitir" primeiro e então as regras “Negar” mais genéricas por último. A prioridade atribuída ditará quais regras serão avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).
>
>

Declarativamente, as regras a seguir estão sendo criadas para tráfego de entrada:

1. O tráfego interno de DNS (porta 53) é permitido
2. O tráfego de RDP (porta 3389) da Internet para qualquer VM é permitido
3. O tráfego HTTP (porta 80) da Internet ao servidor Web (IIS01) é permitido
4. Todo tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5. Todo o tráfego (todas as portas) da Internet para a Rede Virtual inteira (todas as sub-redes) é Negado
6. Todo tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend é negado

Com essas regras associadas a cada sub-rede, se uma solicitação HTTP tiver entrado da Internet para o servidor Web, ambas as regras 3 (permitir) e 5 (negar) se aplicariam, mas já que a regra 3 tem uma prioridade maior, somente ela se aplicaria e a regra 5 não seria utilizada. Portanto, a solicitação HTTP seria permitida para o servidor Web. Se esse mesmo tráfego estivesse tentando acessar o servidor DNS01, a regra 5 (Negar) seria a primeira a ser aplicada e o tráfego não teria permissão para passar para o servidor. A regra 6 (Negar) impede que a sub-rede Frontend converse com a sub-rede Backend (exceto pelo tráfego permitido nas regras 1 e 4); esse conjunto de regras protege a rede Backend caso um invasor comprometa o aplicativo Web na rede Frontend, pois ele teria acesso limitado à rede Backend “protegida” (somente para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para aplicar a política de segurança ao tráfego em ambos os sentidos, o Roteamento Definido pelo Usuário é necessário e explorado no “Exemplo 3”, na [Página de práticas recomendadas dos limites de segurança][HOME].

Cada regra é discutida com mais detalhes a seguir:

1. Um recurso do Grupo de Segurança de Rede deve ser instanciado para manter as regras:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. A primeira regra neste exemplo habilita o tráfego DNS entre todas as redes internas para o servidor DNS na sub-rede de back-end. A regra tem alguns parâmetros importantes:
  * “destinationAddressPrefix” – Regras podem usar um tipo especial de prefixo de endereço, chamado de “Marcação Padrão”, tais marcas são identificadores fornecidos pelo sistema que proporcionam uma maneira fácil de atender a uma maior categoria de prefixos de endereço. Essa regra usa a Marcação Padrão “Internet” para significar qualquer endereço fora da VNet. VirtualNetwork e AzureLoadBalancer são outros exemplos de rótulos de prefixo.
  * “Direction” (Direção) indica em qual direção do fluxo de tráfego esta regra entra em vigor. Esta é a direção da perspectiva da sub-rede ou da máquina virtual (dependendo da associação desse NSG). Portanto, se a Direção for “Inbound” (Entrada) e o tráfego estiver entrando na sub-rede, a regra se aplicará e o tráfego que sai da sub-rede não será afetado por essa regra.
  * “Priority” (Prioridade) define a ordem na qual um fluxo de tráfego é avaliado. Quanto menor o número, maior a prioridade. Assim que uma regra se aplicar a um fluxo de tráfego específico, nenhuma regra adicional será processada. Portanto, se uma regra com prioridade 1 permite o tráfego e uma regra com prioridade 2 impede o tráfego e ambas as regras se aplicam ao tráfego, ele teria o fluxo permitido (já que a regra 1 tinha uma prioridade mais alta, ela vigorou e nenhuma regra adicional foi aplicada).
  * “Action” (Ação) indica se um tráfego afetado por essa regra é bloqueado (“Deny”, Negar) ou permitido (“Allow”, Permitir).

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Essa regra permite que o tráfego de RDP flua da Internet para a porta RDP em qualquer servidor na sub-rede associada. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Essa regra permite que o tráfego da internet alcance o servidor Web. Essa regra não altera o comportamento de roteamento. A regra permite a passagem somente de tráfego destinado a IIS01. Portanto, se o tráfego da Internet tinha o servidor Web como seu destino, essa regra deve permiti-lo e deve parar de processar outras regras. (Na regra com prioridade 140, todos os outros tráfegos de entrada da Internet estão bloqueados.) Se você estiver processando somente tráfego HTTP, essa regra poderá ser mais restrita para permitir somente a porta de destino 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Essa regra permite que o tráfego passe do servidor IIS01 para o servidor AppVM01; uma regra posterior bloqueia todo o tráfego de Frontend para Backend. Para melhorar essa regra, se a porta for conhecida, ela deve ser adicionada. Por exemplo, se o servidor IIS está atingindo somente o SQL Server no AppVM01, o intervalo de porta de destino deve ser alterado de "*" (qualquer) para 1433 (a porta do SQL), permitindo uma menor superfície de ataque de entrada em AppVM01 se o aplicativo Web for comprometido.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Essa regra nega o tráfego da Internet para todos os servidores na rede. Com a regra em prioridades 110 e 120, o efeito é que ela permite somente tráfego de entrada da Internet para o firewall e portas RDP nos servidores e bloqueia todo o resto. Essa é uma regra “à prova de falhas” para bloquear todos os fluxos inesperados.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. A regra final nega odo o tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend. Como essa é uma regra somente de Entrada, o tráfego invertido é permitido (de Backend para Frontend).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-internet-to-web-server"></a>(*Permitido*) Internet para servidor Web
1. Um usuário de Internet solicita uma página HTTP do endereço IP público da NIC associada à NIC IIS01
2. O endereço IP público passa tráfego para a VNet em direção a IIS01 (o servidor Web)
3. A sub-rede Frontend inicia o processamento da regra de entrada:
  1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
  2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
  3. A Regra NSG 3 (Internet para IIS01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. O tráfego atinge o endereço IP interno do servidor Web IIS01 (10.0.1.5)
5. O IIS01 está escutando o tráfego da Web, recebe essa solicitação e começa a processar a solicitação
6. O IIS01 solicita informações do SQL Server na AppVM01
7. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
8. A sub-rede Backend inicia o processamento da regra de entrada:
  1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
  2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
  3. A Regra NSG 3 (Internet para Firewall) não se aplica; vá para a próxima regra
  4. A regra NSG 4 (IIS01 para AppVM01) não se aplica; o tráfego é permitido, pare o processamento da regra
9. AppVM01 recebe a consulta SQL e responde
10. Como não há nenhuma regra de saída na sub-rede de Back-end, a resposta é permitida
11. A sub-rede Frontend inicia o processamento da regra de entrada:
  1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
  2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido.
12. O servidor IIS recebe a resposta SQL, conclui a resposta HTTP e a envia ao solicitante
13. Como não há nenhuma regra de saída na sub-rede Frontend, a resposta é permitida e o Usuário da Internet recebe a página da Web solicitada.

#### <a name="allowed-rdp-to-iis-server"></a>(*Permitido*) RDP para servidor IIS
1. Um Administrador do Servidor na Internet solicita uma sessão RDP para IIS01 no endereço IP público da NIC associada à NIC IIS01 (esse endereço IP público pode ser encontrado por meio do Portal ou do PowerShell)
2. O endereço IP público passa tráfego para a VNet em direção a IIS01 (o servidor Web)
3. A sub-rede Frontend inicia o processamento da regra de entrada:
  1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
  2. A regra NSG 2 (RDP) não se aplica; o tráfego é permitido, pare o processamento da regra
4. Sem regras de saída, as regras padrão serão aplicadas e o tráfego de retorno será permitido
5. A sessão RDP está habilitada
6. O IIS01 solicita o nome de usuário e senha

>[!NOTE]
>Para usar o RDP em quaisquer servidores de back-end nesta instância, o servidor IIS é usado como uma “jump box”. Primeiro use RDP no servidor IIS e, em seguida, do RDP do Servidor IIS para o servidor de back-end.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitido*) Pesquisa de DNS do servidor Web no servidor DNS
1. O Servidor Web, IIS01, necessita de um feed de dados em www.data.gov, mas precisa resolver o endereço.
2. A configuração de rede para a Rede Virtual lista DNS01 (10.0.2.4 na sub-rede Backend), já que o servidor DNS primário, IIS01, envia a solicitação DNS para DNS01
3. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
4. A sub-rede Backend inicia o processamento da regra de entrada:
  * A regra NSG 1 (DNS) não se aplica; o tráfego é permitido, pare o processamento da regra
5. O servidor DNS recebe a solicitação
6. O servidor DNS não tem o endereço armazenado em cache e consulta um servidor DNS raiz na Internet
7. Nenhuma regra de saída na sub-rede Backend; o tráfego é permitido
8. O servidor DNS da Internet responde e, desde que esta sessão tenha sido iniciada internamente, a resposta será permitida
9. O servidor DNS armazena em cache a resposta e responde à solicitação inicial para IIS01
10. Nenhuma regra de saída na sub-rede Backend; o tráfego é permitido
11. A sub-rede Frontend inicia o processamento da regra de entrada:
  1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
  2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido
12. O IIS01 recebe a resposta do DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitido*) Arquivo de acesso do servidor Web em AppVM01
1. IIS01 solicita um arquivo em AppVM01
2. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
3. A sub-rede Backend inicia o processamento da regra de entrada:
  1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
  2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
  3. A Regra NSG 3 (Internet para IIS01) não se aplica; vá para a próxima regra
  4. A regra NSG 4 (IIS01 para AppVM01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. AppVM01 recebe a solicitação e responde com o arquivo (supondo que o acesso é autorizado)
5. Como não há nenhuma regra de saída na sub-rede de Back-end, a resposta é permitida
6. A sub-rede Frontend inicia o processamento da regra de entrada:
  1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
  2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido.
7. O servidor IIS recebe o arquivo

#### <a name="denied-rdp-to-backend"></a>(*Negado*) RDP para back-end
1. Um usuário da Internet tenta realizar RDP para o servidor AppVM01
2. Como não há nenhum endereço IP público associado a esta NIC dos servidores, esse tráfego nunca entraria na VNet e não alcançaria o servidor
3. Contudo, se um endereço IP público tiver sido habilitado por algum motivo, a regra NSG 2 (RDP) permitiria esse tráfego

>[!NOTE]
>Para usar o RDP em quaisquer servidores de back-end nesta instância, o servidor IIS é usado como uma “jump box”. Primeiro use RDP no servidor IIS e, em seguida, do RDP do Servidor IIS para o servidor de back-end.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web para o servidor de back-end
1. Um usuário da Internet tenta acessar um arquivo no AppVM01
2. Como não há nenhum endereço IP público associado a esta NIC dos servidores, esse tráfego nunca entraria na VNet e não alcançaria o servidor
3. Se um endereço IP público tiver sido habilitado por algum motivo, a regra NSG 5 (Internet para VNet) bloquearia esse tráfego

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Negado*) Pesquisa de DNS na Web no servidor DNS
1. Um usuário da Internet tenta pesquisar um registro DNS interno no DNS01
2. Como não há nenhum endereço IP público associado a esta NIC dos servidores, esse tráfego nunca entraria na VNet e não alcançaria o servidor
3. Se um endereço IP público tiver sido habilitado por algum motivo, a regra NSG 5 (Internet para VNet) bloquearia esse tráfego (Observação: aquela Regra 1 (DNS) não se aplicaria, pois as solicitações de endereço de origem estão na Internet e a Regra 1 se aplica apenas à VNet local como a origem)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Negado*) Acesso a SQL no servidor Web
1. Um usuário da Internet solicita dados SQL do IIS01
2. Como não há nenhum endereço IP público associado a esta NIC dos servidores, esse tráfego nunca entraria na VNet e não alcançaria o servidor
3. Se um endereço IP Público tiver sido habilitado por algum motivo, a sub-rede Frontend iniciará o processamento da regra de entrada:
  1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
  2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
  3. A Regra NSG 3 (Internet para IIS01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. O tráfego atinge o endereço IP interno do IIS01 (10.0.1.5)
5. O IIS01 não está escutando na porta 1433; portanto, não há resposta para a solicitação

## <a name="conclusion"></a>Conclusão
Esse exemplo representa uma maneira relativamente simples e direta de isolar a sub-rede de back-end do tráfego de entrada.

Mais exemplos e uma visão geral dos limites de segurança de rede podem ser encontrados [aqui][HOME].

## <a name="references"></a>Referências
### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Esse exemplo usa um modelo do Azure Resource Manager predefinido em um repositório GitHub mantido pela Microsoft e aberto para a comunidade. Esse modelo pode ser implantado diretamente do GitHub ou baixados e modificados para atender às suas necessidades. 

O modelo principal está no arquivo chamado “azuredeploy.json”. Esse modelo pode ser enviado por meio do PowerShell ou da CLI (com o arquivo associado “azuredeploy.parameters.json”) para implantar esse modelo. Creio que a maneira mais fácil é usar o botão “Implantar no Azure” na página README.md no GitHub.

Para implantar o modelo que cria esse exemplo do GitHub e do Portal do Azure, siga estas etapas:

1. Em um navegador, navegue para o [Modelo][Template]
2. Clique no botão “Implantar no Azure” (ou no botão “Visualizar” para ver uma representação gráfica deste modelo)
3. Digite a Conta de Armazenamento, o Nome de Usuário e a Senha na folha Parâmetros e clique em **OK**
5. Criar um Grupo de Recursos para essa implantação (você pode usar uma existente, mas é recomendável usar uma nova para obter melhores resultados)
6. Se necessário, altere as configurações de Assinatura e Local da sua VNet.
7. Clique em **Ler os termos legais**, leia-os e clique em **Comprar** para concordar.
8. Clique em **Criar** para iniciar a implantação deste modelo.
9. Depois que a implantação for concluída com êxito, navegue até o Grupo de Recursos criado para essa implantação para ver os recursos configurados dentro.

>[!NOTE]
>Este modelo permite realizar RDP somente no servidor IIS01 (encontre o IP público para IIS01 no Portal). Para usar o RDP em quaisquer servidores de back-end nesta instância, o servidor IIS é usado como uma “jump box”. Primeiro use RDP no servidor IIS e, em seguida, do RDP do Servidor IIS para o servidor de back-end.
>
>

Para remover essa implantação, exclua o Grupo de Recursos e todos os recursos filho também serão excluídos.

#### <a name="sample-application-scripts"></a>Scripts de aplicativo de exemplo
Depois que o modelo for executado com êxito, você poderá configurar o servidor Web e o servidor de aplicativos com um aplicativo Web simples para testar a configuração desta DMZ. Se você desejar instalar um aplicativo de exemplo para esse e outros Exemplos de DMZ, um é fornecido no seguinte link: [Script de aplicativo de exemplo][SampleApp]

## <a name="next-steps"></a>Próximas etapas

* Implantar este exemplo
* Compilar o aplicativo de exemplo
* Testar diferentes fluxos de tráfego por meio dessa DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "DMZ de entrada com NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md