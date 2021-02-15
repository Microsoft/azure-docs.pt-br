---
title: Visão geral da segurança do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve Azure Data Box recursos de segurança no dispositivo, serviço e dados que residem em Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: alkohli
ms.openlocfilehash: 4d6c77b3e8920cabc397cdcbc235baefa031e5ab
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655485"
---
# <a name="azure-data-box-security-and-data-protection"></a>Segurança e proteção de dados do Azure Data Box

O Data Box fornece uma solução segura para a proteção de dados, garantindo que somente entidades autorizadas possam exibir, modificar ou excluir seus dados. Este artigo descreve os recursos de segurança do Azure Data Box que ajudam a proteger cada um dos componentes da solução do Data Box e os dados armazenados neles.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Fluxo de dados por meio de componentes

A solução Microsoft Azure Data Box consiste em quatro componentes principais que interagem entre si:

- **Serviço do Azure Data Box hospedado no Azure** – o serviço de gerenciamento que você usa para criar o pedido do disco, configurar o dispositivo e, em seguida, controlar o pedido até a conclusão.
- **Dispositivo Data Box** – o dispositivo de transferência que é enviado para você para importar seus dados locais no Azure.
- **Clientes/hosts conectados ao dispositivo** – os clientes em sua infraestrutura que se conectam ao dispositivo do Data Box e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem** – O local na nuvem do Azure em que os dados ficam armazenados. Esse local normalmente é a conta de armazenamento vinculada ao recurso de Azure Data Box que você criou.

O diagrama a seguir indica o fluxo de dados por meio da solução de Azure Data Box do local para o Azure e os vários recursos de segurança em vigor, pois os dados fluem pela solução. Esse fluxo é para uma ordem de importação para seu Data Box.

![Segurança de importação de Data Box](media/data-box-security/data-box-security-import.png)

O diagrama a seguir é para a ordem de exportação para seu Data Box.

![Segurança de exportação de Data Box](media/data-box-security/data-box-security-export.png)

Conforme os dados fluem por essa solução, os eventos são registrados e os logs são gerados. Para obter mais informações, consulte:

- [Rastreamento e log de eventos para seus Azure data Box importar ordens](data-box-logs.md).
- [Rastreamento e log de eventos para seus Azure Data Box ordens de exportação](data-box-export-logs.md)

## <a name="security-features"></a>Recursos de segurança

O Data Box fornece uma solução segura para a proteção de dados, garantindo que somente entidades autorizadas possam exibir, modificar ou excluir seus dados. Os recursos de segurança para essa solução são destinados ao disco e ao serviço associado, garantindo a segurança dos dados armazenados neles.

### <a name="data-box-device-protection"></a>Proteção de dispositivo do Data Box

O dispositivo do Data Box é protegido pelos seguintes recursos:

- Uma caixa reforçada para o dispositivo que suporta choques, transporte adverso e condições ambientais. 
- Detecção de violação de hardware e software que impede operações do dispositivo.
- Executa apenas o software específico do Data Box.
- Inicializa em um estado bloqueado.
- Controla o acesso ao dispositivo por meio de um dispositivo desbloqueio de chaves. Essa chave de acesso é protegida por uma chave de criptografia. Você pode usar sua própria chave gerenciada pelo cliente para proteger a chave de acesso. Para obter mais informações, confira [Usar chaves gerenciadas pelo cliente no Azure Key Vault para o Azure Data Box](data-box-customer-managed-encryption-key-portal.md).
- Credenciais de acesso para copiar dados dentro e fora do dispositivo. Cada acesso à página de **credenciais do dispositivo** na portal do Azure é registrado nos logs de [atividade](data-box-logs.md#query-activity-logs-during-setup).
- Você pode usar suas próprias senhas para acesso de dispositivo e compartilhamento. Para obter mais informações, consulte [tutorial: Order Azure data Box](data-box-deploy-ordered.md).

### <a name="establish-trust-with-the-device-via-certificates"></a>Estabelecer confiança com o dispositivo por meio de certificados

Um dispositivo Data Box permite que você traga seus próprios certificados e instale-os para serem usados para se conectar à interface do usuário da Web local e ao armazenamento de BLOBs. Para obter mais informações, consulte [usar seus próprios certificados com data box e dispositivos data Box Heavy](data-box-bring-your-own-certificates.md).

### <a name="data-box-data-protection"></a>Proteção de dados do Data Box

Os dados que entram e saem do Data Box são protegidos pelos seguintes recursos:

- Criptografia de 256 bits AES para dados em repouso. Em um ambiente de alta segurança, você pode usar a criptografia dupla baseada em software. Para obter mais informações, consulte [tutorial: Order Azure data Box](data-box-deploy-ordered.md).
- Protocolos criptografados podem ser usados para dados em trânsito. Recomendamos que você use o SMB 3,0 com criptografia para proteger os dados ao copiá-los de seus servidores de dados.
- Eliminação segura de dados do dispositivo após a conclusão do carregamento para o Azure. A eliminação de dados está de acordo com as diretrizes no [Apêndice A para unidades de disco rígido ATA nos padrões NIST 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). O evento de apagamento de dados é registrado no [histórico de pedidos](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Proteção de serviço do Data Box

O serviço do Data Box é protegido pelos seguintes recursos.

- O acesso ao serviço de Data Box exige que sua organização tenha uma assinatura do Azure que inclua Data Box. Sua assinatura determina os recursos que você pode acessar no portal do Azure.
- Como o serviço do Data Box está hospedado no Azure, ele é protegido pelos recursos de segurança do Azure. Para obter mais informações sobre os recursos de segurança fornecidos pelo Microsoft Azure, acesse a [Central de Confiabilidade do Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- O acesso ao pedido de Data Box pode ser controlado por meio do uso de funções do Azure. Para saber mais, confira [Configurar controle de acesso para pedido do Data Box](data-box-logs.md#set-up-access-control-on-the-order)
- O serviço de Data Box armazena a senha de desbloqueio que é usada para desbloquear o dispositivo no serviço.
- O serviço do Data Box armazena os detalhes do pedido e o status no serviço. Essas informações são excluídas quando o pedido é excluído.

## <a name="managing-personal-data"></a>Gerenciando dados pessoais

O Azure Data Box reúne e exibe informações pessoais nas seguintes instâncias de chave no serviço:

- **Configurações de notificação** - ao criar um pedido, você configura o endereço de email dos usuários nas configurações de notificação. Essas informações podem ser visualizadas pelo administrador. Essas informações são excluídas pelo serviço quando o trabalho atingir o estado terminal ou quando você excluir o pedido.

- **Detalhes do pedido** – depois que a ordem é criada, o endereço de envio, o email e as informações de contato dos usuários são armazenados no portal do Azure. As informações salvas incluem:

  - Nome de contato
  - Número do telefone
  - Email
  - Endereço
  - City
  - CEP/Código postal
  - Estado
  - Região/País/Região
  - Número da conta da operadora
  - Enviar número de controle

    Os detalhes do pedido serão excluídos pelo serviço do Data Box quando o trabalho for concluído ou quando você excluir o pedido.

- **Endereço para entrega** – depois que o pedido for feito, o serviço do Data Box fornecerá o endereço de entrega para operadoras de terceiros, como a UPS ou a DHL. 

Para mais informações, revise a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Referência das diretrizes de segurança

As seguintes diretrizes de segurança são implementadas no Data Box:

|Diretriz   |Descrição   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Para proteção contra água e poeira         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Para durabilidade em condições adversas de transporte          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Para atualização de firmware segura         |
|[FIPS 140-2 Nível 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Para proteção de dados         |
|Apêndice A para unidades de disco rígido ATA em [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Para limpeza de dados         |

## <a name="next-steps"></a>Próximas etapas

- Examine os [requisitos do Data Box](data-box-system-requirements.md).
- Entenda os [limites do Data Box](data-box-limits.md).
- Implante rapidamente o [Azure Data Box](data-box-quickstart-portal.md) no portal do Azure.
