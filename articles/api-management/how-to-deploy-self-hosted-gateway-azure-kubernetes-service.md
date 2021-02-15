---
title: Implantar um gateway auto-hospedado no serviço kubernetes do Azure | Microsoft Docs
description: Saiba como implantar o componente de gateway auto-hospedado do gerenciamento de API do Azure no serviço kubernetes do Azure
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015214"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Implantar no Serviço de Kubernetes do Azure

Este artigo fornece as etapas para implantar o componente de gateway de hospedagem interna do gerenciamento de API do Azure para o [serviço kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/). Para implantar o gateway auto-hospedado em um cluster kubernetes, confira este[documento](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
- [Criar um cluster kubernetes do Azure](../aks/kubernetes-walkthrough-portal.md)
- [Provisione um recurso de gateway em sua instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Implantar o gateway auto-hospedado no AKS

1. Selecione **gateways** em **implantação e infraestrutura**.
2. Selecione o recurso de gateway auto-hospedado que você pretende implantar.
3. Selecione **implantação**.
4. Observe que um novo token na caixa de texto de **token** foi gerado automaticamente para você usando os valores padrão de **expiração** e **chave secreta** . Ajuste um ou ambos, se desejado, e selecione **gerar** para criar um novo token.
5. Verifique se **kubernetes** está selecionado em **scripts de implantação**.
6. Selecione **<nome do gateway>** o link do arquivo. yml ao lado da **implantação** para baixar o arquivo.
7. Ajuste os mapeamentos de porta e o nome do contêiner no arquivo yml, conforme necessário.
8. Dependendo do seu cenário, talvez seja necessário alterar o tipo de [serviço](../aks/concepts-network.md#services). O valor padrão é `NodePort`.
9. Selecione o ícone de **cópia** localizado na extremidade direita da caixa de texto **implantar** para salvar o `kubectl` comando na área de transferência.
10. Cole o comando na janela de terminal (ou comando). Observe que o comando espera que o arquivo de ambiente baixado esteja presente no diretório atual.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Execute o comando. O comando instrui o cluster AKS a executar o contêiner, usando a imagem do gateway de hospedagem interna baixada do registro de contêiner da Microsoft e para configurar o contêiner para expor portas HTTP (8080) e HTTPS (443).
12. Execute o comando abaixo para verificar se o pod de gateway está em execução. Observe que o nome do pod será diferente.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Execute o comando abaixo para verificar se o serviço de gateway está em execução. Observe que o nome do serviço e os endereços IP serão diferentes.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Volte para a portal do Azure e confirme se o nó de gateway que você acabou de implantar está relatando o status Íntegro.

> [!TIP]
> Use <code>kubectl logs <gateway-pod-name></code> o comando para exibir um instantâneo do log de gateway auto-hospedado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md)
* Saiba mais sobre o [serviço kubernetes do Azure](../aks/intro-kubernetes.md)
* Saiba [como configurar e persistir logs na nuvem](how-to-configure-cloud-metrics-logs.md)
* * Saiba [como configurar e persistir logs localmente](how-to-configure-local-metrics-logs.md)
