# Atividade Biblioteca - Vinicius Perdigão

# Criando tabelas do DW

- 
    
    ```sql
    -- ### EDITORA
      -- Criando tabela dw_editora
    CREATE TABLE biblioteca.dw_editora LIKE biblioteca.origem_editora;
    
      -- Alterando os DataTypes da tabela dw_editora
    ALTER TABLE `biblioteca`.`dw_editora` 
    ADD COLUMN `Numero` INT NULL AFTER `Logradouro`,
    ADD COLUMN `CEP` INT NULL AFTER `Cidade`,
    ADD COLUMN `Bairro` VARCHAR(40) NULL AFTER `CEP`,
    ADD COLUMN `Estado` VARCHAR(20) NULL AFTER `Bairro`,
    ADD COLUMN `Pais` VARCHAR(3) NULL AFTER `Estado`,
    ADD COLUMN `Tel1` VARCHAR(9) NULL AFTER `Pais`,
    ADD COLUMN `Tel2` VARCHAR(9) NULL AFTER `Tel1`,
    CHANGE COLUMN `ID_Editora` `ID_Editora` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `Nome_Editora` `Nome_Editora` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `Logradouro` `Logradouro` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `Cidade` `Cidade` VARCHAR(20) NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Editora`);
    
      -- Populando tabela dw_editora
    INSERT INTO biblioteca.dw_editora (Nome_Editora, Logradouro,Numero,Cidade)
    SELECT Nome_Editora, SUBSTRING_INDEX(Logradouro, (SUBSTRING_INDEX(Logradouro, ' ', -1)),1), SUBSTRING_INDEX(Logradouro, ' ', -1), Cidade FROM origem_editora;
    
    -- ### AUTOR
      -- Criando tabela dw_autor
    CREATE TABLE biblioteca.dw_autor LIKE biblioteca.origem_autor;
      
      -- Alterando os DataTypes da tabela DW_Autor
    ALTER TABLE `biblioteca`.`dw_autor` 
    ADD COLUMN `Pais_Autor` VARCHAR(2) NULL DEFAULT NULL AFTER `Nome_Autor`,
    CHANGE COLUMN `ID_Autor` `ID_Autor` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `Nome_Autor` `Nome_Autor` VARCHAR(40) NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Autor`);
    
      -- Populando a tabela DW_Autor
    INSERT INTO biblioteca.dw_autor (Nome_Autor, Pais_Autor)
    SELECT Nome_Autor, null FROM origem_autor;
    
    -- ### OBRA
      -- Criando tabela dw_obra
    CREATE TABLE biblioteca.dw_obra LIKE biblioteca.origem_obra;
    
      -- Alterando os DataTypes da tabela dw_obra
    ALTER TABLE `biblioteca`.`dw_obra` 
    CHANGE COLUMN `ID_Obra` `ID_Obra` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `Titulo_Obra` `Titulo_Obra` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `Gênero` `Gênero` VARCHAR(20) NULL DEFAULT NULL ,
    CHANGE COLUMN `Data_Publicação` `Data_Publicação` DATE NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Obra`);
    
      -- Populando tabela dw_obra
    INSERT INTO biblioteca.dw_obra (ID_Editora,ID_Autor,Titulo_Obra,Numero_Publicação,Gênero,Data_Publicação)
    SELECT ID_Editora,ID_Autor,Titulo_Obra,Numero_Publicação,Gênero,STR_TO_DATE(Data_Publicação,'%d/%m/%Y') FROM origem_obra;
    
    -- ### ESTOQUE
      -- Criando tabela dw_estoque
    CREATE TABLE biblioteca.dw_estoque LIKE biblioteca.origem_estoque;
    
      -- Alterando os DataTypes da tabela dw_estoque
    ALTER TABLE `biblioteca`.`dw_estoque` 
    CHANGE COLUMN `ID_Estoque` `ID_Estoque` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `ID_Obra` `ID_Obra` INT NOT NULL ,
    CHANGE COLUMN `Valor_Unitario` `Valor_Unitario` DECIMAL(10,2) NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Estoque`, `ID_Obra`);
    
    -- Populando tabela dw_estoque
    INSERT INTO biblioteca.dw_estoque (ID_Obra,Quantidade_Livro,Valor_Unitario)
    SELECT ID_Obra,Quantidade_Livro,REPLACE(REPLACE(Valor_Unitario,'R$ ',''),',','.') FROM origem_estoque;
    
    -- ### USUARIO
     -- Criando tabela dw_usuario
    CREATE TABLE biblioteca.dw_usuario LIKE biblioteca.origem_usuario;
    
     -- Alterando os DataTypes da tabela dw_usuario
    ALTER TABLE `biblioteca`.`dw_usuario` 
    ADD COLUMN `Estado` VARCHAR(45) NULL DEFAULT NULL AFTER `Cidade`,
    ADD COLUMN `Pais` VARCHAR(3) NULL DEFAULT NULL AFTER `Estado`,
    ADD COLUMN `Tel1` VARCHAR(9) NULL DEFAULT NULL AFTER `Pais`,
    ADD COLUMN `Tel2` VARCHAR(9) NULL DEFAULT NULL AFTER `Tel1`,
    ADD COLUMN `CPF` VARCHAR(14) NULL DEFAULT NULL AFTER `Tel2`,
    CHANGE COLUMN `ID_USUARIO` `ID_Usuario` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `NOME_USUARIO` `Nome_Usuario` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `LOGRADOURO` `Endereco` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `BAIRRO` `Numero` INT NULL DEFAULT NULL ,
    CHANGE COLUMN `TELEFONE` `CEP` INT NULL DEFAULT NULL ,
    CHANGE COLUMN `CEP` `Bairro` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `CPF` `Cidade` VARCHAR(20) NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Usuario`);
    
    -- Populando tabela dw_usuario
    INSERT INTO biblioteca.dw_usuario (Nome_Usuario, Endereco, Numero, CEP, Bairro, Tel1,CPF)
    SELECT NOME_USUARIO, SUBSTRING_INDEX(LOGRADOURO, (SUBSTRING_INDEX(LOGRADOURO, ' ', -1)),1), SUBSTRING_INDEX(LOGRADOURO, ' ', -1), REPLACE(CEP,'-',''), BAIRRO, TELEFONE, CPF FROM biblioteca.origem_usuario;
    
    -- ### CARGO
    -- Criando tabela dw_cargo
    CREATE TABLE dw_cargo LIKE origem_cargo;
    
    -- Alterando os Datatypes da tabela dw_cargo.
    ALTER TABLE `biblioteca`.`dw_cargo` 
    CHANGE COLUMN `ID_Cargo` `ID_Cargo` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `Nome_Cargo` `Nome_Cargo` VARCHAR(25) NULL DEFAULT NULL ,
    CHANGE COLUMN `Salario` `Salario` INT NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Cargo`);
    
    -- Populando a tabela dw_cargo com os dados da origem. 
    INSERT INTO biblioteca.dw_cargo (Nome_Cargo, Salario)
    SELECT Nome_Cargo, REPLACE(REPLACE(substring_index(Salario, ',',1),'R$ ',''),'.','') FROM origem_cargo;
    
    -- ### DEPARTAMENTO
    -- Criando tabela dw_departamento
    CREATE TABLE `dw_departamento` LIKE origem_departamento;
    
    -- Alteração do Datatype da tabela origem_departamento
    ALTER TABLE `biblioteca`.`dw_departamento` 
    CHANGE COLUMN `ID_Departamento` `ID_Departamento` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `ID_Cargo` `ID_Cargo` INT NOT NULL ,
    CHANGE COLUMN `Nome_Departamento` `Nome_Departamento` VARCHAR(20) NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Departamento`, `ID_Cargo`);
    
    -- Populando a tabela dw_departamento com os dados da origem. 
    INSERT INTO biblioteca.dw_departamento (ID_Cargo, Nome_Departamento)
    SELECT ID_Cargo, Nome_Departamento FROM origem_departamento;
    
    -- ### FUNCIONARIO
    -- Criando tabela dw_funcionario
    CREATE TABLE dw_funcionario LIKE origem_funcionario;
    
    -- Alterando os Datatypes da tabela DW_Funcionario.
    ALTER TABLE `biblioteca`.`dw_funcionario` 
    ADD COLUMN `Dt_Nascimento` DATE NULL DEFAULT NULL AFTER `DATA_DEMISSAO`,
    CHANGE COLUMN `ID_Funcionario` `ID_Funcionario` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `Nome_Funcionario` `Nome_Funcionario` VARCHAR(40) NULL DEFAULT NULL ,
    CHANGE COLUMN `DATA_ADMISSAO` `DATA_ADMISSAO` DATE NULL DEFAULT NULL ,
    CHANGE COLUMN `DATA_DEMISSAO` `DATA_DEMISSAO` DATE NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Funcionario`);
    
    -- Populando a tabela do DW_Funcionario. 
    INSERT INTO biblioteca.dw_funcionario (ID_Cargo, ID_Departamento, Nome_Funcionario, DATA_ADMISSAO, DATA_DEMISSAO)
    SELECT ID_Cargo, ID_Departamento, Nome_Funcionario, STR_TO_DATE(DATA_ADMISSAO,'%d/%m/%Y'), STR_TO_DATE(DATA_DEMISSAO,'%d/%m/%Y') FROM origem_funcionario;
    
    -- ### EMPRESTIMO
      -- Criando tabela dw_emprestimo
    CREATE TABLE biblioteca.dw_emprestimo LIKE biblioteca.origem_emprestimo;
    
    -- Alterando os DataTypes da tabela dw_emprestimo
    ALTER TABLE `biblioteca`.`dw_emprestimo` 
    CHANGE COLUMN `ID_Emprestimo` `ID_Emprestimo` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `ID_Funcionario` `ID_Funcionario` INT NOT NULL ,
    CHANGE COLUMN `ID_Usuario` `ID_Usuario` INT NOT NULL ,
    CHANGE COLUMN `ID_Estoque` `ID_Estoque` INT NOT NULL ,
    CHANGE COLUMN `ID_Obra` `ID_Obra` INT NOT NULL ,
    CHANGE COLUMN `Data_Emprestimo` `Data_Emprestimo` DATE NULL DEFAULT NULL ,
    CHANGE COLUMN `Hora_Emprestimo` `Hora_Emprestimo` TIME NULL DEFAULT NULL ,
    CHANGE COLUMN `Data_Entrega` `Data_Entrega` DATE NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Emprestimo`, `ID_Funcionario`, `ID_Usuario`, `ID_Estoque`, `ID_Obra`);
    
    -- Populando tabela dw_emprestimo
    INSERT INTO biblioteca.dw_emprestimo (ID_Funcionario, ID_Usuario, ID_Estoque, ID_Obra, Data_Emprestimo, Hora_Emprestimo,Data_Entrega)
    SELECT ID_Funcionario, ID_Usuario, ID_Estoque, ID_Obra, STR_TO_DATE(Data_Emprestimo,'%d/%m/%Y'), STR_TO_DATE(Hora_Emprestimo, '%H:%i'), STR_TO_DATE(Data_Entrega,'%d/%m/%Y') FROM biblioteca.origem_emprestimo;
    
    -- ### RESERVA
    -- Criando tabela dw_reserva
    CREATE TABLE biblioteca.dw_reserva LIKE biblioteca.origem_reserva;
    
    -- Alterando os DataTypes da tabela dw_reserva
    ALTER TABLE `biblioteca`.`dw_reserva` 
    CHANGE COLUMN `ID_Reserva` `ID_Reserva` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `ID_Emprestimo` `ID_Emprestimo` INT NOT NULL ,
    CHANGE COLUMN `ID_Funcionario` `ID_Funcionario` INT NOT NULL ,
    CHANGE COLUMN `ID_Usuario` `ID_Usuario` INT NOT NULL ,
    CHANGE COLUMN `ID_Estoque` `ID_Estoque` INT NOT NULL ,
    CHANGE COLUMN `ID_Obra` `ID_Obra` INT NOT NULL ,
    CHANGE COLUMN `Status_Livro` `Status_Livro` VARCHAR(20) NULL DEFAULT NULL ,
    CHANGE COLUMN `Data_Reserva` `Data_Reserva` DATE NULL DEFAULT NULL ,
    CHANGE COLUMN `Hora_Reserva` `Hora_Reserva` TIME NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Reserva`, `ID_Emprestimo`, `ID_Funcionario`, `ID_Usuario`, `ID_Estoque`, `ID_Obra`);
    
    -- Populando tabela dw_reserva
    INSERT INTO biblioteca.dw_reserva (ID_Emprestimo, ID_Funcionario, ID_Usuario, ID_Estoque, ID_Obra, Status_Livro, Data_Reserva, Hora_Reserva)
    SELECT ID_Emprestimo, ID_Funcionario, ID_Usuario, ID_Estoque, ID_Obra, Status_Livro, str_to_date(Data_Reserva,'%d/%m/%Y'), str_to_date(Hora_Reserva, '%H:%i') FROM biblioteca.origem_reserva;
    
      
      
    -- ### DEVOLUCAO
    -- Criando tabela dw_devolucao
    CREATE TABLE biblioteca.dw_devolucao LIKE biblioteca.origem_devolucao;
    
    -- Alterando os dados da tabela
    ALTER TABLE `biblioteca`.`dw_devolucao` 
    ADD COLUMN `Valor_da_Multa` DECIMAL NULL AFTER `Multa_Atraso`,
    ADD COLUMN `Dias_em_Atraso` INT NULL AFTER `Valor_da_Multa`,
    CHANGE COLUMN `ID_Devolucao` `ID_Devolucao` INT NOT NULL AUTO_INCREMENT ,
    CHANGE COLUMN `ID_Funcionario` `ID_Funcionario` INT NOT NULL ,
    CHANGE COLUMN `ID_Emprestimo` `ID_Emprestimo` INT NOT NULL ,
    CHANGE COLUMN `ID_Usuario` `ID_Usuario` INT NOT NULL ,
    CHANGE COLUMN `ID_Estoque` `ID_Estoque` INT NOT NULL ,
    CHANGE COLUMN `ID_Obra` `ID_Obra` INT NOT NULL ,
    CHANGE COLUMN `Data_Devolucao` `Data_Devolucao` DATE NULL DEFAULT NULL ,
    CHANGE COLUMN `Hora_Devolucao` `Hora_Devolucao` TIME NULL DEFAULT NULL ,
    CHANGE COLUMN `Multa_Atraso` `Multa_Atraso` VARCHAR(2) NULL DEFAULT NULL ,
    ADD PRIMARY KEY (`ID_Devolucao`, `ID_Funcionario`, `ID_Emprestimo`, `ID_Usuario`, `ID_Estoque`, `ID_Obra`);
    
    -- Populando tabela dw_devolucao
    INSERT INTO biblioteca.dw_devolucao (ID_Funcionario, ID_Emprestimo, ID_Usuario, ID_Estoque, ID_Obra, Data_Devolucao, Hora_Devolucao, Multa_Atraso)
    SELECT ID_Funcionario, ID_Emprestimo, ID_Usuario, ID_Estoque, ID_Obra, STR_TO_DATE(Data_Devolucao,'%d/%m/%Y'), str_to_date(Hora_Devolucao, '%H:%i'), Multa_Atraso FROM biblioteca.origem_devolucao;
    
    -- ### CRIANDO CHAVES ESTRANGEIRAS
    -- ### FK's TABELA OBRA
    ALTER TABLE `biblioteca`.`dw_obra` 
    ADD INDEX `fk_obra_editora_idx` (`ID_Editora` ASC) VISIBLE,
    ADD INDEX `fk_obra_autor_idx` (`ID_Autor` ASC) VISIBLE;
    ;
    ALTER TABLE `biblioteca`.`dw_obra` 
    ADD CONSTRAINT `fk_obra_editora`
      FOREIGN KEY (`ID_Editora`)
      REFERENCES `biblioteca`.`dw_editora` (`ID_Editora`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_obra_autor`
      FOREIGN KEY (`ID_Autor`)
      REFERENCES `biblioteca`.`dw_autor` (`ID_Autor`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION;
    
    -- ### FK's TABELA ESTOQUE
    
    ALTER TABLE `biblioteca`.`dw_estoque` 
    ADD INDEX `fk_estoque_obra_idx` (`ID_Obra` ASC) VISIBLE;
    ;
    ALTER TABLE `biblioteca`.`dw_estoque` 
    ADD CONSTRAINT `fk_estoque_obra`
      FOREIGN KEY (`ID_Obra`)
      REFERENCES `biblioteca`.`dw_obra` (`ID_Obra`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION;
    
    -- ### FK's TABELA DEPARTAMENTO
    
    ALTER TABLE `biblioteca`.`dw_departamento` 
    ADD INDEX `fk_departamento_cargo_idx` (`ID_Cargo` ASC) VISIBLE;
    ;
    ALTER TABLE `biblioteca`.`dw_departamento` 
    ADD CONSTRAINT `fk_departamento_cargo`
      FOREIGN KEY (`ID_Cargo`)
      REFERENCES `biblioteca`.`dw_cargo` (`ID_Cargo`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION;
    
    -- ### FK's TABELA FUNCIONARIO
    
    ALTER TABLE `biblioteca`.`dw_funcionario` 
    ADD INDEX `fk_funcionario_departamento_idx` (`ID_Cargo` ASC, `ID_Departamento` ASC) VISIBLE;
    ;
    ALTER TABLE `biblioteca`.`dw_funcionario` 
    ADD CONSTRAINT `fk_funcionario_departamento`
      FOREIGN KEY (`ID_Cargo` , `ID_Departamento`)
      REFERENCES `biblioteca`.`dw_departamento` (`ID_Cargo` , `ID_Departamento`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION;
    
    -- ### FK's TABELA EMPRESTIMO
    
    ALTER TABLE `biblioteca`.`dw_emprestimo` 
    ADD INDEX `fk_emprestimo_funcionario_idx` (`ID_Funcionario` ASC) VISIBLE,
    ADD INDEX `fk_emprestimo_usuario_idx` (`ID_Usuario` ASC) VISIBLE,
    ADD INDEX `fk_emprestimo_estoque_idx` (`ID_Estoque` ASC) VISIBLE,
    ADD INDEX `fk_emprestimo_obra_idx` (`ID_Obra` ASC) VISIBLE;
    ;
    ALTER TABLE `biblioteca`.`dw_emprestimo` 
    ADD CONSTRAINT `fk_emprestimo_funcionario`
      FOREIGN KEY (`ID_Funcionario`)
      REFERENCES `biblioteca`.`dw_funcionario` (`ID_Funcionario`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_emprestimo_usuario`
      FOREIGN KEY (`ID_Usuario`)
      REFERENCES `biblioteca`.`dw_usuario` (`ID_Usuario`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_emprestimo_estoque`
      FOREIGN KEY (`ID_Estoque`)
      REFERENCES `biblioteca`.`dw_estoque` (`ID_Estoque`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_emprestimo_obra`
      FOREIGN KEY (`ID_Obra`)
      REFERENCES `biblioteca`.`dw_obra` (`ID_Obra`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION;
    
    -- ### FK's TABELA DEVOLUCAO
    
    ALTER TABLE `biblioteca`.`dw_devolucao` 
    ADD INDEX `fk_devolucao_funcionario_idx` (`ID_Funcionario` ASC) VISIBLE,
    ADD INDEX `fk_devolucao_emprestimo_idx` (`ID_Emprestimo` ASC) VISIBLE,
    ADD INDEX `fk_devolucao_usuario_idx` (`ID_Usuario` ASC) VISIBLE,
    ADD INDEX `fk_devolucao_obra_idx` (`ID_Obra` ASC) VISIBLE;
    ;
    ALTER TABLE `biblioteca`.`dw_devolucao` 
    ADD CONSTRAINT `fk_devolucao_funcionario`
      FOREIGN KEY (`ID_Funcionario`)
      REFERENCES `biblioteca`.`dw_funcionario` (`ID_Funcionario`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_devolucao_emprestimo`
      FOREIGN KEY (`ID_Emprestimo`)
      REFERENCES `biblioteca`.`dw_emprestimo` (`ID_Emprestimo`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_devolucao_estoque`
      FOREIGN KEY (`ID_Estoque`)
      REFERENCES `biblioteca`.`dw_estoque` (`ID_Estoque`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_devolucao_usuario`
      FOREIGN KEY (`ID_Usuario`)
      REFERENCES `biblioteca`.`dw_usuario` (`ID_Usuario`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION,
    ADD CONSTRAINT `fk_devolucao_obra`
      FOREIGN KEY (`ID_Obra`)
      REFERENCES `biblioteca`.`dw_obra` (`ID_Obra`)
      ON DELETE NO ACTION
      ON UPDATE NO ACTION;
    
    SELECT * FROM dw_reserva as r
    join dw_emprestimo as e
    on r.ID_Emprestimo = e.ID_Emprestimo
    ```
    

# Consultas das Perguntas de Negócio

1 - A Diretora Camilla Prado solicitou uma pesquisa que informe todas as obras cadastradas no acervo ordenadas por data de publicação.

```sql
SELECT O.Titulo_Obra as Obra,
			A.Nome_Autor as Autor, 
			E.Nome_Editora as Editora, 
			O.Data_Publicação as 'Data de Publicação'
FROM dw_obra AS O
JOIN dw_editora AS E
ON O.ID_Editora = E.ID_Editora
JOIN dw_autor AS A
ON O.ID_Autor = A.ID_Autor
ORDER BY O.Data_Publicação
```

2 - O Governador vai doar duzentos livros para a Biblioteca, mas só irá doar se a biblioteca tiver menos de 300 obras. O Gerente Márcio Tales solicitou que fosse feita a contagem de quantas obras a Biblioteca possui atualmente.

```sql
SELECT count(distinct titulo_obra) as 'Total de Obras' FROM dw_obra;
```

3 -  A Gerência solicitou uma pesquisa para saber quais datas ocorreram empréstimos de livros e a quantidade emprestada. A consulta deverá retornar apenas um registro para cada data.

```sql
select count(id_emprestimo) as Total_de_Emprestimos, Data_Emprestimo
from dw_emprestimo
group by Data_Emprestimo
order by Total_de_Emprestimos DESC;
```

4 - O Funcionário João Paulo Assistente de RH solicitou uma pesquisa que informasse todos os empréstimos que a Recepcionista Alice Meire fez no horário das 8hs as 9hs.

```sql
select F.Nome_Funcionario as 'Nome do Funcionario', 
			O.Titulo_Obra as 'Titulo da Obra', 
			E.hora_emprestimo as 'Horário do Empréstimo' 
from dw_emprestimo as E
join dw_funcionario as F
on E.ID_Funcionario = F.ID_Funcionario
join dw_obra as O
on E.ID_Obra = O.ID_Obra
where E.ID_Funcionario = 8
and E.Hora_Emprestimo between '08:00' AND '09:00';
```

5 - A Diretoria solicitou uma pesquisa sobre devolução de livros entre as datas 29/03/2012 a 02/02/2013.

```sql
select U.Nome_Usuario as 'Nome Usuário', 
			O.Titulo_Obra as 'Titulo da Obra', 
			D.Data_Devolucao as 'Data Devolução', 
			D.Hora_Devolucao as 'Hora Devolução', 
			D.Multa_Atraso as 'Multa por Atraso' 
from dw_devolucao as D
inner join dw_usuario as U
on D.ID_Usuario = U.ID_Usuario
inner join dw_obra as O
on D.ID_Obra = O.ID_Obra
where Data_Devolucao between '2012-03-29' and '2013-02-02'
order by Data_Devolucao;
```

6 - A Gerência solicitou uma pesquisa para saber quais reservas de livros que foram feitas com data maior ou igual a 18/08/2011.

```sql
select O.Titulo_Obra as 'Titulo da Obra',
			R.Status_Livro as 'Status da Reserva' , 
			R.Data_Reserva as 'Data da Reserva', 
			R.Hora_Reserva as 'Hora da Reserva',
			U.Nome_Usuario as 'Resevado por' 
from dw_reserva as R
inner join dw_usuario as U
on R.ID_Usuario = U.ID_Usuario
inner join dw_obra as O
on R.ID_Obra = O.ID_Obra
where Data_Reserva >= '2011-08-18'
AND R.Status_Livro = 'Reservado';
```

7 - A área de RH solicitou uma pesquisa para saber quais devoluções de livros foram feitas antes de 29/03/2012.

```sql
select O.Titulo_Obra,D.Data_Devolucao, 
			D.Hora_Devolucao,D.Multa_Atraso 
from dw_devolucao as D
inner join dw_obra as O
on D.ID_Obra = O.ID_Obra
where Data_Devolucao < '2012-03-29';
```

8 - A Gerência solicitou uma pesquisa para saber quais obras existem no acervo que são diferentes dos títulos das obras ‘O Conde de Monte Cristo’ e ‘Filhos e Amantes’ .

```sql
select O.Titulo_Obra, 
			O.Gênero, 
			O.Data_Publicação 
from dw_obra as O
WHERE Titulo_Obra <> 'O Conde de Monte Cristo' 
and Titulo_Obra <> 'Filhos e Amantes' ;
```

9 - O Funcionário João Paulo solicitou uma pesquisa para saber quantas obras do gênero ‘Ficção’ existem no acervo.

```sql
select count(*) as 'Total Livros Ficção' 
from dw_obra
where Gênero='Ficção' ;
```

10 - A Diretoria solicitou uma pesquisa para identificar qual o livro possuiu a maior quantidade em estoque.

```sql
select O.Titulo_Obra as 'Titulo da Obra', 
			E.Quantidade_Livro as 'Maior Quantidade em Estoque' 
from dw_estoque as E
inner join dw_obra O
on E.ID_Obra = O.ID_Obra
ORDER BY Quantidade_Livro DESC
LIMIT 1;
```

11 - O Financeiro precisa precisa saber qual é o livro tem a menor quantidade em estoque e quantas vezes ele foi emprestado para que seja feita uma analise para compra de mais alguns exemplares.

```sql
select O.Titulo_Obra as 'Titulo da Obra',
			 EM.Data_Emprestimo as 'Data Emprestimo', 
			EM.Hora_Emprestimo as 'Hora Emprestimo', 
			EM.Data_Entrega as 'Data Entrega' 
from dw_emprestimo as EM
inner join dw_obra as O
on EM.ID_Obra = O.ID_Obra
where EM.ID_Obra = (
select ID_Obra from dw_estoque
where Quantidade_Livro = (select min(Quantidade_Livro) from dw_estoque));
```

12 - A área de RH precisa identificar a quantidade total dos empréstimos feitos por cada funcionário ativos.

```sql
select E.ID_Funcionario as 'ID Funcionario Ativo',
			 FU.Nome_Funcionario as 'Nome Funcionario',
			count(DISTINCT E.ID_Emprestimo) as 'Total de Emprestimos' 
from dw_emprestimo as E
inner JOIN dw_funcionario as FU
ON E.ID_Funcionario = FU.ID_Funcionario
WHERE E.ID_Funcionario NOT IN (select group_concat(F.id_funcionario) 
															from dw_funcionario as F
															where F.DATA_DEMISSAO <> '9999-01-01')
group by FU.Nome_Funcionario;
```

13 - A Diretoria do precisa identificar as obras com os maiores números de publicações agrupados por gênero.

```sql
select Titulo_Obra as 'Título da Obra',
			 Numero_Publicação as 'Número de Publicações', 
			 Gênero, 
		Data_Publicação as 'Data de Publicação', 
	max(Numero_Publicação) as Total_Publicacoes 
from dw_obra
group by Gênero
order by Total_Publicacoes DESC;
```

14 - A Funcionária Alice Meire solicitou a alteração da obra ‘Discurso do Método’ para o gênero Político.

```sql
UPDATE dw_obra
SET Gênero = 'Político'
WHERE Titulo_Obra = 'Discurso do Método';
```

15 - A Recepcionista Alice Meire solicitou a alteração do bairro do usuário ‘Alberto Roberto’ que morava no ‘IAPI’ e agora mora no bairro de ‘Perdizes’.

```sql
UPDATE dw_usuario
SET Bairro = 'Perdizes'
WHERE Nome_Usuario = 'Alberto Roberto';
```

16 - A Biblioteca recebeu a visita de um grupo de alunos, mas após a visita foi verificado desaparecimento de três livros, as obras que sumiram do acervo foram ‘Filho Nativo’, ‘Vidas Secas’ e ‘Dom Casmurro’, com isto será necessária a alteração da quantidade de livros no estoque de cada obra.

```sql
UPDATE dw_estoque
SET Quantidade_Livro = Quantidade_Livro - 1
WHERE ID_Obra IN (
    SELECT ID_Obra
    FROM dw_obra
    WHERE Titulo_Obra IN ('Filho Nativo', 'Vidas Secas', 'Dom Casmurro')
);
```

17 -  A Recepcionista Claudia Cristina não conseguiu terminar o cadastro de cinco usuários que passaram pela Biblioteca, foi solicitado a inserção desses usuários. São eles:
31, Alfredo Tenttoni, Rua Amazonas 58, Pirai, 6549-5421, 02170-251, 294.264.875-32
32, Cindy Crall, Rua Ipiranga 123, Vila Cristal, 5846-6577, 02182-637, 122.147.655-49
33, Rubens Pardo, Avenida dos Monges 51, Campo Grande, 5184-8978, 52412-365, 654.586.472-98
34, Carlos Pracidelli, Travessa dos Irmãos 48, Cotia, 8945-7986, 23124-005, 341.251.651-75
35, Ernesto Coimbra, Avenida Ampére 414, Jardim Elvira, 5844-2654, 05728-368, 193.107.214-35

```sql
INSERT INTO dw_usuario (Nome_Usuario,Endereco,Numero,Bairro,Tel1,CEP,CPF)
VALUES
('Alfredo Tenttoni', 'Rua Amazonas', 58, 'Pirai', '6549-5421', '02170251', '294.264.875-32'),
('Cindy Crall', 'Rua Ipiranga', 123, 'Vila Cristal', '5846-6577', '02182637', '122.147.655-49'),
('Rubens Pardo', 'Avenida dos Monges', 51, 'Campo Grande', '5184-8978', 52412365, '654.586.472-98'),
('Carlos Pracidelli', 'Travessa dos Irmãos', 48, 'Cotia', '8945-7986', '23124005', '341.251.651-75'),
('Ernesto Coimbra', 'Avenida Ampére', 414, 'Jardim Elvira', '5844-2654', 05728368, '193.107.214-35');
```

18  - Gerência solicitou uma consulta para verificar se existe duplicidade de usuários no Banco de Dados.

```sql
select *, count(*) as 'Numero de Registros' 
from dw_usuario
group by CPF;
```

19 - A Recepção recebeu a informação que existem dois usuários duplicados na base, será necessária a exclusão de um desses registros.

```sql
CREATE TEMPORARY TABLE tmp_table
SELECT MAX(ID_Usuario) as ID_Usuario FROM dw_usuario GROUP BY Nome_Usuario,CPF;

SET FOREIGN_KEY_CHECKS = 0;

DELETE FROM dw_usuario
WHERE ID_Usuario NOT IN(SELECT ID_Usuario FROM tmp_table);

SET FOREIGN_KEY_CHECKS = 0;

DROP TABLE tmp_table;
```

20 - O Financeiro solicitou a inserção do valor individual de cada obra. Crie um campo com o nome Valor_Livro na tabela Obra. Defina o tipo de dados que poderá ser aceito e o valor de cada título.

```sql
ALTER TABLE dw_obra ADD Valor_Livro DECIMAL(6,2) AFTER Data_Publicação;
```

21 - A Consultoria verificou que o campo Multa_Atraso está com o tamanho Varchar(2), foi solicitada a alteração do campo para Varchar(3).

```sql
ALTER TABLE dw_devolucao
MODIFY COLUMN Multa_Atraso VARCHAR(3);
```

22 - Foi verificado que o campo Multa_Atraso está com os registros preenchidos de forma errada, foi solicitada a alteração dos registros que forem 0 para Não e 1 para SIM.

```sql
ALTER TABLE dw_devolucao
ADD COLUMN col_auxiliar VARCHAR(3) AFTER Multa_Atraso;

UPDATE dw_devolucao
SET col_auxiliar = IF(Multa_Atraso = 1, 'Sim', 'Não');

ALTER TABLE dw_devolucao
DROP COLUMN Multa_Atraso;

ALTER TABLE dw_devolucao
CHANGE COLUMN col_auxiliar Multa_Atraso VARCHAR(3);
```

23 - A Diretoria solicitou a exclusão do campo Valor_Livro da tabela Obra pois, o pedido feito pelo Financeiro estava errado.

```sql
ALTER TABLE dw_obra
DROP COLUMN Valor_Livro;
```

24 - A Diretoria solicitou a lista de todos os livros que já foram emprestados mas foram entregues com atraso e os respectivos os nomes dos funcionários que fizeram os empréstimos.

```sql
select O.Titulo_Obra as 'Titulo do Livro',
			F.Nome_Funcionario as 'Nome do Funcionário', 
			E.Data_Emprestimo as 'Data de Empréstimo', 
			E.Data_Entrega as 'Data de Entrega', 
			D.Data_Devolucao as 'Data de Devolução', 
			D.Multa_Atraso as 'Multa por Atraso' 
from dw_emprestimo as E
inner join dw_devolucao as D
on E.ID_Emprestimo = D.ID_Emprestimo
inner join dw_funcionario as F
on E.ID_Funcionario = F.ID_Funcionario
inner join dw_obra as O
on E.ID_Obra = O.ID_Obra
WHERE D.Data_Devolucao > E.Data_Entrega;
```

25 -  A Gerência solicitou a lista de todos os livros, cujos autores não são brasileiros, que já foram devolvidos e o valor total de cada livro .

```sql
select O.Titulo_Obra,
			A.Nome_Autor,
			A.Pais_Autor,
			D.Data_Devolucao,
			E.Valor_Unitario 
from dw_devolucao as D
inner join dw_estoque as E
on D.ID_Obra = E.ID_Obra
inner join dw_obra as O
on D.ID_Obra = O.ID_Obra
inner join dw_autor as A
on O.ID_Autor = A.ID_Autor
WHERE D.ID_Obra IN (select ID_Obra from dw_obra
WHERE ID_Autor IN (SELECT ID_Autor from dw_autor
where Pais_Autor <> 'BR'));
```

26 - A área de RH solicitou a lista de todos usuários que fizeram empréstimo com o dia de entrega igual 21/08/2011.

```sql
select U.Nome_Usuario as 'Nome do Usuário', 
			U.Endereco, 
			U.Numero, 
			U.CEP, 
			U.Cidade, 
			U.Estado, 
			U.Pais, 
			U.Tel1 as 'Telefone 1', 
			U.Tel2 as 'Telefone 2', 
			U.CPF, 
			E.Data_Entrega as 'Data da Entrega'
from dw_emprestimo as E
inner join dw_usuario as U
on E.ID_Usuario = U.ID_Usuario
where Data_Entrega = '2011-08-21';
```

27 - O Financeiro solicitou a lista de todas as obras que tiveram data de publicação menor que 04/03/2013, sua respectiva quantidade e o seu valor unitario.

```sql
select O.Titulo_Obra, 
			O.Gênero, 
			O.Data_Publicação, 
			E.Quantidade_Livro, 
			E.Valor_Unitario 
from dw_Obra as O
LEFT JOIN dw_estoque as E
on O.ID_Obra = E.ID_Obra
where Data_Publicação < '2013-03-04'
```

28 - A área de RH solicitou a lista de todos os funcionários separados por ativos ou não, seus respectivos cargos e salários.

```sql
select F.Nome_Funcionario, 
			IF(DATA_DEMISSAO = '9999-01-01', 'Ativo','Inativo') as Situacao, 
			C.Nome_Cargo, 
			C.Salario 
from dw_funcionario as F
left join dw_cargo as C
on F.ID_Cargo = C.ID_Cargo
ORDER BY DATA_DEMISSAO, Salario DESC;
```

29 - A Gerência solicitou uma lista de todos os livros com os nomes dos autores, nomes das editoras e a quantidade de livros em estoque.

```sql
select O.Titulo_Obra, 
			O.Numero_Publicação,
			O.Gênero,
			O.Data_Publicação, 
			A.Nome_Autor, 
			ED.Nome_Editora, 
			ES.Quantidade_Livro from dw_obra as O
left join dw_autor as A
ON O.ID_Autor = A.ID_Autor
JOIN dw_editora as ED
ON ED.ID_Editora = O.ID_Editora
JOIN dw_estoque as ES
ON O.ID_Obra = ES.ID_Obra
ORDER BY Quantidade_Livro;
```

30 - A Diretoria solicitou a lista de todos os funcionários da Biblioteca com seus respectivos departamentos que tem idade entre 30 e 40 anos.

```sql
SELECT F.Nome_Funcionario as 'Nome Funcionario', 
			D.Nome_Departamento as 'Departamento', 
			F.Dt_Nascimento as 'Data de Nascimento',
			YEAR(CURDATE()) - YEAR(Dt_Nascimento) - 
				CASE 
						WHEN MONTH(CURDATE()) > MONTH(Dt_Nascimento) 
						OR (MONTH(CURDATE()) = MONTH(Dt_Nascimento) 
						AND DAY(CURDATE()) >= DAY(Dt_Nascimento)) 
    THEN 0 
    ELSE 1 
END AS Idade 
FROM dw_funcionario as F
left join dw_departamento as D
on F.ID_Departamento = D.ID_Departamento
WHERE YEAR(CURDATE()) - YEAR(Dt_Nascimento) - 
	CASE 
		WHEN MONTH(CURDATE()) > MONTH(Dt_Nascimento) 
    OR (MONTH(CURDATE()) = MONTH(Dt_Nascimento) 
		AND DAY(CURDATE()) >= DAY(Dt_Nascimento)) 
    THEN 0 
    ELSE 1 
END BETWEEN 30 AND 40
ORDER BY Idade
;
```

31 - O Financeiro solicitou a criação de uma visão que retorne o nome do livro, seu autor e o valor.

```sql
CREATE VIEW visao AS
SELECT Titulo_Obra as 'Nome do Livro' ,
			 A.Nome_Autor as 'Nome do Autor', 
			E.Valor_Unitario as 'Valor Unitário' 
from dw_obra as O
inner join dw_autor as A
on O.ID_Autor = A.ID_Autor
inner join dw_estoque AS E
on O.ID_Obra = E.ID_Obra;
```

32 - A Recepção solicitou uma lista com o código do livro, nome do livro cujo valor do livro seja maior que R$ 90,00.

```sql
select O.ID_Obra, 
			O.Titulo_Obra,
			E.Valor_Unitario
from dw_obra as O
join dw_estoque as E
on O.ID_Obra = E.ID_Obra
where Valor_Unitario > 90
order by Valor_Unitario;
```

33 - A área de RH solicitou a atualização do salário do Auxiliar Financeiro de 12% sobre o seu salário atual.

```sql
UPDATE dw_cargo
SET Salario = Salario * 1.12
WHERE Nome_Cargo = 'Auxiliar Financeiro'
```

34 - O Financeiro solicitou uma atualização da data de admissão da funcionária Alice Meire para o último dia do Mês que você se encontra atualmente.

```sql
UPDATE dw_funcionario 
SET DATA_ADMISSAO = LAST_DAY(CURDATE())
WHERE Nome_Funcionario = 'Alice Meire';
```

35 - - A Gerência solicitou uma lista alfabética com todos os dados da tabela Obra ordenada por data de publicação e gênero.

```sql
select * 
from dw_obra
order by Titulo_Obra, Gênero, Data_Publicação;
```

36 - A Recepção solicitou uma lista como todos os funcionários da Biblioteca com código, nome, e departamento, classificado pelo nome do funcionário que não emprestaram nenhum livro.

```sql
select F.ID_Funcionario , 
			F.Nome_Funcionario, 
			D.Nome_Departamento 
from dw_funcionario as F
inner join dw_departamento as D
on F.ID_Departamento = D.ID_Departamento
where ID_Funcionario NOT IN (select ID_Funcionario from dw_emprestimo)
ORDER BY Nome_Funcionario;
```

37 - - A Biblioteca solicitou uma lista que exiba a quantidade de logradouros de usuários agrupados por número do CEP.

```sql
select CEP, count(Endereco) as 'Quantidade Logradouros por CEP' 
from dw_usuario
GROUP BY CEP;
```

38 - - A Diretoria solicitou uma lista que exiba a quantidade de endereços agrupados por usuário

```sql
select Nome_Usuario, count(Endereco) as 'Quantidade Logradouros por CEP' 
from dw_usuario
GROUP BY Nome_Usuario;
```

39 - Foi solicitado a busca de informações de todas as obras que foram reservadas no dia 18/08/11 as 15:00 e o nome do responsável pela reserva.

```sql
select O.Titulo_Obra as 'Titulo da Obra', 
			R.Data_Reserva as 'Data da Reserva', 
			R.Hora_Reserva as 'Hora da Reserva', 
			U.Nome_Usuario as 'Reservado por' 
from dw_reserva as R
inner join dw_obra as O
on R.ID_Obra = O.ID_Obra
inner join dw_usuario as U
on R.ID_Usuario = U.ID_Usuario
where Data_Reserva = '2011-08-18'
AND Hora_Reserva = '15:00'
```

40 - O financeiro solicitou o levantamento da informação de quando as usuárias Emily Mall e Whitney Cinse pegaram livros emprestados, quais foram os livros e seus respectivos valores unitários.

```sql
select U.Nome_Usuario, 
			O.Titulo_Obra, 
			E.Data_Emprestimo, 
			ES.Valor_Unitario 
from dw_emprestimo as E
inner join dw_usuario as U
on E.ID_Usuario = U.ID_Usuario
inner join dw_estoque as  ES
on E.ID_Obra = ES.ID_Obra
inner join dw_obra as O
on E.ID_Obra = O.ID_Obra
where Nome_Usuario = 'Emily Mall' or Nome_Usuario ='Whitney Cinse';
```

41 - - Hoje é aniversario da biblioteca e para comemorar foram comprados presentes a todos os usuários que foram os primeiros a utilizar a biblioteca, o gerente Carlos mendes pediu a relação da primeira pessoa a reservar, entregar, e pegar emprestado um livro e suas respectivas informações para que possa ser entrado em contato com ele.

```sql
select E.Data_Emprestimo as 'Data de Emprestimo', 
			E.Data_Entrega as 'Data de Entrega', 
			U.Nome_Usuario as 'Nome do Usuário',
			U.Endereco as 'Endereço', 
			U.Numero , 
			U.CEP,
			U.Bairro,
			U.CPF, 
			U.Tel1 as 'Telefone 1'
from dw_emprestimo as E
inner join dw_usuario as U
order by Data_Emprestimo
limit 1;
```

42 - Foi solicitado pela diretoria saber quantas obras cada editora tem na biblioteca

```sql
select Nome_Editora,
		 count(Titulo_Obra) 
from dw_obra as O
inner join dw_editora as E
on O.ID_Editora = E.ID_Editora
group by Nome_Editora;
```

43 - - É fechamento referente ao mês de janeiro (hoje dia 03/02/2013) e a biblioteca está no vermelho, foi solicitado pelo financeiro que fosse feito um levantamento de todos os livros que não foram devolvidos, mostrando quantos dias de atraso e para cada dia fosse dado uma multa de 5 reais, e mostrar o nome do usuário , livro emprestado e o total a receber pela biblioteca.

```sql
select U.Nome_Usuario AS 'Nome do Usuário',
		Titulo_Obra as 'Titulo da Obra',
		E.Data_Emprestimo as 'Data de Emprestimo',
		E.Data_Entrega as 'Data de Entrega', 
		DATEDIFF('2013-02-03', Data_Entrega) as 'Dias de Atraso',
		DATEDIFF('2013-02-03', Data_Entrega) * 5 AS 'Valor Total da Multa' 
from dw_emprestimo as E
inner join dw_obra as O
ON E.ID_Obra = O.ID_Obra
inner join dw_usuario as U
on E.ID_Usuario = U.ID_Usuario
where ID_Emprestimo not in (select ID_Emprestimo from dw_devolucao)
and DATEDIFF('2013-02-03', Data_Entrega) > 0;
```

44 - Foi solicitado a informação de todos os usuários que morem em uma avenida, e ainda, que seja mostrado o nome do usuário, CPF e logradouro em ordem de CPF do maior para o menor.

```sql
select Nome_Usuario, CPF, Endereco, Numero
from dw_usuario
where Endereco LIKE '%Avenida%'
order by CPF DESC;
```

45 - Chegou na biblioteca uma mensagem urgente da Camila pedindo um relatório contendo a informação de todos os livros que foram emprestados mais de uma vez nos anos de 2011 a 2013 agrupados pelo nome do livro.

```sql
select Titulo_Obra,
			Data_Emprestimo, 
			count(E.ID_Obra) as Total_Emprestimos,
			Titulo_Obra 
from dw_emprestimo as E
inner join dw_obra as O
on E.ID_Obra = O.ID_Obra 
group by Titulo_Obra
having count(E.ID_Obra) > 1
and Data_Emprestimo BETWEEN '2011-01-01' AND '2013-12-31'
```

46 - O Financeiro pediu para apresentar o valor médio dos livros e informar quais são os que estão abaixo dessa media, referente a devolução e empréstimo.

```sql
select O.Titulo_Obra, 
			ES.Valor_Unitario, 
			E.Data_Emprestimo, 
			E.Data_Entrega,(select ROUND(avg(Valor_Unitario),2) 
from dw_estoque) as 'Valor Médio dos Livros' from dw_emprestimo as E
inner join dw_estoque as ES
on E.ID_Obra = ES.ID_Obra 
inner join dw_obra as O
on E.ID_Obra = O.ID_Obra
GROUP BY E.ID_Obra
ORDER BY Valor_Unitario;
```

47 - Barbara do financeiro pediu para verificar qual a media do salario dos funcionários e quem são os que ganham mais do que a media em cada departamento.

```sql
select F.Nome_Funcionario,
			C.Nome_Cargo,
			D.Nome_Departamento,
			C.Salario, (select round(avg(Salario),2) from dw_cargo) as 'Média_Salarial' 
from dw_funcionario as F
inner join dw_cargo as C
on F.ID_Cargo = C.ID_Cargo
inner join dw_departamento as D
on F.ID_Departamento = D.ID_Departamento
where C.Salario > (select round(avg(Salario),2) from dw_cargo)
;
```

Carlos pediu que seja feita uma pesquisa informando de todos os usuários que tem cadastro na biblioteca e que nunca levaram livros mostrar o nome de todos em Maiúsculas

```sql
select ID_Usuario from dw_emprestimo;

select UPPER(Nome_Usuario) as 'Não fez emprestimo' from dw_usuario
where ID_Usuario not in (select ID_Usuario from dw_emprestimo);
```

49 - A recepção pediu para verificar quais os usuários que já pegaram mais de 3 livros, e em caso positivo, mostrar nomes e quais livros, ordenando pelo CEP.

```sql
select U.Nome_Usuario, 
			O.Titulo_Obra, 
			count(E.ID_Usuario) as 'Total Emprestimos' 
from dw_emprestimo as E
inner join dw_usuario as U
on E.ID_Usuario = U.ID_Usuario
inner join dw_obra as O
on E.ID_Obra = O.ID_Obra
group by Nome_Usuario
having count(E.ID_Usuario) > 3;
```

50 - - A diretoria pediu que fosse feito uma analise do estoque, apresentando uma lista com todos os livros que já foram reservados e emprestados e, mostrar quantos estão disponíveis classificados por Gênero.

```sql
select O.Titulo_Obra as 'Titulo da Obra',
			 O.Numero_Publicação as 'Numero de publicações',
			 O.Gênero, 
			 O.Data_Publicação as ' Data de Publicação',
			 E.Quantidade_Livro as 'Quantidade de Livros',
		   E.Valor_Unitario  as 'Valor Unitário' 
from dw_obra as O
inner join dw_estoque as E
on O.ID_Obra = E.ID_Obra
where O.ID_obra IN (Select  id_obra from dw_reserva
UNION DISTINCT
select  id_obra from dw_reserva)
ORDER BY O.Gênero;
```

51 - Foi solicitada a pesquisa de qual a hora que a biblioteca tem mais movimento e, também, qual o horário de menor movimentação, faça a avaliação de todas as horas de devolução, empréstimo e reserva

```sql
WITH cte AS
 (select Hora_Reserva AS HORA from dw_reserva
UNION ALL 
select Hora_Emprestimo AS HORA from dw_emprestimo
UNION ALL
select Hora_Devolucao AS HORA from dw_devolucao)
select 
CASE
    WHEN HORA BETWEEN TIME('00:00:00') AND TIME('05:59:59') THEN 'Madrugada 00:00 as 05:59'
    WHEN HORA BETWEEN TIME('06:00:00') AND TIME('11:59:59') THEN 'Manhã - 06:00 as 11:59'
    WHEN HORA BETWEEN TIME('12:00:00') AND TIME('17:59:59') THEN 'Tarde - 12:00 as 17:59'
    ELSE 'Noite - 18:00 as 23:59'
  END as PERIODO, count(HORA) as CONTAGEM
 FROM cte
 GROUP BY PERIODO
 ORDER BY PERIODO;
```

52 - A sociedade brasileira de editoras solicitou o levantamento para a biblioteca de qual são os 3 autores que tem mais livros lidos no ano de 2012 e 2013 e os 2 que tem menos obras lidas.

```sql
(select A.Nome_Autor, count(*) as Total_Livros_Lidos, 'Mais Lido' as STATUS from dw_emprestimo as  E
join dw_obra as O
ON E.ID_Obra= O.ID_Obra
join dw_autor as A
ON O.ID_Autor = A.ID_Autor
WHERE YEAR(Data_Emprestimo) = 2013 OR YEAR(Data_Emprestimo) = 2012
GROUP BY A.Nome_Autor
ORDER BY Total_Livros_Lidos DESC
LIMIT 3)
UNION

(select A.Nome_Autor, count(*) as Total_Livros_Lidos, 'Menos Lido' as STATUS from dw_emprestimo as  E
join dw_obra as O
ON E.ID_Obra= O.ID_Obra
join dw_autor as A
ON O.ID_Autor = A.ID_Autor
WHERE YEAR(Data_Emprestimo) = 2013 OR YEAR(Data_Emprestimo) = 2012
GROUP BY A.Nome_Autor
ORDER BY Total_Livros_Lidos
LIMIT 2);
```

53 - Crie uma tabela que exiba uma lista de todos livros por funcionario, o total de empréstimos e devoluções que cada um atendeu.

```sql
select Nome_Funcionario,
			 count(DISTINCT E.ID_Emprestimo) as Quantidade_Emprestimo,
			 count(DISTINCT D.ID_Devolucao) as Quantidade_Devolucao 
from dw_emprestimo as E
join dw_funcionario as F
on E.ID_Funcionario = F.ID_Funcionario
LEFT JOIN dw_devolucao as D
on E.ID_Funcionario = D.ID_Funcionario
GROUP BY Nome_Funcionario;
```

54 - A biblioteca quer fechar parceria com as editoras, e existem usuários que trabalham nas editoras facilitando esse contato, para tanto, verificar quais usuários possuem o mesmo endereço das editoras, informe seu nome, telefone e o nome da editora que trabalha.

```sql
select U.Nome_Usuario as 'Usuários',
			 U.Tel1 as 'Telefone', 
			E.Nome_Editora as 'Nome Editora' 
from dw_usuario as U
inner join dw_editora as E
ON U.Endereco = E.Logradouro;
```

55- Foi solicitado a criação de uma visão que traga todos os livros e o preço da editora Leya.

```sql
select O.Titulo_Obra as 'Titulo da Obra',
			 O.Numero_Publicação as 'Numero de Publicação',
			 O.Gênero, O.Data_Publicação as 'Data de Publicação',
			 E.Nome_Editora as 'Nome da Editora', 
			 ES.Valor_Unitario as 'Valor Unitário'  
from dw_obra AS O
inner join dw_editora AS E 
ON O.ID_Editora = E.ID_Editora
inner join dw_estoque as ES
on O.ID_Obra = ES.ID_Obra
WHERE Nome_Editora = 'Leya'
```

56 - A biblioteca foi comprar mais livros para aumentar seu acervo, mas a editora Saraiva aumentou em 16% o valor de seus livros, atualize os preços dos livros da editora na porcentagem designada.

```sql
UPDATE dw_estoque AS ES
INNER JOIN (
    SELECT O.ID_Obra
    FROM dw_obra AS O
    INNER JOIN dw_editora AS E ON O.ID_Editora = E.ID_Editora
    WHERE E.Nome_Editora = 'Saraiva'
) AS subquery ON ES.ID_Obra = subquery.ID_Obra
SET ES.Valor_Unitario = ES.Valor_Unitario * 1.16;
```

57 - Foi solicitado apresentarmos quais as 5 obras que tiveram menos publicações e as 5 de maior, ainda, mostrar qual o autor, editora, nome do livro e quantidade de publicações em ordem descrescente.

```sql
(select O.Titulo_Obra, 
			O.Numero_Publicação,
			A.Nome_Autor, 
			E.Nome_Editora 
from dw_obra as O
inner join dw_autor as A
on O.ID_Autor = a.ID_Autor
INNER JOIN dw_editora AS E
ON O.ID_Editora = E.ID_Editora
order by Numero_Publicação DESC
LIMIT 5)
UNION
(select O.Titulo_Obra,
			 O.Numero_Publicação,
			 A.Nome_Autor, 
			 E.Nome_Editora from dw_obra as O
inner join dw_autor as A
on O.ID_Autor = a.ID_Autor
INNER JOIN dw_editora AS E
ON O.ID_Editora = E.ID_Editora
order by Numero_Publicação ASC
LIMIT 5)
```

58 - Fazem cento e noventa e três meses que o instituto bibliográfico brasileiro surgiu. Para comemorar a diretoria pediu a relação de todos os usuários que tem o cpf com o começo 193 que receberão um presente de comemoração. Para isto, foi solicitado informar o nome e o cpf de todos os usuários que estejam nesse padrão mas mostrar cpf os 3 primeiros dígitos e os dois últimos os do meio apresentar um "*" como no exemplo: 193.***.**-35, lembrando que devemos apresentar dessa maneira, pois a diretoria quer preservar essas informações que são sigilosas.

```sql
select Nome_Usuario as 'Nome do Usuário',
			 Endereco, 
			 Numero, 
			 CEP, 
			 Bairro, 
			 Tel1 as 'Telefone 1', 
			 CONCAT(SUBSTRING(CPF, 1, 3), '.', '***', '.', '***', '-', SUBSTRING(CPF, 10, 2)) AS CPF
from dw_usuario
where CPF LIKE '193.%';
```