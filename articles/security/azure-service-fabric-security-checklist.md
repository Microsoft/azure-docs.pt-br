---
title: Lista de verificação de segurança do Azure Service Fabric | Microsoft Docs
description: Este artigo fornece um conjunto de listas de verificação de segurança do banco de dados do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611534"
---
# <a name="azure-service-fabric-security-checklist"></a>Lista de verificação de segurança do Azure Service Fabric
Este artigo fornece uma lista de verificação fácil de usar que ajudará você a proteger seu ambiente do Azure Service Fabric.

## <a name="introduction"></a>Introdução
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis.

## <a name="checklist"></a>Lista de verificação
Use a seguinte lista de verificação para ajudá-lo a garantir que você não negligenciou problemas importantes no gerenciamento e na configuração de uma solução segura do Azure Service Fabric.


|Categoria da lista de verificação| DESCRIÇÃO |
| ------------ | -------- |
|[RBAC (Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.</li><li>Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). </li><li> Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.</li></ul>|
|[Certificados X.509 e Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Os [certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) usados em clusters que executam cargas de trabalho de produção devem ser criados por meio de um serviço de certificado do Windows Server configurado corretamente ou obtidos por meio de uma [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada.</li><li>Nunca use nenhum [certificado temporário ou de teste](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) em produção criado com ferramentas como [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Você pode usar um [certificado autoassinado](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), mas deve fazer isso somente para clusters de teste e não em produção.</li></ul>|
|[Segurança de Cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Os cenários de segurança de cluster incluem a segurança de Nó para Nó, a segurança de Cliente para nó, o [Controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Autenticação de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica a [comunicação nó a nó](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) para a federação de cluster. </li></ul>|
|[Autenticação de servidor](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica os [pontos de extremidade de gerenciamento de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) para um cliente de gerenciamento.</li></ul>|
|[Segurança de aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Criptografia e descriptografia de valores de configuração de aplicativo.</li><li>   Criptografia de dados entre nós durante a replicação.</li></ul>|
|[Certificado de Cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Esse certificado é necessário para proteger a comunicação entre os nós em um cluster.</li><li>    Defina a impressão digital do certificado principal na seção Impressão Digital e a do secundário nas variáveis ThumbprintSecondary.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Você pode usar dois certificados de servidor diferentes, um principal e um secundário, para atualização.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Esse é um conjunto de certificados que você deseja instalar nos clientes autenticados. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Defina o nome comum do primeiro certificado do cliente para CertificateCommonName. A CertificateIssuerThumbprint é a impressão digital para o emissor deste certificado. </li></ul>|
|ReverseProxyCertificate| <ul><li>Esse é um certificado opcional que poderá ser especificado se você desejar proteger o [Proxy Reverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Usado para gerenciar certificados de clusters do Service Fabric no Azure.  </li></ul>|


## <a name="next-steps"></a>Próximas etapas

- [Melhores práticas de segurança do Service Fabric](azure-service-fabric-security-best-practices.md)
- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Introdução ao modelo de Integridade do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).
