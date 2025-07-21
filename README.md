# utl-transpose-pivot-sas-arts-macro-and-sql-sas-r-python
Transpose pivot sas arts macro and sql sas r python
    %let pgm=utl-transpose-pivot-sas-arts-macro-and-sql-sas-r-python;

    %stop_submission;

    Transpose pivot sas arts macro and sql sas r python ;

    SOLUTIONS
       1 sas transpose macro
       2 sas sql (w and w/o sql arrays)
       3 python sql (with sql arrays)
       4 r sql (with sql arrays)
       5 sql python excel spss/pspp matlab/octave postreSQL sqlite3
             not shown,  use sqlite3 with very similar code as r

    github
    https://tinyurl.com/36hy4sc3
    https://github.com/rogerjdeangelis/utl-transpose-pivot-sas-arts-macro-and-sql-sas-r-python

    SOAPBOX ON

      Note sas proc transpose cannot transpose sets of variables like lat and lon.

      PROC TRANSPOSE

      ID YEAR LAT LON        proc transpose          ID    YEAR    _NAME_    COL1
                              data=sd1.have(obs=2)
      ID YEAR  LAT   LON      out=xpo;               19    1995     LAT      28.8
                             by id year;             19    1995     LON      97.7
      19 1995  28.8  97.7    var lat lon             19    1999     LAT      28.8
      19 1999  28.8  97.7    ;run;quit;              19    1999     LON      97.7

      WANT

      ID YEAR LAT1  LON1

      19 1995 28.8  97.7
      19 1999 28.8  97.7

      The most common posted solution involves two transposes

      Art's transpose macro is a must have for your toolbox;
      There have been dozens of posted problems that are much easier to solve using Arts transpose macro

    SOAPBOX OFF

    sas communities
    https://tinyurl.com/4d448k58
    https://communities.sas.com/t5/SAS-Programming/How-to-transpose-and-rename-variables/m-p/760286#M240401

    Related repo
    --------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl_sophisticated_transpose_with_proc_summary_idgroup



    /***************************************************************************************************************************************************
    /* INPUT                 | PROCESS                                                               | OUTPUT                                          *
    /* =====                 | =======                                                               | ======                                          *
    /*                       |                                                                       |                                                 *
    /* ID YEAR LAT LON       | 1 SAS TRANSPOSE MACRO                                                 |                                                 *
    /*                       | =====================                                                 |                                                 *
    /* ID YEAR  LAT   LON    |                                                                       |                                                 *
    /*                       | %utl_transpose(                                                       | ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON *
    /* 19 1995  28.8  97.7   |   data=sd1.have                                                       |                                                 *
    /* 19 1999  28.8  97.7   |   ,out=want                                                           | 19 1995 28.8   .    .    .   97.7   .    .    . *
    /* 23 1990  38.8  81.1   |   ,by=id year                                                         | 19 1999 28.8   .    .    .   97.7   .    .    . *
    /* 23 1995  33.3  33.3   |   ,guessingrows=1000                                                  | 23 1990 38.8   .    .    .   81.1   .    .    . *
    /* 23 1995  38.8  81.1   |   ,var=lat lon);                                                      | 23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   . *
    /* 23 1995  57.7  77.7   | * rearrange columns;                                                  | 23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77. *
    /* 23 1996  33.3  33.3   | data wantvue                                                          | 23 1998 48.8 38.8   .    .   91.1 81.1   .    . *
    /* 23 1996  38.8  81.1   |   /view=wantvue;                                                      | 23 1999 33.3 38.8   .    .   33.3 81.0   .    . *
    /* 23 1996  70.0  66.6   |  retain id year                                                       |                                                 *
    /* 23 1996  57.7  77.7   |    %utl_varlist(                                                      |                                                 *
    /* 23 1998  48.8  91.1   |     want                                                              |                                                 *
    /* 23 1998  38.8  81.1   |    ,prx=/^lat/i);                                                     |                                                 *
    /* 23 1999  33.3  33.3   |  set want;                                                            |                                                 *
    /* 23 1999  38.8  81.0   | run;quit;                                                             |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       |                                                                       |                                                 *
    /* options               | 2 SAS SQL (W AND W/O SQL ARRAYS(                                      |                                                 *
    /*  validvarname=upcase; | ===============================                                       |                                                 *
    /* libname sd1 "d:/sd1"; |                                                                       |                                                 *
    /* data sd1.have;        | proc sql;                                                             |                                                 *
    /* input id year lat lon;|  create                                                               |                                                 *
    /* cards4;               |    table want as                                                      |                                                 *
    /* 19 1995 28.8 97.7     |  select                                                               |                                                 *
    /* 19 1999 28.8 97.7     |    id                                                                 |                                                 *
    /* 23 1990 38.8 81.1     |   ,year                                                               |                                                 *
    /* 23 1995 33.3 33.3     |   ,max(case when partition=1 then lat else . end) as lat1             |                                                 *
    /* 23 1995 38.8 81.1     |   ,max(case when partition=2 then lat else . end) as lat2             |                                                 *
    /* 23 1995 57.7 77.7     |   ,max(case when partition=3 then lat else . end) as lat3             |                                                 *
    /* 23 1996 33.3 33.3     |   ,max(case when partition=4 then lat else . end) as lat4             |                                                 *
    /* 23 1996 38.8 81.1     |                                                                       |                                                 *
    /* 23 1996 70.0 66.6     |   ,max(case when partition=1 then lon else . end) as lon1             |                                                 *
    /* 23 1996 57.7 77.7     |   ,max(case when partition=2 then lon else . end) as lon2             |                                                 *
    /* 23 1998 48.8 91.1     |   ,max(case when partition=3 then lon else . end) as lon3             |                                                 *
    /* 23 1998 38.8 81.1     |   ,max(case when partition=4 then lon else . end) as lon4             |                                                 *
    /* 23 1999 33.3 33.3     |  from                                                                 |                                                 *
    /* 23 1999 38.8 81.0     |    %sqlpartitionx(sd1.have,by=%str(id, year))                         |                                                 *
    /* ;;;;                  |  group                                                                |                                                 *
    /* run;quit;             |    by id, year                                                        |                                                 *
    /*                       | ;quit;                                                                |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       | * with arrays;                                                        | ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON *
    /*                       |                                                                       |                                                 *
    /*                       | proc datasets lib=work nodetails nolist;                              | 19 1995 28.8   .    .    .   97.7   .    .    . *
    /*                       |   delete want;                                                        | 19 1999 28.8   .    .    .   97.7   .    .    . *
    /*                       | run;quit;                                                             | 23 1990 38.8   .    .    .   81.1   .    .    . *
    /*                       |                                                                       | 23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   . *
    /*                       | proc sql;                                                             | 23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77. *
    /*                       |   select                                                              | 23 1998 48.8 38.8   .    .   91.1 81.1   .    . *
    /*                       |     max(n)                                                            | 23 1999 33.3 38.8   .    .   33.3 81.0   .    . *
    /*                       |   into : n                                                            |                                                 *
    /*                       |     from                                                              |                                                 *
    /*                       |       (select                                                         |                                                 *
    /*                       |          count(*) as n                                                |                                                 *
    /*                       |             from                                                      |                                                 *
    /*                       |          have                                                         |                                                 *
    /*                       |       group                                                           |                                                 *
    /*                       |          by id,year)                                                  |                                                 *
    /*                       | ;                                                                     |                                                 *
    /*                       |  %array(_ll,values=1-&n);                                             |                                                 *
    /*                       |  create                                                               |                                                 *
    /*                       |    table want as                                                      |                                                 *
    /*                       |  select                                                               |                                                 *
    /*                       |    id                                                                 |                                                 *
    /*                       |   ,year                                                               |                                                 *
    /*                       |  %do_over(_ll,phrase=%str(                                            |                                                 *
    /*                       |   ,max(case when partition=? then lat else . end) as lat?             |                                                 *
    /*                       |   ,max(case when partition=? then lon else . end) as lon?))           |                                                 *
    /*                       |  from                                                                 |                                                 *
    /*                       |    %sqlpartitionx(sd1.have,by=%str(id, year))                         |                                                 *
    /*                       |  group                                                                |                                                 *
    /*                       |    by id, year                                                        |                                                 *
    /*                       | ;quit;                                                                |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       | 3 PYTHON SQL (WITH SQL ARRAYS)                                        | PYTHON                                          *
    /*                       | ==============================                                        | ID YEAR lat1 lat2 lat3 lat4 lon1 lon2 lon3 lon4 *
    /*                       |                                                                       |                                                 *
    /*                       | proc datasets lib=sd1 nolist nodetails;                               | 19 1995 28.8  NaN  NaN  NaN 97.7  NaN  NaN  NaN *
    /*                       |  delete pywant;                                                       | 19 1999 28.8  NaN  NaN  NaN 97.7  NaN  NaN  NaN *
    /*                       | run;quit;                                                             | 23 1990 38.8  NaN  NaN  NaN 81.1  NaN  NaN  NaN *
    /*                       |                                                                       | 23 1995 33.3 38.8 57.7  NaN 33.3 81.1 77.7  NaN *
    /*                       | %utl_pybeginx;                                                        | 23 1996 33.3 38.8 70.0 57.7 33.3 81.1 66.6 77.7 *
    /*                       | parmcards4;                                                           | 23 1998 48.8 38.8  NaN  NaN 91.1 81.1  NaN  NaN *
    /*                       | exec(open('c:/oto/fn_pythonx.py').read());                            | 23 1999 33.3 38.8  NaN  NaN 33.3 81.0  NaN  NaN *
    /*                       | have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');                 |                                                 *
    /*                       | phrases_lon = ' '.join(                                               | SAS                                             *
    /*                       |  [f",max(case when partition={i} then lon else NULL end) as lon{i}"   | ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON4*
    /*                       |   for i in [1, 2, 3, 4]]                                              |                                                 *
    /*                       | )                                                                     | 19 1995 28.8   .    .    .   97.7   .    .    . *
    /*                       | print(phrases_lon)                                                    | 19 1999 28.8   .    .    .   97.7   .    .    . *
    /*                       | phrases_lat = ' '.join(                                               | 23 1990 38.8   .    .    .   81.1   .    .    . *
    /*                       |  [f",max(case when partition={i} then lat else NULL end) as lat{i}"   | 23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   . *
    /*                       |   for i in [1, 2, 3, 4]]                                              | 23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77.7*
    /*                       | )                                                                     | 23 1998 48.8 38.8   .    .   91.1 81.1   .    . *
    /*                       | print(phrases_lat)                                                    | 23 1999 33.3 38.8   .    .   33.3 81.0   .    . *
    /*                       | query= (                                                              |                                                 *
    /*                       |     "select id, year " +                                              |                                                 *
    /*                       |     phrases_lat + " " +                                               |                                                 *
    /*                       |     phrases_lon + " " +                                               |                                                 *
    /*                       |     "from (select *, row_number() over" + " " +                       |                                                 *
    /*                       |        "(partition by id, year) as partition" + " " +                 |                                                 *
    /*                       |        "from have ) group by id, year"                                |                                                 *
    /*                       | )                                                                     |                                                 *
    /*                       | print(query)                                                          |                                                 *
    /*                       | want=pdsql(query)                                                     |                                                 *
    /*                       | print(want)                                                           |                                                 *
    /*                       | fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);          |                                                 *
    /*                       | ;;;;                                                                  |                                                 *
    /*                       | %utl_pyendx;                                                          |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       | proc print data=sd1.pywant;                                           |                                                 *
    /*                       | run;quit;                                                             |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       | 4 R SQL (WITH SQL ARRAYS)                                             | want                                            *
    /*                       | =========================                                             | ID YEAR lat1 lat2 lat3 lat4 lon1 lon2 lon3 lon4 *
    /*                       |                                                                       | 19 1995 28.8   NA   NA   NA 97.7   NA   NA   NA *
    /*                       | proc datasets lib=sd1 nolist nodetails;                               | 19 1999 28.8   NA   NA   NA 97.7   NA   NA   NA *
    /*                       |  delete want;                                                         | 23 1990 38.8   NA   NA   NA 81.1   NA   NA   NA *
    /*                       | run;quit;                                                             | 23 1995 33.3 38.8 57.7   NA 33.3 81.1 77.7   NA *
    /*                       |                                                                       | 23 1996 33.3 38.8 70.0 57.7 33.3 81.1 66.6 77.7 *
    /*                       | %utl_rbeginx;                                                         | 23 1998 48.8 38.8   NA   NA 91.1 81.1   NA   NA *
    /*                       | parmcards4;                                                           | 23 1999 33.3 38.8   NA   NA 33.3 81.0   NA   NA *
    /*                       | library(haven)                                                        |                                                 *
    /*                       | library(sqldf)                                                        | SAS                                             *
    /*                       | source("c:/oto/fn_tosas9x.R")                                         | ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON4*
    /*                       | options(sqldf.dll = "d:/dll/sqlean.dll")                              |                                                 *
    /*                       | have<-read_sas("d:/sd1/have.sas7bdat")                                | 19 1995 28.8   .    .    .   97.7   .    .    . *
    /*                       | print(have)                                                           | 19 1999 28.8   .    .    .   97.7   .    .    . *
    /*                       | phrases_lat <- paste(sprintf(                                         | 23 1990 38.8   .    .    .   81.1   .    .    . *
    /*                       |  ",max(case when partition=%1$s then lat else NULL end) as lat%1$s"   | 23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   . *
    /*                       |   ,c(1,2,3,4)                                                         | 23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77.7*
    /*                       |  ),collapse=' ')                                                      | 23 1998 48.8 38.8   .    .   91.1 81.1   .    . *
    /*                       | phrases_lon <- paste(sprintf(                                         | 23 1999 33.3 38.8   .    .   33.3 81.0   .    . *
    /*                       |  ",max(case when partition=%1$s then lon else NULL end) as lon%1$s"   |                                                 *
    /*                       |   ,c(1,2,3,4)                                                         |                                                 *
    /*                       |  ),collapse=' ')                                                      |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       | want<-sqldf(paste(                                                    |                                                 *
    /*                       |   'select id, year'                                                   |                                                 *
    /*                       |   ,phrases_lat                                                        |                                                 *
    /*                       |   ,phrases_lon                                                        |                                                 *
    /*                       |   ,'from (select *, row_number()                                      |                                                 *
    /*                       |     over (partition by id, year) as partition                         |                                                 *
    /*                       |     from have ) group by id, year'))                                  |                                                 *
    /*                       | want                                                                  |                                                 *
    /*                       | fn_tosas9x(                                                           |                                                 *
    /*                       |       inp    = want                                                   |                                                 *
    /*                       |      ,outlib ="d:/sd1/"                                               |                                                 *
    /*                       |      ,outdsn ="want"                                                  |                                                 *
    /*                       |      )                                                                |                                                 *
    /*                       | ;;;;                                                                  |                                                 *
    /*                       | %utl_rendx;                                                           |                                                 *
    /*                       |                                                                       |                                                 *
    /*                       | proc print data=sd1.want;                                             |                                                 *
    /*                       | run;quit;                                                             |                                                 *
    /***************************************************************************************************************************************************

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options
     validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input id year lat lon;
    cards4;
    19 1995 28.8 97.7
    19 1999 28.8 97.7
    23 1990 38.8 81.1
    23 1995 33.3 33.3
    23 1995 38.8 81.1
    23 1995 57.7 77.7
    23 1996 33.3 33.3
    23 1996 38.8 81.1
    23 1996 70.0 66.6
    23 1996 57.7 77.7
    23 1998 48.8 91.1
    23 1998 38.8 81.1
    23 1999 33.3 33.3
    23 1999 38.8 81.0
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*  ID    YEAR     LAT     LON                                                                                            */
    /*                                                                                                                        */
    /*  19    1995    28.8    97.7                                                                                            */
    /*  19    1999    28.8    97.7                                                                                            */
    /*  23    1990    38.8    81.1                                                                                            */
    /*  23    1995    33.3    33.3                                                                                            */
    /*  23    1995    38.8    81.1                                                                                            */
    /*  23    1995    57.7    77.7                                                                                            */
    /*  23    1996    33.3    33.3                                                                                            */
    /*  23    1996    38.8    81.1                                                                                            */
    /*  23    1996    70.0    66.6                                                                                            */
    /*  23    1996    57.7    77.7                                                                                            */
    /*  23    1998    48.8    91.1                                                                                            */
    /*  23    1998    38.8    81.1                                                                                            */
    /*  23    1999    33.3    33.3                                                                                            */
    /*  23    1999    38.8    81.0                                                                                            */
    /**************************************************************************************************************************/

    /*              _         _
    / |   __ _ _ __| |_ ___  | |_ _ __ __ _ _ __  ___ _ __   ___  ___  ___
    | |  / _` | `__| __/ __| | __| `__/ _` | `_ \/ __| `_ \ / _ \/ __|/ _ \
    | | | (_| | |  | |_\__ \ | |_| | | (_| | | | \__ \ |_) | (_) \__ \  __/
    |_|  \__,_|_|   \__|___/  \__|_|  \__,_|_| |_|___/ .__/ \___/|___/\___|
                                                     |_|
    */

    1 SAS TRANSPOSE MACRO
    =====================

    %utl_transpose(
      data=sd1.have
      ,out=want
      ,by=id year
      ,guessingrows=1000
      ,var=lat lon);

    * rearrange columns;
    data wantvue
      /view=wantvue;
     retain id year
       %utl_varlist(
        want
       ,prx=/^lat/i);
     set want;
    run;quit;

    /**************************************************************************************************************************/
    /* Before rearranging columns                       | Rearranged columns                                                  */
    /*                                                  |                                                                     */
    /* ID YEAR LAT1 LON1 LAT2 LON2 LAT3 LON3 LAT4 LON4  | ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON                     */
    /*                                                  |                                                                     */
    /* 19 1995 28.8 97.7   .    .    .    .    .    .   | 19 1995 28.8   .    .    .   97.7   .    .    .                     */
    /* 19 1999 28.8 97.7   .    .    .    .    .    .   | 19 1999 28.8   .    .    .   97.7   .    .    .                     */
    /* 23 1990 38.8 81.1   .    .    .    .    .    .   | 23 1990 38.8   .    .    .   81.1   .    .    .                     */
    /* 23 1995 33.3 33.3 38.8 81.1 57.7 77.7   .    .   | 23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   .                     */
    /* 23 1996 33.3 33.3 38.8 81.1 70.0 66.6 57.7 77.7  | 23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77.                     */
    /* 23 1998 48.8 91.1 38.8 81.1   .    .    .    .   | 23 1998 48.8 38.8   .    .   91.1 81.1   .    .                     */
    /* 23 1999 33.3 33.3 38.8 81.0   .    .    .    .   | 23 1999 33.3 38.8   .    .   33.3 81.0   .    .                     */
    /**************************************************************************************************************************/

    /*___                              _
    |___ \   ___  __ _ ___   ___  __ _| | __      __  __ _ _ __ _ __ __ _ _   _ ___
      __) | / __|/ _` / __| / __|/ _` | | \ \ /\ / / / _` | `__| `__/ _` | | | / __|
     / __/  \__ \ (_| \__ \ \__ \ (_| | |  \ V  V / | (_| | |  | | | (_| | |_| \__ \
    |_____| |___/\__,_|___/ |___/\__, |_|   \_/\_/   \__,_|_|  |_|  \__,_|\__, |___/
                                    |_|                                   |___/

    */

    proc datasets lib=work nodetails nolist;
      delete want;
    run;quit;

    proc sql;
      select
        max(n)
      into : n
        from
          (select
             count(*) as n
                from
             have
          group
             by id,year)
    ;
     %array(_ll,values=1-&n);
     create
       table want as
     select
       id
      ,year
     %do_over(_ll,phrase=%str(
      ,max(case when partition=? then lat else . end) as lat?
      ,max(case when partition=? then lon else . end) as lon?))
     from
       %sqlpartitionx(sd1.have,by=%str(id, year))
     group
       by id, year
    ;quit;

    /**************************************************************************************************************************/
    /* TWO                                                                                                                    */
    /*                                                                                                                        */
    /*  ID    YEAR    LAT1    LON1    LAT2    LON2    LAT3    LON3    LAT4    LON4                                            */
    /*                                                                                                                        */
    /*  19    1995    28.8    97.7      .       .       .       .       .       .                                             */
    /*  19    1999    28.8    97.7      .       .       .       .       .       .                                             */
    /*  23    1990    38.8    81.1      .       .       .       .       .       .                                             */
    /*  23    1995    33.3    33.3    38.8    81.1    57.7    77.7      .       .                                             */
    /*  23    1996    33.3    33.3    38.8    81.1    70.0    66.6    57.7    77.7                                            */
    /*  23    1998    48.8    91.1    38.8    81.1      .       .       .       .                                             */
    /*  23    1999    33.3    33.3    38.8    81.0      .       .       .       .                                             */
    /**************************************************************************************************************************/

    /*___               _   _                             _
    |___ /  _ __  _   _| |_| |__   ___  _ __    ___  __ _| | __      __  __ _ _ __ _ __ __ _ _   _ ___
      |_ \ | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | | \ \ /\ / / / _` | `__| `__/ _` | | | / __|
     ___) || |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |  \ V  V / | (_| | |  | | | (_| | |_| \__ \
    |____/ | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|   \_/\_/   \__,_|_|  |_|  \__,_|\__, |___/
           |_|    |___/                                |_|                                   |___/
    */

    3 PYTHON SQL (WITH SQL ARRAYS)
    ==============================

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_pythonx.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    phrases_lon = ' '.join(
     [f",max(case when partition={i} then lon else NULL end) as lon{i}"
      for i in [1, 2, 3, 4]]
    )
    print(phrases_lon)
    phrases_lat = ' '.join(
     [f",max(case when partition={i} then lat else NULL end) as lat{i}"
      for i in [1, 2, 3, 4]]
    )
    print(phrases_lat)
    query= (
        "select id, year " +
        phrases_lat + " " +
        phrases_lon + " " +
        "from (select *, row_number() over" + " " +
           "(partition by id, year) as partition" + " " +
           "from have ) group by id, year"
    )
    print(query)
    want=pdsql(query)
    print(want)
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /* PYTHON                                                           | SAS                                                 */
    /*       ID    YEAR  lat1  lat2  lat3  lat4  lon1  lon2  lon3  lon4 | ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON4    */
    /*                                                                  |                                                     */
    /*  0  19.0  1995.0  28.8   NaN   NaN   NaN  97.7   NaN   NaN   NaN | 19 1995 28.8   .    .    .   97.7   .    .    .     */
    /*  1  19.0  1999.0  28.8   NaN   NaN   NaN  97.7   NaN   NaN   NaN | 19 1999 28.8   .    .    .   97.7   .    .    .     */
    /*  2  23.0  1990.0  38.8   NaN   NaN   NaN  81.1   NaN   NaN   NaN | 23 1990 38.8   .    .    .   81.1   .    .    .     */
    /*  3  23.0  1995.0  33.3  38.8  57.7   NaN  33.3  81.1  77.7   NaN | 23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   .     */
    /*  4  23.0  1996.0  33.3  38.8  70.0  57.7  33.3  81.1  66.6  77.7 | 23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77.7    */
    /*  5  23.0  1998.0  48.8  38.8   NaN   NaN  91.1  81.1   NaN   NaN | 23 1998 48.8 38.8   .    .   91.1 81.1   .    .     */
    /*  6  23.0  1999.0  33.3  38.8   NaN   NaN  33.3  81.0   NaN   NaN | 23 1999 33.3 38.8   .    .   33.3 81.0   .    .     */
    /**************************************************************************************************************************/

    /*  _                      _
    | || |    _ __   ___  __ _| | __      __   __ _ _ __ _ __ __ _ _   _ ___
    | || |_  | `__| / __|/ _` | | \ \ /\ / /  / _` | `__| `__/ _` | | | / __|
    |__   _| | |    \__ \ (_| | |  \ V  V /  | (_| | |  | | | (_| | |_| \__ \
       |_|   |_|    |___/\__, |_|   \_/\_/    \__,_|_|  |_|  \__,_|\__, |___/
                            |_|                                    |___/
    */

    4 R SQL (WITH SQL ARRAYS)
    =========================

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    options(sqldf.dll = "d:/dll/sqlean.dll")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    phrases_lat <- paste(sprintf(
     ",max(case when partition=%1$s then lat else NULL end) as lat%1$s"
      ,c(1,2,3,4)
     ),collapse=' ')
    phrases_lon <- paste(sprintf(
     ",max(case when partition=%1$s then lon else NULL end) as lon%1$s"
      ,c(1,2,3,4)
     ),collapse=' ')

    want<-sqldf(paste(
      'select id, year'
      ,phrases_lat
      ,phrases_lon
      ,'from (select *, row_number()
        over (partition by id, year) as partition
        from have ) group by id, year'))
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*  R                                                |  SAS                                                               */
    /*  ID YEAR lat1 lat2 lat3 lat4 lon1 lon2 lon3 lon4  |  ID YEAR LAT1 LAT2 LAT3 LAT4  LON1 LON2 LON3 LON4                  */
    /*                                                   |                                                                    */
    /*  19 1995 28.8   NA   NA   NA 97.7   NA   NA   NA  |  19 1995 28.8   .    .    .   97.7   .    .    .                   */
    /*  19 1999 28.8   NA   NA   NA 97.7   NA   NA   NA  |  19 1999 28.8   .    .    .   97.7   .    .    .                   */
    /*  23 1990 38.8   NA   NA   NA 81.1   NA   NA   NA  |  23 1990 38.8   .    .    .   81.1   .    .    .                   */
    /*  23 1995 33.3 38.8 57.7   NA 33.3 81.1 77.7   NA  |  23 1995 33.3 38.8 57.7   .   33.3 81.1 77.7   .                   */
    /*  23 1996 33.3 38.8 70.0 57.7 33.3 81.1 66.6 77.7  |  23 1996 33.3 38.8 70.0 57.7  33.3 81.1 66.6 77.7                  */
    /*  23 1998 48.8 38.8   NA   NA 91.1 81.1   NA   NA  |  23 1998 48.8 38.8   .    .   91.1 81.1   .    .                   */
    /*  23 1999 33.3 38.8   NA   NA 33.3 81.0   NA   NA  |  23 1999 33.3 38.8   .    .   33.3 81.0   .    .                   */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
