---
title: Visão geral da segurança
description: Informações de segurança sobre servidores habilitados para Arc do Azure.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aa8653b783e7eb3e211b7514831604dd5642cfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117020"
---
# <a name="azure-arc-for-servers-security-overview"></a>Visão geral de segurança do Arc para servidores do Azure

Este artigo descreve a configuração de segurança e as considerações que devem ser avaliadas antes da implantação de servidores habilitados para Arc do Azure em sua empresa.

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

Cada servidor habilitado para Arc do Azure tem uma identidade gerenciada como parte de um grupo de recursos dentro de uma assinatura do Azure, essa identidade representa o servidor em execução no local ou em outro ambiente de nuvem. O acesso a esse recurso é controlado pelo [controle de acesso baseado em função do Azure](../../role-based-access-control/overview.md)padrão. Na página [**controle de acesso (iam)**](../../role-based-access-control/role-assignments-portal.md) no portal do Azure, você pode verificar quem tem acesso ao seu servidor habilitado para Arc do Azure.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Controle de acesso do servidor habilitado para Arc do Azure" border="false" lightbox="./media/security-overview/access-control-page.png":::

Os usuários e aplicativos com acesso à função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou administrador ao recurso podem fazer alterações no recurso, incluindo a implantação ou exclusão de [extensões](manage-vm-extensions.md) no computador. As extensões podem incluir scripts arbitrários que são executados em um contexto privilegiado, portanto, considere qualquer colaborador no recurso do Azure para ser um administrador indireto do servidor.

A função de **integração do computador conectado do Azure** está disponível para a integração em escala e só é capaz de ler ou criar novos servidores habilitados para Arc no Azure. Ele não pode ser usado para excluir servidores já registrados ou gerenciar extensões. Como prática recomendada, recomendamos atribuir essa função somente à entidade de serviço Azure Active Directory (AD do Azure) usada para integrar computadores em escala.

Os usuários como membros da função de **administrador de recursos do computador conectado do Azure** podem ler, modificar, reintegrer e excluir um computador. Essa função foi projetada para dar suporte ao gerenciamento de servidores habilitados para Arc, mas não a outros recursos no grupo de recursos ou na assinatura.

## <a name="agent-security-and-permissions"></a>Segurança e permissões do agente

Para gerenciar o agente do computador conectado do Azure (azcmagent) no Windows, sua conta de usuário precisa ser um membro do grupo local de administradores. No Linux, você deve ter permissões de acesso à raiz.

O agente do computador conectado do Azure é composto por três serviços, que são executados em seu computador.

* O serviço himds (serviço de metadados de instância híbrida) é responsável por toda a funcionalidade básica do arco. Isso inclui o envio de pulsações para o Azure, expondo um serviço de metadados de instância local para outros aplicativos para saber mais sobre a ID de recurso do Azure do computador e recuperar Tokens do Azure AD para autenticar para outros serviços do Azure. Esse serviço é executado como uma conta de serviço virtual sem privilégios no Windows e como o usuário **himds** no Linux.

* O GCService (serviço de configuração de convidado) é responsável por avaliar Azure Policy no computador.

* O ExtensionService (serviço de extensão de configuração de convidado) é responsável por instalar, atualizar e excluir extensões (agentes, scripts ou outros softwares) no computador.

Os serviços de configuração e extensão de convidado são executados como sistema local no Windows e como raiz no Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Usando uma identidade gerenciada com servidores habilitados para Arc

Por padrão, a identidade atribuída pelo sistema Azure Active Directory usada pelo Arc só pode ser usada para atualizar o status do servidor habilitado para Arc no Azure. Por exemplo, o *último* status de pulsação visto. Opcionalmente, você pode atribuir funções adicionais à identidade se um aplicativo em seu servidor usar a identidade atribuída pelo sistema para acessar outros serviços do Azure.

Embora o serviço de metadados de instância híbrida possa ser acessado por qualquer aplicativo em execução no computador, somente os aplicativos autorizados podem solicitar um token do Azure AD para a identidade atribuída pelo sistema. Na primeira tentativa de acessar o URI do token, o serviço irá gerar um blob criptográfico gerado aleatoriamente em um local no sistema de arquivos que somente os chamadores confiáveis poderão ler. O chamador deve ler o arquivo (comprovando que tem a permissão apropriada) e tentar novamente a solicitação com o conteúdo do arquivo no cabeçalho de autorização para recuperar com êxito um token do Azure AD.

* No Windows, o chamador deve ser membro do grupo local de **Administradores** ou do grupo de **aplicativos de extensão de agente híbrido** para ler o blob.

* No Linux, o chamador deve ser um membro do grupo **himds** para ler o blob.

## <a name="using-disk-encryption"></a>Usando a criptografia de disco

O agente do computador conectado do Azure usa a autenticação de chave pública para se comunicar com o serviço do Azure. Depois de integrar um servidor ao Arc do Azure, uma chave privada é salva no disco e usada sempre que o agente se comunica com o Azure. Se for roubado, a chave privada poderá ser usada em outro servidor para se comunicar com o serviço e agir como se fosse o servidor original. Isso inclui obter acesso à identidade atribuída ao sistema e a todos os recursos aos quais a identidade tem acesso. O arquivo de chave privada é protegido para permitir somente o acesso de conta do **himds** para lê-lo. Para evitar ataques offline, recomendamos enfaticamente o uso de criptografia de disco completo (por exemplo, BitLocker, DM-cript, etc.) no volume do sistema operacional do seu servidor.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar servidores habilitados para Arc em vários computadores híbridos, examine [Visão geral do agente do Computador Conectado](agent-overview.md) para entender os requisitos, os detalhes técnicos sobre o agente e os métodos de implantação.
