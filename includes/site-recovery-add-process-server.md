---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: c9a0d4387511bbfa033bcb90d9f83e1a7bb39719
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171627"
---
1. Inicie o UnifiedSetup.exe do Azure Site Recovery
2. Em **Antes de começar**, selecione **Adicionar servidores em processo adicionais para escalar horizontalmente a implantação**.

   ![Adicionar servidor de processo](./media/site-recovery-add-process-server/ps-page-1.png)

3. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do Servidor de Configuração e a senha.

   ![Adicionar servidor de processo 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. Em **Configurações da Internet**, especifique como o Provedor em execução no Servidor de Configuração se conecta ao Azure Site Recovery pela Internet.

   ![Adicionar servidor de processo 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Se você desejar se conectar ao proxy que está configurado atualmente no computador, selecione **Conectar-se com as configurações de proxy existentes**.
   * Se você desejar que o Provedor se conecte diretamente, selecione **Conectar diretamente sem um proxy**.
   * Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do Provedor, escolha **Conectar-se com configurações de proxy personalizadas**.

     * Se você usar um proxy personalizado, especifique o endereço, a porta e as credenciais.
     * Se você estiver usando um proxy, já deverá ter permitido o acesso às URLs de serviço.

5. Em **Verificação de Pré-requisitos**, a configuração executa uma verificação para garantir que a instalação pode ser executada. Se aparecer um aviso sobre a **Verificação de sincronização de tempo global**, verifique se a hora no relógio do sistema (configurações de **Data e Hora**) é a mesma que a do fuso horário.

     ![Adicionar servidor de processo 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. Em **Detalhes do Ambiente**, selecione se você replicará as VMs VMware. Se a resposta for positiva, a instalação verificará se o PowerCLI 6.0 está instalado.

     ![Adicionar servidor de processo 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. Em **Localização de Instalação**, selecione a localização em que você deseja instalar os binários e armazenar o cache. A unidade selecionado deve ter ao menos 5 GB de espaço em disco disponível, mas é recomendável uma unidade de cache com ao menos 600 GB de espaço livre.
     ![Adicionar servidor de processo 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. Em **Seleção da Rede**, especifique o ouvinte (adaptador de rede e porta SSL) no qual o Servidor de Configuração envia e recebe os dados de replicação. A porta 9443 é a porta padrão usada para enviar e receber o tráfego de replicação, mas você pode modificar esse número de porta para atender aos requisitos do seu ambiente. Além da porta 9443, também podemos abrir a porta 443, usada por um servidor Web para coordenar operações de replicação. Não use a porta 443 para enviar ou receber tráfego de replicação.

     ![Adicionar servidor de processo 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. Em **Resumo**, examine as informações e clique em **Instalar**. Após a conclusão da instalação, uma frase secreta é gerada. Você precisará dela quando habilitar a replicação, portanto copie-a e guarde-a em um local seguro.

     ![Adicionar servidor de processo 7](./media/site-recovery-add-process-server/ps-page-8.png)
