---
title: "Recursos de segurança que podem ser usados com o Armazenamento do Azure | Microsoft Docs"
description: " Este artigo fornece uma visão geral dos recursos de segurança centrais do Azure que podem ser usados com o Armazenamento do Azure. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
ms.openlocfilehash: da28cbf5f6f91df1f89114a63bc3f2ebac0f6d73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-security-overview"></a>Visão geral da segurança do armazenamento do Azure
O Armazenamento do Azure é a solução de armazenamento em nuvem para aplicativos modernos que dependem de durabilidade, disponibilidade e escalabilidade para atender às necessidades dos clientes. O Armazenamento do Azure fornece um conjunto abrangente de funcionalidades de segurança:

* A conta de armazenamento pode ser protegida usando o Controle de Acesso Baseado em Função e o Azure Active Directory.
* Os dados podem ser protegidos em trânsito, entre um aplicativo e o Azure usando a Criptografia do cliente, HTTPs ou SMB 3.0.
* Os dados podem ser definidos para serem criptografados automaticamente quando gravados no Armazenamento do Azure usando a Criptografia do Serviço de Armazenamento.
* Os discos do SO e de dados usados pelas máquinas virtuais podem ser definidos para serem criptografados usando o Azure Disk Encryption.
* O acesso delegado aos objetos de dados no Armazenamento do Azure pode ser concedido usando Assinaturas de Acesso Compartilhado.
* O método de autenticação usado por alguém ao acessar o armazenamento pode ser acompanhado usando a análise de Armazenamento.

Para obter uma visão mais detalhada sobre a segurança no Armazenamento do Azure, confira o [Guia de segurança do Armazenamento do Azure](../storage/common/storage-security-guide.md). Este guia fornece uma visão aprofundada dos recursos de segurança do Armazenamento do Azure, como chaves de conta de armazenamento, criptografia de dados em trânsito e em repouso e análise de armazenamento.

Este artigo fornece uma visão geral dos recursos de segurança do Azure que podem ser usados com o Armazenamento do Azure. São fornecidos links para artigos que apresentam detalhes de cada recurso para que você possa saber mais sobre eles.

Aqui estão os principais recursos que serão abordados neste artigo:

* Controle de Acesso Baseado em Função
* Acesso delegado aos objetos de armazenamento
* Criptografia em trânsito
* Criptografia em repouso/criptografia do serviço de armazenamento
* Azure Disk Encryption
* Cofre da Chave do Azure

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
Você pode proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função). Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função de RBAC apropriada a grupos e aplicativos em determinado escopo. Você pode usar [funções RBAC internas](../active-directory/role-based-access-built-in-roles.md), como Colaborador da Conta de Armazenamento, para atribuir privilégios aos usuários.

Saiba mais:

* [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado aos objetos de armazenamento
Uma SAS (Assinatura de Acesso Compartilhado) fornece acesso delegado aos recursos da sua conta de armazenamento. A SAS significa que você pode conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado período de tempo e com um conjunto específico de permissões. Você pode conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa fornecer a SAS ao construtor apropriado ou ao método apropriado.

Saiba mais:

* [Noções básicas sobre o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Criar e usar um SAS com o armazenamento de Blobs](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o Armazenamento do Azure, você pode proteger dados usando:

* [Criptografia de nível de transporte](../storage/common/storage-security-guide.md#encryption-in-transit), como HTTPS ao transferir dados dentro ou fora do Armazenamento do Azure.
* [Criptografia na transmissão](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como a criptografia SMB 3.0 para compartilhamentos de Arquivo do Azure.
* [Criptografia do cliente](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), que permite criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografá-los após serem transferidos para fora do armazenamento.

Saiba mais sobre a criptografia do cliente:

* [Client-Side Encryption for Microsoft Azure Storage (Criptografia do cliente para o Armazenamento do Microsoft Azure)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit (Série sobre controles de segurança de nuvem: criptografando dados em trânsito)](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Criptografia em repouso
Para muitas organizações, a [criptografia de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória no sentido de garantir a soberania, a privacidade e a conformidade dos dados. Há três recursos do Azure que fornecem criptografia de dados que estão “em repouso”:

* [Criptografia do Serviço de Armazenamento](../storage/common/storage-security-guide.md#encryption-at-rest) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no Armazenamento do Azure.
* [Client-side Encryption](../storage/common/storage-security-guide.md#client-side-encryption) também fornece o recurso de criptografia em repouso.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

Saiba mais sobre a Criptografia do Serviço de Armazenamento:

* A [Criptografia do Serviço Azure Storage](https://azure.microsoft.com/services/storage/) está disponível para [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento do Azure, confira [Arquivo](https://azure.microsoft.com/services/storage/files/), [Disco (Armazenamento Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Tabela](https://azure.microsoft.com/services/storage/tables/) e [Fila](https://azure.microsoft.com/services/storage/queues/).
* [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
O Azure Disk Encryption para VMs (Máquinas Virtuais) o ajuda a atender aos requisitos de conformidade e segurança organizacionais criptografando os discos de VM (incluindo discos de dados e de inicialização) com chaves e políticas controladas no [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/).

A criptografia de disco para VMs funciona para sistemas operacionais Windows e Linux. Ela também usa o Cofre de Chaves para ajudá-lo a proteger, gerenciar e auditar o uso de suas chaves de criptografia de disco. Todos os dados nos discos de VM são criptografados em repouso usando a tecnologia de criptografia padrão do setor em suas contas de Armazenamento do Azure. A solução de Criptografia de Disco para Windows é baseada na [Criptografia de Unidade de Disco BitLocker da Microsoft](https://technet.microsoft.com/library/cc732774.aspx) e a solução para Linux é baseada no [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba mais:

* [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Azure Disk Encryption para máquinas virtuais IaaS Windows e Linux)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Cofre da Chave do Azure
A Azure Disk Encryption usa o [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no Armazenamento do Azure. Você deve usar o Cofre de Chaves para auditar as chaves e o uso de políticas.

Saiba mais:

* [O que é o Cofre da Chave do Azure?](../key-vault/key-vault-whatis.md)
* [Introdução ao Cofre da Chave do Azure](../key-vault/key-vault-get-started.md)
