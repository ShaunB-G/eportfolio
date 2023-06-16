```python
!pip install pandas

!pip install sqlalchemy # ORM for databases

!pip install ipython-sql # SQL magic function
!pip install mysqlclient
```


```python
%load_ext sql
#%config SqlMagic.autocommit=False # for engines that do not support autommit
```


```python
%sql mysql://SBG:Tazthedog11!@127.0.0.1:3306/mydb
```


```python
%sql show tables
```

     * mysql://SBG:***@127.0.0.1:3306/mydb
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>Tables_in_mydb</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>course</td>
        </tr>
        <tr>
            <td>student</td>
        </tr>
        <tr>
            <td>student-course</td>
        </tr>
    </tbody>
</table>




```python
%sql select * from course
```


```python
%sql ALTER TABLE `course` ADD FOREIGN KEY (`Course Name`) REFERENCES `Student-Course` (`Course Name`);
```


```sql
%%sql insert into course (Course Name, Exam Boards, Teacher Name) VALUES ('Computer Science', 'BCS', 'Mr Jones'), 
("Maths", "EdExcel", "Ms Parker"), 
("Physics", "OCR", "Mr Peters"), 
("Biology", "WJEC", "Mrs Patel"), 
("Music", "AQA", "Ms Daniels");
```


```sql
%%sql insert into student (Student Number, Student First Name, Student Last Name, Exam Score, Support, Date of Birth) VALUES (1001, 'Bob', 'Baker', 78, 'No', '25/08/01'), 
(1002, 'Sally', 'Davies', 55, 'Yes', '10/02/1999'),  
(1003, 'Mark', 'Hanmill', 90, 'No', '06/05/1995'), 
(1004, 'Anas', 'Ali', 70, 'No', '08/03/1980'), 
(1005, 'Cheuk', 'Yin', 45, 'Yes', '05/01/2002');
```


```sql
%%sql 
insert into student-course (Student Number, Course Name) values (1001, Computer Science), (1001, Maths),
(1001, "Physics"),
(1002, "Maths"),
(1002, "Biology"),
(1002, "Music"),
(1003, "Computer Science"),
(1003, "Maths"),
(1003, "Physics"),
(1004, "Maths"),
(1004, "Physics"),
(1004, "Biology"),
(1005, "Computer Science"),
(1005, "Maths"),
(1005, "Music")
;
```


```python
##%sql mysql://SBG:Tazthedog11!@127.0.0.1:3306/Mysql
```


```python
##%sql CREATE TABLE `Course` ( `Course Name` varchar(255), `Exam Boards` varchar(255), `Teacher Name` varchar(255));
```


```python

```
