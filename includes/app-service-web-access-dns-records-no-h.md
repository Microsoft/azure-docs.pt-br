---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d001d76bca5b9a0837349b6e05b3b0a271ea3a73
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171736"
---
> [!NOTE]
> Você pode usar o DNS do Azure para configurar um nome DNS personalizado para os seus Aplicativos Web do Azure. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Entre no site de seu provedor de domínio.

Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS; portanto, consulte a documentação do provedor. Procure áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. 

Normalmente, você pode encontrar a página de registros DNS exibindo as informações da conta e procurando um link como **Meus domínios**. Vá para essa página e, em seguida, procure um link com um nome semelhante a **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

A captura de tela a seguir é um exemplo de uma página de registros DNS:

![Exemplo de página de registros DNS](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Na captura de tela de exemplo, selecione **Adicionar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Novamente, consulte a documentação do provedor.

> [!NOTE]
> Para alguns provedores, como GoDaddy, as alterações nos registros DNS só entram em vigor quando você seleciona um link separado **Salvar Alterações**. 
