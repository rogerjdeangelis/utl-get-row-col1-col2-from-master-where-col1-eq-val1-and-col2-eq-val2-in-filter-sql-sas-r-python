# utl-get-row-col1-col2-from-master-where-col1-eq-val1-and-col2-eq-val2-in-filter-sql-sas-r-python
Select row col1 col2 from master where col1 eq val1 and col2 eq val2 in filter sql sas r python 
    %let pgm=utl-get-row-col1-col2-from-master-where-col1-eq-val1-and-col2-eq-val2-in-filter-sql-sas-r-python;

    %stop_submission;

    Select row col1 col2 from master where col1 eq val1 and col2 eq val2 in filter sql sas r python

         SOLUTIONS

              1 sas sql
              2 r sql
              4 python sql
              5 r base (>= version 4)
                r syntax (merge, cbind, seq_len, |>, and setNames)

    github
    https://tinyurl.com/y9duu5kv
    https://github.com/rogerjdeangelis/utl-get-row-col1-col2-from-master-where-col1-eq-val1-and-col2-eq-val2-in-filter-sql-sas-r-python

    stackoverflow r
    https://tinyurl.com/2a9pvy76
    https://stackoverflow.com/questions/79324988/identifying-data-frame-rows-in-r-with-specific-pairs-of-values-in-two-columns

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                         |                                             |                                */
    /*        INPUT (MATCH ON V1 & V2)         |     PROCESS                                 |       OUTPUT                   */
    /*        =====                            | SQL SELF EXPLANTORY                         |       ======                   */
    /*                                         | ====================                        |                                */
    /*                                         |                                             |  ROWNAMES ROWID V1  V2         */
    /*  SD1 MASTER  SD1.TRANSACT    OUTPUT     | 2-3 R AND PYTHON                            |                                */
    /*  ----------  ------------    ------     | ================                            |      1      2    1   2         */
    /*                                         |                                             |      2      5    2   4         */
    /*   Obs V1 V2   V1 V2        ROW   V1  V2 |   select                                    |                                */
    /*                                         |      l.rowid                                |                                */
    /*    1   1  1                 2    1   2  |     ,l.v1                                   |                                */
    /*    2   1  2 -- 1  2         5    2   4  |     ,l.v2                                   |                                */
    /*    3   1  4                             |   from                                      |                                */
    /*    4   2  1                             |      master as l                            |                                */
    /*    5   2  4 -- 2  4                     |   inner                                     |                                */
    /*    6   3  4                             |     join transact as r                      |                                */
    /*    7   3 10                             |   on                                        |                                */
    /*                                         |         l.v1 = r.v1                         |                                */
    /*                                         |     and l.v2 = r.v2                         |                                */
    /*                                         |    ')                                       |                                */
    /*                                         |                                             |                                */
    /*                                         |---------------------------------------------|                                */
    /*                                         |                                             |                                */
    /*                                         | 1 SAS SQL                                   |                                */
    /*                                         | =========                                   |                                */
    /*                                         |                                             |                                */
    /*                                         |   select                                    |                                */
    /*                                         |      l.seq                                  |                                */
    /*                                         |     ,l.v1                                   |                                */
    /*                                         |     ,l.v2                                   |                                */
    /*                                         |   from                                      |                                */
    /*                                         |     (select                                 |                                */
    /*                                         |         monotonic() as seq                  |                                */
    /*                                         |        ,v1                                  |                                */
    /*                                         |        ,v2                                  |                                */
    /*                                         |      from                                   |                                */
    /*                                         |         sd1.master) as l                    |                                */
    /*                                         |   inner                                     |                                */
    /*                                         |     join sd1.transaction as r               |                                */
    /*                                         |   on                                        |                                */
    /*                                         |         l.v1 = r.v1                         |                                */
    /*                                         |     and l.v2 = r.v2                         |                                */
    /*                                         |                                             |                                */
    /*                                         |---------------------------------------------|                                */
    /*                                         |                                             |                                */
    /*                                         |  4 R BASE                                   |                                */
    /*                                         |  ========                                   |                                */
    /*                                         |                                             |                                */
    /*                                         |   want<-merge(                              |                                */
    /*                                         |     cbind(master, seq_len(nrow(master))),   |                                */
    /*                                         |     transact                                |                                */
    /*                                         |     ) |> setNames(c("x", "y", "row_num"))   |                                */
    /*                                         |                                             |                                */
    /*                                         |                                             |                                */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";

    data sd1.master;
    input
          V1 V2;
    cards4;
    1 1
    1 2
    1 4
    2 1
    2 4
    3 4
    3 10
    ;;;;
    run;quit;

    data sd1.transact;
    input
      V1 V2;
    cards4;
    1 2
    2 4
    ;;;;
    run;quit;


    /*                             _
    / |  ___  __ _ ___   ___  __ _| |
    | | / __|/ _` / __| / __|/ _` | |
    | | \__ \ (_| \__ \ \__ \ (_| | |
    |_| |___/\__,_|___/ |___/\__, |_|
                                |_|
    */

    proc sql;
      create
         table want as
      select
         l.seq
        ,l.v1
        ,l.v2
      from
        (select
            monotonic() as seq
           ,v1
           ,v2
         from
            sd1.master) as l
      inner
        join sd1.transaction as r
      on
            l.v1 = r.v1
        and l.v2 = r.v2
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*    SEQ    V1    V2                                                                                                     */
    /*                                                                                                                        */
    /*     2      1     2                                                                                                     */
    /*     5      2     4                                                                                                     */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    master<-read_sas("d:/sd1/master.sas7bdat")
    transact<-read_sas("d:/sd1/transact.sas7bdat")
    transact
    master
    want<-sqldf('
      select
         l.rowid
        ,l.v1
        ,l.v2
      from
         master as l
      inner
        join transact as r
      on
            l.v1 = r.v1
        and l.v2 = r.v2
       ')
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
    /*                                |                                                                                       */
    /*  R                  SAS        |                                                                                       */
    /*                                |                                                                                       */
    /*    rowid V1 V2      ROWNAMES   | ROWID    V1    V2                                                                     */
    /*                                |                                                                                       */
    /*  1     2  1  2          1      |   2       1     2                                                                     */
    /*  2     5  2  4          2      |   5       2     4                                                                     */
    /*                                |                                                                                       */
    /**************************************************************************************************************************/

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    master,meta = ps.read_sas7bdat('d:/sd1/master.sas7bdat');
    transact,meta = ps.read_sas7bdat('d:/sd1/transact.sas7bdat');
    want=pdsql('''
      select               \
         l.rowid           \
        ,l.v1              \
        ,l.v2              \
      from                 \
         master as l       \
      inner                \
        join transact as r \
      on                   \
            l.v1 = r.v1    \
        and l.v2 = r.v2    \
       ''');
    print(want)
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3)
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*                       |                                                                                                */
    /*  R                    |  SAS                                                                                           */
    /*                       |                                                                                                */
    /*     rowid   V1   V2   |  Obs    ROWID    V1    V2                                                                      */
    /*                       |                                                                                                */
    /*  0      2  1.0  2.0   |   1       2       1     2                                                                      */
    /*  1      5  2.0  4.0   |   2       5       2     4                                                                      */
    /*                       |                                                                                                */
    /**************************************************************************************************************************/

    /*  _     _
    | || |   | |__   __ _ ___  ___   _ __
    | || |_  | `_ \ / _` / __|/ _ \ | `__|
    |__   _| | |_) | (_| \__ \  __/ | |
       |_|   |_.__/ \__,_|___/\___| |_|

    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;


    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    master<-read_sas("d:/sd1/master.sas7bdat")
    transact<-read_sas("d:/sd1/transact.sas7bdat")
    master
    transact
    want<-merge(
      cbind(master, seq_len(nrow(master))),
      transact
      ) |> setNames(c("x", "y", "row_num"))
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
    /*                       |                                                                                                */
    /*  PYTHON               |  SAS                                                                                           */
    /*                       |                                                                                                */
    /*     rowid   V1   V2   |  Obs    ROWID    V1    V2                                                                      */
    /*                       |                                                                                                */
    /*  0      2  1.0  2.0   |   1       2       1     2                                                                      */
    /*  1      5  2.0  4.0   |   2       5       2     4                                                                      */
    /*                       |                                                                                                */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
