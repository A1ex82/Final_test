7. В подключенном MySQL репозитории создать базу данных “Друзья
человека”

    ```
    CREATE DATABASE Human_friends;
    USE Human_friends;
    ```

8. Создать таблицы с иерархией из диаграммы в БД

    ```
    CREATE TABLE Животные (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50));

    CREATE TABLE Домашние_животные (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    animal_id INT,FOREIGN KEY (animal_id) REFERENCES Животные(id));

    CREATE TABLE Собаки (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    pet_id INT,FOREIGN KEY (pet_id) REFERENCES Домашние_животные(id));

    CREATE TABLE Кошки (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    pet_id INT,FOREIGN KEY (pet_id) REFERENCES Домашние_животные(id));

    CREATE TABLE Хомяки (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    pet_id INT,FOREIGN KEY (pet_id) REFERENCES Домашние_животные(id));

    CREATE TABLE Вьючные_животные (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    animal_id INT,
    FOREIGN KEY (animal_id) REFERENCES Животные(id));

    CREATE TABLE Лошади (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    draft_animal_id INT,FOREIGN KEY (draft_animal_id) REFERENCES Вьючные_животные(id));

    CREATE TABLE Верблюды (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    draft_animal_id INT,FOREIGN KEY (draft_animal_id) REFERENCES Вьючные_животные(id));

    CREATE TABLE Ослы (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    draft_animal_id INT,FOREIGN KEY (draft_animal_id) REFERENCES Вьючные_животные(id));
    ```

9. Заполнить низкоуровневые таблицы именами(животных), командами
которые они выполняют и датами рождения

    ```
    INSERT INTO Животные (name) VALUES
    ('Рекс'),
    ('Мурка'),
    ('Чарли'),
    ('Белка'),
    ('Шарик'),
    ('Барсик'),
    ('Джек'),
    ('Кеша');

    INSERT INTO Собаки (name, command, birth_date) VALUES
    ('Рекс', 'Сидеть', '2015-02-10'),
    ('Шарик', 'Фечить', '2018-08-22');

    INSERT INTO Кошки (name, command, birth_date) VALUES
    ('Мурка', 'Мурлыкать', '2016-05-15'),
    ('Барсик', 'Охотиться', '2017-11-30');

    INSERT INTO Хомяки (name, command, birth_date) VALUES
    ('Чарли', 'Крутиться в колесе', '2019-03-05');

    INSERT INTO Лошади (name, command, birth_date) VALUES
    ('Джек', 'Тянуть плуг', '2014-07-18');

    INSERT INTO Верблюды (name, command, birth_date) VALUES
    ('Белка', 'Переносить грузы', '2016-10-25');


    INSERT INTO Ослы (name, command, birth_date) VALUES
    ('Кеша', 'Тащить сани', '2017-12-10');
    ```

10. Удалив из таблицы верблюдов, т.к. верблюдов решили перевезти в другой
питомник на зимовку. Объединить таблицы лошади, и ослы в одну таблицу.

    ```
    DELETE FROM Верблюды;

    CREATE TABLE Перевозимые_животные (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    command VARCHAR(100),
    birth_date DATE);

    INSERT INTO Перевозимые_животные (name, command, birth_date)
    SELECT name, command, birth_date FROM Лошади;

    DELETE FROM Лошади;

    INSERT INTO Перевозимые_животные (name, command, birth_date)
    SELECT name, command, birth_date FROM Ослы;

    DELETE FROM Ослы;
    ```

11. Создать новую таблицу “молодые животные в которую попадут все животные старше 1 года, но младше 3 лет и в отдельном столбце с точностью
до месяца подсчитать возраст животных в новой таблице

    ```
    CREATE TABLE молодые_животные (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    command VARCHAR(100),
    birth_date DATE,
    age_months INT);

    INSERT INTO молодые_животные (name, command, birth_date, age_months)
    SELECT name, command, birth_date, 
    TIMESTAMPDIFF(MONTH, birth_date, NOW()) AS age_months
    FROM Животные
    WHERE birth_date BETWEEN DATE_SUB(NOW(), INTERVAL 3 YEAR) AND DATE_SUB(NOW(), INTERVAL 1 YEAR);
    ```
12. Объединить все таблицы в одну, при этом сохраняя поля, указывающие на
прошлую принадлежность к старым таблицам.

    ```

    SELECT 'Домашние_животные' AS source_table, id, name, command, birth_date, NULL AS draft_animal_id
    FROM Домашние_животные
    UNION ALL
    SELECT 'Вьючные_животные' AS source_table, id, name, command, birth_date, NULL AS pet_id
    FROM Вьючные_животные
    UNION ALL
    SELECT 'Собаки' AS source_table, id, name, command, birth_date, NULL AS pet_id
    FROM Собаки
    UNION ALL
    SELECT 'Кошки' AS source_table, id, name, command, birth_date, NULL AS pet_id
    FROM Кошки
    UNION ALL
    SELECT 'Хомяки' AS source_table, id, name, command, birth_date, NULL AS pet_id
    FROM Хомяки
    UNION ALL
    SELECT 'Лошади' AS source_table, id, name, command, birth_date, NULL AS animal_id
    FROM Лошади
    UNION ALL
    SELECT 'Верблюды' AS source_table, id, name, command, birth_date, NULL AS animal_id
    FROM Верблюды
    UNION ALL
    SELECT 'Ослы' AS source_table, id, name, command, birth_date, NULL AS animal_id
    FROM Ослы;

    ```
