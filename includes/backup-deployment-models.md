O serviço de Backup do Azure tem dois tipos de cofres – o cofre de Backup e o cofre de Serviços de Recuperação. O Cofre de Backup veio primeiro. Em seguida, o Cofre dos Serviços de Recuperação surgiu no mercado para oferecer suporte às implantações expandidas do Gerenciador de Recursos. A Microsoft recomenda o uso das implantações do Resource Manager, a menos que você exija especificamente uma implantação Clássica.

| **Implantação** | **Portal** | **Cofre** |
| --- | --- | --- |
| Clássico |[Clássico](https://manage.windowsazure.com) |Backup |
| Gerenciador de Recursos |[As tabelas](https://portal.azure.com) |Serviços de Recuperação |

> [!NOTE]
> Cofres de backup não podem ser usados para proteger soluções implantadas pelo Resource Manager. No entanto, você pode usar um cofre de Serviços de Recuperação para proteger VMs e servidores implantados de modo clássico.  
> 
> 

