Modellizzare la struttura di un database per memorizzare tutti i dati riguardanti una università:
- sono presenti diversi Dipartimenti (es.: Lettere e Filosofia, Matematica, Ingegneria ecc.);
- ogni Dipartimento offre più Corsi di Laurea (es.: Civiltà e Letterature Classiche, Informatica, Ingegneria Elettronica ecc..)
- ogni Corso di Laurea prevede diversi Corsi (es.: Letteratura Latina, Sistemi Operativi 1, Analisi Matematica 2 ecc.);
- ogni Corso può essere tenuto da diversi Insegnanti;
- ogni Corso prevede più appelli d'Esame;
- ogni Studente è iscritto ad un solo Corso di Laurea;
- ogni Studente può iscriversi a più appelli di Esame;
- per ogni appello d'Esame a cui lo Studente ha partecipato, è necessario memorizzare il voto ottenuto, anche se non sufficiente.
Pensiamo a quali entità (tabelle) creare per il nostro database e cerchiamo poi di stabilirne le relazioni. Infine, andiamo a definire le colonne e i tipi di dato di ogni tabella.
Utilizzare https://www.drawio.com/ per la creazione dello schema.
Esportare quindi il diagramma in jpg e caricarlo nella repo.

ESERCIZIO 12.09.2024

1. Selezionare tutti gli studenti nati nel 1990 (160)
SELECT * FROM `students` WHERE YEAR(`date_of_birth`) = 1990;

2. Selezionare tutti i corsi che valgono più di 10 crediti (479)
SELECT * FROM `courses` WHERE `cfu` > 10;

3. Selezionare tutti gli studenti che hanno più di 30 anni (3602)
SELECT * FROM `students` WHERE TIMESTAMPDIFF(YEAR, `date_of_birth`, CURDATE()) > 30;

4. Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di
laurea (286)
SELECT * FROM `courses` WHERE `period` = 'I semestre' AND `year` = 1;

5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del
20/06/2020 (21)
SELECT * FROM `exams` WHERE `date` = '2020-06-20' AND `hour` >= '14:00'; WHERE HOUR(`hour`) >= '14' è lo stesso

6. Selezionare tutti i corsi di laurea magistrale (38)
SELECT * FROM `degrees` WHERE `level` = 'magistrale';

7. Da quanti dipartimenti è composta l'università? (12)
SELECT COUNT(*) FROM `departments`;

8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50)
SELECT COUNT(*) FROM `teachers` WHERE `phone` IS NULL;

9. Inserire nella tabella degli studenti un nuovo record con i propri dati (per il campo
degree_id, inserire un valore casuale)
INSERT INTO `students` (`degree_id`, `name`, `surname`, `date_of_birth`, `fiscal_code`, `enrolment_date`, `registration_number`, `email`) VALUES ('18', 'Marianna', 'Coppa', '1995-05-30', 'DOFNKT95E70F865X', '2014-09-01', '625999', 'stenella@gmail.com');

10. Cambiare il numero dell’ufficio del professor Pietro Rizzo in 126
UPDATE `teachers` SET `office_number` = '126' WHERE `name` = 'Pietro' AND `surname` = 'Rizzo';

11. Eliminare dalla tabella studenti il record creato precedentemente al punto 9
DELETE FROM `students` WHERE `name` = 'Marianna' AND `surname` = 'Coppa' AND `email` = 'stenella@gmail.com';




QUERY CON GROUP BY

1. Contare quanti iscritti ci sono stati ogni anno
SELECT COUNT(*) AS `numeroStudenti`, YEAR(`enrolment_date`) AS `anno` FROM `students` GROUP BY `anno`;

2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio
SELECT COUNT(*) AS `numeroInsegnanti`, `office_address` FROM `teachers` GROUP BY `office_address`;

3. Calcolare la media dei voti di ogni appello d'esame
SELECT AVG(`vote`) AS `media_voto`, `exam_id` FROM `exam_student` GROUP BY `exam_id`;

4. Contare quanti corsi di Laurea ci sono per ogni Dipartimento
SELECT COUNT(*) AS `numero_corsi_di_laurea`, `department_id` FROM `degrees` GROUP BY `department_id`;




QUERY CON JOIN

1. Selezionare tutti gli studenti iscritti al corso di Laurea in Economia
SELECT `students`.`surname`, `students`.`name`, `students`.`registration_number`, `students`.`enrolment_date` FROM `students` JOIN `degrees` ON `students`.`degree_id` = `degrees`.`id` WHERE `degrees`.`name` = 'Corso di Laurea in Economia';

2. Selezionare tutti i corsi di Laurea del Dipartimento di Neuroscienze
SELECT `degrees`.`name`, `degrees`.`level` FROM `degrees` JOIN `departments` ON `departments`.`id` = `degrees`.`department_id` WHERE `departments`.`name` = 'Dipartimento di Neuroscienze' AND `degrees`.`level` = 'magistrale';

3. Selezionare tutti i corsi in cui insegna Fulvio Amato
SELECT `courses`.`name`, `courses`.`id` FROM `courses` JOIN `course_teacher` ON `course_teacher`.`course_id` = `courses`.`id` JOIN `teachers` ON `teachers`.`id` = `course_teacher`.`teacher_id` WHERE `teachers`.`name` = 'Fulvio' AND `teachers`.`surname` = 'Amato';

4. Selezionare tutti gli studenti con i dati relativi al corso di Laurea a cui sono iscritti e il relativo Dipartimento, in ordine alfabetico per cognome e nome
SELECT `students`.`surname` AS `nome_studente`, `students`.`name`, `students`.`date_of_birth`, `students`.`fiscal_code`, `students`.`enrolment_date`, `students`.`registration_number`, `students`.`email`, `degrees`.`name` AS `nome_corso_di_laurea`, `degrees`.`level`, `departments`.`name` AS `nome_dipartimento`
FROM `students`
JOIN `degrees` ON `degrees`.`id` = `students`.`degree_id`
JOIN `departments` ON `departments`.`id` = `degrees`.`department_id`
ORDER BY `students`.`surname`, `students`.`name`;

5. Selezionare tutti i corsi di laurea con i relativi corsi ed insegnanti
SELECT `degrees`.`name` AS `nome_corso_di_laurea`, `courses`.`name` AS `nome_corso`, `teachers`.`name` AS `nome_docente`, `teachers`.`surname` AS `cognome_docente` FROM `degrees` JOIN `courses` ON `courses`.`degree_id` = `degrees`.`id` JOIN `course_teacher` ON `course_teacher`.`course_id` = `courses`.`id` JOIN `teachers` ON `teachers`.`id` = `course_teacher`.`teacher_id`;