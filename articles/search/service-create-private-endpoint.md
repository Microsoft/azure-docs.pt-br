---
title: Criar um ponto de extremidade privado para uma conexão segura
titleSuffix: Azure Cognitive Search
description: Configure um ponto de extremidade privado em uma rede virtual para uma conexão segura com um serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545523"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Criar um ponto de extremidade privado para uma conexão segura com o Azure Pesquisa Cognitiva

Neste artigo, você usará o portal do Azure para criar uma nova instância do serviço de Pesquisa Cognitiva do Azure que não pode ser acessada pela Internet. Em seguida, você configurará uma máquina virtual do Azure na mesma rede virtual e a usará para acessar o serviço de pesquisa por meio de um ponto de extremidade privado.

Pontos de extremidade privados são fornecidos pelo [link privado do Azure](../private-link/private-link-overview.md), como um serviço separado. Para obter mais informações sobre os custos, consulte a [página de preços](https://azure.microsoft.com/pricing/details/private-link/).

Você pode criar um ponto de extremidade privado no portal do Azure, conforme descrito neste artigo. Como alternativa, você pode usar a [API REST de gerenciamento versão 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search)ou [CLI do Azure](/cli/azure/search).

> [!NOTE]
> Quando o ponto de extremidade de serviço é privado, alguns recursos do portal são desabilitados. Você pode exibir e gerenciar informações de nível de serviço, mas as informações de índice, indexador e de qualificações ficam ocultas por motivos de segurança. Como alternativa ao portal, você pode usar a [extensão vs Code](https://aka.ms/vscode-search) para interagir com os vários componentes no serviço.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Por que usar um ponto de extremidade privado para acesso seguro?

[Pontos de extremidade privados](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permitem que um cliente em uma rede virtual acesse dados com segurança em um índice de pesquisa por um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do [espaço de endereço de rede virtual](../virtual-network/private-ip-addresses.md) para o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. Para obter uma lista de outros serviços de PaaS que dão suporte ao link privado, verifique a [seção disponibilidade](../private-link/private-link-overview.md#availability) na documentação do produto.

Pontos de extremidade privados para o serviço de pesquisa permitem que você:

- Bloquear todas as conexões no ponto de extremidade público para o serviço de pesquisa.
- Aumente a segurança para a rede virtual, permitindo que você bloqueie vazamento de dados da rede virtual.
- Conecte-se com segurança ao serviço de pesquisa de redes locais que se conectam à rede virtual usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../expressroute/expressroute-locations.md) com emparelhamento privado.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar a VM que será usada para acessar o ponto de extremidade privado do serviço de pesquisa.

1. Na guia portal do Azure página inicial, selecione **criar um recurso**  >    >  **rede redes virtuais**.

1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecionar sua assinatura|
    | Resource group | Selecione **criar novo**, insira *MyResource* e, em seguida, selecione **OK** |
    | Nome | Insira *MyVirtualNetwork* |
    | Região | Selecione a região desejada |
    |||

1. Deixe os padrões para o restante das configurações. Clique em **revisar + criar** e em **criar**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Criar um serviço de pesquisa com um ponto de extremidade privado

Nesta seção, você criará um novo serviço de Pesquisa Cognitiva do Azure com um ponto de extremidade privado. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **Web**  >  **Azure pesquisa cognitiva**.

1. Em **novos serviço de pesquisa-noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | URL | Insira um nome exclusivo. |
    | Location | Selecione a região desejada. |
    | Tipo de preço | Selecione **alterar tipo de preço** e escolha a camada de serviço desejada. (Sem suporte na camada **gratuita** . Deve ser **básico** ou superior.) |
    |||
  
1. Selecione **Avançar: escala**.

1. Deixe os valores como padrão e selecione **Avançar: rede**.

1. Em **nova serviço de pesquisa-rede**, selecione **privado** para **conectividade de ponto de extremidade (dados)**.

1. Em **novo serviço de pesquisa-rede**, selecione **+ Adicionar** em **ponto de extremidade privado**. 

1. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | Location | Selecione **Oeste dos EUA**.|
    | Nome | Insira *myPrivateEndpoint*.  |
    | Sub-recurso de destino | Deixe o **searchService** padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource* Group. |
    | Sub-rede | Selecione *mySubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO** |  |
    | Integrar com a zona DNS privado  | Mantenha o padrão **Sim**. |
    | Zona DNS privada  | Deixe o padrão * * (New) privatelink.search.windows.net * *. |
    |||

1. Selecione **OK**. 

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 

1. Depois que o provisionamento do novo serviço for concluído, navegue até o recurso que você acabou de criar.

1. Selecione **chaves** no menu conteúdo à esquerda.

1. Copie a **chave de administração primária** para mais tarde, ao conectar-se ao serviço.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **computação**  >  **máquina virtual**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **oeste dos EUA** ou qualquer região que você esteja usando. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixe o padrão **permitir portas selecionadas**. |
    | Selecione as portas de entrada | Deixe o **RDP padrão (3389)**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **Avançar: Discos**.

1. Em **Criar uma máquina virtual – Discos**, mantenha os padrões e selecione **Avançar: Rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o padrão **MyVirtualNetwork**.  |
    | Espaço de endereço | Deixar o padrão **10.1.0.0/24**.|
    | Sub-rede | Deixar o padrão **mySubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

   > [!NOTE]
   > Os endereços IPv4 podem ser expressos no formato [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Lembre-se de evitar o intervalo de IP reservado para rede privada, conforme descrito em [RFC 1918](https://tools.ietf.org/html/rfc1918):
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Baixe e, em seguida, conecte-se à VM *myVm* da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="test-connections"></a>Conexões de teste

Nesta seção, você verificará o acesso à rede privada ao serviço de pesquisa e se conectará de modo privado ao usando o ponto de extremidade privado.

Quando o ponto de extremidade do serviço de pesquisa é privado, alguns recursos do portal são desabilitados. Você poderá exibir e gerenciar as configurações de nível de serviço, mas o acesso ao portal para indexar dados e vários outros componentes no serviço, como o índice, o indexador e as definições de qualificações, é restrito por motivos de segurança.

1. Na Área de Trabalho Remota do *myVM*, abra o PowerShell.

1. Insira ' nslookup [nome do serviço de pesquisa]. Search. Windows. net '

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Na VM, conecte-se ao serviço de pesquisa e crie um índice. Você pode seguir este guia de [início rápido](search-get-started-rest.md) para criar um novo índice de pesquisa em seu serviço usando a API REST. A configuração de solicitações de uma ferramenta de teste da API Web requer o ponto de extremidade do serviço de pesquisa (https://[nome do serviço de pesquisa]. Search. Windows. net) e a chave de API de administração que você copiou em uma etapa anterior.

1. A conclusão do início rápido da VM é sua confirmação de que o serviço está totalmente operacional.

1. Feche a Conexão da Área de Trabalho Remota com *myVM*. 

1. Para verificar se o serviço não está acessível em um ponto de extremidade público, abra o postmaster em sua estação de trabalho local e tente as várias tarefas no início rápido. Se você receber um erro informando que o servidor remoto não existe, você configurou com êxito um ponto de extremidade privado para o serviço de pesquisa.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando você terminar de usar o ponto de extremidade privado, o serviço de pesquisa e a VM, exclua o grupo de recursos e todos os recursos que ele contém:
1. Insira  *MyResource*   The na caixa de **pesquisa** na parte superior do portal e selecione  *MyResource*   Bedos resultados da pesquisa. 
1. Selecione **Excluir grupo de recursos**. 
1. Insira  *MyResource*   Group para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou uma VM em uma rede virtual e um serviço de pesquisa com um ponto de extremidade privado. Você se conectou à VM da Internet e se comunica com segurança ao serviço de pesquisa usando o link privado. Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](../private-link/private-endpoint-overview.md).
