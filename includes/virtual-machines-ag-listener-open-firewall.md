---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171868"
---
Nesta etapa, você pode criar uma regra de firewall para abrir a porta de investigação para o ponto de extremidade com balanceamento de carga (59999 conforme especificado anteriormente), e outra regra para abrir a porta do ouvinte do grupo de disponibilidade. Como você criou o ponto de extremidade com balanceamento de carga nas VMs que contêm réplicas do grupo de disponibilidade, você precisa abrir a porta de investigação e a porta do ouvinte nasVMs.

1. Em VMs que hospedam réplicas, inicie o **Firewall do Windows com segurança avançada**.

2. Clique em **Regras de entrada** e, em seguida, clique em **Nova Regra**.

3. Na página **Tipo de regra**, selecione **Porta** e clique em **Avançar**.

4. Na página **Protocolo e portas**, selecione **TCP** e digite **59999** na caixa **Portas locais específicas** e, em seguida, clique em **Avançar**.

5. Na página **Ação**, mantenha selecionado **Permitir a conexão** e, em seguida, clique em **Avançar**.

6. Na página **Perfil**, aceite as configurações padrão e, em seguida, clique em **Avançar**.

7. Na página **Nome**, na caixa de texto **Nome**, especifique um nome de regra, como **Porta de investigação do ouvinte AlwaysOn** e clique em **Concluir**.

8. Repita as etapas acima para a porta do ouvinte do grupo de disponibilidade (conforme especificado anteriormente no parâmetro $EndpointPort do script) e, em seguida, especifique um nome de regra apropriado, como **Porta do ouvinte AlwaysOn**.

