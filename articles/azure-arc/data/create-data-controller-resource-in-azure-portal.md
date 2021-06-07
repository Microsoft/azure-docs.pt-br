---
title: Criar um controlador de dados de arco do Azure no portal do Azure
description: Criar um controlador de dados de arco do Azure no portal do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 9c928040aa2ff5a6ebfb7102c03450d3d7297b59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686471"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Criar um controlador de dados de arco do Azure no portal do Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Introdução

Você pode usar o portal do Azure para criar um controlador de dados de arco do Azure.

Muitas das experiências de criação para o Arc do Azure começam no portal do Azure embora o recurso a ser criado ou gerenciado esteja fora da infraestrutura do Azure. O padrão de experiência do usuário nesses casos, especialmente quando não há conectividade direta entre o Azure e seu ambiente, é usar o portal do Azure para gerar um script que pode ser baixado e executado em seu ambiente para estabelecer uma conexão segura de volta para o Azure. Por exemplo, os servidores habilitados para Arc do Azure seguem esse padrão para [criar servidores habilitados para Arc](../servers/onboard-portal.md).

Por enquanto, Considerando que a visualização só dá suporte ao modo indireto conectado de serviços de dados habilitados para Arc do Azure, você pode usar o portal do Azure para gerar um notebook para você que pode ser baixado e executado em Azure Data Studio em seu cluster kubernetes. No futuro, quando o modo conectado diretamente estiver disponível, você poderá provisionar o controlador de dados diretamente do portal do Azure. Você pode ler mais sobre [modos de conectividade](connectivity.md).

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Usar o portal do Azure para criar um controlador de dados de arco do Azure

Siga as etapas abaixo para criar um controlador de dados de arco do Azure usando o portal do Azure e Azure Data Studio.

1. Primeiro, faça logon no [portal do Azure Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Os resultados da pesquisa do Marketplace serão filtrados para mostrar o ' controlador de dados do arco do Azure '.
2. Se a primeira etapa não tiver inserido os critérios de pesquisa. Insira nos resultados da pesquisa, clique em ' controlador de dados do Arc do Azure '.
3. Selecione o bloco controlador de dados do Azure no Marketplace.
4. Clique no botão **Criar**.
5. Examine os requisitos para criar um controlador de dados de arco do Azure e instalar qualquer software de pré-requisito ausente, como Azure Data Studio e kubectl.
6. Clique no botão **detalhes do controlador de dados** .
7. Escolha uma assinatura, um grupo de recursos e uma localização do Azure da mesma forma que faria com qualquer outro recurso que você criaria na portal do Azure. Nesse caso, o local do Azure que você selecionar será onde os metadados sobre o recurso serão armazenados.  O recurso em si será criado em qualquer infraestrutura que você escolher. Ele não precisa estar na infraestrutura do Azure.
8. Insira um nome para o controlador de dados.
9. Selecione o modo de conectividade para o controlador de dados. Saiba mais sobre [os requisitos e modos de conectividade](./connectivity.md). 

   > [!NOTE] 
   > Se você selecionar o modo de conectividade **direta** , verifique se as credenciais da entidade de serviço estão definidas por meio de variáveis de ambiente, conforme descrito em [criar entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

1. Selecione um perfil de configuração de implantação.
1. Clique no botão **abrir no Azure Studio** .
1. Na próxima tela, você verá um resumo de suas seleções e um bloco de anotações que é gerado.  Você pode clicar no botão **baixar o bloco de anotações de provisionamento** para baixar o bloco de anotações.

   > [!IMPORTANT]
   > Na plataforma de contêiner do Azure Red Hat OpenShift ou Red Hat OpenShift, você deve aplicar a restrição de contexto de segurança antes de criar o controlador de dados. Siga as instruções em [aplicar uma restrição de contexto de segurança para serviços de dados habilitados para o Azure Arc no OpenShift](how-to-apply-security-context-constraint.md).

1. Abra o bloco de anotações no Azure Data Studio e clique no botão **executar tudo** na parte superior.
1. Siga os prompts e as instruções no bloco de anotações para concluir a criação do controlador de dados.

## <a name="monitoring-the-creation-status"></a>Monitorando o status de criação

A criação do controlador levará alguns minutos para ser concluída. Você pode monitorar o progresso em outra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo a seguir pressupõem que você criou um controlador de dados e um namespace kubernetes com o nome ' Arc '.  Se você usou um nome de controlador de dados/namespace diferente, poderá substituir "Arc" pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Você também pode verificar o status de criação de qualquer Pod específico executando um comando como abaixo.  Isso é especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solucionando problemas de criação

Se você encontrar qualquer problemas com a criação, consulte o [Guia de solução de problemas](troubleshoot-guide.md).