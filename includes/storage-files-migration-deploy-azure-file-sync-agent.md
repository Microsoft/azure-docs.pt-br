---
title: Implantar o agente de Sincronização de Arquivos do Azure
description: Implante o agente de Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f038392f03b94aa2c2450531c9da4a11d9900295
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043191"
---
Nesta seção, você instalará o agente de Sincronização de Arquivos do Azure em sua instância do Windows Server.

O [Guia de implantação](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustra que você precisa desativar a **configuração de segurança reforçada do Internet Explorer** . Essa medida de segurança não é aplicável com Sincronização de Arquivos do Azure. Desativá-lo permite que você se autentique no Azure sem problemas.

Abra o PowerShell e instale os módulos do PowerShell necessários usando os comandos a seguir. Certifique-se de instalar o módulo completo e o provedor do NuGet quando for solicitado.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se você tiver problemas para acessar a Internet do seu servidor, agora é o momento de solucioná-los. Sincronização de Arquivos do Azure usa qualquer conexão de rede disponível com a Internet. Também há suporte para exigir um servidor proxy para acessar a Internet. Você pode configurar um proxy de todo o computador agora ou especificar um proxy que somente Sincronização de Arquivos do Azure será usado durante a instalação do agente.

Se configurar um proxy significa que você precisa abrir seus firewalls para esse servidor, essa abordagem pode ser aceitável para você. No final da instalação do servidor, depois de concluir o registro do servidor, um relatório de conectividade de rede mostrará as URLs exatas do ponto de extremidade no Azure que Sincronização de Arquivos do Azure precisa se comunicar com a região que você selecionou. O relatório também informa o motivo pelo qual a comunicação é necessária. Você pode usar o relatório para bloquear os firewalls em todo esse servidor para URLs específicas.

Você também pode seguir uma abordagem mais conservadora na qual não abre os firewalls, mas sim limitar o servidor para se comunicar com namespaces DNS de nível superior. Para obter mais informações, consulte [sincronização de arquivos do Azure configurações de proxy e firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Siga suas próprias práticas recomendadas de rede.

No final do assistente de instalação do servidor, um assistente de registro do servidor será aberto. Registre o servidor no recurso do Azure do serviço de sincronização de armazenamento anteriormente.

Essas etapas são descritas mais detalhadamente no guia de implantação, que inclui os módulos do PowerShell que você deve instalar primeiro: [sincronização de arquivos do Azure instalação do agente](../articles/storage/files/storage-sync-files-deployment-guide.md).

Use o agente mais recente. Você pode baixá-lo no centro de download da Microsoft: [agente de sincronização de arquivos do Azure](https://aka.ms/AFS/agent "Download do agente de Sincronização de Arquivos do Azure").

Após uma instalação bem-sucedida e um registro de servidor, você pode verificar se concluiu com êxito esta etapa. Vá para o recurso serviço de sincronização de armazenamento no portal do Azure. No menu à esquerda, vá para **servidores registrados** . Você verá o servidor listado lá.
