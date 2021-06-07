---
title: Conectar a proteção de informações do Azure ao Azure Sentinel
description: Transmita informações de log da proteção de informações do Azure para o Azure Sentinel Configurando o conector de dados da proteção de informações do Azure.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655979"
---
# <a name="connect-data-from-azure-information-protection"></a>Conectar dados da proteção de informações do Azure

> [!IMPORTANT]
> O conector de dados da proteção de informações do Azure no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir informações de log da [proteção de informações do Azure](https://azure.microsoft.com/services/information-protection/) para o Azure Sentinel Configurando o conector de dados da proteção de informações do Azure. A proteção de informações do Azure ajuda a controlar e proteger seus dados confidenciais, sejam eles armazenados na nuvem ou no local.

Se o [relatório central da proteção de informações do Azure](/azure/information-protection/reports-aip) já estiver configurado para que as informações de log desse serviço sejam armazenadas no mesmo espaço de trabalho log Analytics que você selecionou atualmente para o Azure Sentinel, você poderá ignorar a configuração desse conector de dados. As informações de log da proteção de informações do Azure já estão disponíveis para o Azure Sentinel.

No entanto, se as informações de log da proteção de informações do Azure estiverem indo para um espaço de trabalho Log Analytics diferente daquele selecionado no momento para o Azure Sentinel, siga um destes procedimentos:

- Altere o espaço de trabalho selecionado no Azure Sentinel.

- Altere o espaço de trabalho da proteção de informações do Azure, que você pode fazer Configurando esse conector de dados.
    
    Se você alterar o espaço de trabalho, novos dados de relatório para a proteção de informações do Azure agora serão armazenados no espaço de trabalho que você está usando para o Azure Sentinel, e os dados históricos não estarão disponíveis para o Azure Sentinel. Além disso, se o espaço de trabalho anterior estiver configurado para consultas personalizadas, alertas ou APIs REST, eles deverão ser reconfigurados para o espaço de trabalho do Azure Sentinel se você quiser usá-los para a proteção de informações do Azure. Nenhuma reconfiguração é necessária para clientes e serviços que usam a proteção de informações do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma das seguintes funções de administrador do Azure AD para seu locatário: 
    - Administrador da Proteção de Informações do Azure
    - Administrador de segurança
    - Administrador de conformidade
    - Administrador de dados de conformidade
    - Administrador global
    
    > [!NOTE]
    > Você não poderá usar a função de administrador da proteção de informações do Azure se seu locatário estiver na [plataforma de rotulamento unificada](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Essas funções de administrador são necessárias apenas para configurar o conector da proteção de informações do Azure e não são necessárias quando o Azure Sentinel está conectado à proteção de informações do Azure.

- Permissões para ler e gravar no espaço de trabalho de Log Analytics que você está usando para o Azure Sentinel e a proteção de informações do Azure.

- A proteção de informações do Azure foi adicionada ao portal do Azure. Se precisar de ajuda com esta etapa, consulte [Adicionar proteção de informações do Azure ao portal do Azure](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Conectar-se à proteção de informações do Azure

Use as instruções a seguir se você não tiver configurado um espaço de trabalho Log Analytics para a proteção de informações do Azure ou se precisar alterar o espaço de trabalho que armazena as informações de log da proteção de informações do Azure.

1. No Azure Sentinel, selecione **conectores de dados**  >  **proteção de informações do Azure (versão prévia)**.

2. Clique em **Abrir página do conector**.

3. Em **configuração**, selecione **conectar logs da proteção de informações do Azure**.

4. Na folha **Configurar análise (versão prévia)** , selecione o espaço de trabalho que você usa atualmente para o Azure Sentinel. Se você selecionar um espaço de trabalho diferente, os dados de relatório da proteção de informações do Azure não estarão disponíveis para o Azure Sentinel.

5. Depois de selecionar um espaço de trabalho, selecione **OK**. O **status** do conector é alterado para **conectado**.

6. Os dados de relatório da proteção de informações do Azure são armazenados na tabela **InformationProtectionLogs_CL** no espaço de trabalho selecionado. 
    
    Para usar o esquema relevante no Azure Monitor para esses dados de relatório, procure **InformationProtectionEvents**. Para obter informações sobre essas funções de evento, consulte a seção [referência de esquema amigável para funções de evento](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) da documentação da proteção de informações do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a proteção de informações do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).