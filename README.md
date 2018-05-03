# utl_visit_patterns_by_frequency_and_number_percentage_of_visits.
 Trying to analyze Visit patterns by frequency and number/percentage of visits.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Trying to analyze Visit patterns by frequency and number/percentage of visits

    Same result in SAS and WPS

    github
    https://tinyurl.com/y8emy8jr
    https://github.com/rogerjdeangelis/utl_visit_patterns_by_frequency_and_number_percentage_of_visits.

    SAS Forum
    https://tinyurl.com/y8emy8jr
    https://communities.sas.com/t5/General-SAS-Programming/Trying-to-analyze-visit-pattern-by-frequency-and-number/m-p/459771

    INPUT
    ====

    Up to 40 obs WORK.FAT total obs=6

    Obs    NAME     LOC1    LOC2    LOC3    LOC4    LOC5

     1     Sarah     A       B       A       A       A
     2     James     B       B       C       D
     3     Alber     D       E       F
     4     Rosie     E       E       E       A       A
     5     Willi     B       B       B       B
     6     Ashle     D       D


    EXAMPLE OF WANT

    p to 40 obs WORK.WANT total obs=12

    bs    NAME     LOCATION    CNT    CNTNAM    PCT

     1    Alber       F         1        3       33  Most frequent
     2    Alber       D         1        3       33  Next frequent
     3    Alber       E         1        3       33  Least Frequent

     4    Ashle       D         2        2      100

     5    James       B         2        4       50  Most frequent
     6    James       D         1        4       25  Next frequent
     7    James       C         1        4       25  Least Frequent

     8    Rosie       E         3        5       60  Most frequent
     9    Rosie       A         2        5       40  Least frequent

    10    Sarah       A         4        5       80  Most frequent
    11    Sarah       B         1        5       20  Least frequent

    12    Willi       B         4        4      100

    PROCESS
    =======

    proc transpose data=fat out=nrm(where=(col1 ne '') drop=_name_);
      by name notsorted;
      var loc:;
    run;quit;

    /*
    Up to 40 obs WORK.NRM total obs=23

    Obs    NAME     COL1

      1    Sarah     A
      2    Sarah     B
      3    Sarah     A
      4    Sarah     A
    ...
    */

    proc sql;
      create
         table want as
      select
         l.name
        ,l.col1  as location
        ,count(l.col1) as cnt
        ,r.cntNam
        ,100*calculated cnt/r.cntNam as pct
      from
        nrm as l left join
        (select name, count(name) as cntNam from nrm group by name) as r
      on
        l.name = r.name
      group
        by l.name, l.col1, r.cntNam
      order
        by name, pct descending
    ;quit;


    OUTPUT
    ======

     Up to 40 obs WORK.WANT total obs=12

     Obs    NAME     LOCATION    CNT    CNTNAM    PCT

       1    Alber       F         1        3       33
       2    Alber       D         1        3       33
       3    Alber       E         1        3       33
       4    Ashle       D         2        2      100
       5    James       B         2        4       50
       6    James       D         1        4       25
       7    James       C         1        4       25
       8    Rosie       E         3        5       60
       9    Rosie       A         2        5       40
      10    Sarah       A         4        5       80
      11    Sarah       B         1        5       20
      12    Willi       B         4        4      100

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    data fat;
      input (Name Loc1 Loc2 Loc3 Loc4 Loc5) ($);
    cards4;
    Sarah A B A A A
    James B B C D .
    Alber D E F . .
    Rosie E E E A A
    Willi B B B B .
    Ashle D D . . .
    ;;;;
    run;quit;
    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;
    proc transpose data=fat out=nrm(where=(col1 ne '') drop=_name_);
      by name notsorted;
      var loc:;
    run;quit;

    /*
    Up to 40 obs WORK.NRM total obs=23

    Obs    NAME     COL1

      1    Sarah     A
      2    Sarah     B
      3    Sarah     A
      4    Sarah     A
    ...
    */

    proc sql;
      create
         table want as
      select
         l.name
        ,l.col1  as location
        ,count(l.col1) as cnt
        ,r.cntNam
        ,100*calculated cnt/r.cntNam as pct
      from
        nrm as l left join
        (select name, count(name) as cntNam from nrm group by name) as r
      on
        l.name = r.name
      group
        by l.name, l.col1, r.cntNam
      order
        by pct descending
    ;quit;

    *
    __      ___ __  ___
    \ \ /\ / / '_ \/ __|
     \ V  V /| |_) \__ \
      \_/\_/ | .__/|___/
             |_|
    ;

    %utl_submit_wps64('

    libname wrk sas7bdat "%sysfunc(pathname(work))";

    proc transpose data=wrk.fat out=nrm(where=(col1 ne "") drop=_name_);
      by name notsorted;
      var loc:;
    run;quit;

    proc sql;
      select
         l.name
        ,l.col1  as location
        ,count(l.col1) as cnt
        ,r.cntNam
        ,100*calculated cnt/r.cntNam as pct
      from
        nrm as l left join
        (select name, count(name) as cntNam from nrm group by name) as r
      on
        l.name = r.name
      group
        by l.name, l.col1, r.cntNam
      order
        by pct descending
    ;quit;
    run;quit;
    ');

