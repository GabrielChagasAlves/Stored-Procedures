# Stored-Procedures

## Neste exercicio irei mostrar como gerar um banco de dados onde ira armazer dados de cursos, professores e alunos, como tambem ira gerar automaticamente um email para cada aluno mesmo que ocorra a possibilidade de um aluno tiver o mesmo nome e sobrenome de outro aluno

### 1- Crie um banco de dados para armazenar alunos, cursos e professores de uma universidade
```SQL
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema Universidade
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema Universidade
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `Universidade` DEFAULT CHARACTER SET utf8 COLLATE utf8_czech_ci ;
USE `Universidade` ;

-- -----------------------------------------------------
-- Table `Universidade`.`curso`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`curso` (
  `id_curso` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `materia` VARCHAR(100) NULL,
  PRIMARY KEY (`id_curso`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Universidade`.`professor`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`professor` (
  `id_professor` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `nome` VARCHAR(100) NULL,
  PRIMARY KEY (`id_professor`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Universidade`.`aluno`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`aluno` (
  `id_aluno` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `nome` VARCHAR(100) NULL,
  `sobrenome` VARCHAR(100) NULL,
  `ra` VARCHAR(100) NULL,
  `email` VARCHAR(100) NULL,
  PRIMARY KEY (`id_aluno`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Universidade`.`aluno_has_curso`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`aluno_has_curso` (
  `aluno_id_aluno` INT UNSIGNED NOT NULL,
  `curso_id_curso` INT UNSIGNED NOT NULL,
  PRIMARY KEY (`aluno_id_aluno`, `curso_id_curso`),
  INDEX `fk_aluno_has_curso_curso1_idx` (`curso_id_curso` ASC) VISIBLE,
  INDEX `fk_aluno_has_curso_aluno_idx` (`aluno_id_aluno` ASC) VISIBLE,
  CONSTRAINT `fk_aluno_has_curso_aluno`
    FOREIGN KEY (`aluno_id_aluno`)
    REFERENCES `Universidade`.`aluno` (`id_aluno`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_aluno_has_curso_curso1`
    FOREIGN KEY (`curso_id_curso`)
    REFERENCES `Universidade`.`curso` (`id_curso`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Universidade`.`curso_has_professor`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`curso_has_professor` (
  `curso_id_curso` INT UNSIGNED NOT NULL,
  `professor_id_professor` INT UNSIGNED NOT NULL,
  PRIMARY KEY (`curso_id_curso`, `professor_id_professor`),
  INDEX `fk_curso_has_professor_professor1_idx` (`professor_id_professor` ASC) VISIBLE,
  INDEX `fk_curso_has_professor_curso1_idx` (`curso_id_curso` ASC) VISIBLE,
  CONSTRAINT `fk_curso_has_professor_curso1`
    FOREIGN KEY (`curso_id_curso`)
    REFERENCES `Universidade`.`curso` (`id_curso`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_curso_has_professor_professor1`
    FOREIGN KEY (`professor_id_professor`)
    REFERENCES `Universidade`.`professor` (`id_professor`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;

```
### 2-Faça a modelagem do banco e identifique as entidades, seus atributos e relacionamentos
![2](https://github.com/GabrielChagasAlves/Stored-Procedures/assets/125607847/354b2d29-2a1c-4b3a-a3eb-2a6cdf949066)

### 3- Crie o modelo físico do banco de dados (script SQL)
![image](https://github.com/GabrielChagasAlves/Stored-Procedures/assets/125607847/b4dcdb5d-e919-4087-96ec-4eff0fe100f0)

### 4- Utilize Stored Procedures para automatizar a inserção e seleção dos cursos
```SQL
DELIMITER //
CREATE PROCEDURE InserirCurso(IN nomeMateria VARCHAR(255))
BEGIN
    INSERT INTO curso (materia) VALUES (nomeMateria);
END //
DELIMITER ;
```
```SQL
DELIMITER //
CREATE PROCEDURE SelecionarCursos()
BEGIN
    SELECT * FROM curso;
END //
DELIMITER ;
```

### 5/6 -O aluno possui um email que deve ter seu endereço gerado automaticamente no seguinte formato: nome.sobrenome@dominio.com. Como fica o email se duas pessoas tiverem o mesmo nome e sobrenome?
```SQL
DELIMITER $$
CREATE TRIGGER GenerateE_mail
BEFORE INSERT ON universidade.aluno
FOR EACH ROW
BEGIN
  DECLARE email_count INT;
  SET email_count = 0;
  
  -- Verifique se já existe um e-mail com o mesmo nome e sobrenome
  SELECT COUNT(*) INTO email_count FROM universidade.aluno WHERE email = CONCAT(NEW.nome, '.', NEW.sobrenome, '@facens.com');
  
  -- Se houver um conflito, adicione um número incremental ao e-mail
  IF email_count > 0 THEN
    SET NEW.email = CONCAT(NEW.nome, '.', NEW.sobrenome, email_count, '@facens.com');
  ELSE
    SET NEW.email = CONCAT(NEW.nome, '.', NEW.sobrenome, '@facens.com');
  END IF;
END;
$$
DELIMITER ;
```
### 7- Com tudo inserido e criado, nossa tabela alunos fica da seguinte forma 
![image](https://github.com/GabrielChagasAlves/Stored-Procedures/assets/125607847/83c77cc2-ea91-4862-9923-50dbc4f6e184)

### 8- tabela curso
![image](https://github.com/GabrielChagasAlves/Stored-Procedures/assets/125607847/4bd8360c-5239-4552-b3b8-2f4ebc3dcb95)

### 9- Tabela Professor 
![image](https://github.com/GabrielChagasAlves/Stored-Procedures/assets/125607847/e3cee638-74aa-479c-a227-91ad44c60851)



