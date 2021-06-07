---
title: 'Tutorial: Hospedar seu domínio e subdomínio – DNS do Azure'
description: Neste tutorial, você aprenderá a configurar o DNS do Azure para hospedar suas Zonas DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: a8f64ab3141459142def12a1758b0fe0a94ca432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282169"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Tutorial: Hospede seu domínio no DNS do Azure

Você pode usar o DNS do Azure para hospedar seu domínio DNS e gerenciar seus registros DNS. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure.

Suponha que você compre o domínio 'contoso.net' de um registrador de nome de domínio e, em seguida, crie uma zona chamada contoso.net no DNS do Azure. Como o proprietário do domínio, seu registrador oferece a opção de configurar os registros de servidor de nomes (NS) para seu domínio. O registrador armazena os registros NS na zona pai .NET. Os usuários da Internet em todo o mundo serão então direcionados para seu domínio na zona DNS do Azure quando tentarem resolver os registros DNS em contoso.net.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma zona DNS.
> * Recuperar uma lista de servidores de nomes.
> * Delegar o domínio.
> * Verificar se a delegação está funcionando.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter um nome de domínio disponível com o qual seja possível testar e que você possa hospedar no DNS do Azure. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Neste exemplo, vamos fazer referência ao domínio pai como **contoso.net**

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Acesse o [Portal do Azure](https://portal.azure.com/) para criar uma zona DNS. Pesquise e selecione **zonas DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Selecione **Criar zona DNS**.
1. Na página **Criar zona DNS**, insira os seguintes valores e selecione **Criar**: por exemplo, **contoso.net**
      > [!NOTE] 
      > Se a zona que você está criando for uma zona filho (por exemplo, zona pai = contoso.net zona filho = child.contoso.net), veja o nosso [Tutorial para criar um tutorial de zona DNS filho](./tutorial-public-dns-zones-child.md)

    | **Configuração** | **Valor** | **Detalhes** |
    |--|--|--|
    | **Detalhes do projeto:**  |  |  |
    | **Grupo de recursos**    | ContosoRG | Crie um grupo de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. A localização do grupo de recursos não tem impacto sobre a localização da zona DNS. A localização da zona DNS é sempre "global" e não é exibido. |
    | **Detalhes da instância:** |  |  |
    | **Filho da zona**        | deixe desmarcado | Como essa zona **não** é uma [zona filho](./tutorial-public-dns-zones-child.md), você deve deixá-la desmarcada |
    | **Nome**              | contoso.net | Campo do nome da zona pai      |
    | **Localidade**          | Leste dos EUA | Esse campo é baseado na localização selecionada como parte da Criação do grupo de recursos  |
    

## <a name="retrieve-name-servers"></a>Recuperar servidores de nome

Antes que você possa delegar a zona DNS ao DNS do Azure, você precisará saber os nomes do servidor para sua zona. O Azure DNS aloca os servidores de nomes de um pool sempre que uma zona é criada.

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, selecione **Todos os recursos**. Na página **Todos os recursos**, selecione sua zona DNS. Se a assinatura que você selecionou já contiver vários recursos, insira o nome de domínio na caixa **Filtrar por nome** para acessar com facilidade o gateway de aplicativo. 

1. Recupere os servidores de nomes da página da zona DNS. Neste exemplo, os servidores de nomes *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* e *ns4-01.azure-dns.info*:

   ![Lista de servidores de nome](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

O DNS do Azure cria automaticamente os registros NS autoritativos na zona que contém os servidores de nomes atribuídos.

## <a name="delegate-the-domain"></a>Delegar o domínio

Agora que a zona DNS foi criada e você tem os servidores de nomes, você precisa atualizar o domínio pai com os servidores de nome do DNS do Azure. Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. 

1. Na página de gerenciamento do DNS do registrador, edite os registros NS e substitua-os por aqueles criados pelos servidores de nomes de domínio do Azure.

1. Ao delegar um domínio ao DNS do Azure, você precisará usar os servidores de nomes fornecidos pelo DNS do Azure. Use todos os quatro servidores de nomes, independentemente do nome do domínio. A delegação de domínio não exige que o servidor de nomes use o mesmo domínio primário que o do seu domínio.

> [!NOTE]
> Ao copiar cada endereço de servidor de nome, lembre-se de copiar o ponto à direita no final do endereço. O ponto à direita indica o final de um nome de domínio totalmente qualificado. Alguns registradores acrescentarão o ponto se o nome NS não tiver o ponto no final. Para estar em conformidade com o RFC do DNS, inclua o ponto à direita.

Atualmente, o DNS do Azure não dá suporte às delegações que usam servidores de nomes em sua própria zona, às vezes chamados de *servidores de nome intuitivos*.

## <a name="verify-the-delegation"></a>Verificar a delegação

Após concluir a delegação, você poderá verificar se ela está funcionando usando uma ferramenta como o *nslookup* para consultar o registro SOA da zona. O registro SOA é criado automaticamente quando a zona é criada. Talvez você precise aguardar 10 minutos ou mais depois de concluir a delegação, antes de verificar com êxito que ele está funcionando. Pode levar algum tempo para que as alterações se propaguem por meio do sistema DNS.

Você não precisa especificar os servidores de nomes DNS do Azure. Se a delegação estiver configurada corretamente, o processo normal de resolução DNS localiza automaticamente os servidores de nomes.

1. Em um prompt de comando, insira um comando nslookup semelhante ao seguinte exemplo:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Verifique se a sua resposta é semelhante à seguinte saída do nslookup:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter o grupo de recursos **contosoRG** se você pretender fazer o próximo tutorial. Caso contrário, exclua o grupo de recursos **contosoRG** para excluir os recursos criados neste tutorial.

- Selecione o grupo de recursos **contosoRG** e, em seguida, **Excluir grupo de recursos**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma zona DNS para seu domínio e delegou-a ao DNS do Azure. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
