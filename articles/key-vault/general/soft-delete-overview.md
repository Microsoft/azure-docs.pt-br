---
title: Exclusão reversível do Azure Key Vault | Microsoft Docs
description: A exclusão reversível no Azure Key Vault permite que você recupere cofres de chaves e objetos excluídos do cofre de chaves, como chaves, segredos e certificados.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.openlocfilehash: 52cd7742f3c6961350f907cde8ffe19235cff9b8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753246"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Visão geral de exclusão reversível do Azure Key Vault

> [!IMPORTANT]
> Você deve habilitar a exclusão reversível em seus cofres de chaves imediatamente. A capacidade de recusar a exclusão reversível será preterida em breve. Veja mais detalhes [aqui](soft-delete-change.md)

> [!IMPORTANT]
> O cofre com exclusão reversível dispara as configurações de exclusão para integração com os serviços do Key Vault, ou seja, atribuições de funções do RBAC do Azure, assinaturas da Grade de Eventos. Após a recuperação do Key Vault excluído de maneira reversível as configurações de serviços integrados precisarão ser recriadas manualmente. 

O recurso de exclusão reversível do Key Vault permite a recuperação dos cofres excluídos e dos objetos do cofre de chaves excluídos (por exemplo, chaves, segredos, certificados); isso é conhecido como exclusão reversível. Estamos nos referindo especificamente aos seguintes cenários: essa proteção oferece as seguintes proteções:

- Depois que um segredo, uma chave, um certificado ou um cofre de chaves for excluído, ele permanecerá recuperável por um período configurável de 7 a 90 dias corridos. Se nenhuma configuração for especificada, o período de recuperação padrão será definido como 90 dias. Isso fornece aos usuários tempo suficiente para perceber uma exclusão de segredo acidental e responder.
- É necessário fazer duas operações para excluir permanentemente um segredo. Primeiro, um usuário deve excluir o objeto, colocando-o no estado excluído de maneira reversível. Em segundo lugar, o usuário deve limpar o objeto no estado excluído de maneira reversível. A operação de limpeza requer permissões de política de acesso adicionais. Essas proteções adicionais reduzem o risco de um usuário excluir acidentalmente ou maliciosamente um segredo ou um cofre de chaves.  
- Para limpar um segredo no estado de exclusão reversível, uma entidade de serviço deve receber uma permissão adicional de "limpeza" da política de acesso. A permissão limpar da política de acesso não é concedida por padrão a nenhuma entidade de serviço, incluindo o cofre de chaves e os proprietários de assinatura, e deve ser definida deliberadamente. Ao exigir uma permissão de política de acesso elevada para limpar um segredo excluído reversivelmente, se reduzirá a probabilidade de excluir acidentalmente um segredo.

## <a name="supporting-interfaces"></a>Interfaces de suporte

O recurso de exclusão reversível está disponível por meio das interfaces [API REST](/rest/api/keyvault/), [CLI do Azure](./key-vault-recovery.md), [Azure PowerShell](./key-vault-recovery.md) e [.NET/C#](/dotnet/api/microsoft.azure.keyvault), bem como [modelos do ARM](/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Cenários

Os Azure Key Vaults são recursos controlados, gerenciados pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para a exclusão, que exige que uma operação DELETE bem-sucedida faça com que esse recurso não esteja mais acessível. O recurso de exclusão reversível aborda a recuperação do objeto excluído, independentemente se a exclusão foi acidental ou intencional.

1. No cenário típico, um usuário pode ter acidentalmente excluído um cofre de chaves ou um objeto do cofre de chaves; se esse cofre de chaves ou objeto do cofre de chaves precisar ser recuperável por um período predeterminado, o usuário poderá desfazer a exclusão e recuperar seus dados.

2. Em um cenário diferente, um usuário mal-intencionado pode tentar excluir um cofre de chaves ou um objeto do cofre de chaves, como uma chave dentro de um cofre, para causar uma interrupção dos negócios. A separação da exclusão do cofre de chaves ou do objeto do cofre de chaves da exclusão real dos dados subjacentes pode ser usada como uma medida de segurança, por exemplo, restringindo as permissões de exclusão de dados a outra função confiável. Essa abordagem efetivamente exige quorum para uma operação que, de outro modo, poderá resultar em uma perda de dados imediata.

### <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

Quando a exclusão reversível está habilitada, os recursos marcados como recursos excluídos são mantidos por um período especificado (90 dias por padrão). Além disso, o serviço fornece um mecanismo para recuperar o objeto excluído, basicamente, desfazendo a exclusão.

Ao criar um cofre de chaves, a exclusão reversível é ativada por padrão. É possível criar um cofre de chaves sem a exclusão reversível por meio da [CLI do Azure](./key-vault-recovery.md) ou do [Azure PowerShell](./key-vault-recovery.md). Quando a exclusão reversível está habilitada em um cofre de chaves, ela não pode ser desabilitada

O período de retenção padrão é de 90 dias, mas durante a criação do cofre de chaves, é possível definir o intervalo da política de retenção para um valor de 7 a 90 dias através do portal do Azure. A política de retenção de proteção de limpeza usa o mesmo intervalo. Uma vez definido, o intervalo da política de retenção não pode ser alterado.

Não é possível reutilizar o nome de um cofre de chaves que foi excluído de maneira reversível até que o período de retenção tenha passado.

### <a name="purge-protection"></a>Proteção contra limpeza

A proteção contra limpeza é um comportamento opcional do Key Vault que **não está habilitado por padrão**. A proteção contra limpeza só poderá ser habilitada quando a exclusão reversível estiver habilitada.  Ela pode ser ativado via [CLI](./key-vault-recovery.md?tabs=azure-cli) ou [PowerShell](./key-vault-recovery.md?tabs=azure-powershell).

Quando a proteção contra limpeza está ativada, um cofre ou um objeto no estado excluído não pode ser limpo até que tenha passado o período de retenção. Os cofres e objetos excluídos de maneira reversível ainda podem ser recuperados, garantindo que a política de retenção será seguida.

O período de retenção padrão é de 90 dias, mas é possível definir o intervalo da política de retenção para um valor de 7 a 90 dias através do portal do Azure. Depois que o intervalo da política de retenção for definido e salvo, ele não poderá ser alterado para esse cofre.

### <a name="permitted-purge"></a>Limpeza permitida

A exclusão permanente, limpeza, de um cofre de chaves é possível por meio de uma operação POST no recurso de proxy e exige privilégios especiais. Geralmente, apenas o proprietário da assinatura poderá limpar um cofre de chaves. A operação POST dispara a exclusão imediata e irrecuperável desse cofre. 

As exceções são:
- Quando a assinatura do Azure foi marcada como *não excluível*. Neste caso, apenas o serviço pode executar a exclusão real e ele o fará como um processo agendado. 
- Quando a `--enable-purge-protection flag` está habilitada no próprio cofre. Nesse caso, o Key Vault aguardará 90 dias desde quando o objeto secreto original foi marcado para exclusão para então excluí-lo permanentemente.

Para conhecer as etapas, confira [Como usar a exclusão reversível do Key Vault com a CLI: limpando um cofre de chaves](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) ou [Como usar a exclusão reversível do Key Vault com o PowerShell: limpando um cofre de chaves](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).

### <a name="key-vault-recovery"></a>Recuperação do cofre de chaves

Ao excluir um cofre de chaves, o serviço cria um recurso de proxy na assinatura, adicionando metadados suficientes para recuperação. O recurso de proxy é um objeto armazenado, disponível na mesma localização do cofre de chaves excluído. 

### <a name="key-vault-object-recovery"></a>Recuperação de objetos do cofre de chaves

Depois de excluir um objeto do cofre de chaves, como uma chave, o serviço colocará o objeto em um estado excluído, tornando-o inacessível para todas as operações de recuperação. Nesse estado, o objeto do cofre de chaves só poderá ser listado, recuperado ou excluído permanentemente/de maneira forçada. Para exibir os objetos, use o comando `az keyvault key list-deleted` da CLI do Azure (conforme documentado em [Como usar a exclusão reversível do Key Vault com a CLI](./key-vault-recovery.md)) ou o parâmetro `-InRemovedState` do Azure PowerShell (conforme descrito em [Como usar a exclusão reversível do Key Vault com o PowerShell](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)).  

Ao mesmo tempo, o Key Vault agendará a exclusão dos dados subjacentes correspondentes ao cofre de chaves ou objeto do cofre de chaves excluído para execução após um intervalo de retenção predeterminado. O registro DNS correspondente ao cofre também é mantido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção da exclusão reversível

Os recursos excluídos de maneira reversível são mantidos por um período definido de tempo: 90 dias. Durante o intervalo de retenção da exclusão reversível, o seguinte se aplica:

- É possível listar todos os cofres de chaves e objetos do cofre de chaves no estado de exclusão reversível de sua assinatura, bem como informações de exclusão e recuperação de acesso sobre eles.
  - Somente usuários com permissões especiais podem listar os cofres excluídos. Recomendamos que nossos usuários criem uma função personalizada com essas permissões especiais para a manipulação dos cofres excluídos.
- Não é possível criar um cofre de chaves com o mesmo nome na mesma localização; do mesmo modo, não é possível criar um objeto do cofre de chaves em determinado cofre se esse cofre de chaves contém um objeto com o mesmo nome e que está no estado excluído.
- Somente um usuário com privilégios específicos pode restaurar um cofre de chaves ou objeto do cofre de chaves emitindo um comando de recuperação no recurso de proxy correspondente.
  - O usuário, membro da função personalizada, que tem o privilégio para criar um cofre de chaves no grupo de recursos pode restaurar o cofre.
- Somente um usuário com privilégios específicos pode excluir por imposição um cofre de chaves ou objeto do cofre de chaves emitindo um comando de exclusão no recurso de proxy correspondente.

A menos que um cofre de chaves ou objeto do cofre de chaves seja recuperado, ao final do intervalo de retenção, o serviço realizará uma limpeza do cofre de chaves ou do objeto do cofre de chaves de excluídos de maneira reversível e de seu conteúdo. A exclusão de recursos não pode ser reagendada.

### <a name="billing-implications"></a>Implicações de cobrança

Em geral, quando um objeto (um cofre de chaves ou uma chave ou um segredo) está no estado excluído, há apenas duas operações possíveis: “limpar” e “recuperar”. Todas as outras operações falharão. Portanto, mesmo que o objeto exista, nenhuma operação pode ser executada e, portanto, nenhum uso ocorrerá, portanto, não haverá cobrança. No entanto, há as exceções a seguir:

- Ações “limpar' e “recuperar” contarão até as operações do cofre de chaves normal e serão cobradas.
- Se o objeto for uma chave de HSM, o encargo”chave Protegida HSM” por versão de chave por encargo mensal será aplicada se uma versão de chave tiver sido usada nos últimos 30 dias. Depois disso, uma vez que o objeto está no estado excluído, nenhuma operação pode ser executada em relação a ela, portanto nenhum encargo será aplicado.

## <a name="next-steps"></a>Próximas etapas

As duas guias a seguir oferecem os cenários de uso primário para usar a exclusão reversível.

- [Como usar a exclusão reversível do Key Vault com o portal](./key-vault-recovery.md?tabs=azure-portal)
- [Como usar a exclusão reversível do Key Vault com o PowerShell](./key-vault-recovery.md) 
- [Como usar a exclusão reversível do Key Vault com a CLI](./key-vault-recovery.md)
