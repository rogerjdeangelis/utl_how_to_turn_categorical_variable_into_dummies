# utl_how_to_turn_categorical_variable_into_dummies
How to turn categorical variables into dummies. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    How to turn categorical variables into dummies

      Two Examples WPS/PROC R or IML/R

          1. Single categorical variable
          2. Multiple categorical variables

    github
    https://github.com/rogerjdeangelis/utl_how_to_turn_categorical_variable_into_dummies

    see
    https://goo.gl/VDz91x
    https://stackoverflow.com/questions/48343837/r-how-to-turn-categorical-variable-into-dummies-and-collapsing-id-variables

    INPUT
    =====

     SD1.HAVE total obs=6

       ID    COLOR

       A     Red
       B     Blue
       C     Green
       A     Blue
       C     Blue
       B     Red

     RULES

        A has colors Blue and Red
        B has colors Blue and Red
        C has colors Blue and Green

        WORK.WANTWPS total obs=3

        ID  BLUE    GREEN    RED

         A     1       0       1
         B     1       0       1
         C     1       1       0


    PROCESS (working R code - clear use of pipes?)
    ===============================================

      have %>%
         mutate(counts = 1) %>%
         spread(COLOR, counts, fill = 0) -> want;


      EXPLANATION

      * mutate(counts = 1) just sets counts on each row

               ID COLOR counts
          1     A   Red      1
          2     B  Blue      1
          3     C Green      1
          4     A  Blue      1
          5     C  Blue      1
          6     B   Red      1

       spread(COLOR, counts, fill = 0)

       spread color across and fille with 0s

         ID  BLUE    GREEN    RED

          A     1       0       1
          B     1       0       1
          C     1       1       0

    OUTPUT
    ======

     WORK.WANTWPS total obs=3

        ID  BLUE    GREEN    RED

         A     1       0       1
         B     1       0       1
         C     1       1       0

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;
    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input id$ color$;
    cards4;
    A Red
    B Blue
    C Green
    A Blue
    C Blue
    B Red
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname sd1 sas7bdat "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.2";
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    libname hlp sas7bdat "C:\Program Files\SASHome\SASFoundation\9.4\core\sashelp";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    library(haven);
    library(tidyverse);
    library(dplyr);
    have<-read_sas("d:/sd1/have.sas7bdat");
    have;
    have %>%
      mutate(counts = 1);
    have %>%
      mutate(counts = 1) %>%
      spread(COLOR, counts, fill = 0) -> want;
    want;
    endsubmit;
    import r=want  data=wrk.wantwps;
    run;quit;
    ');


    /* T1007520 SAS forum: Macro To Create Indicator Variables Fails For Numerical Categorical Variables

    SAS forum: Macro To Create Indicator Variables Fails For Numerical Categorical Variables


    see
    https://goo.gl/ZDVoZ9
    https://communities.sas.com/t5/Base-SAS-Programming/Macro-To-Create-Indicator-Variables-Fails-For-Numerical/m-p/390750

    HAVE
    ====

    Up to 40 obs WORK.HAVE total obs=6

    Obs    CATEGORY1    ID    CATEGORY2

     1     CategoryA     1        1
     2     CategoryB     2        1
     3     CategoryA     3        1
     4     CategoryA     4        2
     5     CategoryC     5        3
     6     CategoryB     6        2

    WORKING CODE
    =============

          IML/R  WPS/PROC R

           dmy <- dummyVars(" ~ .", data = have);
           trsf <- data.frame(predict(dmy, newdata = have));

           * trsf is very usable all alone.
           * renaming variables is a lot of string manipulation
           * R package caret jas a full rank option



    WANT
    ====
    Up to 40 obs from want total obs=6

                                           IND_CAT1_    IND_CAT1_    IND_CAT1_     IND_      IND_      IND_
    Obs    CATEGORY1    ID    CATEGORY2    CATEGORYA    CATEGORYB    CATEGORYC    CAT2_1    CAT2_2    CAT2_3

     1     CategoryA     1        1            1            0            0           1         0         0
     2     CategoryB     2        1            0            1            0           1         0         0
     3     CategoryA     3        1            1            0            0           1         0         0
     4     CategoryA     4        2            1            0            0           0         1         0
     5     CategoryC     5        3            0            0            1           0         0         1
     6     CategoryB     6        2            0            1            0           0         1         0


    This is what SAS/WPS/R intermediate dataset TRSF looks like

    Up to 40 obs from trsf total obs=6

    Obs   ID   CATEGORY1CATEGORYA    CATEGORY1CATEGORYB    CATEGORY1CATEGORYC    CATEGORY21    CATEGORY22    CATEGORY23

     1     1            1                     0                     0                 1             0             0
     2     2            0                     1                     0                 1             0             0
     3     3            1                     0                     0                 1             0             0
     4     4            1                     0                     0                 0             1             0
     5     5            0                     0                     1                 0             0             1
     6     6            0                     1                     0                 0             1             0


    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    DATA have;
          LENGTH Category1 $ 9;
          INPUT ID Category1 Category2;
    cards4;
    1 CategoryA 1
    2 CategoryB 1
    3 CategoryA 1
    4 CategoryA 2
    5 CategoryC 3
    6 CategoryB 2
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.4.0";
    libname wrk "%sysfunc(pathname(work))";
    libname hlp "C:\Program Files\SASHome\SASFoundation\9.4\core\sashelp";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.4.0/etc/Rprofile.site", echo=T);
    library(haven);
    library(caret);
    have<-read_sas("d:/sd1/have.sas7bdat");
    have;
    dmy <- dummyVars(" ~ .", data = have);
    trsf <- data.frame(predict(dmy, newdata = have));
    print(trsf);
    endsubmit;
    import r=trsf data=wrk.trsf;
    run;quit;
    ');

    /*
    Up to 40 obs from trsf total obs=6

    Obs   ID  CATEGORY1CATEGORYA    CATEGORY1CATEGORYB    CATEGORY1CATEGORYC    CATEGORY21    CATEGORY22    CATEGORY23

     1     1           1                     0                     0                 1             0             0
     2     2           0                     1                     0                 1             0             0
     3     3           1                     0                     0                 1             0             0
     4     4           1                     0                     0                 0             1             0
     5     5           0                     0                     1                 0             0             1
     6     6           0                     1                     0                 0             1             0
    */

    * all this to create the ops variable names;
    data want;
     if _n_=0 then do;
        %let rc=%sysfunc(dosubl('
          data _null_;
             length ren1 $4096 ren2 $4096;
             retain ren1 ren2;
             set trsf end=dne;
             array cat1st[*] category1:;
             array cat2nd[*] category2:;
             do i=1 to dim(cat1st);
                nams1=catx(" ",vname(cat1st[i]),"=",cats("IND_CAT1","_",scan(vname(cat1st[i]),2,"1")));
                ren1=catx(" ",ren1,nams1);
                nams2=catx(" ",vname(cat2nd[i]),"=",cats("IND_CAT2","_",substr(vname(cat2nd[i]),10,1)));
                ren2=catx(" ",ren2,nams2);
             end;
             if dne then do;
                call symputx("ren1",ren1);
                call symputx("ren2",ren2);
             end;
          run;quit;
        '));
      end;

      merge have(keep=id category1 category2) trsf(rename=(&ren1 &ren2));
      by id;

    run;quit;

