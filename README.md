# SYSTEM PROGRAMMING - DOCUMENTATION FOR PARSER FOR SQL SELECT QUERY



The SELECT statement is used to select data from a database.

The data returned is stored in a result table, called the result-set.

 

## Introduction of flex and yacc

- l file gets compiled using flex and we get lex.yy.c file

- y file gets compiled using bison or yacc and we get two files y.tab.c and y.tab.h(y.output –optional)
- yacc uses LALR parsing table
- y.tab.h needs to be included in declaration of l file to fulfil the basic requirement of interfacing.
- yyparse() assumes existence of yylex()

 

##  CFG FOR SELECT QUERY:

(1)start       : (i)SELECT exp NEWLINE  {printf("\nValid SQL Select Statement\n"); exit(0);}

​              ;

 

(2)exp        : (i)cols FROM TB_COL_AL_NAME tables

​             | (ii)cols FROM TB_COL_AL_NAME create_alias tables

​              ;

 

(3)cols        : (i) '*'

​              | (ii) TB_COL_AL_NAME cols

​              | (iii) alias_ref TB_COL_AL_NAME cols

​              | (iv) TB_COL_AL_NAME    

​              | (v) alias_ref TB_COL_AL_NAME

​              | (vi)  ',' TB_COL_AL_NAME cols

​              | (vii)  ',' alias_ref TB_COL_AL_NAME cols

​              | (viii) ',' TB_COL_AL_NAME

​              |(ix)  ',' alias_ref TB_COL_AL_NAME

​              | (x) TB_COL_AL_NAME create_alias_col

​              | (xi) TB_COL_AL_NAME create_alias_col cols

   |(xii)  ',' TB_COL_AL_NAME create_alias_col cols

​              |(xiii)  ',' TB_COL_AL_NAME create_alias_col

​              |(xiv)  alias_ref TB_COL_AL_NAME create_alias_col cols

​              | (xv) alias_ref TB_COL_AL_NAME create_alias_col

​              | (xvi),alias_ref TB_COL_AL_NAME create_alias_col cols

​              | (xvii),alias_ref TB_COL_AL_NAME create_alias_col

   ;

 

(4)tables      : (i) ';'

​               | (ii) condition ';'

​              | (iii) ',' TB_COL_AL_NAME create_alias tables

​              | (iv)  ',' TB_COL_AL_NAME tables

​               ;

 

(5)alias_ref    :  (i)TB_COL_AL_NAME '.'

​         ;

 

(6)create_alias_col : (i) AS STRCON

​                     ;

 

(7)create_alias   : (i) AS TB_COL_AL_NAME

​                 | (ii) ' ' TB_COL_AL_NAME

​                 ;

 

(8)condition     : (i) WHERE where_cond

​                 ;

 

(9)where_cond     : (i) cond

​                  | (ii) cond OR where_cond

​                  | (iii) cond AND where_cond

​                  ;

 

(10)cond  : (i) tb '<' NUM

​         | (ii) tb '<' STRCON

​         | (iii) tb '<' tb

​         |(iv) tb '>' NUM

​         | (v) tb '>' tb

​         | (vi) tb '>' STRCON

​         |(vii) tb LE NUM

​         | (viii) tb LE tb

​         | (ix) tb LE STRCON

​         |(x) tb GE NUM

​         |(xi) tb GE tb

​         | (xii) tb GE STRCON

​         | (xiii) tb EQ NUM

​         |(xiv) tb EQ tb

​         |(xv) tb EQ STRCON

​         | (xvi) tb NE NUM

​         | (xvii) tb NE tb

​         | (xviii) tb NE STRCON

​    ;

 

(11)tb     :  (i)TB_COL_AL_NAME

​          | (ii) alias_ref TB_COL_AL_NAME

​          ;

 

## INPUT TYPE:

- Input will be read from the stdin and the query should be typed in only one line.
- Columns and tables should be separated using a single ‘,’ without any spaces before or after it.
- Each query should end with a ‘;’ and after ‘;’ , no other character (even space) will not be handled. Parsing ends at ‘;’.
- Keywords can not be used for naming table, column names and as aliases.
- Column name alias should be enclosed within single quotes only (use of [],”” not allowed).

 

Example: SELECT columnname1,columnname2 AS ‘col2_alias’ FROM tablename1 tablename1alias,tablename2 AS tablename2alias WHERE condition;

 

## PARSING EXAMPLES:

**SELECT \* FROM emp;**

The parser starts parsing this statement and 1(i) production rule (start : SELECT exp NEWLINE )will be matched and expands on exp

2(i) production rule (exp : cols FROM TB_COL_AL_NAME tables ) will be matched and expands on cols

3(i) production rule (cols : '*') will be matched and expands on tables

4(i) production rule (tables : ';') will be matched.

 

LEFT MOST DERIVATION FOR THE SAME:

 

*start =>SELECT exp NEWLINE*

​     *=>SELECT cols FROM TB_COL_AL_NAME tables NEWLINE*

​     *=>SELECT \* FROM TB_COL_AL_NAME tables NEWLINE*

​     *=>SELECT \* FROM TB_COL_AL_NAME ; NEWLINE*

 

Note: TB_COL_AL_NAME is emp

 

 

**SELECT o.OrderID,o.OrderDate,CustomerName AS ‘customer name’ FROM Customers**

**AS c,Orders AS o WHERE c.CustomerName=’Around the Horn’ AND c.CustomerID=**

**o.CustomerID AND o.OrderID=15469;**

 

LEFT MOST DERIVATION FOR THIS QUERY:

 

*using  1(i) **start** => SELECT **exp** NEWLINE*                             

 

*using 2(ii)       => SELECT **cols** FROM TB_COL_AL_NAME create_alias tables NEWLINE*       

 

*using 3(iii)       => SELECT **alias_ref** TB_COL_AL_NAME cols FROM TB_COL_AL_NAME create_alias*         

​                 *tables NEWLINE* 

 

 *using 5(i)       => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME **cols** FROM TB_COL_AL_NAME* 

​                 *create_alias tables NEWLINE* 

​    

*using 3(vii)      => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' **alias_ref** TB_COL_AL_NAME cols*

​                 *FROM TB_COL_AL_NAME create_alias tables NEWLINE* 

​    

 *using 5(i)       => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME **cols***

​                *FROM TB_COL_AL_NAME create_alias tables NEWLINE* 

​    

*using 3(xiii)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME **create_alias_col** FROM TB_COL_AL_NAME create_alias tables NEWLINE* 

   

*using 6(i)       => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME **create_alias** tables NEWLINE* 

 

*using 7(i)      => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME **tables** NEWLINE* 

 

*using 4(iii)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME **create_alias** tables NEWLINE*

 

 *using 7(i)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME **tables** NEWLINE*

 

*using 4(ii)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME **condition**; NEWLINE*

 

*using 8(i)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE **where_cond**; NEWLINE*

 

*using 9(iii)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE **cond** AND where_cond; NEWLINE*

 

*using 10(xv)   => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE **tb** EQ STRCON AND where_cond; NEWLINE*

 

*using 11(ii)   => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE **alias_ref** TB_COL_AL_NAME EQ STRCON AND*        

​                *where_cond; NEWLINE*

 

 

*using 5(i)    => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​               *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​              *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​              *STRCON AND **where_cond**; NEWLINE*

 

*using 9(iii)   => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​               *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​              *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​              *STRCON AND **cond** AND where_cond; NEWLINE*

 

*using 10(xiv)  => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​               *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​                *STRCON AND **tb** EQ tb AND where_cond; NEWLINE*

 

*using 11(ii)   => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​                *STRCON AND **alias_ref** TB_COL_AL_NAME EQ tb AND where_cond; NEWLINE*

 

*using 5(i)     => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​               *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​                *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ **tb** AND where_cond; NEWLINE*

 

*Using11(ii)  => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​               *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​                *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ **alias_ref** TB_COL_AL_NAME AND*   

​                *where_cond; NEWLINE*

 

*Using5(i)    => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​                *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​               *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​             *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ TB_COL_AL_NAME '.'TB_COL_AL_NAME AND*  

​             ***where_cond**; NEWLINE*

 

*Using9(i)    => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​               *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​                *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​             *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ TB_COL_AL_NAME '.'TB_COL_AL_NAME AND*  

​             ***cond**; NEWLINE*

 

*Using10(xiii) => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​            *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​            *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​             *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ TB_COL_AL_NAME '.'TB_COL_AL_NAME AND*  

​             ***tb** EQ NUM; NEWLINE*

 

*Using11(ii) => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​              *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​              *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​             *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ TB_COL_AL_NAME '.'TB_COL_AL_NAME AND*  

​             ***alias_ref** TB_COL_AL_NAME EQ NUM; NEWLINE*

 

 

*Using5(i)  => SELECT TB_COL_AL_NAME '.' TB_COL_AL_NAME ',' TB_COL_AL_NAME '.' TB_COL_AL_NAME ','*          

​             *TB_COL_AL_NAME AS STRCON FROM TB_COL_AL_NAME AS TB_COL_AL_NAME ','*  

​            *TB_COL_AL_NAME AS TB_COL_AL_NAME WHERE TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ*  

​             *STRCON AND TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ TB_COL_AL_NAME '.'TB_COL_AL_NAME AND*  

​             *TB_COL_AL_NAME '.' TB_COL_AL_NAME EQ NUM; NEWLINE*

## Sample queries that will be returned as ‘valid’ by the parser: 

 1.SELECT * FROM emp;

 

2.SELECT ename,job,sal FROM emp;

 

3.SELECT job,sal,ename AS 'name' FROM emp;

 

4.SELECT job,sal,ename AS 'employee names' FROM emp;

 

5.SELECT ename AS 'name',eno AS 'emp no' FROM emp;

 

6.SELECT ename,deptname FROM emp e,dept d WHERE e.deptno=d.deptno;

 

7.SELECT ename AS 'name',deptname FROM emp AS e,dept AS d WHERE 



e.deptno=d.deptno;

 

8.SELECT o.OrderID,o.OrderDate,c.CustomerName FROM Customers AS c,Orders 



AS o WHERE c.CustomerName='Around the Horn' AND 



c.CustomerID=o.CustomerID;

 

9.SELECT o.OrderID,o.OrderDate,c.CustomerName FROM Customers AS c,Orders 



AS o WHERE c.CustomerName='Around the Horn' AND 



c.CustomerID=o.CustomerID;

 

10.SELECT * FROM Customers WHERE Country='Mexico';

 

11.SELECT * FROM Customers WHERE CustomerID=1;

 

12.SELECT title,type FROM invoice I,movie M,cust C WHERE M.mv_no=I.mv_no 



AND I.cust_id=C.cust_id AND fname=’Vandana’;

 

13.SELECT ename FROM emp WHERE sal>=10000;

 

14.SELECT ename,job,sal,eno FROM emp WHERE eno<>105;

 

15.SELECT ename FROM emp WHERE ename='karan' OR ename='Raghav';

 

16.SELECT o.OrderID,o.OrderDate,c.CustomerName AS 'customer name' FROM 



Customers AS c,Orders AS o WHERE c.CustomerName='Around the Horn' AND 



c.CustomerID=o.CustomerID AND o.OrderID=15469;

 

17.SELECT o.OrderID,o.OrderDate,c.CustomerName AS 'customer name' FROM 



Customers c,Orders AS o WHERE c.CustomerID=o.CustomerID AND 



o.OrderID=15469;

 

## The following cases are not handled by the parser:

 

- Subqueries or nested SQL

 

SELECT ename,hiredate FROM emp WHERE (deptno=(SELECT deptno FROM emp WHERE ename=’BLAKE’)); //no  /* SELECT statement built using a subquery. */

 

SELECT ename,hiredate,id FROM emp WHERE (ename=’radhika’ OR ename=’shreya’) AND (id=3451);

/*parenthesis i.e. nested conditions are not handled*/

 

- Queries associated with DUAL table

SELECT 2*3 FROM DUAL;

 

- Formatting of outputs:

 

SELECT ‘the movie taken by’ || fname,lname || ‘is’ || title FROM cust C WHERE C.mov_no>=3;

 

- Spaces after ; after the query is written or any extra unnecessary spaces:

 

SELECT * FROM EMP;(spaces)   

 

 ‘invalid SQL statement’ will be returned by the parser for the above queries.

 

 

 

  