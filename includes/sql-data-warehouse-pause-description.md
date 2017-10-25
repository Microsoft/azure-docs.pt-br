
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Para economizar custos, é possível pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. Você não será cobrado por DWUs enquanto o banco de dados estiver em pausa.

Quando você pausa um banco de dados:

* Recursos de computação e memória são retornados ao pool de recursos disponíveis no data center
* Os custos de DWU são zero durante a pausa.
* O armazenamento de dados não é afetado e seus dados permanecem intactos. 
* O SQL Data Warehouse cancela todas as operações em execução ou em fila.

