---
title: Configurar um firewall IP para o serviço de Pesquisa Cognitiva do Azure
titleSuffix: Azure Cognitive Search
description: Configure as políticas de controle de IP para restringir o acesso ao serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 67fa53e565de70d3da0b90e36a968b68d54d667f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573238"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Configurar o firewall IP para o Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva dá suporte a regras de IP para suporte de firewall de entrada. Esse modelo fornece uma camada adicional de segurança para o serviço de pesquisa semelhante às regras de IP que você encontrará em um grupo de segurança de rede virtual do Azure. Com essas regras de IP, você pode configurar seu serviço de pesquisa para ser acessível somente de um conjunto aprovado de computadores e/ou serviços de nuvem. O acesso aos dados armazenados em seu serviço de pesquisa por meio desses conjuntos aprovados de computadores e serviços ainda exigirá que o chamador apresente um token de autorização válido.

> [!Important]
> As regras de IP em seu serviço de Pesquisa Cognitiva do Azure podem ser configuradas usando o portal do Azure ou a [API REST de gerenciamento versão 2020-03-13](/rest/api/searchmanagement/).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Configurar um firewall IP usando o portal do Azure

Para definir a política de controle de acesso IP no portal do Azure, vá para a página do serviço de Pesquisa Cognitiva do Azure e selecione **rede** no menu de navegação. A conectividade de rede do ponto de extremidade deve ser **pública**. Se sua conectividade estiver definida como **particular**, você só poderá acessar o serviço de pesquisa por meio de um ponto de extremidade privado.

![Captura de tela mostrando como configurar o firewall IP no portal do Azure](./media/service-configure-firewall/azure-portal-firewall.png)

O portal do Azure fornece a capacidade de especificar endereços IP e intervalos de endereços IP no formato CIDR. Um exemplo de notação CIDR é 8.8.8.0/24, que representa os IPs que variam de 8.8.8.0 para 8.8.8.255.

> [!NOTE]
> Depois de habilitar a política de controle de acesso IP para o serviço de Pesquisa Cognitiva do Azure, todas as solicitações para o plano de dados de computadores fora da lista de endereços IP de intervalos permitidos serão rejeitadas. Quando as regras de IP são configuradas, alguns recursos do portal do Azure são desabilitados. Você poderá exibir e gerenciar as informações de nível de serviço, mas o acesso ao portal para indexar dados e os vários componentes no serviço, como o índice, o indexador e as definições de qualificações, é restrito por motivos de segurança. Como alternativa ao portal, você pode usar a [extensão vs Code](https://aka.ms/vscode-search) para interagir com os vários componentes no serviço.

### <a name="requests-from-your-current-ip"></a>Solicitações do seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda a identificar e adicionar o IP de sua máquina cliente à lista permitida. Os aplicativos em execução no seu computador podem acessar o serviço de Pesquisa Cognitiva do Azure.

O portal detecta automaticamente o endereço IP do cliente. Pode ser o endereço IP do cliente do seu computador ou gateway de rede. Certifique-se de remover esse endereço IP antes de levar sua carga de trabalho para produção.

Para adicionar seu IP atual à lista de IPs, marque **Adicionar seu endereço IP do cliente**. Em seguida, selecione **Salvar**.

![Captura de tela mostrando como definir as configurações de firewall IP para permitir o IP atual](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Solucione problemas com uma política de controle de acesso IP

Você pode solucionar problemas com uma política de controle de acesso IP usando as seguintes opções:

### <a name="azure-portal"></a>Portal do Azure

A habilitação de uma política de controle de acesso IP para o serviço de Pesquisa Cognitiva do Azure bloqueia todas as solicitações de computadores fora da lista de permissões de intervalos de endereços IP, incluindo o portal do Azure.  Você poderá exibir e gerenciar as informações de nível de serviço, mas o acesso ao portal para indexar dados e os vários componentes no serviço, como o índice, o indexador e as definições de qualificações, é restrito por motivos de segurança. 

### <a name="sdks"></a>SDKs

Quando você acessa o serviço de Pesquisa Cognitiva do Azure usando o SDK de computadores que não estão na lista de permissões, uma resposta genérica **403 Proibido** é retornada sem detalhes adicionais. Verifique a lista de IP permitida para sua conta e certifique-se de que a configuração correta foi atualizada para o serviço de pesquisa.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como acessar o serviço de pesquisa por meio do link privado, consulte o seguinte artigo:

* [Criar um ponto de extremidade privado para uma conexão segura com o Azure Pesquisa Cognitiva](service-create-private-endpoint.md)