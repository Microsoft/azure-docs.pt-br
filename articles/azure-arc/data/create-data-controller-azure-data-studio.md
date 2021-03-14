---
title: Criar controlador de dados no Azure Data Studio
description: Criar controlador de dados no Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 2b35abf7f318d4db6166dd48c4f7bd35aaf4d98a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644401"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Criar controlador de dados no Azure Data Studio

Você pode criar um controlador de dados usando Azure Data Studio por meio do assistente de implantação e notebooks.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter acesso a um cluster kubernetes e ter o arquivo kubeconfig configurado para apontar para o cluster kubernetes no qual você deseja implantar.
- Você precisa [instalar as ferramentas de cliente](install-client-tools.md) , incluindo **Azure Data Studio** as extensões de Azure Data Studio chamadas **Azure Arc** e **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Você precisa fazer logon no Azure em Azure Data Studio.  Para fazer isso: digite CTRL/Command + SHIFT + P para abrir a janela de texto do comando e digite **Azure**.  Escolha **Azure: entrar**.   No painel, que aparece, clique no ícone + no canto superior direito para adicionar uma conta do Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Usar o assistente de implantação para criar o controlador de dados de arco do Azure

Siga estas etapas para criar um controlador de dados de arco do Azure usando o assistente de implantação.

1. Em Azure Data Studio, clique na guia conexões no painel de navegação esquerdo.
2. Clique no botão **...** na parte superior do painel conexões e escolha **nova implantação...**
3. No assistente de nova implantação, escolha **controlador de dados de arco do Azure** e, em seguida, clique no botão **selecionar** na parte inferior.
4. Verifique se as ferramentas de pré-requisito estão disponíveis e atenda às versões necessárias. **Clique em Avançar**.
5. Use o arquivo kubeconfig padrão ou selecione outro.  Clique em **Avançar**.
6. Escolha um contexto de cluster kubernetes. Clique em **Avançar**.
7. Escolha um perfil de configuração de implantação dependendo do cluster kubernetes de destino. **Clique em Avançar**.
8. Se você estiver usando a plataforma de contêiner do Azure Red Hat OpenShift ou Red Hat OpenShift, aplique restrições de contexto de segurança. Siga as instruções em [aplicar uma restrição de contexto de segurança para serviços de dados habilitados para o Azure Arc no OpenShift](how-to-apply-security-context-constraint.md).

   >[!IMPORTANT]
   >Na plataforma de contêiner do Azure Red Hat OpenShift ou Red Hat OpenShift, você deve aplicar a restrição de contexto de segurança antes de criar o controlador de dados.

1. Escolha a assinatura e o grupo de recursos desejados.
1. Selecione um local do Azure.
   
   O local do Azure selecionado aqui é o local no Azure, no qual os *metadados* sobre o controlador de dados e as instâncias de banco que ele gerencia serão armazenados. As instâncias do controlador de dados e do banco de dados serão realmente crewted no cluster do kubernetes, onde quer que possa ser.

10. Selecione o modo de conectividade apropriado. Saiba mais sobre [modos de conectividade](./connectivity.md). **Clique em Avançar**.

    Se você selecionar o modo de conectividade direta, as credenciais da entidade de serviço serão necessárias, conforme descrito em [criar entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).

11. Insira um nome para o controlador de dados e para o namespace no qual o controlador de dados será criado.

    O controlador de dados e o nome do namespace serão usados para criar um recurso personalizado no cluster kubernetes para que eles devam estar em conformidade com as [convenções de nomenclatura do kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Se o namespace já existir, ele será usado se o namespace ainda não contiver outros objetos kubernetes-pods, etc.  Se o namespace não existir, será feita uma tentativa de criar o namespace.  A criação de um namespace em um cluster kubernetes requer privilégios de administrador de cluster kubernetes.  Se você não tiver privilégios de administrador de cluster kubernetes, peça ao administrador de cluster do kubernetes para executar as primeiras etapas no artigo [criar um controlador de dados usando ferramentas nativas do kubernetes](./create-data-controller-using-kubernetes-native-tools.md) que devem ser executadas por um administrador do kubernetes antes de concluir este assistente.


12. Selecione a classe de armazenamento na qual o controlador de dados será implantado. 
13.  Insira um nome de usuário e uma senha e confirme a senha da conta do administrador do controlador de dados. Clique em **Avançar**.

14. Examine a configuração de implantação.
15. Clique em **implantar** para implantar a configuração desejada ou o **script para o bloco de anotações** para examinar as instruções de implantação ou fazer as alterações necessárias, como os nomes de classe de armazenamento ou tipos de serviço. Clique em **executar tudo** na parte superior do bloco de anotações.

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