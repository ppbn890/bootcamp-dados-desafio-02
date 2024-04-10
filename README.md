# Bootcamp Dados com Python - Digital Inovation One (Desafio 02)

**Link Principal**: [Ver Projeto](https://app.powerbi.com/links/xSKy6wCgkd?ctid=5302e0ca-772c-481a-9c22-0624b80be5ee&pbi_source=linkShare)

Projeto onde realizamos a criação de um banco de dados em na plataforma Azure, persistimos o banco de dados populando o mesmo com informações sobre uma empresa e seus funcionários, conectamos o banco de dados da Azure no Power BI, realizamos transformações e criamos visões desses dados e criamos um relatório para validar a estrutura das informações.

Competências trabalhadas no projeto:

- Power BI
- Excel
- Coleta de dados
- Extração de dados
- Transformação de dados
- SQL
- MySQL
- MySQL Workbench
- Azure

## Contato

- Nome: Paulo Braga
- E-mail: paulobraga@pauloads.com.br
- Linkedin: https://www.linkedin.com/in/paulo-b-neto/

## Autores

- [@ppbn890](https://github.com/ppbn890)

## Passo a Passo das ações realizadas no projeto:

- Criei o banco de dados via bash na azure
- Conectei o banco de dados com o workbench
- Criei as tabelas com o workbench
- Existiam alguns problemas e erros no código fornecido como passo a passo, resolvi adaptando a sintaxe, por exemplo:
	
> Onde tinha `sql alter table departament drop  departament_ibfk_1; deveria ser` deveria ser `alter table departament drop foreign key departament_ibfk_1;` (se tentamos com a query fornecida o SQL entende que queremos retirar um coluna e não uma constraint e sempre apresenta o erro de não estar achando a coluna para ser excluída.

> Sempre que tentava popular a tabela employee era apresentado um erro de constraint da foreing key. Depois de pensar um pouco vi que o SQL estava apresentando o erro porque ele não achava a referência para a qual a foreign key estava apontando. Então excluí a constraint, foram populadas as tabelas, depois disso consegui definir a foreign key referenciando Super_ssn. Isso resolveu o problema.

- Populei todas as tabelas com o workbench
- Fiz a validação na Azure e vi que todos os dados estavam corretos
- Foram retiradas as colunas que não iria usar
- Fiz a modificação dos tipos de dados das tabelas
- Avaliação preliminar dos dados
- Foi modificado o valor de salário para decimal fixo
- Preferi deixar o null no Super_ssn do gerente geral pois sabemos do que se trata quando vendo que o valor é null, se colocar qualquer outra coisa outras pessoas podem não entender do que se trata a questão
- Fiz a separação de informações da coluna Adrees. Usei separar colunas primeiramente usando o caractere na extrema direita e depois mais duas vezes usando o caractere separador na extrema esquerda. Assim consegui separar apropriadamente os endereços de cidades que tinham nomes contendo hífen como fire-oak
- Foram reordenadas as colunas separadas para ficar no padrão rua, número, cidade e estado.
- Fiz a mescla das tabelas employee e departament, depois expandi a visualização das informações da tabela departament na tabela resultante da mescla. Usei os atributos employee.Dno e departament.Dnumber como referência para a ação.
- Renomeei a tabela resultante da mescla para employee_departament
- Eliminei a coluna Dno pois estava redundante com a coluna Dnumber da tabela resultante
- Renomeei as colunas da tabela para ficarem mais simples de serem entendidos
- Realizei a junção dos colaboradores com os seus gerentes utilizando o self join na seguinte query:

	```sql
	select concat(t1.Fname, " ", t1.Minit, " ", t1.Lname) as Employee, 
	if (t1.Super_ssn is NULL, "Company Director", concat(t2.Fname, " ", t2.Minit, " ", t2.Lname)) as Manager
	from employee t1
	join employee t2 on t1.Super_ssn = t2.Ssn 
	or (t1.Super_ssn is NULL and t1.Ssn = t2.Ssn);
	```
	- Renomeei essa tabela para employee_manager

- Mesclei as colunas Fname, Minit e Lname  da tabela employee_departament em uma coluna chamada Fullname separando os valores por espaços
- Juntei a tabelas employee_departament na tabela employee_manager usando os atributos employee_department.Fullname e employee_manager.employee como referências para a junção
- Usei a visão expandida da junção para visualizar as informações de forma integral
- Removi a coluna Fullname e Super_ssn pois estavam redundantes
- Renomeei os cabeçalhos para melhor entendimento da tabela resultante
- Fiz a junção das informações de localização do departamento mais o nome do departamento separando as informações com o separador personalizado ", ". Podemos utilizar o mesclar aqui pois não temos nenhum valor repetido dentro dos valores que serão mesclados, eles inclusive estão formando informações únicas que serão facilmente localizáveis no futuro.
- Renomeei a tabela resultante para employee_manager_departament
- Foi extraída tabela com a quantidade de funcionários por gerente usando SQL:

	```sql
	select count(*) as Employee, 
	concat(t2.Fname, " ", t2.Minit, " ", t2.Lname) as Manager
	from employee t1
	join employee t2 on t1.Super_ssn = t2.Ssn 
	or (t1.Super_ssn is NULL and t1.Ssn = t2.Ssn)
	group by 2;
	```
