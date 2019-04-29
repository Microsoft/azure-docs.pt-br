---
title: Configurar conexões seguras do cluster do Service Fabric | Microsoft Docs
description: Saiba mais sobre como usar o Visual Studio para configurar conexões seguras às quais o cluster do Azure Service Fabric dá suporte.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628263"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configurar conexões seguras para um cluster do Service Fabric do Visual Studio
Saiba mais sobre como usar o Visual Studio para acessar com segurança um cluster do Service Fabric do Azure com políticas de controle de acesso configuradas.

## <a name="cluster-connection-types"></a>Tipos de conexão do cluster
O cluster do Azure Service Fabric dá suporte a dois tipos de conexão: conexões **não seguras** e conexões seguras **baseadas no certificado x509**. (Para clusters do Service Fabric hospedados localmente, também há suporte para autenticações do **Windows** e **dSTS**.) Você precisa configurar o tipo de conexão de cluster durante a criação do cluster. Depois de criado, o tipo de conexão não pode ser alterado.

As Ferramentas do Service Fabric do Visual Studio dão suporte a todos os tipos de autenticação para conexão com um cluster para publicação. Veja [Configurando um cluster do Service Fabric no Portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter instruções sobre como configurar um cluster do Service Fabric seguro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurar conexões do cluster em perfis de publicação
Se você publicar um projeto do Service Fabric do Visual Studio, use a caixa de diálogo **Publicar Aplicativo do Service Fabric** para escolher um cluster do Azure Service Fabric. Em **Ponto de extremidade de conexão**, selecione um cluster existente em sua assinatura.

![A caixa de diálogo **Publicar Aplicativo do Service Fabric** é usada para configurar uma conexão do Service Fabric.][publishdialog]

A caixa de diálogo **Publicar Aplicativo do Service Fabric** valida automaticamente a conexão do cluster. Se solicitado, entre em sua conta do Azure. Se a validação for bem-sucedida, significa que seu sistema possui os certificados corretos instalados para se conectar ao cluster com segurança, ou o cluster é não seguro. As falhas de validação podem ser causadas por problemas de rede ou caso o sistema não tenha sido corretamente configurado para conectar-se a um cluster seguro.

![A caixa de diálogo **Publicar Aplicativo do Service Fabric** valida uma conexão de cluster do Service Fabric existente e corretamente configurada.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Para conectar-se a um cluster seguro
1. Verifique se você pode acessar um dos certificados de cliente nos quais o cluster de destino confia. Normalmente, o certificado é compartilhado como um arquivo de Troca de Informações Pessoais (.pfx). Veja [Configurando um cluster do Service Fabric do Portal do Azure](service-fabric-cluster-creation-via-portal.md) para saber como configurar o servidor para conceder acesso a um cliente.
2. Instale o certificado confiável. Para fazer isso, clique duas vezes no arquivo .pfx ou use o script do PowerShell, Import-PfxCertificate, para importar os certificados. Instale o certificado em **Cert:\LocalMachine\My**. É possível aceitar todas as configurações padrão durante a importação do certificado.
3. Escolha o comando **Publicar...** no menu de atalho do projeto para abrir a caixa de diálogo **Publicar Aplicativo do Azure** e selecione o cluster de destino. A ferramenta resolve automaticamente a conexão e salva os parâmetros da conexão segura no perfil de publicação.
4. Opcional: Você pode editar o perfil de publicação para especificar uma conexão de cluster seguro.
   
   Como você está editando manualmente o arquivo XML do Perfil de Publicação a fim de especificar as informações do certificado, anote o nome do repositório de certificados, do local de armazenamento e a impressão digital do certificado. Você precisará fornecer esses valores para o nome e o local do repositório do certificado. Confira [Como: Recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) para obter mais informações.
   
   Você pode usar os parâmetros *ClusterConnectionParameters* para especificar os parâmetros do PowerShell a serem usados na conexão ao cluster do Service Fabric. Os parâmetros válidos são aqueles aceitos pelo cmdlet Connect-ServiceFabricCluster. Confira [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) para obter uma lista dos parâmetros disponíveis.
   
   Se você estiver publicando em um cluster remoto, será necessário especificar os parâmetros adequados para o cluster específico. A seguir, um exemplo de conexão a um cluster não seguro:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Este é um exemplo para se conectar a um cluster seguro baseado em certificado X509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Edite outras configurações necessárias, como os parâmetros de atualização e o local do arquivo de Parâmetro do Aplicativo e, em seguida, publique seu aplicativo na caixa de diálogo **Publicar Aplicativo do Service Fabric** no Visual Studio.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como acessar os clusters do Service Fabric, confira [Visualizando o cluster usando o Explorador do Service Fabric](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
