---
title: Delegar um subdomínio DNS do Azure
description: Saiba como delegar um subdomínio de DNS do Azure.
services: dns
author: WenJason
ms.service: dns
ms.topic: article
origin.date: 2/7/2019
ms.date: 02/25/2019
ms.author: v-jay
ms.openlocfilehash: 31543db8e177701ddfe6beaaa3091d6465b0e9cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790803"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegar um subdomínio DNS do Azure

Você pode usar o portal do Azure para delegar um subdomínio de DNS. Por exemplo, se você tem o domínio contoso.com, é possível delegar um subdomínio chamado *engineering* a outra zona separada, que pode ser administrada separadamente da zona contoso.com.

Se você preferir, você pode delegar um subdomínio usando o [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio de DNS do Azure, primeiro você deve delegar seu domínio público ao DNS do Azure. Veja [Delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores de nome para delegação. Depois que seu domínio tiver sido delegado para a zona DNS do Azure, você poderá delegar um subdomínio.

> [!NOTE]
> Contoso.com é usado como um exemplo ao longo deste artigo. Substitua seu próprio nome de domínio para contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Criar uma zona para o subdomínio

Primeiro, crie a zona para o subdomínio **engineering**.

1. No portal do Azure, selecione **Criar um recurso**.
2. Na caixa de pesquisa, digite **DNS**e selecione **Zona DNS**.
3. Selecione **Criar**.
4. No painel **Criar zona DNS**, digite **engineering.contoso.com** na caixa de texto **Nome**.
5. Selecione o grupo de recursos para sua zona. Você talvez queira usar o mesmo grupo de recursos como a zona pai para manter recursos semelhantes juntos.
6. Clique em **Criar**.
7. Após a implantação bem-sucedida, vá para a nova zona.

## <a name="note-the-name-servers"></a>Observe os servidores de nome

Em seguida, observe os quatro servidores de nomes para o subdomínio engineering.

No painel da zona **engineering**, observe os quatro servidores de nomes para a zona. Você usará esses servidores de nome posteriormente.

## <a name="create-a-test-record"></a>Criar um registro de teste

Crie um registro **A** para uso em testes. Por exemplo, crie um registro **www** A e configure-o com um endereço IP **10.10.10.10**.

## <a name="create-an-ns-record"></a>Criar um registro NS

Em seguida, crie um registro NS (servidor de nome) para a zona **engenharia**.

1. Navegue até a zona para o domínio pai.
2. Selecione **+ Conjunto de registros**.
3. No painel **Adicionar conjunto de registros**, digite **engineering** na caixa de texto **Nome**.
4. Para **Tipo**, selecione **NS**.
5. Em **Servidor de nomes**, insira os quatro servidores de nome que você registrou anteriormente na zona **engineering**.
6. Clique em **OK**.

## <a name="test-the-delegation"></a>Testar a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No prompt de comando, digite `nslookup www.engineering.contoso.com.`
3. Você deve receber uma resposta não autoritativa mostrando o endereço **10.10.10.10**.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).